# Instructions

> General usage instructions and conventions for this project.

---

## Table of Contents

1. [Overview](#overview)
2. [Getting Started](#getting-started)
3. [Folder Conventions](#folder-conventions)
4. [Naming Conventions](#naming-conventions)
5. [Coding Standards](#coding-standards)
6. [Commit Conventions](#commit-conventions)
7. [Branch Strategy](#branch-strategy)
8. [Review Process](#review-process)
9. [Documentation](#documentation)

---

## Overview

This document serves as the primary reference for contributors and maintainers. Read it before making any changes to the project.

---

## Getting Started

```bash
# 1. Clone the repository
git clone https://github.com/BENQME/benqme.git
cd benqme

# 2. Install dependencies
npm install        # or: pnpm install / yarn install

# 3. Start development server
npm run dev

# 4. Run tests
npm run test

# 5. Build for production
npm run build
```

---

## Folder Conventions

```
src/
├── app/           # Application entry points and routing
├── components/    # Reusable UI components
├── features/      # Feature-scoped modules
├── hooks/         # Custom React hooks
├── lib/           # Third-party wrappers and utilities
├── styles/        # Global styles and tokens
├── types/         # TypeScript type definitions
└── utils/         # Pure utility functions
docs/              # Project documentation
public/            # Static assets
```

---

## Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Components | PascalCase | `Button.tsx` |
| Hooks | camelCase with `use` prefix | `useTheme.ts` |
| Utilities | camelCase | `formatDate.ts` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_RETRY` |
| Types/Interfaces | PascalCase | `UserProfile` |
| CSS modules | camelCase | `button.module.css` |
| Files | kebab-case | `user-profile.tsx` |
| Folders | kebab-case | `user-settings/` |

---

## Coding Standards

- **Language**: TypeScript strict mode enabled
- **Formatting**: Prettier with project config
- **Linting**: ESLint with recommended rules
- **Imports**: Absolute paths via `@/` alias
- **Exports**: Named exports preferred; default exports for pages/layouts
- **Props**: Destructure at function signature; explicit types required
- **Errors**: Never swallow errors silently; always log or propagate
- **Side effects**: Isolate in hooks or service modules

---

## Commit Conventions

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

### Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no logic change |
| `refactor` | Code change, no feature/fix |
| `perf` | Performance improvement |
| `test` | Adding or updating tests |
| `chore` | Build process, dependencies |
| `ci` | CI/CD changes |

---

## Branch Strategy

```
main          # Production-ready code
develop       # Integration branch
feature/*     # New features
fix/*         # Bug fixes
docs/*        # Documentation updates
chore/*       # Maintenance tasks
release/*     # Release preparation
```

- Branch from `develop` for features and fixes
- Open PRs against `develop`
- `develop` merges into `main` at release

---

## Review Process

1. Self-review your diff before requesting review
2. Ensure CI passes before assigning reviewers
3. Address all review comments before merging
4. Squash commits when merging feature branches
5. Delete the branch after merge

---

## Documentation

- Update relevant `docs/*.md` files alongside code changes
- Inline comments only where intent is non-obvious
- Keep README.md concise; link to docs for details
- Document breaking changes in `CHANGELOG.md`
