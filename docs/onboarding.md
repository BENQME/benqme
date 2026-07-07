# Onboarding

> A guided journey for new contributors to get productive quickly.

---

## Table of Contents

1. [Welcome](#welcome)
2. [Day 1: Setup](#day-1-setup)
3. [Day 2: Orientation](#day-2-orientation)
4. [Day 3: First Contribution](#day-3-first-contribution)
5. [Week 1: Go Deeper](#week-1-go-deeper)
6. [Key People & Communication](#key-people--communication)
7. [Useful Links](#useful-links)
8. [FAQ](#faq)

---

## Welcome

Welcome to the project! This guide will walk you through everything you need to get started and make your first contribution.

The goal for your first week:
- [ ] Dev environment set up and running
- [ ] Understand the project structure and architecture
- [ ] Submit at least one pull request (even a small docs fix counts!)
- [ ] Ask questions freely — there are no bad questions

---

## Day 1: Setup

### Prerequisites
Install the required tools:
- [Node.js 20 LTS](https://nodejs.org)
- [pnpm](https://pnpm.io): `npm install -g pnpm`
- [Docker Desktop](https://docker.com)
- [Git](https://git-scm.com)
- [VS Code](https://code.visualstudio.com) (recommended)

### Clone & Install
```bash
git clone https://github.com/BENQME/benqme.git
cd benqme
pnpm install
```

### Environment Setup
```bash
cp .env.example .env.local
# Open .env.local and fill in your local values
# Ask a teammate for any shared development API keys
```

### Start the App
```bash
docker compose up -d    # Start local database
pnpm db:migrate         # Run migrations
pnpm db:seed            # Add seed data
pnpm dev                # Start dev server → http://localhost:3000
```

### Verify Everything Works
```bash
pnpm test               # All tests should pass
pnpm build              # Build should succeed
```

**Checkpoint**: You should see the app running at http://localhost:3000. If anything doesn't work, check the [Troubleshooting](./development.md#troubleshooting) section.

---

## Day 2: Orientation

### Read the Docs
Work through these documents in order:
1. [`instructions.md`](./instructions.md) — coding conventions and workflow
2. [`architecture.md`](./architecture.md) — how the system is designed
3. [`structure.md`](./structure.md) — where things live
4. [`design.md`](./design.md) — design system overview
5. [`development.md`](./development.md) — daily development workflow

### Explore the Codebase
```bash
# Understand the folder structure
ls -la src/

# Find all page components
find src/app -name "page.tsx"

# Find all shared components
ls src/components/ui/

# Understand how data fetching works
cat src/lib/db.ts
cat src/lib/auth.ts
```

### Run the Test Suite
```bash
pnpm test --coverage     # See what's tested and what isn't
pnpm test:e2e            # See what user flows are covered
pnpm storybook           # Explore the component library
```

### Browse Storybook
```bash
pnpm storybook
# Open http://localhost:6006
# Click through the component library
# Each component shows its variants and interactive states
```

---

## Day 3: First Contribution

### Find Something to Work On
Look for issues labeled:
- `good first issue` — beginner-friendly tasks
- `documentation` — documentation improvements
- `bug` — confirmed bugs looking for a fix

Or make your own improvement:
- Fix a typo in the docs
- Add a missing `alt` text
- Improve test coverage for an untested function
- Add a missing Storybook story

### Make Your First PR
```bash
# 1. Create a branch
git checkout -b docs/fix-typo-in-readme

# 2. Make your change
# ... edit the file ...

# 3. Verify
pnpm lint
pnpm test

# 4. Commit
git add .
git commit -m "docs: fix typo in onboarding guide"

# 5. Push
git push origin docs/fix-typo-in-readme

# 6. Open a PR on GitHub targeting 'develop'
```

Your first PR will be reviewed promptly. Don't worry about it being perfect — the review process is collaborative!

---

## Week 1: Go Deeper

### Understand the Tech Stack
- **[Next.js App Router](https://nextjs.org/docs/app)** — especially Server Components and layouts
- **[Prisma](https://www.prisma.io/docs)** — the ORM for database access
- **[TanStack Query](https://tanstack.com/query/v5)** — server state management
- **[Tailwind CSS](https://tailwindcss.com/docs)** — utility-first styling
- **[Auth.js](https://authjs.dev/)** — authentication

### Explore Key Features
Pick one feature from [`tasks.md`](./tasks.md) and trace it through the entire stack:
1. Find the page component (`src/app/`)
2. Find the data fetching hook (`src/features/*/hooks/`)
3. Find the API route (`src/app/api/`)
4. Find the database query (`src/lib/db.ts` or Prisma calls)
5. Find the tests

### Set Up Your IDE
Install recommended VS Code extensions:
- **ESLint** — `dbaeumer.vscode-eslint`
- **Prettier** — `esbenp.prettier-vscode`
- **Tailwind CSS IntelliSense** — `bradlc.vscode-tailwindcss`
- **Prisma** — `prisma.prisma`
- **GitLens** — `eamodio.gitlens`

Import the project workspace settings:
```bash
cp .vscode/settings.example.json .vscode/settings.json
```

---

## Key People & Communication

| Role | Contact |
|------|---------|
| Maintainer | [@BENQME](https://github.com/BENQME) |

**Communication channels:**
- GitHub Issues — bug reports and feature requests
- GitHub Discussions — questions, ideas, and general discussion
- PR comments — code-specific feedback

---

## Useful Links

| Resource | URL |
|----------|-----|
| Repository | https://github.com/BENQME/benqme |
| Issues | https://github.com/BENQME/benqme/issues |
| Discussions | https://github.com/BENQME/benqme/discussions |
| Docs folder | `./docs/` |

---

## FAQ

### "I can't connect to the database"
```bash
docker compose up -d     # Start Docker containers
docker compose ps        # Verify they're running
```

### "My changes aren't showing up"
```bash
rm -rf .next && pnpm dev   # Clear Next.js cache
```

### "TypeScript is showing errors I didn't introduce"
```bash
pnpm prisma generate       # Regenerate Prisma client after schema changes
```
In VS Code: `Cmd/Ctrl + Shift + P → TypeScript: Restart TS Server`

### "The tests are failing on unrelated files"
```bash
pnpm test --force          # Ignore test cache
```

### "I don't understand this code — who do I ask?"
Open a GitHub Discussion or leave a comment on the relevant file. There's no such thing as a question too small.

### "Can I work on something not in the issue tracker?"
Yes! Open an issue first to describe what you want to do and get feedback before investing significant time.
