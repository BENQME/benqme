# Roadmap

> High-level product and technical roadmap organized by phase.

---

## Table of Contents

1. [Vision](#vision)
2. [Current Phase](#current-phase)
3. [Roadmap Phases](#roadmap-phases)
4. [Feature Backlog](#feature-backlog)
5. [Technical Debt](#technical-debt)
6. [Completed Milestones](#completed-milestones)

---

## Vision

Build a fast, accessible, and delightful web experience that serves as both a personal portfolio and a platform for future projects — with a codebase that's a reference for modern web development best practices.

---

## Current Phase

**Phase 1 — Foundation** *(In Progress)*

Establishing the core infrastructure, design system, and baseline functionality.

Progress: `████████░░` 80%

---

## Roadmap Phases

### Phase 1 — Foundation
**Goal**: Stable infrastructure, design system, and core pages.

| Item | Status |
|------|--------|
| Repository setup & CI/CD | ✅ Done |
| Design system (tokens, typography, colors) | ✅ Done |
| Component library (atoms, molecules) | 🔄 In Progress |
| Accessibility baseline (WCAG 2.1 AA) | 🔄 In Progress |
| Authentication (email + GitHub OAuth) | ⬜ Planned |
| Database schema (core tables) | ⬜ Planned |
| Dark mode | ⬜ Planned |
| Responsive layout system | ⬜ Planned |
| Documentation site | ⬜ Planned |

---

### Phase 2 — Core Features
**Goal**: Deliver the primary user-facing features.

| Item | Status |
|------|--------|
| Profile page | ⬜ Planned |
| Projects showcase | ⬜ Planned |
| Blog / writing section | ⬜ Planned |
| Contact form | ⬜ Planned |
| RSS feed | ⬜ Planned |
| Search | ⬜ Planned |
| Analytics dashboard | ⬜ Planned |

---

### Phase 3 — Polish & Performance
**Goal**: Reach production-quality performance and UX.

| Item | Status |
|------|--------|
| Core Web Vitals: all Green | ⬜ Planned |
| Image optimization pipeline | ⬜ Planned |
| Advanced animations (scroll-driven) | ⬜ Planned |
| Skeleton loading states | ⬜ Planned |
| Optimistic UI updates | ⬜ Planned |
| Error boundary improvements | ⬜ Planned |
| Comprehensive E2E test suite | ⬜ Planned |

---

### Phase 4 — Growth
**Goal**: Community, discovery, and platform expansion.

| Item | Status |
|------|--------|
| Open Graph images (dynamic) | ⬜ Planned |
| Sitemap + robots.txt | ⬜ Planned |
| i18n (internationalization) | ⬜ Planned |
| Newsletter integration | ⬜ Planned |
| Comments system | ⬜ Planned |
| Public API | ⬜ Planned |
| Plugin/extension system | ⬜ Planned |

---

## Feature Backlog

Features that are identified but not yet scheduled:

| Feature | Priority | Effort | Notes |
|---------|----------|--------|-------|
| Command palette (⌘K) | High | M | Search + navigation |
| Code snippet sharing | High | M | Syntax highlighting |
| Reading time estimates | Low | S | Blog posts |
| Print stylesheet | Low | S | — |
| PDF resume export | Medium | M | — |
| GitHub contribution graph | Medium | S | Via GitHub API |
| Guestbook | Low | S | Visitor messages |
| WebMentions | Low | M | Indie web |
| Social cards | Medium | M | Twitter/OG images |
| PWA / offline support | Medium | L | Service worker |
| AI-powered search | Low | L | Vector embeddings |

**Effort**: S = Small (< 1 day), M = Medium (1–3 days), L = Large (> 3 days)

---

## Technical Debt

Known technical issues to address:

| Item | Priority | Impact |
|------|----------|--------|
| Increase test coverage to 80% | High | Reliability |
| Migrate to server actions from API routes | Medium | Simplicity |
| Consolidate duplicate styles | Low | Maintainability |
| Add Lighthouse CI budget assertions | Medium | Performance |
| Document all public component APIs | Medium | DX |
| Upgrade to latest major dependencies | Low | Security |

---

## Completed Milestones

| Milestone | Completed |
|-----------|-----------|
| Initial project setup | ✅ |
| GitHub profile README | ✅ |
| CI/CD pipeline | ✅ |
| Design token system | ✅ |
| Documentation framework | ✅ |

---

*Last updated: 2026-07. For detailed task tracking, see [`tasks.md`](./tasks.md).*
