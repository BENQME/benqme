# Implementation Guide

> Step-by-step patterns and recipes for implementing common features.

---

## Table of Contents

1. [Adding a New Page](#adding-a-new-page)
2. [Adding a New API Route](#adding-a-new-api-route)
3. [Adding a New Component](#adding-a-new-component)
4. [Adding a New Feature Module](#adding-a-new-feature-module)
5. [Data Fetching Patterns](#data-fetching-patterns)
6. [Form Implementation](#form-implementation)
7. [Authentication-Gated Pages](#authentication-gated-pages)
8. [Adding a New Database Table](#adding-a-new-database-table)
9. [Adding Environment Variables](#adding-environment-variables)
10. [Error Boundaries](#error-boundaries)

---

## Adding a New Page

### App Router (Next.js 14+)

```bash
# Create the page file
src/app/(app)/your-page/page.tsx
```

```tsx
// src/app/(app)/your-page/page.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Your Page | BENQME",
  description: "Page description for SEO",
};

export default function YourPage() {
  return (
    <main>
      <h1>Your Page</h1>
    </main>
  );
}
```

### Dynamic Route
```
src/app/(app)/posts/[slug]/page.tsx
```
```tsx
interface Props {
  params: { slug: string };
}

export async function generateStaticParams() {
  // Return list of slugs for SSG
  const posts = await fetchAllPosts();
  return posts.map((p) => ({ slug: p.slug }));
}

export default async function PostPage({ params }: Props) {
  const post = await fetchPost(params.slug);
  if (!post) notFound();
  return <article>{/* ... */}</article>;
}
```

---

## Adding a New API Route

```
src/app/api/[resource]/route.ts
```

```typescript
// src/app/api/users/route.ts
import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { db } from "@/lib/db";
import { auth } from "@/lib/auth";

const CreateUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email(),
});

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    if (!session) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const users = await db.user.findMany();
    return NextResponse.json(users);
  } catch (error) {
    console.error("[GET /api/users]", error);
    return NextResponse.json({ error: "Internal Server Error" }, { status: 500 });
  }
}

export async function POST(request: NextRequest) {
  try {
    const session = await auth();
    if (!session) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    const body = await request.json();
    const parsed = CreateUserSchema.safeParse(body);
    if (!parsed.success) {
      return NextResponse.json({ error: parsed.error.flatten() }, { status: 400 });
    }

    const user = await db.user.create({ data: parsed.data });
    return NextResponse.json(user, { status: 201 });
  } catch (error) {
    console.error("[POST /api/users]", error);
    return NextResponse.json({ error: "Internal Server Error" }, { status: 500 });
  }
}
```

---

## Adding a New Component

### 1. Create the component folder
```bash
mkdir src/components/ui/MyComponent
```

### 2. Implement the component
```tsx
// src/components/ui/MyComponent/MyComponent.tsx
import { cn } from "@/utils/cn";

interface MyComponentProps {
  className?: string;
  children: React.ReactNode;
}

export function MyComponent({ className, children }: MyComponentProps) {
  return (
    <div className={cn("base-styles", className)}>
      {children}
    </div>
  );
}
```

### 3. Create barrel export
```typescript
// src/components/ui/MyComponent/index.ts
export { MyComponent } from "./MyComponent";
export type { MyComponentProps } from "./MyComponent";
```

### 4. Add to shared UI barrel (if needed)
```typescript
// src/components/ui/index.ts
export { MyComponent } from "./MyComponent";
```

### 5. Write tests
```tsx
// src/components/ui/MyComponent/MyComponent.test.tsx
import { render, screen } from "@testing-library/react";
import { MyComponent } from "./MyComponent";

describe("MyComponent", () => {
  it("renders children", () => {
    render(<MyComponent>Hello</MyComponent>);
    expect(screen.getByText("Hello")).toBeInTheDocument();
  });
});
```

---

## Adding a New Feature Module

```bash
mkdir -p src/features/my-feature/{components,hooks}
touch src/features/my-feature/{index.ts,api.ts,types.ts,utils.ts}
```

Structure:
```
src/features/my-feature/
├── index.ts          # Public API
├── api.ts            # Data fetching
├── types.ts          # Types/interfaces
├── utils.ts          # Feature utilities
├── components/       # Feature-specific UI
└── hooks/            # Feature-specific hooks
```

---

## Data Fetching Patterns

### Server Component (preferred)
```tsx
// src/app/(app)/dashboard/page.tsx
export default async function DashboardPage() {
  const data = await fetchDashboardData(); // Direct DB or API call
  return <Dashboard data={data} />;
}
```

### React Query (client components)
```tsx
// src/features/users/hooks/useUsers.ts
import { useQuery } from "@tanstack/react-query";

export function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then((r) => r.json()),
    staleTime: 60_000, // 1 minute
  });
}
```

### Mutations
```tsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

export function useCreateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateUserInput) =>
      fetch("/api/users", { method: "POST", body: JSON.stringify(data) })
        .then((r) => r.json()),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["users"] });
    },
  });
}
```

---

## Form Implementation

```tsx
// Using React Hook Form + Zod
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

const schema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Invalid email"),
});

type FormValues = z.infer<typeof schema>;

export function CreateUserForm() {
  const { register, handleSubmit, formState: { errors, isSubmitting } } =
    useForm<FormValues>({ resolver: zodResolver(schema) });

  const onSubmit = async (data: FormValues) => {
    await createUser(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label htmlFor="name">Name</label>
        <input id="name" {...register("name")} aria-describedby="name-error" />
        {errors.name && (
          <span id="name-error" role="alert">{errors.name.message}</span>
        )}
      </div>
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Saving…" : "Save"}
      </button>
    </form>
  );
}
```

---

## Authentication-Gated Pages

### Middleware (route protection)
```typescript
// middleware.ts
import { auth } from "@/lib/auth";

export default auth((req) => {
  if (!req.auth && req.nextUrl.pathname.startsWith("/app")) {
    return Response.redirect(new URL("/login", req.url));
  }
});

export const config = {
  matcher: ["/app/:path*"],
};
```

### Server Component
```tsx
import { auth } from "@/lib/auth";
import { redirect } from "next/navigation";

export default async function ProtectedPage() {
  const session = await auth();
  if (!session) redirect("/login");

  return <div>Welcome, {session.user.name}</div>;
}
```

---

## Adding a New Database Table

### 1. Update schema
```prisma
// prisma/schema.prisma
model Post {
  id        String   @id @default(cuid())
  title     String
  content   String?
  published Boolean  @default(false)
  authorId  String
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([authorId])
}
```

### 2. Generate migration
```bash
pnpm prisma migrate dev --name add-posts
```

### 3. Regenerate client
```bash
pnpm prisma generate
```

---

## Adding Environment Variables

1. Add to `.env.example` with a placeholder value and comment:
```bash
# Required for email sending
RESEND_API_KEY=your-resend-api-key-here
```

2. Add to `.env.local` with the real value

3. Add to the environment variables table in [`development.md`](./development.md)

4. Validate at startup:
```typescript
// src/lib/env.ts
import { z } from "zod";

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  RESEND_API_KEY: z.string().min(1),
  NODE_ENV: z.enum(["development", "test", "production"]),
});

export const env = envSchema.parse(process.env);
```

---

## Error Boundaries

```tsx
// src/components/ErrorBoundary.tsx
"use client";

import { Component, type ReactNode } from "react";

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error("ErrorBoundary caught:", error, info);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback ?? (
        <div role="alert">
          <h2>Something went wrong</h2>
          <p>{this.state.error?.message}</p>
        </div>
      );
    }
    return this.props.children;
  }
}
```
