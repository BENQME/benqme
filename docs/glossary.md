# Glossary

> Definitions of terms, acronyms, and concepts used throughout this project.

---

## A

**A11y** — Abbreviation for "accessibility" (a + 11 letters + y). Refers to designing products usable by people with disabilities.

**ADR (Architecture Decision Record)** — A document that captures an architectural decision, its context, and its consequences. See [`decisions.md`](./decisions.md).

**API (Application Programming Interface)** — A set of rules and protocols that allow different software components to communicate with each other.

**App Router** — Next.js routing system introduced in version 13, based on the file system under `src/app/`. Supports React Server Components and nested layouts.

**ARIA (Accessible Rich Internet Applications)** — A set of HTML attributes that enhance accessibility for users of assistive technologies.

**Atomic Design** — A design methodology that structures UI components into a hierarchy: Atoms → Molecules → Organisms → Templates → Pages.

---

## B

**Barrel file** — An `index.ts` file that re-exports from multiple modules in a folder, creating a single entry point.

**Bundle** — The compiled and optimized output of a JavaScript build process, ready to be served to browsers.

**BFF (Backend for Frontend)** — A server-side layer purpose-built for a specific frontend application, rather than a generic API.

---

## C

**CLS (Cumulative Layout Shift)** — A Core Web Vital measuring visual stability. Quantifies unexpected layout shifts.

**CSR (Client-Side Rendering)** — Rendering that happens in the browser using JavaScript.

**CVA (Class Variance Authority)** — A utility for managing component variants with Tailwind CSS.

**Core Web Vitals** — Google's set of metrics (LCP, INP, CLS) used to measure user experience quality.

---

## D

**Design Token** — A named design value (color, spacing, font size, etc.) that can be referenced across the design system. Stored as CSS custom properties.

**DRY (Don't Repeat Yourself)** — A software principle that every piece of knowledge should have a single, unambiguous representation.

---

## E

**Edge Function** — A serverless function deployed to a CDN edge network, closer to the user. Used for low-latency operations like auth middleware.

**ESR (Edge-Side Rendering)** — Rendering that happens at the CDN edge, combining performance of static sites with dynamism of SSR.

---

## F

**FCP (First Contentful Paint)** — The time from navigation start to when any content (text, image) is first rendered on screen.

**Feature flag** — A technique to enable or disable features at runtime without deploying new code.

**FOUC (Flash of Unstyled Content)** — A brief moment where content appears without its intended styles, often caused by late-loading CSS or theme scripts.

---

## G

**Gutter** — The space between columns in a grid layout.

---

## H

**HOC (Higher-Order Component)** — A React pattern that takes a component and returns a new enhanced component. Largely replaced by hooks.

**Hydration** — The process of attaching React event handlers to server-rendered HTML in the browser.

---

## I

**INP (Interaction to Next Paint)** — A Core Web Vital measuring responsiveness to user interactions.

**ISR (Incremental Static Regeneration)** — A Next.js feature that updates statically generated pages in the background at a configurable interval.

---

## J

**JWT (JSON Web Token)** — A compact, URL-safe token format used for authentication. Contains a signed payload that can be verified without a database lookup.

---

## L

**LCP (Largest Contentful Paint)** — A Core Web Vital measuring loading performance. The render time of the largest visible content element.

---

## M

**MSW (Mock Service Worker)** — A library that intercepts network requests at the service worker level, used for API mocking in tests.

**Mutation** — In TanStack Query terminology, an operation that changes data on the server (POST, PATCH, DELETE).

---

## O

**ORM (Object-Relational Mapper)** — A tool that translates between database tables and programming language objects. Prisma is the ORM used in this project.

**OG (Open Graph)** — A protocol that enables URLs to become rich objects when shared on social media. Used for social preview cards.

---

## P

**PII (Personally Identifiable Information)** — Data that could be used to identify an individual person. Regulated by privacy laws (GDPR, CCPA, etc.).

**Polymorphic component** — A React component that can render as different HTML elements or other components via an `as` prop.

**Prisma** — A type-safe database ORM and query builder for Node.js and TypeScript.

---

## Q

**Query** — In TanStack Query terminology, a read operation that fetches data from the server.

**Query key** — A unique identifier for a TanStack Query cache entry. An array of values that describe what data is being fetched.

---

## R

**RSC (React Server Component)** — A React component that renders on the server and sends HTML to the client, without shipping its JavaScript to the browser.

**RUM (Real User Monitoring)** — Performance data collected from actual users in production, as opposed to synthetic (lab) measurements.

---

## S

**SemVer (Semantic Versioning)** — A versioning scheme: `MAJOR.MINOR.PATCH`. See [`versioning.md`](./versioning.md).

**SSG (Static Site Generation)** — Pre-rendering pages at build time.

**SSR (Server-Side Rendering)** — Rendering pages on the server on each request.

**Stacking context** — A CSS concept that defines a three-dimensional space within which z-index values compete. See [`z-index.md`](./z-index.md).

**Stale time** — In TanStack Query, the duration after which cached data is considered stale and will be refetched in the background on the next access.

---

## T

**TailwindCSS** — A utility-first CSS framework that provides composable single-purpose class names.

**TanStack Query** — A library for managing server state in React applications. Handles caching, background updates, and optimistic updates.

**TTFB (Time to First Byte)** — The time from a browser request to the first byte of the server response.

**TTI (Time to Interactive)** — The time until a page is reliably interactive for the user.

**Tree shaking** — A build optimization that removes unused code (dead code elimination).

---

## V

**Virtualization** — Rendering only the visible items in a long list to improve performance.

---

## W

**WCAG (Web Content Accessibility Guidelines)** — A set of guidelines for making web content accessible to people with disabilities. See [`accessibility.md`](./accessibility.md).

**Web Vitals** — A set of metrics defined by Google to measure user experience quality. Includes Core Web Vitals (LCP, INP, CLS) and additional metrics (FCP, TTFB, FID).

---

## Z

**Zod** — A TypeScript-first schema declaration and validation library used for runtime validation of API inputs and form data.
