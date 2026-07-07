# Architecture

> System design, technology choices, and architectural decision records.

---

## Table of Contents

1. [Overview](#overview)
2. [Technology Stack](#technology-stack)
3. [System Diagram](#system-diagram)
4. [Application Layers](#application-layers)
5. [Data Flow](#data-flow)
6. [State Management](#state-management)
7. [Authentication](#authentication)
8. [API Design](#api-design)
9. [Database Design](#database-design)
10. [Infrastructure](#infrastructure)
11. [Architectural Decisions](#architectural-decisions)

---

## Overview

The application is a **full-stack web application** built on the Next.js App Router with a PostgreSQL database. It follows a layered architecture with clear separation between presentation, business logic, and data access.

**Core goals:**
- Fast initial page loads via SSR/SSG
- Type safety end-to-end (TypeScript + Zod + Prisma)
- Incremental adoption — features can be built and deployed independently
- Observability — every request is traceable

---

## Technology Stack

### Frontend
| Technology | Role |
|-----------|------|
| Next.js 14 | Framework (App Router, RSC) |
| React 18 | UI library |
| TypeScript | Type safety |
| Tailwind CSS | Styling |
| Framer Motion | Animations |
| React Hook Form | Form management |
| Zod | Schema validation |
| TanStack Query | Client-side data fetching |

### Backend
| Technology | Role |
|-----------|------|
| Next.js API Routes | HTTP handlers |
| Prisma | ORM + migrations |
| PostgreSQL | Primary database |
| Redis | Cache + pub/sub |
| Auth.js | Authentication |

### Infrastructure
| Technology | Role |
|-----------|------|
| Vercel | Hosting + edge functions |
| Neon / Supabase | Managed PostgreSQL |
| Upstash | Managed Redis |
| Cloudflare R2 / S3 | Object storage |
| Resend | Transactional email |

### Tooling
| Technology | Role |
|-----------|------|
| Vitest | Unit testing |
| Playwright | End-to-end testing |
| Storybook | Component development |
| ESLint + Prettier | Code quality |
| GitHub Actions | CI/CD |

---

## System Diagram

```
┌─────────────────────────────────────────────────┐
│                    Browser                       │
│  ┌──────────────────────────────────────────┐   │
│  │  React (Client Components + Hydration)   │   │
│  └──────────────────────────────────────────┘   │
└───────────────────┬─────────────────────────────┘
                    │ HTTPS
┌───────────────────▼─────────────────────────────┐
│                   Vercel Edge                    │
│  ┌──────────────┐  ┌──────────────────────────┐ │
│  │  Middleware  │  │  Static Assets (CDN)      │ │
│  └──────┬───────┘  └──────────────────────────┘ │
└─────────┼───────────────────────────────────────┘
          │
┌─────────▼───────────────────────────────────────┐
│                 Next.js Server                   │
│  ┌────────────┐  ┌────────────┐  ┌───────────┐  │
│  │  RSC Page  │  │  API Route │  │ Middleware │  │
│  │  Renderer  │  │  Handlers  │  │           │  │
│  └─────┬──────┘  └─────┬──────┘  └───────────┘  │
└────────┼───────────────┼─────────────────────────┘
         │               │
┌────────▼───────────────▼─────────────────────────┐
│              Service / Business Logic             │
│  ┌─────────────────┐   ┌───────────────────────┐ │
│  │ Feature Services│   │  Shared Utilities      │ │
│  └────────┬────────┘   └───────────────────────┘ │
└───────────┼────────────────────────────────────── ┘
            │
┌───────────▼─────────────────────────────────────┐
│                 Data Layer                       │
│  ┌───────────┐  ┌───────────┐  ┌─────────────┐  │
│  │  Prisma   │  │   Redis   │  │  External   │  │
│  │  (ORM)    │  │  (Cache)  │  │  APIs       │  │
│  └─────┬─────┘  └─────┬─────┘  └─────────────┘  │
└────────┼──────────────┼──────────────────────────┘
         │              │
    ┌────▼────┐    ┌────▼────┐
    │PostgreSQL│    │  Redis  │
    └─────────┘    └─────────┘
```

---

## Application Layers

### 1. Presentation Layer (`src/app/`, `src/components/`)
- React Server Components for static/data-fetching pages
- Client Components for interactive widgets
- Route handlers for form submissions

### 2. Application Layer (`src/features/`)
- Orchestrates business logic
- Validates inputs (Zod schemas)
- Calls data layer; formats output
- No direct HTTP logic

### 3. Data Access Layer (`src/lib/db.ts`, `src/features/*/api.ts`)
- Prisma queries
- Redis cache reads/writes
- External API calls
- No business logic

### 4. Infrastructure Layer (`src/lib/`)
- Database client initialization
- Auth configuration
- Third-party service clients
- Environment variable access

---

## Data Flow

### Server-side Render (Happy Path)
```
Request → Middleware → RSC → Service → Prisma → DB
                                ↑
                            Redis Cache (if hit)
```

### API Mutation
```
POST /api/resource
  → Auth check (middleware)
  → Zod validation
  → Service function
  → Prisma write
  → Cache invalidation
  → Response (201)
```

### Client-side Data Fetching
```
Component mount
  → TanStack Query (cache check)
  → GET /api/resource
  → Server: Auth + DB query
  → JSON response
  → TanStack Query updates cache
  → Component re-renders
```

---

## State Management

| Scope | Solution |
|-------|---------|
| Server state | React Server Components + TanStack Query |
| URL state | `useSearchParams` / `useRouter` |
| Form state | React Hook Form |
| Global UI state | Zustand (theme, sidebar, modals) |
| Local component state | `useState` / `useReducer` |

**Rule**: Do not use global state for server data — TanStack Query handles it.

---

## Authentication

Auth.js (formerly NextAuth.js) with:
- Credentials provider (email + password)
- GitHub OAuth
- Magic links (email)

Session strategy: JWT (stateless, edge-compatible)

```typescript
// Session contains:
interface Session {
  user: {
    id: string;
    name: string;
    email: string;
    role: "user" | "admin";
  };
  expires: string;
}
```

---

## API Design

RESTful JSON API. Base path: `/api/`

### Conventions
- Resource URLs are plural nouns: `/api/users`, `/api/posts`
- Nested resources: `/api/users/[id]/posts`
- Actions as sub-resources: `/api/posts/[id]/publish`
- HTTP methods: `GET`, `POST`, `PATCH`, `DELETE`
- Response envelope: `{ data, error, meta }`

### Error Format
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": { "email": ["Invalid email address"] }
  }
}
```

---

## Database Design

### Conventions
- UUIDs via `cuid()` for all primary keys
- `createdAt` and `updatedAt` on all tables
- Soft deletes with `deletedAt` where needed
- Indexes on foreign keys and frequently queried columns

### Key Tables
- `users` — authentication and profile
- `sessions` — (managed by Auth.js if DB strategy)
- `accounts` — OAuth account links

---

## Infrastructure

### Environments
| Environment | Branch | URL |
|-------------|--------|-----|
| Production | `main` | `https://benqme.com` |
| Preview | PR branches | `https://pr-{n}.benqme.vercel.app` |
| Development | local | `http://localhost:3000` |

### Deployment
- Automatic on push to `main` (via Vercel GitHub integration)
- Preview deployments for all PRs
- Environment variables managed in Vercel dashboard

---

## Architectural Decisions

See [`decisions.md`](./decisions.md) for the full Architecture Decision Record log.

Key decisions:
- **Next.js App Router** over Pages Router: RSC, nested layouts, streaming
- **Prisma** over raw SQL: type safety, migrations, ergonomics
- **TanStack Query** over SWR: better DevX, optimistic updates, infinite queries
- **Tailwind CSS** over CSS Modules: faster iteration, consistent design tokens
- **Zod** for validation: shared schemas between client and server
