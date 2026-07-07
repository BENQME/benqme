# Checklists

> Pre-flight checklists for common development activities.

---

## Table of Contents

1. [New Feature Checklist](#new-feature-checklist)
2. [Bug Fix Checklist](#bug-fix-checklist)
3. [PR Review Checklist](#pr-review-checklist)
4. [Component Release Checklist](#component-release-checklist)
5. [Production Deploy Checklist](#production-deploy-checklist)
6. [Security Review Checklist](#security-review-checklist)
7. [Accessibility Checklist](#accessibility-checklist)
8. [Performance Checklist](#performance-checklist)
9. [New Dependency Checklist](#new-dependency-checklist)
10. [Onboarding Checklist](#onboarding-checklist)

---

## New Feature Checklist

Before opening a PR for a new feature:

### Implementation
- [ ] Feature works as described in the task/issue
- [ ] Edge cases handled (empty states, loading, errors)
- [ ] No hardcoded strings — use constants or i18n keys
- [ ] No `console.log` or debug code left in
- [ ] No commented-out code
- [ ] TypeScript — no `any` types added

### Testing
- [ ] Unit tests written for all new functions/components
- [ ] Tests cover happy path, edge cases, and error cases
- [ ] All existing tests still pass (`pnpm test`)
- [ ] Integration/E2E tests added for user-facing flows

### Quality
- [ ] `pnpm lint` passes with no new errors
- [ ] `pnpm typecheck` passes
- [ ] `pnpm build` succeeds
- [ ] Code reviewed by at least one other person (or self-reviewed thoroughly)

### Documentation
- [ ] Relevant `docs/*.md` files updated
- [ ] Public API documented with JSDoc
- [ ] `CHANGELOG.md` entry added (for user-visible changes)

### Accessibility
- [ ] Keyboard navigable
- [ ] Screen reader tested (or ARIA verified manually)
- [ ] Color contrast checked
- [ ] Reduced motion respected

---

## Bug Fix Checklist

- [ ] Root cause identified (not just symptom)
- [ ] Fix addresses root cause
- [ ] Regression test added to prevent recurrence
- [ ] Related areas checked for same issue
- [ ] `CHANGELOG.md` entry added
- [ ] Relevant documentation updated if behavior changed

---

## PR Review Checklist

When reviewing someone else's PR:

### Correctness
- [ ] Logic is correct and matches the described intent
- [ ] Edge cases are handled
- [ ] Error states are handled
- [ ] No obvious bugs

### Code Quality
- [ ] Code is readable and understandable
- [ ] No unnecessary complexity
- [ ] No code duplication (DRY)
- [ ] Follows project conventions (see `instructions.md`)

### Tests
- [ ] Tests cover the new/changed behavior
- [ ] Tests are meaningful (not just coverage padding)
- [ ] Tests will catch regressions

### Security
- [ ] No sensitive data exposed
- [ ] Input is validated and sanitized
- [ ] Auth/authorization checked appropriately
- [ ] No new attack surface introduced

### Performance
- [ ] No obviously expensive operations in hot paths
- [ ] No unnecessary network requests
- [ ] Images/assets optimized

---

## Component Release Checklist

Before publishing a new component to the shared library:

- [ ] Component name follows PascalCase convention
- [ ] Props interface is fully typed and exported
- [ ] All props have JSDoc comments
- [ ] All variants and states implemented
- [ ] Accessible (keyboard, ARIA, contrast)
- [ ] Works in both light and dark mode
- [ ] Tested at all responsive breakpoints
- [ ] Unit tests with ≥ 80% coverage
- [ ] Storybook story with all variants
- [ ] Story has controls for all props
- [ ] Added to component library barrel export
- [ ] Added to `components.md` documentation

---

## Production Deploy Checklist

Before deploying to production:

### Pre-deploy
- [ ] All CI checks pass on `main`
- [ ] Preview deployment tested manually
- [ ] Database migrations tested on staging
- [ ] Environment variables set in production dashboard
- [ ] Third-party service configurations verified (webhooks, API keys)
- [ ] Rollback plan documented

### Deploy
- [ ] Announce in team channel
- [ ] Merge `develop` → `main` (or trigger workflow)
- [ ] Monitor deployment progress

### Post-deploy
- [ ] Smoke test critical user flows
- [ ] Check error monitoring for new errors
- [ ] Verify Core Web Vitals are green
- [ ] Database migration applied successfully
- [ ] Announce completion

---

## Security Review Checklist

- [ ] All user inputs are validated with Zod
- [ ] SQL queries use parameterized queries (Prisma handles this)
- [ ] No `eval()` or `Function()` with user input
- [ ] No secrets in client-side code or git history
- [ ] API routes check authentication and authorization
- [ ] CORS policy is correct
- [ ] Rate limiting on auth endpoints
- [ ] Dependencies checked for known CVEs (`pnpm audit`)
- [ ] HTTP security headers configured
- [ ] Sensitive data not logged

---

## Accessibility Checklist

- [ ] Semantic HTML used throughout
- [ ] Only one `<h1>` per page
- [ ] Heading hierarchy is logical (no skipped levels)
- [ ] All images have meaningful `alt` text (or `alt=""` if decorative)
- [ ] All form inputs have associated `<label>` elements
- [ ] Error messages are linked to inputs via `aria-describedby`
- [ ] Interactive elements are keyboard focusable
- [ ] Focus order follows visual reading order
- [ ] Focus indicator is visible in all interactive states
- [ ] Color contrast ≥ 4.5:1 for text, ≥ 3:1 for UI elements
- [ ] Color is not the only means of conveying information
- [ ] Animations respect `prefers-reduced-motion`
- [ ] Dynamic content changes announced to screen readers
- [ ] No keyboard traps
- [ ] Touch targets ≥ 44×44px

---

## Performance Checklist

- [ ] Bundle size within budget (< 150KB JS gzipped)
- [ ] LCP candidate image has `fetchpriority="high"` and no `loading="lazy"`
- [ ] Below-the-fold images have `loading="lazy"`
- [ ] All images have `width` and `height` attributes
- [ ] Web fonts use `font-display: swap`
- [ ] Critical fonts preloaded in `<head>`
- [ ] No render-blocking scripts
- [ ] Lighthouse score ≥ 90 on all categories
- [ ] Core Web Vitals in green
- [ ] Long lists virtualized (> 100 items)
- [ ] No layout-triggering properties animated

---

## New Dependency Checklist

Before adding a new npm package:

- [ ] Is it actually necessary? (can't be solved with existing deps)
- [ ] Package is actively maintained (recent commits, issues addressed)
- [ ] No known security vulnerabilities (`pnpm audit` / Snyk)
- [ ] Bundle size is acceptable (`bundlephobia.com`)
- [ ] License is compatible (MIT, Apache 2.0, ISC)
- [ ] Added to the right category in `package.json` (dependencies vs devDependencies)
- [ ] Pinned to a minor version range (`^1.2.0`)

---

## Onboarding Checklist

For new contributors joining the project:

- [ ] Repository cloned and dev server running
- [ ] `.env.local` configured (see `development.md`)
- [ ] All tests pass locally
- [ ] Read `instructions.md`
- [ ] Read `architecture.md`
- [ ] Read `design.md`
- [ ] Storybook running and explored
- [ ] Opened a first small PR (documentation or minor fix)
- [ ] Access to relevant tools (Vercel, Figma, etc.)
