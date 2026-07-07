# Tasks

> Active task tracking and sprint planning.

---

## Table of Contents

1. [Active Sprint](#active-sprint)
2. [Backlog](#backlog)
3. [In Review](#in-review)
4. [Done](#done)
5. [Task Template](#task-template)

---

## Active Sprint

> Sprint: Phase 1 Foundation | Updated: 2026-07

### 🔄 In Progress

#### TASK-007 — Component Library: Button
- **Priority**: High
- **Effort**: S
- **Owner**: @BENQME
- **Branch**: `feature/button-component`
- **Description**: Implement `Button` component with all variants (primary, secondary, ghost, danger), sizes (sm, md, lg), and states (default, hover, focus, loading, disabled).
- **Acceptance criteria**:
  - [ ] All 4 variants implemented
  - [ ] All 3 sizes work
  - [ ] Loading state with spinner
  - [ ] Keyboard accessible
  - [ ] Storybook story
  - [ ] Unit tests with ≥ 90% coverage

#### TASK-008 — Design Token CSS Variables
- **Priority**: High
- **Effort**: S
- **Owner**: @BENQME
- **Branch**: `feature/design-tokens`
- **Description**: Define all design tokens as CSS custom properties. See `css-tokens.md`.
- **Acceptance criteria**:
  - [ ] Color tokens (light + dark)
  - [ ] Typography tokens
  - [ ] Spacing tokens
  - [ ] Animation tokens
  - [ ] All tokens documented in `css-tokens.md`

---

### 📋 Todo (This Sprint)

#### TASK-009 — Dark Mode Toggle
- **Priority**: High
- **Effort**: S
- **Description**: Implement system preference detection + manual toggle with `localStorage` persistence.

#### TASK-010 — Responsive Navigation
- **Priority**: High
- **Effort**: M
- **Description**: Desktop navigation bar + mobile hamburger menu with animated drawer.

#### TASK-011 — Authentication Setup
- **Priority**: High
- **Effort**: M
- **Description**: Configure Auth.js with GitHub OAuth + email/password. Session management with JWT.

#### TASK-012 — Database Schema (Core)
- **Priority**: High
- **Effort**: M
- **Description**: Prisma schema for `users`, `accounts`, `sessions` tables. Migrations + seed data.

---

## Backlog

> Upcoming tasks not yet in a sprint.

| ID | Task | Priority | Effort | Labels |
|----|------|----------|--------|--------|
| TASK-013 | Profile page layout | High | M | UI, Page |
| TASK-014 | Projects showcase page | High | M | UI, Page |
| TASK-015 | Blog/writing index page | Medium | M | UI, Page |
| TASK-016 | Blog post page (MDX) | Medium | M | UI, Content |
| TASK-017 | Contact form + API | Medium | S | UI, API |
| TASK-018 | SEO: metadata + OG tags | Medium | S | SEO |
| TASK-019 | Sitemap generation | Medium | S | SEO |
| TASK-020 | RSS feed | Low | S | Content |
| TASK-021 | Analytics integration | Medium | S | Infra |
| TASK-022 | Error pages (404, 500) | Medium | S | UI |
| TASK-023 | Loading states / skeletons | Medium | M | UI |
| TASK-024 | Image optimization | High | M | Performance |
| TASK-025 | Lighthouse CI integration | High | S | CI, Performance |
| TASK-026 | E2E tests (Playwright) | High | L | Testing |
| TASK-027 | GitHub Actions CI matrix | Medium | S | CI |

---

## In Review

| ID | Task | PR | Reviewer |
|----|------|-----|---------|
| TASK-005 | GitHub README | #1 | — |

---

## Done

| ID | Task | Completed |
|----|------|-----------|
| TASK-001 | Repository initialization | 2026-07 |
| TASK-002 | GitHub Actions CI workflow | 2026-07 |
| TASK-003 | TypeScript configuration | 2026-07 |
| TASK-004 | ESLint + Prettier setup | 2026-07 |
| TASK-005 | GitHub profile README | 2026-07 |
| TASK-006 | Documentation framework | 2026-07 |

---

## Task Template

Use this template when creating new tasks:

```markdown
#### TASK-XXX — [Short Title]
- **Priority**: Critical / High / Medium / Low
- **Effort**: XS (< 2h) / S (< 1d) / M (1–3d) / L (3–7d) / XL (> 1 week)
- **Owner**: @username
- **Branch**: `type/short-description`
- **Labels**: UI, API, Testing, Performance, Security, Docs, CI
- **Description**: [Clear description of what needs to be done and why]
- **Acceptance criteria**:
  - [ ] Criterion 1
  - [ ] Criterion 2
  - [ ] Tests written
  - [ ] Documentation updated
```
