# Deployment

> Environments, deployment process, CI/CD pipeline, and rollback procedures.

---

## Table of Contents

1. [Environments](#environments)
2. [CI/CD Pipeline](#cicd-pipeline)
3. [Deployment Process](#deployment-process)
4. [Environment Variables](#environment-variables)
5. [Database Migrations](#database-migrations)
6. [Rollback](#rollback)
7. [Monitoring](#monitoring)
8. [Deployment Checklist](#deployment-checklist)

---

## Environments

| Environment | Branch | URL | Auto-deploy |
|-------------|--------|-----|-------------|
| **Production** | `main` | `https://benqme.com` | ✅ On merge |
| **Staging** | `develop` | `https://staging.benqme.com` | ✅ On merge |
| **Preview** | Any PR branch | `https://pr-{n}.benqme.vercel.app` | ✅ On push |
| **Local** | — | `http://localhost:3000` | — |

---

## CI/CD Pipeline

```
Push / PR Open
     │
     ▼
┌─────────────────┐
│  Install deps   │  pnpm install
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Lint & types   │  pnpm lint && pnpm typecheck
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Unit tests     │  pnpm test --coverage
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Build          │  pnpm build
└────────┬────────┘
         │
     (if main/develop)
         │
         ▼
┌─────────────────┐
│  Deploy         │  Vercel
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  E2E tests      │  pnpm test:e2e (against deployed URL)
└─────────────────┘
```

### GitHub Actions Workflows

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm

      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm typecheck
      - run: pnpm test --coverage
      - run: pnpm build
```

---

## Deployment Process

### Production Deployment (Automated)

1. Merge PR to `develop` → staging deploys automatically
2. Staging tested and approved by team
3. Create PR from `develop` → `main`
4. PR merged → production deploys automatically
5. Vercel runs build + deploys to edge network
6. Post-deploy smoke tests run automatically

### Manual Deployment (Emergency)

```bash
# Deploy from local machine (requires Vercel CLI)
npm install -g vercel
vercel login

# Deploy to preview
vercel

# Deploy to production (use sparingly!)
vercel --prod
```

---

## Environment Variables

### Setting Production Variables

All secrets are managed in the **Vercel dashboard** (never in git):

1. Go to: Vercel → Project → Settings → Environment Variables
2. Add variables for each environment (Production / Preview / Development)
3. Variables prefixed with `NEXT_PUBLIC_` are exposed to the client

### Required Production Variables

| Variable | Notes |
|----------|-------|
| `DATABASE_URL` | PostgreSQL connection string (use connection pooler) |
| `NEXTAUTH_SECRET` | Random string, min 32 chars: `openssl rand -base64 32` |
| `NEXTAUTH_URL` | Production URL: `https://benqme.com` |
| `GITHUB_CLIENT_ID` | From GitHub OAuth app |
| `GITHUB_CLIENT_SECRET` | From GitHub OAuth app |
| `RESEND_API_KEY` | Email sending |

See [`.env.example`](../.env.example) for the full list.

---

## Database Migrations

### On Every Deployment

Migrations run automatically as part of the build/deploy process:

```json
// package.json
{
  "scripts": {
    "postbuild": "prisma migrate deploy"
  }
}
```

### Manual Migration (Staging/Production)

```bash
# Apply pending migrations to production DB
DATABASE_URL="<prod-db-url>" pnpm prisma migrate deploy

# Verify migration status
DATABASE_URL="<prod-db-url>" pnpm prisma migrate status
```

### Migration Safety Rules
- Never run `prisma migrate dev` against production
- Use `migrate deploy` only (applies pending migrations, no prompts)
- Always test migrations on staging before production
- For breaking schema changes, use expand-contract pattern

---

## Rollback

### Vercel Rollback (Instant)

Vercel keeps all previous deployments. To rollback:

1. Go to Vercel → Project → Deployments
2. Find the last known-good deployment
3. Click "Promote to Production"
4. Production is instantly restored (no rebuild needed)

### Database Rollback

If a migration broke things:

1. Do **not** run `prisma migrate rollback` (it doesn't exist in Prisma)
2. Write a new migration that reverts the breaking change
3. Apply it via `prisma migrate deploy`
4. For data loss situations, restore from backup

### Git Rollback

```bash
# Revert the last merge commit (creates a new revert commit)
git revert -m 1 <merge-commit-sha>
git push origin main
```

---

## Monitoring

### Error Tracking
Configure Sentry for production error tracking:

```typescript
// sentry.client.config.ts
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 0.1,
  environment: process.env.NODE_ENV,
});
```

### Performance Monitoring
- Core Web Vitals tracked via `web-vitals` library → sent to analytics
- Vercel Analytics for real user monitoring (RUM)
- Lighthouse CI on every PR for synthetic monitoring

### Uptime Monitoring
- Configure uptime checks (e.g., Better Uptime, Checkly) on production URL
- Alert on: > 1 minute downtime, > 5xx errors > 1%

### Logs
- Vercel Functions logs: accessible in Vercel dashboard → Project → Logs
- Filter by: level (error, warn, info), function name, timestamp

---

## Deployment Checklist

### Pre-deployment
- [ ] All CI checks pass on the branch
- [ ] Staging environment tested manually
- [ ] Database migration tested on staging
- [ ] Environment variables configured in Vercel dashboard
- [ ] Dependent services configured (webhooks, third-party callbacks)
- [ ] Rollback plan identified

### During Deployment
- [ ] Team notified of deployment start
- [ ] Monitoring dashboards open

### Post-deployment
- [ ] Smoke test: home page, auth flow, key features
- [ ] Error rate: no spike in Sentry/logs
- [ ] Core Web Vitals: still green in Vercel Analytics
- [ ] Database migrations: applied successfully
- [ ] Team notified of deployment success/failure
