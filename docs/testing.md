# Testing

> Testing strategy, tools, and patterns for unit, integration, and end-to-end tests.

---

## Table of Contents

1. [Testing Philosophy](#testing-philosophy)
2. [Testing Pyramid](#testing-pyramid)
3. [Tools](#tools)
4. [Unit Testing](#unit-testing)
5. [Integration Testing](#integration-testing)
6. [End-to-End Testing](#end-to-end-testing)
7. [Component Testing](#component-testing)
8. [API Testing](#api-testing)
9. [Mocking](#mocking)
10. [Coverage](#coverage)
11. [CI Integration](#ci-integration)

---

## Testing Philosophy

- **Test behavior, not implementation** — tests should survive refactors
- **Write tests as the user would use them** — simulate real interactions
- **Fast feedback** — unit tests run in milliseconds; save slow tests for CI
- **Tests are first-class code** — readable, maintainable, DRY (with care)
- **Don't test the framework** — test your logic, not React's rendering engine

---

## Testing Pyramid

```
          ╱‾‾‾‾‾‾‾‾‾╲
         ╱   E2E (10%)╲       Playwright — full user flows
        ╱───────────────╲
       ╱ Integration (20%)╲    API routes, hooks with network
      ╱─────────────────────╲
     ╱    Unit Tests (70%)    ╲  Functions, components, utils
    ╱───────────────────────────╲
```

---

## Tools

| Tool | Purpose |
|------|---------|
| **Vitest** | Unit and integration test runner |
| **React Testing Library** | Component testing utilities |
| **Playwright** | End-to-end browser testing |
| **MSW** (Mock Service Worker) | API mocking in tests |
| **@testing-library/user-event** | Realistic user interaction simulation |
| **vitest-dom** | DOM assertion matchers |
| **Prisma mock** | Database mocking |

### Setup

```bash
pnpm test          # Run all unit/integration tests
pnpm test:watch    # Watch mode
pnpm test:ui       # Vitest UI
pnpm test:coverage # Run with coverage report
pnpm test:e2e      # Playwright E2E tests
pnpm test:e2e:ui   # Playwright UI mode
```

---

## Unit Testing

### Functions and Utilities

```typescript
// src/utils/format.test.ts
import { describe, it, expect } from "vitest";
import { formatDate, formatCurrency } from "./format";

describe("formatDate", () => {
  it("formats a date in the expected locale format", () => {
    const date = new Date("2024-01-15");
    expect(formatDate(date)).toBe("Jan 15, 2024");
  });

  it("returns empty string for null/undefined", () => {
    expect(formatDate(null)).toBe("");
    expect(formatDate(undefined)).toBe("");
  });

  it("handles invalid date gracefully", () => {
    expect(formatDate(new Date("invalid"))).toBe("Invalid date");
  });
});
```

### Hooks

```typescript
// src/hooks/useCounter.test.ts
import { renderHook, act } from "@testing-library/react";
import { useCounter } from "./useCounter";

describe("useCounter", () => {
  it("initializes with the provided value", () => {
    const { result } = renderHook(() => useCounter(5));
    expect(result.current.count).toBe(5);
  });

  it("increments correctly", () => {
    const { result } = renderHook(() => useCounter(0));
    act(() => result.current.increment());
    expect(result.current.count).toBe(1);
  });
});
```

---

## Integration Testing

### API Routes

```typescript
// src/app/api/users/route.test.ts
import { describe, it, expect, vi } from "vitest";
import { GET, POST } from "./route";
import { NextRequest } from "next/server";

// Mock dependencies
vi.mock("@/lib/db", () => ({
  db: {
    user: {
      findMany: vi.fn().mockResolvedValue([{ id: "1", name: "Test User" }]),
      create: vi.fn().mockResolvedValue({ id: "2", name: "New User" }),
    },
  },
}));

vi.mock("@/lib/auth", () => ({
  auth: vi.fn().mockResolvedValue({ user: { id: "1", role: "user" } }),
}));

describe("GET /api/users", () => {
  it("returns users list", async () => {
    const request = new NextRequest("http://localhost/api/users");
    const response = await GET(request);
    const data = await response.json();

    expect(response.status).toBe(200);
    expect(data).toHaveLength(1);
  });
});
```

---

## End-to-End Testing

### Page Object Model

```typescript
// tests/pages/LoginPage.ts
import { type Page, type Locator } from "@playwright/test";

export class LoginPage {
  readonly page: Page;
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly submitButton: Locator;
  readonly errorMessage: Locator;

  constructor(page: Page) {
    this.page = page;
    this.emailInput = page.getByLabel("Email address");
    this.passwordInput = page.getByLabel("Password");
    this.submitButton = page.getByRole("button", { name: "Sign in" });
    this.errorMessage = page.getByRole("alert");
  }

  async goto() {
    await this.page.goto("/login");
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.submitButton.click();
  }
}
```

### E2E Test

```typescript
// tests/e2e/auth.spec.ts
import { test, expect } from "@playwright/test";
import { LoginPage } from "../pages/LoginPage";

test.describe("Authentication", () => {
  test("user can log in with valid credentials", async ({ page }) => {
    const loginPage = new LoginPage(page);
    await loginPage.goto();
    await loginPage.login("test@example.com", "password123");

    await expect(page).toHaveURL("/dashboard");
    await expect(page.getByText("Welcome back")).toBeVisible();
  });

  test("shows error for invalid credentials", async ({ page }) => {
    const loginPage = new LoginPage(page);
    await loginPage.goto();
    await loginPage.login("wrong@example.com", "wrongpass");

    await expect(loginPage.errorMessage).toContainText("Invalid credentials");
  });
});
```

---

## Component Testing

```tsx
// src/components/ui/Button/Button.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { describe, it, expect, vi } from "vitest";
import { Button } from "./Button";

describe("Button", () => {
  it("renders with the provided label", () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole("button", { name: "Click me" })).toBeInTheDocument();
  });

  it("calls onClick when clicked", async () => {
    const user = userEvent.setup();
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click</Button>);

    await user.click(screen.getByRole("button"));
    expect(handleClick).toHaveBeenCalledOnce();
  });

  it("is disabled when the disabled prop is set", () => {
    render(<Button disabled>Disabled</Button>);
    expect(screen.getByRole("button")).toBeDisabled();
  });

  it("shows loading spinner when loading is true", () => {
    render(<Button loading>Save</Button>);
    expect(screen.getByRole("button")).toBeDisabled();
    expect(screen.getByLabelText("Loading")).toBeInTheDocument();
  });

  it("is keyboard accessible", async () => {
    const user = userEvent.setup();
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Submit</Button>);

    screen.getByRole("button").focus();
    await user.keyboard("{Enter}");
    expect(handleClick).toHaveBeenCalledOnce();
  });
});
```

---

## API Mocking

### MSW Setup

```typescript
// tests/mocks/handlers.ts
import { http, HttpResponse } from "msw";

export const handlers = [
  http.get("/api/users", () => {
    return HttpResponse.json([
      { id: "1", name: "Alice", email: "alice@example.com" },
    ]);
  }),

  http.post("/api/users", async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({ id: "2", ...body }, { status: 201 });
  }),

  http.get("/api/users/:id", ({ params }) => {
    if (params.id === "999") {
      return HttpResponse.json({ error: "Not found" }, { status: 404 });
    }
    return HttpResponse.json({ id: params.id, name: "Alice" });
  }),
];
```

---

## Coverage

Target coverage thresholds:

```typescript
// vitest.config.ts
export default {
  test: {
    coverage: {
      provider: "v8",
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 70,
        statements: 80,
      },
      exclude: [
        "**/*.stories.tsx",
        "**/*.config.ts",
        "**/types/**",
        "src/app/**", // Next.js pages — test via E2E
      ],
    },
  },
};
```

---

## CI Integration

```yaml
# .github/workflows/ci.yml
- name: Run unit tests
  run: pnpm test --coverage

- name: Upload coverage
  uses: codecov/codecov-action@v4

- name: Run E2E tests
  run: pnpm test:e2e
  env:
    CI: true
```
