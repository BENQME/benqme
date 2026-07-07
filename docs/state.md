# State Management

> Patterns and tools for managing application state.

---

## Table of Contents

1. [State Taxonomy](#state-taxonomy)
2. [When to Use What](#when-to-use-what)
3. [Server State (TanStack Query)](#server-state-tanstack-query)
4. [Global UI State (Zustand)](#global-ui-state-zustand)
5. [URL State](#url-state)
6. [Form State (React Hook Form)](#form-state-react-hook-form)
7. [Local Component State](#local-component-state)
8. [State Machines (XState)](#state-machines-xstate)
9. [Anti-patterns](#anti-patterns)

---

## State Taxonomy

| Category | Definition | Tool |
|----------|-----------|------|
| **Server state** | Data from an external source (API, DB) | TanStack Query |
| **Global UI state** | App-wide UI flags (theme, sidebar, modal) | Zustand |
| **URL state** | State encoded in the URL (filters, tabs, search) | `useSearchParams` / router |
| **Form state** | Input values, dirty/valid state | React Hook Form |
| **Local component state** | Private, ephemeral state | `useState` / `useReducer` |

---

## When to Use What

```
Is it from a server or async source?
  → TanStack Query

Does multiple routes/components need it without prop drilling?
  → Zustand (if UI state) or TanStack Query (if server data)

Should it survive a page refresh or be shareable via URL?
  → URL state (useSearchParams)

Is it only needed within a form?
  → React Hook Form

Is it only needed within a single component tree?
  → useState / useReducer / React Context
```

---

## Server State (TanStack Query)

### Setup
```typescript
// src/app/providers.tsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 60_000,       // 1 minute
      gcTime: 5 * 60_000,      // 5 minutes
      retry: 2,
      refetchOnWindowFocus: true,
    },
  },
});

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );
}
```

### Query Hook
```typescript
// src/features/posts/hooks/usePosts.ts
import { useQuery } from "@tanstack/react-query";

export const postsKeys = {
  all: ["posts"] as const,
  list: (filters: PostFilters) => [...postsKeys.all, "list", filters] as const,
  detail: (id: string) => [...postsKeys.all, "detail", id] as const,
};

export function usePosts(filters: PostFilters = {}) {
  return useQuery({
    queryKey: postsKeys.list(filters),
    queryFn: () => fetchPosts(filters),
    select: (data) => data.sort((a, b) => b.createdAt - a.createdAt),
  });
}

export function usePost(id: string) {
  return useQuery({
    queryKey: postsKeys.detail(id),
    queryFn: () => fetchPost(id),
    enabled: !!id,
  });
}
```

### Mutation Hook
```typescript
export function useCreatePost() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreatePostInput) => createPost(data),
    onSuccess: (newPost) => {
      // Add to cache immediately
      queryClient.setQueryData(postsKeys.detail(newPost.id), newPost);
      // Invalidate list queries to refetch
      queryClient.invalidateQueries({ queryKey: postsKeys.all });
    },
    onError: (error) => {
      toast.error("Failed to create post: " + error.message);
    },
  });
}
```

### Optimistic Updates
```typescript
export function useDeletePost() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: deletePost,
    onMutate: async (id) => {
      // Cancel in-flight queries
      await queryClient.cancelQueries({ queryKey: postsKeys.all });

      // Snapshot for rollback
      const previous = queryClient.getQueryData(postsKeys.list({}));

      // Optimistically remove from list
      queryClient.setQueryData(postsKeys.list({}), (old: Post[]) =>
        old.filter((p) => p.id !== id)
      );

      return { previous };
    },
    onError: (_err, _id, context) => {
      // Rollback
      queryClient.setQueryData(postsKeys.list({}), context?.previous);
    },
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: postsKeys.all });
    },
  });
}
```

---

## Global UI State (Zustand)

### Store Definition
```typescript
// src/stores/ui.store.ts
import { create } from "zustand";

interface UIStore {
  // Sidebar
  sidebarOpen: boolean;
  setSidebarOpen: (open: boolean) => void;
  toggleSidebar: () => void;

  // Modal
  activeModal: string | null;
  openModal: (id: string) => void;
  closeModal: () => void;
}

export const useUIStore = create<UIStore>((set) => ({
  sidebarOpen: false,
  setSidebarOpen: (open) => set({ sidebarOpen: open }),
  toggleSidebar: () => set((s) => ({ sidebarOpen: !s.sidebarOpen })),

  activeModal: null,
  openModal: (id) => set({ activeModal: id }),
  closeModal: () => set({ activeModal: null }),
}));
```

### Usage
```tsx
import { useUIStore } from "@/stores/ui.store";

// Component A — opens the modal
function OpenButton() {
  const openModal = useUIStore((s) => s.openModal);
  return <button onClick={() => openModal("confirm-delete")}>Delete</button>;
}

// Component B — anywhere in the tree
function ModalRenderer() {
  const { activeModal, closeModal } = useUIStore();
  if (activeModal !== "confirm-delete") return null;
  return <ConfirmDeleteModal onClose={closeModal} />;
}
```

### Persist to localStorage
```typescript
import { persist } from "zustand/middleware";

export const usePrefsStore = create<PrefsStore>()(
  persist(
    (set) => ({
      theme: "system" as const,
      setTheme: (theme) => set({ theme }),
    }),
    { name: "user-preferences" }
  )
);
```

---

## URL State

Use URL state when:
- State should survive refresh
- State should be shareable via URL
- Examples: search query, active filters, pagination, selected tab

```tsx
import { useSearchParams, useRouter, usePathname } from "next/navigation";
import { useCallback } from "react";

export function useFilters() {
  const searchParams = useSearchParams();
  const router = useRouter();
  const pathname = usePathname();

  const setFilter = useCallback(
    (key: string, value: string | null) => {
      const params = new URLSearchParams(searchParams.toString());
      if (value === null) {
        params.delete(key);
      } else {
        params.set(key, value);
      }
      params.delete("page"); // Reset pagination on filter change
      router.push(`${pathname}?${params.toString()}`);
    },
    [searchParams, router, pathname]
  );

  return {
    search: searchParams.get("search") ?? "",
    status: searchParams.get("status") ?? "all",
    page: Number(searchParams.get("page") ?? "1"),
    setFilter,
  };
}
```

---

## Form State (React Hook Form)

See [`forms.md`](./forms.md) for the complete form guide.

---

## Local Component State

Use `useState` for simple cases:
```tsx
const [isExpanded, setIsExpanded] = useState(false);
const [count, setCount] = useState(0);
```

Use `useReducer` for complex state transitions:
```tsx
type Action =
  | { type: "SET_LOADING" }
  | { type: "SET_DATA"; payload: Data }
  | { type: "SET_ERROR"; payload: string };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "SET_LOADING": return { ...state, status: "loading" };
    case "SET_DATA": return { status: "success", data: action.payload, error: null };
    case "SET_ERROR": return { status: "error", data: null, error: action.payload };
  }
}
```

---

## State Machines (XState)

For complex state transitions with many states and guards:

```typescript
import { createMachine, assign } from "xstate";

const uploadMachine = createMachine({
  id: "upload",
  initial: "idle",
  context: { progress: 0, error: null },
  states: {
    idle: {
      on: { SELECT_FILE: "selected" }
    },
    selected: {
      on: { UPLOAD: "uploading", CANCEL: "idle" }
    },
    uploading: {
      on: {
        PROGRESS: { actions: assign({ progress: ({ event }) => event.value }) },
        SUCCESS: "complete",
        ERROR: { target: "failed", actions: assign({ error: ({ event }) => event.message }) },
      }
    },
    complete: { type: "final" },
    failed: {
      on: { RETRY: "selected", CANCEL: "idle" }
    }
  }
});
```

---

## Anti-patterns

### ❌ Storing server data in Zustand
```typescript
// ❌ Wrong — use TanStack Query instead
const useUserStore = create(() => ({
  users: [],
  fetchUsers: async () => { /* ... */ }
}));
```

### ❌ Prop drilling more than 2 levels
```tsx
// ❌ Fragile — move to context or Zustand
<Page user={user}>
  <Layout user={user}>
    <Sidebar user={user}>
      <Avatar user={user} />
    </Sidebar>
  </Layout>
</Page>
```

### ❌ Deriving state in render
```tsx
// ❌ Runs on every render
const sortedItems = items.sort(...);

// ✅ Memoize
const sortedItems = useMemo(() => items.sort(...), [items]);
```
