# Stratus - Carbon Market Policy Scoring Platform

## Architecture Overview

### System Design Principles

1. **Single Monorepo**: Next.js handles both frontend and backend in a unified codebase
2. **Type Safety**: End-to-end TypeScript with strict mode
3. **Database-First**: Prisma ORM with PostgreSQL for data integrity
4. **Version Control**: Rubric versions are immutable once published
5. **Audit Trail**: All scoring actions and changes are logged
6. **Role-Based Access**: Granular permissions (PUBLIC, SUBSCRIBER, ANALYST, ADMIN)

### Technology Stack

```
Frontend:
- Next.js 14 (App Router)
- TypeScript 5+
- Tailwind CSS 3
- Recharts (visualizations)
- React Hook Form + Zod (validation)

Backend:
- Next.js API Routes
- Prisma ORM
- PostgreSQL 15+
- NextAuth.js v5 (Auth)

Development:
- Docker Compose (local dev)
- Jest + React Testing Library
- GitHub Actions (CI/CD)
- ESLint + Prettier
```

### Data Model

```
┌─────────────────┐
│ RubricVersion   │──┐
│ - id            │  │
│ - version       │  │
│ - status        │  │
│ - uploadedFile  │  │
└─────────────────┘  │
                     │
         ┌───────────┴───────────┐
         │                       │
    ┌────▼────┐         ┌────────▼────────┐
    │ Pillar  │         │   WeightSet     │
    └────┬────┘         │  - pillarWeights│
         │              │  - criterionWts │
    ┌────▼─────────┐    └─────────────────┘
    │CriterionGroup│
    └────┬─────────┘
         │
    ┌────▼──────────┐
    │ SubCriterion  │
    │ - title       │
    │ - elements    │
    │ - guidance    │
    └───────────────┘

┌──────────┐        ┌────────────────────┐
│ Country  │────────│ CountryAssessment  │
└──────────┘        │ - country          │
                    │ - rubricVersion    │
                    │ - status           │
                    └────┬───────────────┘
                         │
                ┌────────┴────────┐
                │                 │
         ┌──────▼──────┐    ┌────▼──────────┐
         │ ScoreEntry  │    │ComputedScore  │
         │ - score 0-5 │    │ - overall 0-100│
         │ - confidence│    │ - pillarScores │
         │ - evidence  │    │ - completeness │
         │ - notes     │    │ - topRisks     │
         └─────────────┘    └────────────────┘

┌──────────────┐
│  AuditLog    │
│ - action     │
│ - userId     │
│ - metadata   │
└──────────────┘
```

### Excel Import Pipeline

```
1. Upload Excel File
   ↓
2. Parse Workbook (SheetJS)
   ↓
3. For Each Sheet (Pillar):
   - Identify CriterionGroups (Column A headers)
   - Extract SubCriteria (Column B rows)
   - Capture guidance (Columns C & D)
   - Skip "Sub-Criterion" header rows
   - Handle blank rows gracefully
   ↓
4. Create Database Records
   - RubricVersion (draft)
   - Pillars
   - CriterionGroups
   - SubCriteria (with sortOrder)
   ↓
5. Generate Import Report
   - Counts per pillar
   - Skipped rows
   - Validation warnings
   ↓
6. Store Raw File + JSON Snapshot
   ↓
7. Admin Reviews & Publishes
```

### Scoring Engine Logic

```typescript
// Core scoring algorithm
function computeScore(assessment: Assessment, weights: WeightSet) {
  // 1. Group scores by pillar
  const pillarScores = groupByPillar(assessment.scoreEntries);
  
  // 2. For each pillar:
  pillarScores.forEach(pillar => {
    // Apply sub-criterion weights
    const weightedSum = pillar.scores.reduce((sum, score) => {
      const weight = weights.get(score.subCriterionId);
      const confidenceFactor = score.confidence ?? 1.0;
      return sum + (score.value * weight * confidenceFactor);
    }, 0);
    
    // Handle missing scores
    if (config.missingScoresAsZero) {
      // Include missing in denominator
      pillar.score = weightedSum / totalPossibleWeight;
    } else {
      // Re-normalize to scored items only
      pillar.score = weightedSum / sumOfActiveWeights;
    }
  });
  
  // 3. Aggregate to overall score
  const overall = pillars.reduce((sum, p) => {
    return sum + (p.score * weights.pillarWeight(p.id));
  }, 0);
  
  // 4. Convert to 0-100 scale
  return {
    overall: overall * 100,
    pillars: pillarScores.map(p => ({ ...p, score: p.score * 100 })),
    completeness: scoredCount / totalCriteria,
    topRisks: getLowestScoring(pillarScores, 10)
  };
}
```

### API Routes

```
Public API:
  GET  /api/public/countries
  GET  /api/public/country/[id]
  
Subscriber API:
  POST /api/subscriber/compare
  GET  /api/subscriber/export/[countryId]
  
Admin API:
  POST /api/admin/rubric/upload
  POST /api/admin/rubric/publish
  PUT  /api/admin/weights
  GET  /api/admin/import-report/[id]
  
Analyst API:
  GET  /api/analyst/assessment/[countryId]
  POST /api/analyst/score-entry
  PUT  /api/analyst/score-entry/[id]
  DELETE /api/analyst/score-entry/[id]
  POST /api/analyst/assessment/publish
```

### Authentication & Authorization

```typescript
Role Hierarchy:
  PUBLIC < SUBSCRIBER < ANALYST < ADMIN

Permissions:
  PUBLIC:
    - View country list
    - View overall scores + radar
  
  SUBSCRIBER:
    - All PUBLIC permissions
    - View criterion-level details
    - Export reports
    - Compare countries
  
  ANALYST:
    - All SUBSCRIBER permissions
    - Create/edit assessments
    - Enter scores
    - View evidence
  
  ADMIN:
    - All ANALYST permissions
    - Upload rubric
    - Manage weights
    - Publish versions
    - Audit logs
```

### File Storage

```
Development:
  /uploads/rubrics/[version-id].xlsx
  
Production:
  S3-compatible bucket
  Environment variable: STORAGE_TYPE=local|s3
```

### Deployment Architecture

```
Development:
  Docker Compose:
    - postgres:15
    - app:dev (hot reload)
    
Production:
  - Vercel / Railway / Self-hosted
  - PostgreSQL (managed service)
  - S3 or equivalent for file storage
```

## Key Features

### 1. Robust Excel Parser
- Handles inconsistent formatting
- Skips internal headers
- Preserves row order
- Logs skipped content
- Validates structure

### 2. Versioned Rubrics
- Immutable published versions
- Draft → Review → Published workflow
- Import reports with warnings
- Rollback capability

### 3. Flexible Weighting
- Default: equal weights
- Custom weights per pillar/criterion
- Weight sets versioned with rubric
- Re-normalization for missing scores

### 4. Rich Scoring UI
- Table view (bulk entry)
- Form view (guided)
- Evidence URL attachment
- Confidence scoring
- Auto-save drafts

### 5. Comprehensive Analytics
- Overall score (0-100)
- Pillar breakdown
- Radar charts
- Country comparisons
- Change over time
- Top risk identification

### 6. Audit Trail
- Import actions
- Score changes
- Weight updates
- Publish events
- User attribution

## Security Considerations

1. **Input Validation**: Zod schemas for all API inputs
2. **SQL Injection**: Prevented by Prisma ORM
3. **XSS**: React auto-escaping + CSP headers
4. **File Upload**: MIME type validation, size limits
5. **Rate Limiting**: API route middleware (production)
6. **Secrets**: Environment variables, never committed

## Performance Optimization

1. **Database Indexing**: Foreign keys, frequently queried fields
2. **Caching**: Computed scores cached, invalidated on update
3. **Pagination**: Country lists, audit logs
4. **Lazy Loading**: Charts render client-side
5. **Code Splitting**: Route-based chunks

## Testing Strategy

1. **Unit Tests**: Parsing logic, scoring engine
2. **Integration Tests**: API routes
3. **E2E Tests**: Critical user flows (future)
4. **Type Safety**: TypeScript strict mode

## Development Workflow

```bash
# 1. Clone and setup
git clone <repo>
cd stratus
cp .env.example .env
docker-compose up -d

# 2. Install dependencies
npm install

# 3. Database setup
npx prisma migrate dev
npm run seed

# 4. Run dev server
npm run dev

# 5. Run tests
npm test

# 6. Build for production
npm run build
```

## Monitoring & Logging

1. **Application Logs**: Console (dev), structured JSON (prod)
2. **Error Tracking**: Sentry integration (optional)
3. **Performance**: Vercel Analytics or equivalent
4. **Database**: Query performance monitoring

## Future Enhancements

1. Multi-language support
2. PDF export with branding
3. Email notifications
4. Real-time collaboration
5. Mobile app
6. Machine learning risk predictions
7. Third-party data integrations
8. API rate limiting & keys
9. Advanced caching (Redis)
10. GraphQL endpoint option
