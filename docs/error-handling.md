# Error Handling

> Patterns for handling, displaying, and recovering from errors.

---

## Table of Contents

1. [Error Philosophy](#error-philosophy)
2. [Error Types](#error-types)
3. [API Error Handling](#api-error-handling)
4. [UI Error States](#ui-error-states)
5. [Error Boundaries](#error-boundaries)
6. [Form Errors](#form-errors)
7. [Global Error Handling](#global-error-handling)
8. [Logging & Monitoring](#logging--monitoring)
9. [Error Messages](#error-messages)

---

## Error Philosophy

1. **Never swallow errors silently** — always log or propagate
2. **Fail with context** — include enough information to diagnose the issue
3. **Recover gracefully** — offer users a path forward when things go wrong
4. **Distinguish error types** — user errors vs. system errors need different UX
5. **Be honest** — don't show misleading error messages; be clear but not technical

---

## Error Types

| Type | Cause | UX Response |
|------|-------|-------------|
| **Validation error** | User input invalid | Inline field errors, no reload |
| **Auth error** | Not authenticated | Redirect to login |
| **Permission error** | Not authorized | Show 403 page or toast |
| **Not found** | Resource missing | Show 404 page or empty state |
| **Network error** | No connection | Toast with retry option |
| **Server error** | Bug in server code | Toast + retry; report to Sentry |
| **Rate limit** | Too many requests | Toast with countdown |

---

## API Error Handling

### Error Class
```typescript
// src/lib/errors.ts
export class ApiError extends Error {
  constructor(
    message: string,
    public readonly status: number,
    public readonly code: string,
    public readonly details?: unknown
  ) {
    super(message);
    this.name = "ApiError";
  }

  get isNotFound()      { return this.status === 404; }
  get isUnauthorized()  { return this.status === 401; }
  get isForbidden()     { return this.status === 403; }
  get isValidation()    { return this.status === 400; }
  get isRateLimited()   { return this.status === 429; }
  get isServerError()   { return this.status >= 500; }
}
```

### API Route Error Handler
```typescript
// src/lib/api-handler.ts
import { NextResponse } from "next/server";
import { ZodError } from "zod";

export function handleApiError(error: unknown): NextResponse {
  console.error("[API Error]", error);

  if (error instanceof ZodError) {
    return NextResponse.json(
      { error: { code: "VALIDATION_ERROR", message: "Invalid input", details: error.flatten() } },
      { status: 400 }
    );
  }

  if (error instanceof ApiError) {
    return NextResponse.json(
      { error: { code: error.code, message: error.message } },
      { status: error.status }
    );
  }

  // Unexpected error — don't expose internals
  return NextResponse.json(
    { error: { code: "INTERNAL_ERROR", message: "An unexpected error occurred" } },
    { status: 500 }
  );
}

// Usage in route handlers
export async function GET(request: NextRequest) {
  try {
    // ... handler logic
  } catch (error) {
    return handleApiError(error);
  }
}
```

---

## UI Error States

### Component Error State
```tsx
interface ErrorStateProps {
  title?: string;
  message?: string;
  onRetry?: () => void;
}

export function ErrorState({
  title = "Something went wrong",
  message = "An unexpected error occurred. Please try again.",
  onRetry,
}: ErrorStateProps) {
  return (
    <div role="alert" className="flex flex-col items-center gap-4 py-12 text-center">
      <AlertCircleIcon className="text-danger" size={48} aria-hidden="true" />
      <div>
        <h2 className="text-lg font-semibold">{title}</h2>
        <p className="text-text-secondary mt-1">{message}</p>
      </div>
      {onRetry && (
        <Button variant="secondary" onClick={onRetry}>
          Try again
        </Button>
      )}
    </div>
  );
}
```

### React Query Error State
```tsx
export function PostList() {
  const { data, error, isError, isLoading, refetch } = usePosts();

  if (isLoading) return <Skeleton />;

  if (isError) {
    const apiError = error instanceof ApiError ? error : null;

    if (apiError?.isNotFound) {
      return <EmptyState message="No posts found." />;
    }

    return (
      <ErrorState
        message={apiError?.message ?? "Failed to load posts."}
        onRetry={refetch}
      />
    );
  }

  return <ul>{data?.map((p) => <PostCard key={p.id} post={p} />)}</ul>;
}
```

---

## Error Boundaries

React Error Boundaries catch JavaScript errors in the render tree:

```tsx
// src/components/ErrorBoundary.tsx
"use client";

import { Component, type ReactNode } from "react";

interface Props {
  children: ReactNode;
  fallback?: ReactNode | ((error: Error, reset: () => void) => ReactNode);
}

interface State {
  error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
  state: State = { error: null };

  static getDerivedStateFromError(error: Error): State {
    return { error };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error("Uncaught error:", error, info);
    // Report to Sentry: Sentry.captureException(error, { contexts: { react: info } });
  }

  reset = () => this.setState({ error: null });

  render() {
    if (this.state.error) {
      const { fallback } = this.props;
      if (typeof fallback === "function") {
        return fallback(this.state.error, this.reset);
      }
      return fallback ?? (
        <ErrorState
          message={this.state.error.message}
          onRetry={this.reset}
        />
      );
    }
    return this.props.children;
  }
}
```

### Next.js Error Files
```tsx
// src/app/error.tsx — catches errors in the route subtree
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <ErrorState
      title="Something went wrong"
      message={error.message}
      onRetry={reset}
    />
  );
}
```

```tsx
// src/app/not-found.tsx
export default function NotFound() {
  return (
    <div className="flex flex-col items-center gap-4 py-24 text-center">
      <h1 className="text-6xl font-bold text-text-tertiary">404</h1>
      <h2 className="text-2xl font-semibold">Page not found</h2>
      <p className="text-text-secondary">
        The page you're looking for doesn't exist.
      </p>
      <Button as="a" href="/">Go home</Button>
    </div>
  );
}
```

---

## Form Errors

```tsx
// Server-side error from mutation
const { mutateAsync, isPending } = useCreatePost();

const onSubmit = async (data: FormInput) => {
  try {
    await mutateAsync(data);
    toast.success("Post created!");
  } catch (error) {
    if (error instanceof ApiError && error.isValidation) {
      // Set field-specific errors
      const fieldErrors = (error.details as any)?.fieldErrors ?? {};
      for (const [field, messages] of Object.entries(fieldErrors)) {
        setError(field as keyof FormInput, {
          message: (messages as string[])[0],
        });
      }
    } else {
      setFormError(error instanceof Error ? error.message : "Submission failed");
    }
  }
};
```

---

## Global Error Handling

### Unhandled Promise Rejections
```typescript
// src/lib/error-tracking.ts (client-side)
if (typeof window !== "undefined") {
  window.addEventListener("unhandledrejection", (event) => {
    console.error("Unhandled promise rejection:", event.reason);
    // Report to Sentry
  });
}
```

### Toast Notifications for Network Errors
```typescript
// Intercept React Query errors globally
const queryClient = new QueryClient({
  queryCache: new QueryCache({
    onError: (error) => {
      if (error instanceof ApiError && error.isServerError) {
        toast.error("Server error. Please try again later.");
      }
    },
  }),
  mutationCache: new MutationCache({
    onError: (error) => {
      if (error instanceof ApiError && !error.isValidation) {
        toast.error(error.message);
      }
    },
  }),
});
```

---

## Logging & Monitoring

```typescript
// src/lib/logger.ts
export const logger = {
  info:  (msg: string, ctx?: object) => console.log  ("[INFO]",  msg, ctx ?? ""),
  warn:  (msg: string, ctx?: object) => console.warn ("[WARN]",  msg, ctx ?? ""),
  error: (msg: string, ctx?: object) => console.error("[ERROR]", msg, ctx ?? ""),
};

// Usage in API routes
logger.error("Failed to create post", { userId: session.user.id, error });
```

In production, configure Sentry:
```typescript
import * as Sentry from "@sentry/nextjs";

export function captureError(error: unknown, context?: Record<string, unknown>) {
  Sentry.captureException(error, { extra: context });
}
```

---

## Error Messages

### Principles
- Be specific: "Invalid email address" not "Invalid input"
- Be actionable: "Check your internet connection and try again" not "Network error"
- Avoid technical jargon: "Something went wrong" not "500 Internal Server Error"
- Avoid blame: "We couldn't save your changes" not "Your submission failed"

### Message Templates
| Situation | Message |
|-----------|---------|
| Network offline | "You appear to be offline. Check your connection and try again." |
| Server error | "Something went wrong on our end. Please try again in a moment." |
| Not found | "We couldn't find what you're looking for." |
| Auth required | "You need to sign in to do that." |
| Not authorized | "You don't have permission to do that." |
| Rate limited | "You're doing that too quickly. Please wait a moment." |
| Validation | Specific per-field messages from Zod |
