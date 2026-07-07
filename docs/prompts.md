# Prompts

> Reusable prompt templates for common development tasks using AI assistants.

---

## Table of Contents

1. [Component Generation](#component-generation)
2. [Code Review](#code-review)
3. [Testing](#testing)
4. [Documentation](#documentation)
5. [Debugging](#debugging)
6. [Refactoring](#refactoring)
7. [API Design](#api-design)
8. [Database](#database)
9. [Performance Analysis](#performance-analysis)
10. [Security Review](#security-review)
11. [Accessibility Audit](#accessibility-audit)
12. [Architecture Planning](#architecture-planning)

---

## Component Generation

### New UI Component
```
You are working in a Next.js 14 TypeScript project using Tailwind CSS and Framer Motion.

Create a [ComponentName] component with the following requirements:
- Props: [describe props with types]
- Behavior: [describe interactions and states]
- Accessibility: keyboard-navigable, ARIA labels, focus management
- Animation: use CSS transitions for hover/focus; use Framer Motion only if complex
- Styling: Tailwind CSS with design tokens from the project's CSS custom properties
- No default export; use named export
- Include TypeScript interface for props

Reference this existing component for style and patterns:
[paste similar component]
```

### Compound Component Pattern
```
Create a compound [Name] component in TypeScript that:
- Has a root [Name] component and sub-components: [Name.Header, Name.Body, Name.Footer]
- Uses React.createContext for shared state
- Exposes a clean API: <Name><Name.Header /><Name.Body /></Name>
- All sub-components are co-exported from the same file
- Full TypeScript with discriminated union props where needed
```

### Form Component
```
Create a form component for [purpose] using React Hook Form and Zod.

Fields:
- [field]: [type], [validation rules]
- [field]: [type], [validation rules]

Requirements:
- Zod schema + inferred TypeScript types
- Accessible labels, error messages linked via aria-describedby
- Submit handler receives validated data
- Loading state on submit (disable button, show spinner)
- Reset form on successful submission
```

---

## Code Review

### General Review
```
Review the following [TypeScript/React] code for:
1. Correctness — does it do what it claims?
2. Type safety — any unsafe type assertions or `any`?
3. Performance — unnecessary re-renders, expensive computations?
4. Security — injection, auth bypass, data exposure?
5. Accessibility — keyboard support, ARIA, contrast?
6. Edge cases — nulls, empty arrays, network failures?
7. Conventions — does it match the project style?

For each issue: severity (critical/major/minor), location, and suggested fix.

[paste code]
```

### Security Focus
```
Perform a security-focused review of the following API route handler.
Check for: input validation, auth/authorization, SQL injection, XSS, CSRF, 
rate limiting gaps, sensitive data exposure, and improper error handling.
Rate each finding as Critical / High / Medium / Low.

[paste code]
```

---

## Testing

### Unit Tests
```
Write comprehensive unit tests for the following [function/component] using Vitest.

Test cases must cover:
- Happy path (typical valid inputs)
- Edge cases (empty, null, max values)
- Error cases (invalid inputs, thrown errors)
- All branching conditions

Use this test file as a style reference:
[paste existing test]

[paste code to test]
```

### Integration Tests
```
Write integration tests for the following API route using [supertest/next-test-api-route-handler].
Mock the database using [vitest mock / prisma-mock].
Cover: success responses, validation errors, auth errors, not-found cases.

[paste API route]
```

### E2E Tests
```
Write a Playwright end-to-end test for the following user flow:
[describe the flow step by step]

Use the Page Object Model pattern.
Reuse locators from the existing test helpers in tests/helpers/.
Include both happy path and error path scenarios.
```

---

## Documentation

### JSDoc Comments
```
Add JSDoc comments to all exported functions, classes, and types in the following file.
- Include @param, @returns, @throws for functions
- Include @example for non-obvious usage
- Be concise — avoid restating the obvious
- Match the documentation style of the existing code
- Do not change any code, only add comments

[paste file]
```

### README Section
```
Write a README section for [feature/module].
Include: what it does, when to use it, installation/setup, basic usage example, API reference, common pitfalls.
Target audience: developers onboarding to this project.
Tone: direct and practical.
```

---

## Debugging

### Bug Investigation
```
The following code has a bug. Here is what happens vs. what I expect:

Actual: [describe the actual behavior]
Expected: [describe the expected behavior]
Steps to reproduce: [list steps]

Analyze the code, identify the root cause, and suggest a minimal fix.
Do not rewrite the function — make the smallest possible change.

[paste code]
```

### Error Message Analysis
```
I'm getting this error in my [Next.js / Node.js] application:

[paste full error message and stack trace]

Relevant code:
[paste relevant code]

What is causing this error and how do I fix it?
```

---

## Refactoring

### Extract Custom Hook
```
The following component has too much logic. Extract the data fetching and 
state management into a custom React hook called [hookName].

Requirements:
- The hook returns [describe return shape]
- The component becomes a pure presentation component
- All TypeScript types are preserved or improved
- Behavior is identical

[paste component]
```

### Reduce Complexity
```
Refactor the following function to reduce its cyclomatic complexity.
- Preserve all existing behavior (including edge cases)
- Do not change the function signature
- Use early returns, guard clauses, or extraction of sub-functions
- Explain each change

[paste function]
```

---

## API Design

### RESTful API Design
```
Design a RESTful JSON API for [resource/feature].

Requirements:
- Follow REST conventions (plural nouns, proper HTTP methods)
- Include: endpoint, method, request body schema, response schema, error cases
- Use Zod schemas for request/response validation
- Authentication: JWT bearer token
- Include pagination for list endpoints

Feature description:
[describe the feature]
```

---

## Database

### Prisma Schema
```
Design a Prisma schema for the following data model.

Requirements:
- UUIDs with cuid() for all primary keys
- createdAt / updatedAt on every model
- Proper relations with referential actions (onDelete)
- Indexes on all foreign keys and frequently queried fields
- Enums for status fields

Domain description:
[describe the domain/entities]
```

---

## Performance Analysis

```
Analyze the following code for performance issues.
Focus on: unnecessary re-renders, expensive computations in render, 
missing memoization, large bundle contributions, and blocking operations.

For each issue: explain the problem, estimate the impact, and provide a fix.

[paste code]
```

---

## Security Review

```
Review the following code for security vulnerabilities.

Check for:
- Injection attacks (SQL, NoSQL, command injection)
- Authentication/authorization bypass
- Insecure direct object references
- Sensitive data exposure
- Cross-site scripting (XSS)
- Missing input validation/sanitization
- Insecure dependencies
- Hardcoded credentials

Severity: Critical / High / Medium / Low
For each finding: location, explanation, exploitation scenario, remediation.

[paste code]
```

---

## Accessibility Audit

```
Audit the following React component for accessibility issues.

Check for:
- Missing or incorrect ARIA roles, labels, and properties
- Keyboard navigability and focus order
- Color contrast (flag potential issues)
- Missing alt text for images
- Form label associations
- Live region announcements for dynamic content
- Touch target sizes

For each issue: WCAG criterion, description, and suggested fix.

[paste component]
```

---

## Architecture Planning

```
I'm designing [feature/system]. Help me plan the architecture.

Context:
- Tech stack: Next.js 14, TypeScript, Prisma, PostgreSQL, Redis
- Scale: [describe expected load]
- Constraints: [list any constraints]

Feature description:
[describe what you're building]

Provide:
1. High-level architecture diagram (text/ASCII)
2. Key components and their responsibilities
3. Data model (tables/schemas)
4. API endpoints needed
5. State management approach
6. Performance considerations
7. Potential pitfalls and how to avoid them
```
