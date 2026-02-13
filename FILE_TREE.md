# Stratus Project File Tree

```
stratus/
├── .github/
│   └── workflows/
│       └── ci.yml                    # GitHub Actions CI/CD pipeline
│
├── prisma/
│   ├── schema.prisma                 # Complete database schema
│   ├── migrations/                   # Database migration files (generated)
│   └── seed.ts                       # Database seeder with sample data
│
├── src/
│   ├── app/                          # Next.js App Router
│   │   ├── api/                      # API Routes
│   │   │   ├── admin/
│   │   │   │   ├── rubric/
│   │   │   │   │   └── upload/
│   │   │   │   │       └── route.ts  # Upload and parse rubric Excel
│   │   │   │   └── weights/
│   │   │   │       └── route.ts      # Manage pillar/criterion weights
│   │   │   ├── analyst/
│   │   │   │   ├── assessment/
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── route.ts  # Get assessment for editing
│   │   │   │   └── score-entry/
│   │   │   │       └── route.ts      # Create/update/delete scores
│   │   │   ├── subscriber/
│   │   │   │   └── compare/
│   │   │   │       └── route.ts      # Compare multiple countries
│   │   │   └── public/
│   │   │       ├── countries/
│   │   │       │   └── route.ts      # List countries with scores
│   │   │       └── country/
│   │   │           └── [id]/
│   │   │               └── route.ts  # Get country details
│   │   │
│   │   ├── (app)/                    # Protected app pages
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx          # Main dashboard
│   │   │   ├── countries/
│   │   │   │   ├── page.tsx          # Countries list
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx      # Country detail page
│   │   │   ├── assessments/
│   │   │   │   ├── page.tsx          # Assessments list
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx      # Assessment scoring page
│   │   │   ├── admin/
│   │   │   │   ├── rubrics/
│   │   │   │   │   └── page.tsx      # Rubric management
│   │   │   │   ├── weights/
│   │   │   │   │   └── page.tsx      # Weight editor
│   │   │   │   └── audit/
│   │   │   │       └── page.tsx      # Audit log viewer
│   │   │   └── layout.tsx            # App layout with navigation
│   │   │
│   │   ├── (auth)/                   # Auth pages
│   │   │   ├── login/
│   │   │   │   └── page.tsx          # Login page
│   │   │   └── layout.tsx            # Auth layout
│   │   │
│   │   ├── layout.tsx                # Root layout
│   │   ├── page.tsx                  # Home page
│   │   └── globals.css               # Global styles
│   │
│   ├── components/                   # React components
│   │   ├── ui/                       # Reusable UI components
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   └── table.tsx
│   │   ├── charts/
│   │   │   ├── radar-chart.tsx       # Pillar scores radar chart
│   │   │   └── trend-chart.tsx       # Score trends over time
│   │   ├── forms/
│   │   │   ├── score-entry-form.tsx  # Score entry form
│   │   │   └── weight-editor.tsx     # Weight editing form
│   │   └── tables/
│   │       ├── countries-table.tsx   # Countries list table
│   │       └── scores-table.tsx      # Scores entry table
│   │
│   └── lib/                          # Utilities and core logic
│       ├── parser.ts                 # Excel rubric parser (COMPLETE)
│       ├── scoring.ts                # Scoring engine (COMPLETE)
│       ├── auth.ts                   # NextAuth configuration (COMPLETE)
│       ├── prisma.ts                 # Prisma client singleton (COMPLETE)
│       ├── storage.ts                # File storage abstraction
│       ├── validation.ts             # Zod schemas
│       └── __tests__/
│           ├── parser.test.ts        # Parser unit tests (COMPLETE)
│           └── scoring.test.ts       # Scoring engine tests
│
├── public/                           # Static assets
│   ├── images/
│   └── favicon.ico
│
├── uploads/                          # File uploads (local dev, gitignored)
│   ├── rubrics/
│   └── .gitkeep
│
├── .env.example                      # Environment variables template
├── .eslintrc.json                    # ESLint configuration
├── .gitignore                        # Git ignore rules
├── .prettierrc                       # Prettier configuration
├── ARCHITECTURE.md                   # System architecture documentation
├── docker-compose.yml                # Docker Compose for local dev
├── Dockerfile.dev                    # Development Dockerfile
├── jest.config.js                    # Jest configuration
├── jest.setup.js                     # Jest setup
├── next.config.js                    # Next.js configuration
├── package.json                      # Dependencies and scripts
├── postcss.config.js                 # PostCSS configuration
├── README.md                         # Main documentation
├── tailwind.config.js                # Tailwind CSS configuration
└── tsconfig.json                     # TypeScript configuration
```

## Key Files Status

### ✅ Complete and Production-Ready

1. **Core Logic**
   - `src/lib/parser.ts` - Excel rubric parser with robust error handling
   - `src/lib/scoring.ts` - Scoring engine with flexible weighting
   - `src/lib/auth.ts` - NextAuth configuration with RBAC
   - `src/lib/prisma.ts` - Database client singleton

2. **Database**
   - `prisma/schema.prisma` - Complete schema with all models
   - `prisma/seed.ts` - Seed script with sample data

3. **API Routes**
   - `src/app/api/admin/rubric/upload/route.ts` - Rubric upload
   - `src/app/api/analyst/score-entry/route.ts` - Score management
   - `src/app/api/public/countries/route.ts` - Public country list

4. **Testing**
   - `src/lib/__tests__/parser.test.ts` - Parser unit tests
   - `.github/workflows/ci.yml` - CI/CD pipeline

5. **Configuration**
   - All config files (TypeScript, ESLint, Prettier, Tailwind, etc.)
   - Docker setup for local development
   - Environment variables template

6. **Documentation**
   - `README.md` - Comprehensive setup and usage guide
   - `ARCHITECTURE.md` - Detailed system design documentation
   - This file tree

### 🔧 Template Files (Need Implementation)

The following files are listed in the tree but need implementation:

1. **Additional API Routes** - Following the same pattern as the completed routes
2. **Frontend Pages** - UI components for admin, analyst, and subscriber features
3. **UI Components** - Reusable components (buttons, cards, tables, etc.)
4. **Additional Tests** - Scoring engine tests and integration tests

## Implementation Priority

To complete the application, implement in this order:

1. **Phase 1: Essential API Routes** (1-2 days)
   - Country CRUD
   - Assessment CRUD
   - Weight management
   - Publish actions

2. **Phase 2: Core UI** (2-3 days)
   - Login page
   - Dashboard layout
   - Countries list and detail
   - Score entry forms

3. **Phase 3: Advanced Features** (2-3 days)
   - Radar charts
   - Country comparison
   - Export functionality
   - Weight editor UI

4. **Phase 4: Admin Tools** (1-2 days)
   - Rubric upload UI
   - Audit log viewer
   - User management

## Running the Application

```bash
# Install dependencies
npm install

# Start Docker services
docker-compose up -d

# Run migrations
npx prisma migrate dev

# Seed database
npm run seed

# Start dev server
npm run dev
```

The application will be available at http://localhost:3000

Login credentials (created by seed):
- Admin: admin@stratus.com / admin123
- Analyst: analyst@stratus.com / analyst123
- Subscriber: subscriber@stratus.com / subscriber123
```
