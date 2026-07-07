# Project Structure

> Directory layout, module boundaries, and file organization conventions.

---

## Table of Contents

1. [Overview](#overview)
2. [Root Layout](#root-layout)
3. [Source Directory](#source-directory)
4. [Feature Modules](#feature-modules)
5. [Component Organization](#component-organization)
6. [Naming Conventions](#naming-conventions)
7. [Import Paths](#import-paths)
8. [Co-location Rules](#co-location-rules)
9. [Barrel Exports](#barrel-exports)

---

## Overview

The project follows a **feature-first** structure where code is organized by domain concern rather than technical type. Cross-cutting concerns (shared components, utilities, hooks) live in dedicated top-level folders.

---

## Root Layout

```
benqme/
├── .github/                # GitHub Actions workflows and templates
├── docs/                   # Project documentation
├── public/                 # Statically served files (images, fonts, manifests)
├── src/                    # All application source code
├── tests/                  # End-to-end and integration tests
├── .env.example            # Environment variable template
├── .eslintrc.js            # ESLint configuration
├── .prettierrc             # Prettier configuration
├── next.config.ts          # Next.js configuration
├── package.json
├── tailwind.config.ts      # Tailwind CSS configuration
├── tsconfig.json           # TypeScript configuration
└── README.md
```

---

## Source Directory

```
src/
├── app/                    # Next.js App Router: layouts, pages, API routes
│   ├── (marketing)/        # Route group: public/marketing pages
│   ├── (app)/              # Route group: authenticated app pages
│   ├── api/                # API route handlers
│   ├── layout.tsx          # Root layout
│   └── globals.css         # Global styles
│
├── components/             # Shared, reusable UI components
│   ├── ui/                 # Primitive design-system components (Button, Input, …)
│   ├── layout/             # Layout components (Header, Footer, Sidebar)
│   └── [component-name]/   # Grouped by component
│       ├── index.tsx       # Main component export
│       ├── [Name].tsx      # Implementation
│       ├── [Name].test.tsx # Unit tests
│       └── [Name].stories.tsx # Storybook story
│
├── features/               # Feature-scoped modules (see Feature Modules)
│
├── hooks/                  # Shared custom React hooks
│
├── lib/                    # Third-party library wrappers and singletons
│   ├── db.ts               # Database client
│   ├── auth.ts             # Auth configuration
│   └── api.ts              # API client
│
├── providers/              # React context providers
│
├── styles/                 # Global style files and token definitions
│   ├── tokens.css          # CSS custom properties
│   └── typography.css      # Typography base styles
│
├── types/                  # Global TypeScript types and interfaces
│   ├── index.ts            # Re-exports
│   └── api.ts              # API response types
│
└── utils/                  # Pure, stateless utility functions
    ├── format.ts           # Formatting helpers
    ├── validation.ts       # Input validation
    └── cn.ts               # className merge utility
```

---

## Feature Modules

Each feature is a self-contained folder under `src/features/`:

```
src/features/
└── [feature-name]/
    ├── index.ts            # Public API — only export what outside code should use
    ├── api.ts              # Data fetching functions
    ├── components/         # Feature-specific components
    ├── hooks/              # Feature-specific hooks
    ├── store.ts            # Local state (Zustand slice, React Query config)
    ├── types.ts            # Feature-specific types
    └── utils.ts            # Feature-specific utilities
```

**Rule**: Features may import from `src/components`, `src/hooks`, `src/utils`, and `src/lib`. Features must **not** import from other features (use the `src/lib` or event bus pattern for cross-feature communication).

---

## Component Organization

### Single-File Component
For small, self-contained components with no tests or stories yet:
```
Button.tsx
```

### Multi-File Component
For components with tests, stories, or sub-components:
```
Button/
├── index.tsx          # Re-exports from Button.tsx
├── Button.tsx         # Implementation
├── Button.test.tsx    # Tests
├── Button.stories.tsx # Storybook
└── ButtonIcon.tsx     # Sub-component
```

### Do Not
- Nest component folders more than 2 levels deep
- Put non-component logic (hooks, utils) inside component folders

---

## Naming Conventions

| Item | Convention | Example |
|------|-----------|---------|
| Component file | PascalCase | `UserAvatar.tsx` |
| Component folder | PascalCase | `UserAvatar/` |
| Hook file | camelCase | `useUserProfile.ts` |
| Utility file | camelCase | `formatDate.ts` |
| Type file | camelCase | `userTypes.ts` |
| Style module | camelCase + `.module.css` | `button.module.css` |
| Story file | PascalCase + `.stories.tsx` | `Button.stories.tsx` |
| Test file | PascalCase + `.test.tsx` | `Button.test.tsx` |
| Route segment | kebab-case | `user-settings/` |
| API route | kebab-case | `send-email/` |

---

## Import Paths

Use the `@/` alias for all imports within `src/`:
```typescript
// ✅ Absolute import
import { Button } from "@/components/ui/Button";
import { formatDate } from "@/utils/format";

// ❌ Relative import (fragile)
import { Button } from "../../components/ui/Button";
```

Configured in `tsconfig.json`:
```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

---

## Co-location Rules

Co-locate code with what it belongs to:

| Code | Lives with |
|------|-----------|
| Tests | The component/module they test |
| Storybook stories | The component they document |
| Feature-specific types | The feature module |
| Feature-specific hooks | The feature module |
| Shared utilities | `src/utils/` |
| Shared types | `src/types/` |

---

## Barrel Exports

Use barrel files (`index.ts`) to simplify imports from a folder. Keep them small — only export the public API:

```typescript
// src/components/ui/index.ts
export { Button } from "./Button";
export { Input } from "./Input";
export { Badge } from "./Badge";
// Do not re-export internals
```

**Caution**: Avoid deeply nested barrel files — they can hurt tree-shaking. Prefer explicit imports for rarely-used utilities.
