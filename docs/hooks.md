# Hooks

> Custom React hooks: catalog, patterns, and implementation guide.

---

## Table of Contents

1. [Hook Conventions](#hook-conventions)
2. [Data Fetching Hooks](#data-fetching-hooks)
3. [UI Hooks](#ui-hooks)
4. [Browser & DOM Hooks](#browser--dom-hooks)
5. [State Hooks](#state-hooks)
6. [Event Hooks](#event-hooks)
7. [Performance Hooks](#performance-hooks)
8. [Writing a Custom Hook](#writing-a-custom-hook)

---

## Hook Conventions

- Always prefix with `use`
- Return a single object (not an array) if more than 2 values are returned
- Avoid side effects outside of `useEffect`
- Never call hooks conditionally
- Export types alongside the hook
- Write unit tests for all hooks with `renderHook` from React Testing Library

```typescript
// ✅ Return shape
function useFeature() {
  return {
    data,
    isLoading,
    error,
    doSomething,
    reset,
  };
}

// ✅ Array only for setState-style pairs
function useToggle(initial = false): [boolean, () => void] {
  const [state, setState] = useState(initial);
  const toggle = useCallback(() => setState((s) => !s), []);
  return [state, toggle];
}
```

---

## Data Fetching Hooks

These live in `src/features/[name]/hooks/` and are built with TanStack Query.

### useQuery pattern
```typescript
// src/features/posts/hooks/usePosts.ts
import { useQuery } from "@tanstack/react-query";
import type { Post } from "../types";

async function fetchPosts(): Promise<Post[]> {
  const res = await fetch("/api/posts");
  if (!res.ok) throw new Error("Failed to fetch posts");
  return res.json();
}

export function usePosts() {
  return useQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
    staleTime: 60_000,
  });
}
```

---

## UI Hooks

### `useDisclosure`
Controls open/closed state for dialogs, drawers, and dropdowns:
```typescript
// src/hooks/useDisclosure.ts
import { useCallback, useState } from "react";

export function useDisclosure(initial = false) {
  const [isOpen, setIsOpen] = useState(initial);

  const open = useCallback(() => setIsOpen(true), []);
  const close = useCallback(() => setIsOpen(false), []);
  const toggle = useCallback(() => setIsOpen((o) => !o), []);
  const onOpenChange = useCallback((value: boolean) => setIsOpen(value), []);

  return { isOpen, open, close, toggle, onOpenChange };
}
```

### `useToggle`
```typescript
// src/hooks/useToggle.ts
import { useCallback, useState } from "react";

export function useToggle(initial = false): [boolean, () => void, (v: boolean) => void] {
  const [state, setState] = useState(initial);
  const toggle = useCallback(() => setState((s) => !s), []);
  return [state, toggle, setState];
}
```

### `useCopyToClipboard`
```typescript
// src/hooks/useCopyToClipboard.ts
import { useCallback, useState } from "react";

export function useCopyToClipboard(resetDelay = 2000) {
  const [copied, setCopied] = useState(false);

  const copy = useCallback(async (text: string) => {
    try {
      await navigator.clipboard.writeText(text);
      setCopied(true);
      setTimeout(() => setCopied(false), resetDelay);
      return true;
    } catch {
      return false;
    }
  }, [resetDelay]);

  return { copied, copy };
}
```

---

## Browser & DOM Hooks

### `useMediaQuery`
```typescript
// src/hooks/useMediaQuery.ts
import { useEffect, useState } from "react";

export function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    const media = window.matchMedia(query);
    setMatches(media.matches);
    const handler = (e: MediaQueryListEvent) => setMatches(e.matches);
    media.addEventListener("change", handler);
    return () => media.removeEventListener("change", handler);
  }, [query]);

  return matches;
}

// Usage
const isMobile = useMediaQuery("(max-width: 639px)");
const isDark = useMediaQuery("(prefers-color-scheme: dark)");
const prefersReducedMotion = useMediaQuery("(prefers-reduced-motion: reduce)");
```

### `useLocalStorage`
```typescript
// src/hooks/useLocalStorage.ts
import { useCallback, useEffect, useState } from "react";

export function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    if (typeof window === "undefined") return initialValue;
    try {
      const item = window.localStorage.getItem(key);
      return item ? (JSON.parse(item) as T) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = useCallback(
    (value: T | ((val: T) => T)) => {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      if (typeof window !== "undefined") {
        window.localStorage.setItem(key, JSON.stringify(valueToStore));
      }
    },
    [key, storedValue]
  );

  return [storedValue, setValue] as const;
}
```

### `useIntersectionObserver`
```typescript
// src/hooks/useIntersectionObserver.ts
import { useEffect, useRef, useState } from "react";

export function useIntersectionObserver(options?: IntersectionObserverInit) {
  const ref = useRef<HTMLElement | null>(null);
  const [isIntersecting, setIsIntersecting] = useState(false);
  const [hasIntersected, setHasIntersected] = useState(false);

  useEffect(() => {
    const el = ref.current;
    if (!el) return;

    const observer = new IntersectionObserver(([entry]) => {
      setIsIntersecting(entry.isIntersecting);
      if (entry.isIntersecting) setHasIntersected(true);
    }, options);

    observer.observe(el);
    return () => observer.disconnect();
  }, [options]);

  return { ref, isIntersecting, hasIntersected };
}
```

### `useClickOutside`
```typescript
// src/hooks/useClickOutside.ts
import { useEffect, type RefObject } from "react";

export function useClickOutside<T extends HTMLElement>(
  ref: RefObject<T>,
  handler: () => void
) {
  useEffect(() => {
    function listener(e: MouseEvent | TouchEvent) {
      if (!ref.current || ref.current.contains(e.target as Node)) return;
      handler();
    }
    document.addEventListener("mousedown", listener);
    document.addEventListener("touchstart", listener);
    return () => {
      document.removeEventListener("mousedown", listener);
      document.removeEventListener("touchstart", listener);
    };
  }, [ref, handler]);
}
```

---

## State Hooks

### `useCounter`
```typescript
export function useCounter(initialCount = 0, { min, max }: { min?: number; max?: number } = {}) {
  const [count, setCount] = useState(initialCount);

  const increment = useCallback(() =>
    setCount((c) => (max !== undefined ? Math.min(c + 1, max) : c + 1)), [max]);

  const decrement = useCallback(() =>
    setCount((c) => (min !== undefined ? Math.max(c - 1, min) : c - 1)), [min]);

  const reset = useCallback(() => setCount(initialCount), [initialCount]);

  return { count, increment, decrement, reset, setCount };
}
```

### `usePrevious`
```typescript
export function usePrevious<T>(value: T): T | undefined {
  const ref = useRef<T | undefined>(undefined);
  useEffect(() => {
    ref.current = value;
  }, [value]);
  return ref.current;
}
```

---

## Event Hooks

### `useKeyPress`
```typescript
export function useKeyPress(
  key: string,
  handler: (event: KeyboardEvent) => void,
  options: { preventDefault?: boolean } = {}
) {
  useEffect(() => {
    function listener(e: KeyboardEvent) {
      if (e.key !== key) return;
      if (options.preventDefault) e.preventDefault();
      handler(e);
    }
    window.addEventListener("keydown", listener);
    return () => window.removeEventListener("keydown", listener);
  }, [key, handler, options.preventDefault]);
}

// Usage
useKeyPress("Escape", closeModal);
useKeyPress("k", openCommandPalette, { preventDefault: true }); // Meta+K handled externally
```

---

## Performance Hooks

### `useDebounce`
```typescript
export function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debounced;
}

// Usage — search input
const debouncedQuery = useDebounce(searchQuery, 300);
```

### `useThrottle`
```typescript
export function useThrottle<T>(value: T, limit: number): T {
  const [throttled, setThrottled] = useState(value);
  const lastRan = useRef(Date.now());

  useEffect(() => {
    const remaining = limit - (Date.now() - lastRan.current);
    if (remaining <= 0) {
      lastRan.current = Date.now();
      setThrottled(value);
    } else {
      const timer = setTimeout(() => {
        lastRan.current = Date.now();
        setThrottled(value);
      }, remaining);
      return () => clearTimeout(timer);
    }
  }, [value, limit]);

  return throttled;
}
```

---

## Writing a Custom Hook

### Template
```typescript
// src/hooks/useMyHook.ts

import { useCallback, useEffect, useState } from "react";

/**
 * [One-sentence description of what this hook does.]
 *
 * @param param1 - [Description]
 * @param options - [Description]
 * @returns [Describe return shape]
 *
 * @example
 * const { value, reset } = useMyHook(initialValue);
 */
export function useMyHook(param1: string, options: MyHookOptions = {}) {
  const [state, setState] = useState<MyState>({ /* initial */ });

  useEffect(() => {
    // Setup logic
    return () => {
      // Cleanup logic
    };
  }, [param1]);

  const doSomething = useCallback(() => {
    // ...
  }, []);

  return {
    // ...state spread or selected fields
    doSomething,
  };
}

export interface MyHookOptions {
  onSuccess?: () => void;
  onError?: (error: Error) => void;
}
```
