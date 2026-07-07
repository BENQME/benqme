# Architecture Decision Records

> A log of significant architectural decisions and the reasoning behind them.

---

## Table of Contents

- [ADR-001: Next.js App Router over Pages Router](#adr-001-nextjs-app-router-over-pages-router)
- [ADR-002: Prisma as ORM](#adr-002-prisma-as-orm)
- [ADR-003: TanStack Query for client-side state](#adr-003-tanstack-query-for-client-side-state)
- [ADR-004: Tailwind CSS for styling](#adr-004-tailwind-css-for-styling)
- [ADR-005: Zod for runtime validation](#adr-005-zod-for-runtime-validation)
- [ADR-006: Auth.js for authentication](#adr-006-authjs-for-authentication)
- [ADR-007: Zustand for global UI state](#adr-007-zustand-for-global-ui-state)
- [ADR-008: Vitest over Jest](#adr-008-vitest-over-jest)
- [ADR-009: Playwright for end-to-end tests](#adr-009-playwright-for-end-to-end-tests)
- [ADR-010: pnpm as package manager](#adr-010-pnpm-as-package-manager)

---

## ADR Template

```markdown
## ADR-NNN: Title

**Status**: Proposed | Accepted | Deprecated | Superseded by ADR-XXX
**Date**: YYYY-MM-DD
**Deciders**: @username

### Context
[What is the issue? What forces are at play?]

### Decision
[What was decided?]

### Consequences
[What becomes easier? Harder? What risks are introduced?]
```

---

## ADR-001: Next.js App Router over Pages Router

**Status**: Accepted
**Date**: 2024-01-01

### Context
Next.js 13+ introduced the App Router as the recommended approach, replacing the Pages Router. We needed to choose between the two systems for new development.

### Decision
Use the **App Router** (`src/app/`) as the routing and rendering system.

### Rationale
- **React Server Components (RSC)** reduce JavaScript sent to the client
- **Nested layouts** eliminate layout re-renders on navigation
- **Streaming** with `<Suspense>` enables progressive rendering
- **Server Actions** simplify data mutations
- App Router is the future — Pages Router is in maintenance mode

### Consequences
- **Better**: Performance, SEO, DX for new features
- **Harder**: Learning curve for developers unfamiliar with RSC
- **Risk**: Some third-party libraries don't support RSC yet (need `"use client"` wrapper)

---

## ADR-002: Prisma as ORM

**Status**: Accepted
**Date**: 2024-01-01

### Context
We needed a database access layer for PostgreSQL.

### Decision
Use **Prisma** as the ORM.

### Rationale
- End-to-end type safety from schema to query results
- Auto-generated Prisma Client from the schema
- Migrations with `prisma migrate dev`
- Prisma Studio for visual database browsing
- Strong ecosystem and documentation

### Alternatives Considered
- **Drizzle**: More lightweight, but less mature at the time of decision
- **TypeORM**: Mature but verbose; active record pattern less idiomatic in React apps
- **Raw SQL (pg)**: Maximum control, but no type safety without significant boilerplate

### Consequences
- **Better**: Type-safe queries, clear migration workflow
- **Harder**: Schema changes require migration files; `prisma generate` after schema edits
- **Risk**: Prisma Client can be large; optimize with `datasourceUrl` for edge environments

---

## ADR-003: TanStack Query for client-side state

**Status**: Accepted
**Date**: 2024-01-01

### Context
Client components need to fetch and manage server data with caching, refetching, and optimistic updates.

### Decision
Use **TanStack Query** (React Query v5) for client-side server state management.

### Rationale
- Built-in caching with stale/fresh lifecycle
- Optimistic updates with rollback
- Infinite queries out of the box
- Background refetching on window focus
- Excellent DevTools

### Alternatives Considered
- **SWR**: Simpler API but fewer features (no optimistic updates, no infinite query stagger)
- **RTK Query**: Couples to Redux; overkill for this project
- **Custom fetch + useState**: Significant boilerplate; easy to miss edge cases

### Consequences
- **Better**: Consistent data fetching, less boilerplate, race condition handling
- **Risk**: Bundle size addition (~13KB gzipped)

---

## ADR-004: Tailwind CSS for styling

**Status**: Accepted
**Date**: 2024-01-01

### Context
The project needed a styling solution that enables rapid development while maintaining design consistency.

### Decision
Use **Tailwind CSS** with a custom configuration that maps to the design token system.

### Rationale
- Utility classes enable fast iteration without naming decisions
- Design tokens as Tailwind values ensure consistency
- PurgeCSS keeps bundle size minimal
- Excellent IDE support (IntelliSense, class sorting)
- Responsive variants (`md:`, `lg:`) built in

### Alternatives Considered
- **CSS Modules**: Good isolation, but slower iteration; verbose for responsive styles
- **Styled Components**: Runtime cost; doesn't work well with RSC
- **Vanilla Extract**: Zero-runtime, good DX, but less ecosystem support

### Consequences
- **Better**: Fast iteration, consistent design, minimal CSS bundle
- **Harder**: Utility classes can become verbose; requires discipline not to use arbitrary values
- **Risk**: Class list maintenance; mitigated with `clsx` + `tailwind-merge` and CVA

---

## ADR-005: Zod for runtime validation

**Status**: Accepted
**Date**: 2024-01-01

### Context
TypeScript provides compile-time type safety, but runtime validation is needed for API inputs and form data.

### Decision
Use **Zod** as the schema validation library.

### Rationale
- TypeScript-first: schemas generate types with `z.infer<>`
- Same schemas used in both client (form validation) and server (API validation)
- Integration with React Hook Form (`@hookform/resolvers/zod`)
- Detailed error messages out of the box

### Alternatives Considered
- **Yup**: Older, less TypeScript-idiomatic
- **Valibot**: Smaller bundle, but less mature ecosystem
- **io-ts**: More powerful but steeper learning curve

### Consequences
- **Better**: Single source of truth for schemas; consistent validation
- **Risk**: Adds ~14KB to bundle; mitigated by server-side usage for most schemas

---

## ADR-006: Auth.js for authentication

**Status**: Accepted
**Date**: 2024-01-01

### Context
The application needs user authentication with support for OAuth providers and credentials.

### Decision
Use **Auth.js** (formerly NextAuth.js) v5.

### Rationale
- Deep Next.js integration (middleware, server components)
- Built-in OAuth providers (GitHub, Google, etc.)
- Edge-compatible JWT sessions
- Active development and large community

### Alternatives Considered
- **Clerk**: Excellent DX but introduces vendor dependency and cost at scale
- **Custom auth**: Maximum control but significant security risk surface
- **Lucia**: Lightweight and flexible, but more manual setup

### Consequences
- **Better**: Secure auth out of the box; provider management is simple
- **Harder**: v5 has breaking changes from v4; some configuration is opaque
- **Risk**: Vendor dependency; mitigated by the library being open source

---

## ADR-007: Zustand for global UI state

**Status**: Accepted
**Date**: 2024-01-01

### Context
Some UI state (sidebar, modals, notifications) needs to be shared across the component tree without prop drilling.

### Decision
Use **Zustand** for global UI state.

### Rationale
- Minimal API — less boilerplate than Redux
- No provider wrapping needed (unlike React Context for frequent updates)
- Supports subscriptions for fine-grained re-renders
- TypeScript-first

### Alternatives Considered
- **Redux Toolkit**: Mature but overkill; significant boilerplate
- **React Context**: Causes unnecessary re-renders for frequent updates
- **Jotai**: Atomic approach is excellent but less familiar to team

### Consequences
- **Better**: Simple, readable global state; easy testing
- **Risk**: Easy to abuse for server state (use TanStack Query instead)

---

## ADR-008: Vitest over Jest

**Status**: Accepted
**Date**: 2024-01-01

### Context
The project needed a unit testing framework.

### Decision
Use **Vitest** instead of Jest.

### Rationale
- Vite-native: shares the same config and transforms as the dev server
- Dramatically faster than Jest for TypeScript projects (no separate Babel transform)
- Jest-compatible API: trivial to migrate existing Jest tests
- Built-in UI mode and coverage

### Consequences
- **Better**: Faster test runs, especially in watch mode
- **Risk**: Slightly less ecosystem maturity than Jest (rarely an issue in practice)

---

## ADR-009: Playwright for end-to-end tests

**Status**: Accepted
**Date**: 2024-01-01

### Context
The project needed end-to-end browser testing.

### Decision
Use **Playwright** for E2E tests.

### Rationale
- Cross-browser testing (Chromium, Firefox, WebKit)
- Auto-waiting — no manual `waitForElement` calls
- Excellent TypeScript support
- Built-in test runner, reporter, and trace viewer
- Microsoft-maintained; active development

### Alternatives Considered
- **Cypress**: Excellent DX but browser support limited; heavier setup
- **Selenium**: Legacy; poor DX in modern TypeScript stacks

### Consequences
- **Better**: Reliable cross-browser testing; powerful debugging with trace viewer
- **Risk**: Slightly heavier setup than Cypress for beginners

---

## ADR-010: pnpm as package manager

**Status**: Accepted
**Date**: 2024-01-01

### Context
The project needed a package manager.

### Decision
Use **pnpm** as the package manager.

### Rationale
- Significantly faster than npm/Yarn for installs
- Strict dependency isolation (prevents phantom dependencies)
- Monorepo-ready with workspaces
- Disk-efficient content-addressable storage

### Alternatives Considered
- **npm**: Slower, less disk efficient
- **Yarn v3**: Plug'n'Play mode adds complexity; not universally supported

### Consequences
- **Better**: Faster CI, disk savings, dependency safety
- **Risk**: Requires all contributors to install pnpm (`npm install -g pnpm`)
