# Stratus - Carbon Market Policy Scoring Platform

A production-grade web application that transforms Excel-based carbon market readiness rubrics into an interactive scoring engine with analytics, country comparisons, and investor-ready dashboards.

## 🎯 Overview

Stratus enables organizations to:
- **Import** policy assessment rubrics from Excel workbooks
- **Score** countries against standardized criteria
- **Analyze** readiness scores with pillar breakdowns and radar charts
- **Compare** multiple countries side-by-side
- **Track** changes over time with full audit trails

## 🏗️ Architecture

**Tech Stack:**
- **Frontend:** Next.js 14 (App Router) + TypeScript + Tailwind CSS
- **Backend:** Next.js API Routes
- **Database:** PostgreSQL 15 + Prisma ORM
- **Auth:** NextAuth.js v5 with RBAC
- **Charts:** Recharts
- **Excel Parsing:** SheetJS (xlsx)

**Key Features:**
- Versioned rubric management (draft → published)
- Flexible weighting system (pillar & criterion level)
- Two scoring modes: re-normalize or treat missing as zero
- Role-based access control (PUBLIC, SUBSCRIBER, ANALYST, ADMIN)
- Complete audit logging
- Real-time score computation
- File upload (local or S3-compatible storage)

## 🚀 Quick Start

### Prerequisites

- Node.js 18+ 
- Docker & Docker Compose (for local development)
- PostgreSQL 15+ (if not using Docker)

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd stratus
```

2. **Install dependencies**
```bash
npm install
```

3. **Configure environment**
```bash
cp .env.example .env
# Edit .env with your settings
```

4. **Start services with Docker Compose**
```bash
docker-compose up -d
```

This starts:
- PostgreSQL on port 5432
- Next.js dev server on port 3000

5. **Run database migrations**
```bash
npx prisma migrate dev
```

6. **Seed the database**
```bash
npm run seed
```

This creates:
- Admin user: `admin@stratus.com` / `admin123`
- Analyst user: `analyst@stratus.com` / `analyst123`
- Subscriber user: `subscriber@stratus.com` / `subscriber123`
- 3 sample countries (Kenya, Brazil, Vietnam)

7. **Access the application**
```bash
# Development server already running via Docker
# Or run manually:
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

### Without Docker

If you prefer to run PostgreSQL separately:

1. **Start PostgreSQL** (ensure it's running on port 5432)

2. **Update .env** with your database connection

3. **Run migrations and seed**
```bash
npx prisma migrate dev
npm run seed
```

4. **Start dev server**
```bash
npm run dev
```

## 📊 Importing a Rubric

### Excel File Format

The application expects an Excel workbook (`.xlsx`) with the following structure:

- **Each sheet** represents a top-level **Pillar**
- **Column A:** Criterion Group names (section headers)
- **Column B:** Sub-Criterion titles (scored items)
- **Column C:** Elements Assessed (guidance text)
- **Column D:** Justification (additional guidance)

**Important Notes:**
- Rows where Column B = "Sub-Criterion" are treated as internal headers and skipped
- Blank rows are automatically skipped
- Groups are inferred from Column A headers
- Sub-Criteria without a group header are placed in a default "General" group

### Example Structure

```
Sheet: "Registry Readiness"

| Column A (Group)                | Column B (Sub-Criterion)       | Column C (Elements)                 | Column D (Justification) |
|---------------------------------|--------------------------------|-------------------------------------|--------------------------|
| Authorization Clarity           |                                |                                     |                          |
|                                 | Sub-Criterion                  | Elements Assessed                   | Justification            |
|                                 | Statutory authority            | Existence of climate laws...        |                          |
|                                 | NDC alignment                  | Market linked to NDC targets...     |                          |
| Project Approval Processes      |                                |                                     |                          |
|                                 | Defined steps                  | Codified approval flow...           |                          |
```

### Import Process

1. Log in as **Admin**
2. Navigate to **Admin → Import Rubric**
3. Upload your Excel file
4. Review the **Import Report** showing:
   - Pillars, groups, and criteria counts
   - Skipped rows
   - Validation warnings
5. **Publish** the rubric version to make it available for scoring

## 🎯 Scoring Workflow

### 1. Create an Assessment

**As an Analyst or Admin:**
1. Go to **Assessments → New Assessment**
2. Select a country and rubric version
3. The assessment is created in **DRAFT** status

### 2. Enter Scores

Two entry modes available:

**Table View** (bulk entry):
- View all criteria in a filterable table
- Quick entry with keyboard navigation
- Bulk actions

**Form View** (guided):
- Step through criterion groups
- See full guidance text
- Attach evidence URLs
- Add confidence scores (0-1)
- Include analyst notes

### 3. Review & Publish

1. View the **Computed Score** dashboard:
   - Overall score (0-100)
   - Pillar breakdown
   - Radar chart visualization
   - Top 10 risks (lowest scoring criteria)
   - Completeness percentage

2. **Publish** the assessment to make it visible to subscribers

## 📈 Analytics & Comparisons

### Public Access
- Country list with overall scores
- Basic radar chart of pillar scores

### Subscriber Access
- Full criterion-level breakdowns
- Evidence links and analyst notes
- **Side-by-side comparison** of 2-5 countries
- Trend charts (when multiple versions exist)
- Export to CSV & PDF

### Admin/Analyst Access
- All subscriber features
- Score entry and editing
- Weight management
- Audit log viewer

## ⚖️ Weighting System

### Default Weights
- All pillars equally weighted (1.0 each)
- All sub-criteria within a group equally weighted (1.0 each)

### Custom Weights
**Admins can customize:**
1. **Pillar weights** - Emphasize certain pillars over others
2. **Sub-criterion weights** - Adjust importance within groups

### Missing Score Handling

Two modes (configured per rubric version):

1. **RENORMALIZE** (default)
   - Scores re-normalized to only include scored items
   - Denominator excludes missing scores
   - Use when partial scoring is acceptable

2. **AS_ZERO**
   - Missing scores treated as 0
   - Denominator includes all criteria
   - Use when completeness is required

## 🧪 Testing

```bash
# Run all tests
npm test

# Watch mode
npm test:watch

# Coverage report
npm test:coverage
```

## 🔒 Security

- **Input Validation:** Zod schemas on all API endpoints
- **SQL Injection:** Protected by Prisma ORM
- **XSS:** React auto-escaping + CSP headers
- **File Upload:** MIME type and size validation
- **Password Hashing:** bcrypt with salt rounds
- **Session Management:** Secure HTTP-only cookies

## 📦 Deployment

### Vercel (Recommended)

1. **Connect your repository** to Vercel
2. **Add environment variables** in Vercel dashboard
3. **Deploy**

Vercel auto-detects Next.js and handles:
- Build optimization
- Edge functions
- Automatic HTTPS

### Docker Production Build

```bash
# Build production image
docker build -t stratus:latest .

# Run with environment variables
docker run -p 3000:3000 \
  -e DATABASE_URL="postgresql://..." \
  -e NEXTAUTH_SECRET="..." \
  stratus:latest
```

### Database Migrations

```bash
# Production migrations
npx prisma migrate deploy
```

## 🗂️ Project Structure

```
stratus/
├── prisma/
│   ├── schema.prisma          # Database schema
│   ├── migrations/            # Migration files
│   └── seed.ts                # Database seeder
├── src/
│   ├── app/                   # Next.js App Router
│   │   ├── api/              # API routes
│   │   │   ├── admin/        # Admin endpoints
│   │   │   ├── analyst/      # Analyst endpoints
│   │   │   ├── subscriber/   # Subscriber endpoints
│   │   │   └── public/       # Public endpoints
│   │   ├── (app)/            # Protected app pages
│   │   └── (auth)/           # Auth pages
│   ├── components/           # React components
│   │   └── ui/              # Reusable UI components
│   └── lib/                 # Utilities
│       ├── parser.ts        # Excel parsing logic
│       ├── scoring.ts       # Scoring engine
│       ├── auth.ts          # NextAuth config
│       └── prisma.ts        # Prisma client
├── public/                  # Static assets
├── uploads/                 # File uploads (local dev)
└── docker-compose.yml       # Local development setup
```

## 🛠️ Development Commands

```bash
npm run dev          # Start dev server
npm run build        # Production build
npm run start        # Start production server
npm run lint         # Run ESLint
npm run format       # Format code with Prettier
npm run type-check   # TypeScript type checking
npm run prisma:studio # Open Prisma Studio (database GUI)
```

## 📝 API Documentation

### Public Endpoints

```
GET  /api/public/countries           # List all countries with published scores
GET  /api/public/country/[id]        # Get country details with overall score
```

### Subscriber Endpoints

```
POST /api/subscriber/compare         # Compare multiple countries
GET  /api/subscriber/export/[id]     # Export country report (CSV/PDF)
```

### Analyst Endpoints

```
GET    /api/analyst/assessment/[countryId]  # Get assessment for editing
POST   /api/analyst/score-entry             # Create score entry
PUT    /api/analyst/score-entry/[id]        # Update score entry
DELETE /api/analyst/score-entry/[id]        # Delete score entry
POST   /api/analyst/assessment/publish      # Publish assessment
```

### Admin Endpoints

```
POST /api/admin/rubric/upload        # Upload and parse Excel rubric
POST /api/admin/rubric/publish       # Publish rubric version
PUT  /api/admin/weights              # Update pillar/criterion weights
GET  /api/admin/import-report/[id]   # Get import report details
GET  /api/admin/audit-logs           # View audit trail
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License.

## 🙏 Acknowledgments

- Built with [Next.js](https://nextjs.org/)
- Database with [Prisma](https://www.prisma.io/)
- Auth powered by [NextAuth.js](https://next-auth.js.org/)
- Charts by [Recharts](https://recharts.org/)

## 📧 Support

For issues, questions, or feature requests, please open an issue on GitHub.

---

**Built with ❤️ for carbon market transparency**
