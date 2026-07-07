# Data Fetching

> Patterns, conventions, and best practices for fetching data throughout the application.

---

## Table of Contents

1. [Strategy Overview](#strategy-overview)
2. [Server Components](#server-components)
3. [React Query (Client)](#react-query-client)
4. [API Client](#api-client)
5. [Pagination](#pagination)
6. [Infinite Scroll](#infinite-scroll)
7. [Optimistic Updates](#optimistic-updates)
8. [Error Handling](#error-handling)
9. [Caching Strategy](#caching-strategy)
10. [Real-time Data](#real-time-data)

---

## Strategy Overview

| Pattern | When to use | Performance |
|---------|------------|-------------|
| **Server Component** | Static or semi-dynamic data, SEO-critical | Best |
| **`generateStaticParams` + revalidate** | List pages, blog posts | Great |
| **React Query** | Interactive, user-specific, frequently changing | Good |
| **SWR** | Simple key-value data with auto-revalidation | Good |
| **`useEffect` + fetch** | Avoid; use React Query instead | Poor |

---

## Server Components

Preferred for pages and layouts where data is needed for initial render:

```typescript
// src/app/posts/page.tsx
export const revalidate = 60; // ISR: regenerate at most every 60s

export default async function PostsPage() {
  // Direct DB call — no HTTP overhead
  const posts = await db.post.findMany({
    where: { published: true },
    orderBy: { createdAt: "desc" },
    take: 20,
  });

  return <PostList posts={posts} />;
}
```

### With Error Handling
```typescript
export default async function PostPage({ params }: { params: { slug: string } }) {
  const post = await db.post.findUnique({ where: { slug: params.slug } });

  if (!post) notFound(); // Renders 404 page
  
  return <Post post={post} />;
}
```

### With Streaming (Suspense)
```tsx
import { Suspense } from "react";
import { PostSkeleton } from "@/components/ui/Skeleton";

export default function PostsPage() {
  return (
    <div>
      <h1>Latest Posts</h1>
      <Suspense fallback={<PostSkeleton count={5} />}>
        <PostList />    {/* async server component */}
      </Suspense>
    </div>
  );
}
```

---

## React Query (Client)

Use for client-side data that changes based on user interaction or needs to stay fresh.

### Base API Function
```typescript
// src/lib/api.ts
export async function apiFetch<T>(
  path: string,
  options?: RequestInit
): Promise<T> {
  const res = await fetch(`/api${path}`, {
    headers: { "Content-Type": "application/json" },
    ...options,
  });

  if (!res.ok) {
    const error = await res.json().catch(() => ({ message: res.statusText }));
    throw new Error(error.message ?? "Request failed");
  }

  return res.json() as Promise<T>;
}
```

### Query Hook Pattern
```typescript
// src/features/posts/hooks/usePosts.ts
import { useQuery } from "@tanstack/react-query";
import { apiFetch } from "@/lib/api";
import type { Post } from "../types";

export const postKeys = {
  all:    () => ["posts"] as const,
  list:   (filters?: PostFilters) => [...postKeys.all(), "list", filters] as const,
  detail: (id: string) => [...postKeys.all(), "detail", id] as const,
};

export function usePosts(filters?: PostFilters) {
  return useQuery({
    queryKey: postKeys.list(filters),
    queryFn: () => apiFetch<Post[]>(`/posts${filters ? `?${new URLSearchParams(filters as Record<string, string>)}` : ""}`),
    staleTime: 60_000,
    select: (data) => data.sort((a, b) => b.createdAt.localeCompare(a.createdAt)),
  });
}
```

### Mutation Hook
```typescript
export function useCreatePost() {
  const queryClient = useQueryClient();
  const router = useRouter();

  return useMutation({
    mutationFn: (data: CreatePostInput) =>
      apiFetch<Post>("/posts", { method: "POST", body: JSON.stringify(data) }),

    onSuccess: (newPost) => {
      queryClient.setQueryData(postKeys.detail(newPost.id), newPost);
      queryClient.invalidateQueries({ queryKey: postKeys.all() });
      router.push(`/posts/${newPost.slug}`);
      toast.success("Post created successfully");
    },

    onError: (error) => {
      toast.error(`Failed to create post: ${error.message}`);
    },
  });
}
```

---

## API Client

Centralized fetch wrapper with auth and error handling:

```typescript
// src/lib/api.ts

class ApiError extends Error {
  constructor(
    message: string,
    public status: number,
    public data?: unknown
  ) {
    super(message);
    this.name = "ApiError";
  }
}

export async function apiFetch<T>(
  path: string,
  options: RequestInit & { params?: Record<string, string> } = {}
): Promise<T> {
  const { params, ...fetchOptions } = options;

  let url = `/api${path}`;
  if (params) {
    const qs = new URLSearchParams(params).toString();
    if (qs) url += `?${qs}`;
  }

  const res = await fetch(url, {
    headers: { "Content-Type": "application/json" },
    ...fetchOptions,
  });

  if (!res.ok) {
    const body = await res.json().catch(() => ({}));
    throw new ApiError(
      body.error?.message ?? res.statusText,
      res.status,
      body
    );
  }

  if (res.status === 204) return undefined as T; // No content
  return res.json() as Promise<T>;
}
```

---

## Pagination

### Cursor-based (preferred for large datasets)
```typescript
// API
interface PaginatedResponse<T> {
  items: T[];
  nextCursor: string | null;
  total: number;
}

// Hook
export function usePaginatedPosts() {
  const [cursor, setCursor] = useState<string | null>(null);

  const query = useQuery({
    queryKey: postKeys.list({ cursor }),
    queryFn: () => apiFetch<PaginatedResponse<Post>>(
      `/posts${cursor ? `?cursor=${cursor}` : ""}`
    ),
    keepPreviousData: true,
  });

  return {
    ...query,
    loadMore: () => setCursor(query.data?.nextCursor ?? null),
    hasMore: !!query.data?.nextCursor,
  };
}
```

### Offset-based (simpler, use for small datasets)
```typescript
const [page, setPage] = useState(1);
const { data } = useQuery({
  queryKey: postKeys.list({ page }),
  queryFn: () => apiFetch<{ items: Post[]; total: number }>(`/posts?page=${page}&limit=20`),
  keepPreviousData: true,
});
```

---

## Infinite Scroll

```typescript
import { useInfiniteQuery } from "@tanstack/react-query";

export function useInfinitePosts() {
  return useInfiniteQuery({
    queryKey: postKeys.all(),
    queryFn: ({ pageParam = null }) =>
      apiFetch<PaginatedResponse<Post>>(`/posts${pageParam ? `?cursor=${pageParam}` : ""}`),
    getNextPageParam: (lastPage) => lastPage.nextCursor ?? undefined,
    staleTime: 60_000,
  });
}

// In component:
const { data, fetchNextPage, hasNextPage, isFetchingNextPage } = useInfinitePosts();
const posts = data?.pages.flatMap((page) => page.items) ?? [];
```

---

## Optimistic Updates

```typescript
export function useDeletePost() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (id: string) => apiFetch(`/posts/${id}`, { method: "DELETE" }),

    onMutate: async (id) => {
      await queryClient.cancelQueries({ queryKey: postKeys.all() });
      const snapshot = queryClient.getQueriesData({ queryKey: postKeys.all() });
      
      // Remove post from all list queries
      queryClient.setQueriesData({ queryKey: postKeys.all() }, (old: Post[] | undefined) =>
        old?.filter((p) => p.id !== id) ?? []
      );

      return { snapshot };
    },

    onError: (_err, _id, ctx) => {
      // Restore snapshot
      ctx?.snapshot.forEach(([key, data]) => queryClient.setQueryData(key, data));
      toast.error("Failed to delete post");
    },

    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: postKeys.all() });
    },
  });
}
```

---

## Error Handling

```tsx
export function PostList() {
  const { data, error, isLoading, isError, refetch } = usePosts();

  if (isLoading) return <PostListSkeleton />;

  if (isError) {
    return (
      <ErrorState
        message={error instanceof ApiError && error.status === 404
          ? "No posts found."
          : "Failed to load posts."}
        onRetry={refetch}
      />
    );
  }

  if (!data?.length) return <EmptyState message="No posts yet." />;

  return <ul>{data.map((post) => <PostCard key={post.id} post={post} />)}</ul>;
}
```

---

## Caching Strategy

| Data type | `staleTime` | `gcTime` | Reasoning |
|-----------|-------------|----------|-----------|
| User profile | `5 * 60_000` (5min) | `30 * 60_000` | Changes infrequently |
| Post list | `60_000` (1min) | `5 * 60_000` | May update often |
| Post detail | `5 * 60_000` | `10 * 60_000` | Usually static once published |
| Real-time data | `0` | `60_000` | Always fresh |
| Config/settings | `10 * 60_000` | `60 * 60_000` | Rarely changes |

---

## Real-time Data

For data that must stay live, use polling or WebSockets:

### Polling
```typescript
useQuery({
  queryKey: ["notifications"],
  queryFn: fetchNotifications,
  refetchInterval: 30_000,         // Poll every 30 seconds
  refetchIntervalInBackground: false, // Stop when tab is hidden
});
```

### Server-Sent Events (SSE)
```typescript
useEffect(() => {
  const eventSource = new EventSource("/api/events");
  eventSource.onmessage = (e) => {
    queryClient.invalidateQueries({ queryKey: ["notifications"] });
  };
  return () => eventSource.close();
}, [queryClient]);
```
