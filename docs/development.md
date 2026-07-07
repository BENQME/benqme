# Development Guide

> Local setup, workflow, scripts, and environment configuration.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Setup](#setup)
3. [Environment Variables](#environment-variables)
4. [Development Scripts](#development-scripts)
5. [Workflow](#workflow)
6. [Code Quality Tools](#code-quality-tools)
7. [Database](#database)
8. [Debugging](#debugging)
9. [Troubleshooting](#troubleshooting)

---

## Prerequisites

| Tool | Version | Install |
|------|---------|---------|
| Node.js | ≥ 20.x LTS | [nodejs.org](https://nodejs.org) |
| pnpm | ≥ 9.x | `npm install -g pnpm` |
| Git | ≥ 2.40 | [git-scm.com](https://git-scm.com) |
| Docker | ≥ 24.x | [docker.com](https://docker.com) |

---

## Setup

```bash
# 1. Clone the repository
git clone https://github.com/BENQME/benqme.git
cd benqme

# 2. Install dependencies
pnpm install

# 3. Copy environment variables
cp .env.example .env.local

# 4. Fill in environment variables
# Edit .env.local with your values

# 5. Start local services (database, etc.)
docker compose up -d

# 6. Run database migrations
pnpm db:migrate

# 7. Seed development data
pnpm db:seed

# 8. Start development server
pnpm dev
```

The app will be available at [http://localhost:3000](http://localhost:3000).

---

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `DATABASE_URL` | ✅ | PostgreSQL connection string |
| `NEXTAUTH_SECRET` | ✅ | Auth.js secret (min 32 chars) |
| `NEXTAUTH_URL` | ✅ | App base URL (`http://localhost:3000` in dev) |
| `NEXT_PUBLIC_API_URL` | ✅ | Public API base URL |
| `GITHUB_CLIENT_ID` | ⚠️ | GitHub OAuth client ID |
| `GITHUB_CLIENT_SECRET` | ⚠️ | GitHub OAuth client secret |
| `RESEND_API_KEY` | ⚠️ | Email sending API key |
| `UPLOADTHING_SECRET` | ⚠️ | File upload secret |
| `UPLOADTHING_APP_ID` | ⚠️ | File upload app ID |

⚠️ = Required for specific features, optional for basic local development.

**Never commit `.env.local` to version control.** It's already in `.gitignore`.

---

## Development Scripts

```bash
pnpm dev          # Start development server with hot reload
pnpm build        # Production build
pnpm start        # Start production server
pnpm lint         # Run ESLint
pnpm lint:fix     # Run ESLint with auto-fix
pnpm format       # Format with Prettier
pnpm typecheck    # TypeScript type checking (no emit)
pnpm test         # Run unit tests (Vitest)
pnpm test:watch   # Unit tests in watch mode
pnpm test:ui      # Unit tests with Vitest UI
pnpm test:e2e     # End-to-end tests (Playwright)
pnpm test:e2e:ui  # Playwright UI mode
pnpm storybook    # Start Storybook on port 6006
pnpm db:migrate   # Run pending migrations
pnpm db:seed      # Seed development data
pnpm db:studio    # Open Prisma Studio
pnpm db:reset     # Drop, recreate, migrate, seed
pnpm analyze      # Analyze production bundle
```

---

## Workflow

### Starting a new task

```bash
# 1. Ensure you're on develop with latest changes
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/my-feature

# 3. Make changes, commit frequently
git add .
git commit -m "feat(scope): description"

# 4. Keep branch up to date
git fetch origin develop
git rebase origin/develop

# 5. Push and open PR
git push origin feature/my-feature
```

### Before Every Commit

```bash
pnpm lint:fix   # auto-fix linting issues
pnpm typecheck  # catch type errors
pnpm test       # ensure tests pass
```

### Pre-commit Hook

The project uses [Husky](https://typicode.github.io/husky/) + [lint-staged](https://github.com/lint-staged/lint-staged) to automate this:

```json
// package.json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{css,md,json}": ["prettier --write"]
  }
}
```

---

## Code Quality Tools

### ESLint
```bash
pnpm lint         # Check
pnpm lint:fix     # Auto-fix
```

Config: `.eslintrc.js`

### Prettier
```bash
pnpm format       # Format all files
```

Config: `.prettierrc`

### TypeScript
```bash
pnpm typecheck    # Type-check without emitting files
```

Config: `tsconfig.json` (strict mode enabled)

### Commitlint
Commit messages are validated against Conventional Commits:
```bash
# ✅ Valid
git commit -m "feat(auth): add GitHub OAuth"

# ❌ Invalid
git commit -m "added github login"
```

---

## Database

### Local Development (Docker)
```bash
# Start PostgreSQL
docker compose up -d db

# Stop
docker compose stop db

# View logs
docker compose logs -f db
```

### Prisma Commands
```bash
# Generate Prisma Client after schema changes
pnpm prisma generate

# Create a new migration
pnpm prisma migrate dev --name add-user-roles

# Apply migrations in production
pnpm prisma migrate deploy

# Open Prisma Studio (visual DB browser)
pnpm db:studio

# Reset local DB
pnpm db:reset
```

---

## Debugging

### VS Code Launch Config

```json
// .vscode/launch.json
{
  "configurations": [
    {
      "name": "Next.js: debug server-side",
      "type": "node-terminal",
      "request": "launch",
      "command": "pnpm dev"
    },
    {
      "name": "Next.js: debug client-side",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000"
    }
  ]
}
```

### React DevTools
Install the [React DevTools browser extension](https://react.dev/learn/react-developer-tools) to inspect component trees and profiler.

### Network Inspector
Use the browser Network tab with "Preserve log" enabled for debugging API calls across page navigations.

---

## Troubleshooting

### Port already in use
```bash
# Find and kill process on port 3000
lsof -ti:3000 | xargs kill -9
```

### `pnpm install` fails
```bash
# Clear cache and retry
pnpm store prune
rm -rf node_modules
pnpm install
```

### Database connection refused
```bash
# Check if Docker is running
docker compose ps

# Restart DB container
docker compose restart db
```

### TypeScript errors after pulling
```bash
# Regenerate Prisma client
pnpm prisma generate

# Restart TS server in VS Code
Cmd/Ctrl + Shift + P → "TypeScript: Restart TS Server"
```

### Stale `.next` cache
```bash
rm -rf .next
pnpm dev
```
