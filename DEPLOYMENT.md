# Stratus - Setup and Deployment Guide

## Local Development Setup

### Quick Start (Docker - Recommended)

This is the fastest way to get started with Stratus.

```bash
# 1. Clone the repository
git clone <repository-url>
cd stratus

# 2. Create environment file
cp .env.example .env

# 3. Install dependencies
npm install

# 4. Start services (PostgreSQL + App)
docker-compose up -d

# Wait for services to be healthy (about 10-15 seconds)

# 5. Run database migrations
npx prisma migrate dev --name init

# 6. Generate Prisma Client
npx prisma generate

# 7. Seed the database
npm run seed

# 8. Access the application
# Open http://localhost:3000
```

**Login credentials:**
- Admin: `admin@stratus.com` / `admin123`
- Analyst: `analyst@stratus.com` / `analyst123`
- Subscriber: `subscriber@stratus.com` / `subscriber123`

### Manual Setup (Without Docker)

If you prefer to run PostgreSQL separately:

```bash
# 1. Ensure PostgreSQL 15+ is running
# Create a database named 'stratus'

# 2. Update .env with your PostgreSQL connection
DATABASE_URL="postgresql://username:password@localhost:5432/stratus?schema=public"

# 3. Install dependencies
npm install

# 4. Run migrations
npx prisma migrate dev --name init

# 5. Generate Prisma Client
npx prisma generate

# 6. Seed database
npm run seed

# 7. Start development server
npm run dev
```

## Uploading Your Rubric

### Option 1: Auto-Import During Seed

Place your Excel file at `./sample-rubric.xlsx` before running `npm run seed`. The seeder will automatically import it.

### Option 2: Upload via Admin UI

1. Log in as Admin user
2. Navigate to Admin → Import Rubric
3. Upload your Excel file
4. Review the import report
5. Publish the rubric version

### Excel File Requirements

Your Excel workbook must follow this structure:

- **One sheet per Pillar** (sheet name becomes pillar name)
- **Column A:** Criterion Group names
- **Column B:** Sub-Criterion titles
- **Column C:** Elements Assessed (guidance)
- **Column D:** Justification (guidance)

**Important:**
- Rows with Column B = "Sub-Criterion" are skipped (internal headers)
- Blank rows are automatically skipped
- Groups are created from Column A headers
- Criteria without a group are placed in "General" group

## Development Commands

```bash
# Development
npm run dev                  # Start dev server
npm run build               # Production build
npm run start               # Start production server

# Database
npx prisma studio           # Open database GUI
npx prisma migrate dev      # Run migrations (dev)
npx prisma migrate deploy   # Run migrations (production)
npx prisma generate         # Generate Prisma Client
npm run seed                # Seed database

# Code Quality
npm run lint                # Run ESLint
npm run type-check          # TypeScript type checking
npm run format              # Format code with Prettier

# Testing
npm test                    # Run tests
npm run test:watch          # Watch mode
npm run test:coverage       # Coverage report
```

## Production Deployment

### Deploy to Vercel (Recommended)

Vercel is optimized for Next.js applications.

1. **Push to GitHub**
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <your-repo-url>
git push -u origin main
```

2. **Connect to Vercel**
   - Go to [vercel.com](https://vercel.com)
   - Import your GitHub repository
   - Vercel auto-detects Next.js

3. **Configure Environment Variables**

Add these in Vercel dashboard:

```
DATABASE_URL=postgresql://user:pass@host:5432/stratus?schema=public
NEXTAUTH_SECRET=<generate-secure-random-string>
NEXTAUTH_URL=https://your-domain.vercel.app
STORAGE_TYPE=s3
AWS_S3_BUCKET=your-bucket-name
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=your-key
AWS_SECRET_ACCESS_KEY=your-secret
```

4. **Setup PostgreSQL Database**

Options:
- [Vercel Postgres](https://vercel.com/docs/storage/vercel-postgres)
- [Neon](https://neon.tech) (recommended, generous free tier)
- [Supabase](https://supabase.com)
- [Railway](https://railway.app)

5. **Run Migrations**

In Vercel dashboard or locally:
```bash
DATABASE_URL=<your-production-db-url> npx prisma migrate deploy
```

6. **Deploy**

Vercel automatically deploys on push to main branch.

### Deploy to Railway

Railway provides easy PostgreSQL + app deployment.

1. **Install Railway CLI**
```bash
npm install -g @railway/cli
railway login
```

2. **Initialize Project**
```bash
railway init
railway link
```

3. **Add PostgreSQL**
```bash
railway add postgresql
```

Railway automatically sets `DATABASE_URL`.

4. **Set Environment Variables**
```bash
railway variables set NEXTAUTH_SECRET=<secure-random-string>
railway variables set NEXTAUTH_URL=https://your-app.railway.app
```

5. **Deploy**
```bash
railway up
```

6. **Run Migrations**
```bash
railway run npx prisma migrate deploy
```

### Deploy with Docker

Build and run production container:

```bash
# Build production image
docker build -f Dockerfile.prod -t stratus:latest .

# Run container
docker run -p 3000:3000 \
  -e DATABASE_URL="postgresql://..." \
  -e NEXTAUTH_SECRET="..." \
  -e NEXTAUTH_URL="https://your-domain.com" \
  stratus:latest
```

**Production Dockerfile** (`Dockerfile.prod`):

```dockerfile
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npx prisma generate
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs
EXPOSE 3000
ENV PORT 3000

CMD ["node", "server.js"]
```

## Database Backups

### Automated Backups

For production, enable automated backups:

**Vercel Postgres:**
- Automatic daily backups included

**Neon:**
- Automatic continuous backups

**Self-hosted:**
```bash
# Daily backup cron job
0 2 * * * pg_dump stratus > /backups/stratus-$(date +\%Y\%m\%d).sql
```

### Manual Backup

```bash
# Export database
pg_dump stratus > backup.sql

# Restore database
psql stratus < backup.sql
```

## Monitoring and Logging

### Application Logs

**Development:**
```bash
docker-compose logs -f app
```

**Production:**
- Vercel: Built-in logging dashboard
- Railway: `railway logs`
- Self-hosted: Use PM2 or Docker logs

### Error Tracking

Add Sentry for production error tracking:

```bash
npm install @sentry/nextjs
npx @sentry/wizard -i nextjs
```

Update `next.config.js`:
```javascript
const { withSentryConfig } = require('@sentry/nextjs');

module.exports = withSentryConfig(
  nextConfig,
  { silent: true }
);
```

### Performance Monitoring

**Vercel Analytics:**
Add to `src/app/layout.tsx`:
```tsx
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

## Security Checklist

Before deploying to production:

- [ ] Generate strong `NEXTAUTH_SECRET` (use `openssl rand -base64 32`)
- [ ] Set `NEXTAUTH_URL` to your production domain
- [ ] Use managed PostgreSQL with SSL enabled
- [ ] Enable HTTPS (automatic with Vercel/Railway)
- [ ] Set up CORS if needed
- [ ] Review and update RBAC permissions
- [ ] Enable rate limiting on API routes
- [ ] Set up automated backups
- [ ] Configure error tracking (Sentry)
- [ ] Review file upload limits
- [ ] Enable CSP headers
- [ ] Update default admin password after first login

## Troubleshooting

### Common Issues

**Problem: Prisma Client not generated**
```bash
npx prisma generate
```

**Problem: Migration fails**
```bash
npx prisma migrate reset
npx prisma migrate dev
```

**Problem: Can't connect to database**
- Check `DATABASE_URL` in `.env`
- Ensure PostgreSQL is running
- Verify network connectivity

**Problem: Import fails**
- Check Excel file structure
- Review import report for errors
- Ensure all required columns are present

### Database Reset

```bash
# Reset database (WARNING: deletes all data)
npx prisma migrate reset

# Re-run seed
npm run seed
```

## Performance Optimization

### Database Indexing

Indexes are already defined in schema for:
- Foreign keys
- Frequently queried fields
- Status columns

### Caching

Implement Redis caching for computed scores:

```bash
npm install ioredis
```

```typescript
import Redis from 'ioredis';
const redis = new Redis(process.env.REDIS_URL);

// Cache computed score
await redis.setex(
  `score:${assessmentId}`,
  3600, // 1 hour TTL
  JSON.stringify(computedScore)
);
```

### CDN for Static Assets

Use Vercel Edge Network (automatic) or CloudFlare for static assets.

## Scaling Considerations

### Horizontal Scaling

- Use connection pooling (Prisma handles this)
- Consider read replicas for heavy read workloads
- Cache frequently accessed data

### File Storage

Switch to S3 for production:

```env
STORAGE_TYPE=s3
AWS_S3_BUCKET=your-bucket
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=your-key
AWS_SECRET_ACCESS_KEY=your-secret
```

### Rate Limiting

Add rate limiting middleware for API routes:

```typescript
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});
```

## Support

For issues and questions:
- GitHub Issues: [repository-url]/issues
- Documentation: See README.md and ARCHITECTURE.md
- Email: support@stratus.com

---

**Ready to deploy? Start with Vercel for the easiest setup!**
