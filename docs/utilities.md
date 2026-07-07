# Utilities

> Reusable utility functions reference and usage guide.

---

## Table of Contents

1. [String Utilities](#string-utilities)
2. [Date Utilities](#date-utilities)
3. [Number Utilities](#number-utilities)
4. [Array Utilities](#array-utilities)
5. [Object Utilities](#object-utilities)
6. [URL Utilities](#url-utilities)
7. [DOM Utilities](#dom-utilities)
8. [Validation Utilities](#validation-utilities)
9. [Class Name Utilities](#class-name-utilities)
10. [Type Guards](#type-guards)

---

## String Utilities

```typescript
// src/utils/string.ts

/** Truncate a string to maxLength characters, appending an ellipsis if needed. */
export function truncate(str: string, maxLength: number, ellipsis = "…"): string {
  if (str.length <= maxLength) return str;
  return str.slice(0, maxLength - ellipsis.length) + ellipsis;
}

/** Convert a string to a URL-safe slug. */
export function slugify(str: string): string {
  return str
    .toLowerCase()
    .normalize("NFD")
    .replace(/[\u0300-\u036f]/g, "")
    .replace(/[^a-z0-9\s-]/g, "")
    .trim()
    .replace(/[\s_]+/g, "-")
    .replace(/-+/g, "-");
}

/** Capitalize the first letter of a string. */
export function capitalize(str: string): string {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

/** Convert camelCase or snake_case to a human-readable label. */
export function toLabel(str: string): string {
  return str
    .replace(/([a-z])([A-Z])/g, "$1 $2")
    .replace(/_/g, " ")
    .toLowerCase()
    .replace(/^\w/, (c) => c.toUpperCase());
}

/** Count words in a string (for reading time estimates). */
export function wordCount(str: string): number {
  return str.trim().split(/\s+/).filter(Boolean).length;
}

/** Estimate reading time in minutes. */
export function readingTime(text: string, wpm = 200): number {
  return Math.ceil(wordCount(text) / wpm);
}

/** Mask part of a string (e.g., email addresses). */
export function maskEmail(email: string): string {
  const [local, domain] = email.split("@");
  return `${local[0]}${"*".repeat(local.length - 2)}${local.at(-1)}@${domain}`;
}
```

---

## Date Utilities

```typescript
// src/utils/date.ts
import { format, formatDistanceToNow, isValid, parseISO } from "date-fns";

/** Format a date for display. */
export function formatDate(
  date: Date | string | null | undefined,
  pattern = "MMM d, yyyy"
): string {
  if (!date) return "";
  const d = typeof date === "string" ? parseISO(date) : date;
  if (!isValid(d)) return "Invalid date";
  return format(d, pattern);
}

/** Return a relative time string (e.g., "3 days ago"). */
export function timeAgo(date: Date | string): string {
  const d = typeof date === "string" ? parseISO(date) : date;
  return formatDistanceToNow(d, { addSuffix: true });
}

/** Format a date for datetime attributes. */
export function toISODate(date: Date | string): string {
  const d = typeof date === "string" ? parseISO(date) : date;
  return d.toISOString();
}

/** Check if a date is in the past. */
export function isPast(date: Date | string): boolean {
  const d = typeof date === "string" ? parseISO(date) : date;
  return d < new Date();
}
```

---

## Number Utilities

```typescript
// src/utils/number.ts

/** Format a number as currency. */
export function formatCurrency(
  amount: number,
  currency = "USD",
  locale = "en-US"
): string {
  return new Intl.NumberFormat(locale, { style: "currency", currency }).format(amount);
}

/** Format a number with compact notation (e.g., 1,200 → "1.2K"). */
export function formatCompact(n: number, locale = "en-US"): string {
  return new Intl.NumberFormat(locale, { notation: "compact" }).format(n);
}

/** Clamp a number between min and max. */
export function clamp(value: number, min: number, max: number): number {
  return Math.min(Math.max(value, min), max);
}

/** Linear interpolation between two values. */
export function lerp(a: number, b: number, t: number): number {
  return a + (b - a) * clamp(t, 0, 1);
}

/** Map a value from one range to another. */
export function mapRange(
  value: number,
  inMin: number,
  inMax: number,
  outMin: number,
  outMax: number
): number {
  return ((value - inMin) / (inMax - inMin)) * (outMax - outMin) + outMin;
}

/** Format bytes to human-readable string. */
export function formatBytes(bytes: number, decimals = 2): string {
  if (bytes === 0) return "0 Bytes";
  const k = 1024;
  const sizes = ["Bytes", "KB", "MB", "GB", "TB"];
  const i = Math.floor(Math.log(bytes) / Math.log(k));
  return `${parseFloat((bytes / Math.pow(k, i)).toFixed(decimals))} ${sizes[i]}`;
}
```

---

## Array Utilities

```typescript
// src/utils/array.ts

/** Chunk an array into groups of size n. */
export function chunk<T>(arr: T[], size: number): T[][] {
  return Array.from({ length: Math.ceil(arr.length / size) }, (_, i) =>
    arr.slice(i * size, i * size + size)
  );
}

/** Remove duplicate values from an array. */
export function unique<T>(arr: T[]): T[] {
  return [...new Set(arr)];
}

/** Remove duplicates by a key function. */
export function uniqueBy<T>(arr: T[], keyFn: (item: T) => unknown): T[] {
  const seen = new Set();
  return arr.filter((item) => {
    const key = keyFn(item);
    if (seen.has(key)) return false;
    seen.add(key);
    return true;
  });
}

/** Group an array of objects by a key. */
export function groupBy<T>(arr: T[], keyFn: (item: T) => string): Record<string, T[]> {
  return arr.reduce<Record<string, T[]>>((acc, item) => {
    const key = keyFn(item);
    if (!acc[key]) acc[key] = [];
    acc[key].push(item);
    return acc;
  }, {});
}

/** Sort an array of objects by a key. */
export function sortBy<T>(arr: T[], keyFn: (item: T) => number | string, dir: "asc" | "desc" = "asc"): T[] {
  return [...arr].sort((a, b) => {
    const ka = keyFn(a);
    const kb = keyFn(b);
    const cmp = ka < kb ? -1 : ka > kb ? 1 : 0;
    return dir === "asc" ? cmp : -cmp;
  });
}

/** Shuffle an array (Fisher-Yates). */
export function shuffle<T>(arr: T[]): T[] {
  const result = [...arr];
  for (let i = result.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [result[i], result[j]] = [result[j], result[i]];
  }
  return result;
}
```

---

## Object Utilities

```typescript
// src/utils/object.ts

/** Pick specific keys from an object. */
export function pick<T extends object, K extends keyof T>(obj: T, keys: K[]): Pick<T, K> {
  return keys.reduce((acc, key) => {
    if (key in obj) acc[key] = obj[key];
    return acc;
  }, {} as Pick<T, K>);
}

/** Omit specific keys from an object. */
export function omit<T extends object, K extends keyof T>(obj: T, keys: K[]): Omit<T, K> {
  const result = { ...obj };
  keys.forEach((key) => delete result[key]);
  return result as Omit<T, K>;
}

/** Deep merge two objects. */
export function deepMerge<T extends object>(base: T, override: Partial<T>): T {
  const result = { ...base };
  for (const key in override) {
    const v = override[key];
    if (v && typeof v === "object" && !Array.isArray(v)) {
      result[key] = deepMerge(base[key] as object, v as object) as T[typeof key];
    } else {
      result[key] = v as T[typeof key];
    }
  }
  return result;
}

/** Remove null and undefined values from an object. */
export function compact<T extends object>(obj: T): Partial<T> {
  return Object.fromEntries(
    Object.entries(obj).filter(([, v]) => v != null)
  ) as Partial<T>;
}
```

---

## URL Utilities

```typescript
// src/utils/url.ts

/** Build a URL with query parameters. */
export function buildUrl(base: string, params: Record<string, string | number | boolean | null | undefined>): string {
  const url = new URL(base, "https://placeholder.com");
  for (const [key, value] of Object.entries(params)) {
    if (value != null) {
      url.searchParams.set(key, String(value));
    }
  }
  return url.pathname + (url.search ? url.search : "");
}

/** Parse query string into an object. */
export function parseQuery(search: string): Record<string, string> {
  return Object.fromEntries(new URLSearchParams(search));
}

/** Check if a URL is external. */
export function isExternalUrl(url: string): boolean {
  try {
    const parsed = new URL(url);
    return parsed.hostname !== window.location.hostname;
  } catch {
    return false;
  }
}
```

---

## DOM Utilities

```typescript
// src/utils/dom.ts

/** Scroll an element into view smoothly. */
export function scrollIntoView(el: HTMLElement | null, block: ScrollLogicalPosition = "start") {
  el?.scrollIntoView({ behavior: "smooth", block });
}

/** Get all focusable elements within a container. */
export function getFocusableElements(container: HTMLElement): HTMLElement[] {
  return Array.from(
    container.querySelectorAll<HTMLElement>(
      'a[href], button:not([disabled]), input:not([disabled]), ' +
      'select:not([disabled]), textarea:not([disabled]), [tabindex]:not([tabindex="-1"])'
    )
  ).filter((el) => !el.hasAttribute("hidden"));
}

/** Lock body scroll (for modals). */
export function lockScroll() {
  document.body.style.overflow = "hidden";
  document.body.style.paddingRight = `${window.innerWidth - document.documentElement.clientWidth}px`;
}

export function unlockScroll() {
  document.body.style.overflow = "";
  document.body.style.paddingRight = "";
}
```

---

## Validation Utilities

```typescript
// src/utils/validation.ts

export const isEmail = (v: string) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v);
export const isUrl = (v: string) => { try { new URL(v); return true; } catch { return false; } };
export const isEmpty = (v: unknown) => v === null || v === undefined || v === "" || (Array.isArray(v) && v.length === 0);
export const isNumeric = (v: string) => !isNaN(Number(v)) && v.trim() !== "";
```

---

## Class Name Utilities

```typescript
// src/utils/cn.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

/**
 * Merge Tailwind classes, resolving conflicts correctly.
 * Combines clsx and tailwind-merge.
 */
export function cn(...inputs: ClassValue[]): string {
  return twMerge(clsx(inputs));
}
```

---

## Type Guards

```typescript
// src/utils/guards.ts

export const isDefined = <T>(value: T | null | undefined): value is T =>
  value !== null && value !== undefined;

export const isString = (value: unknown): value is string =>
  typeof value === "string";

export const isNumber = (value: unknown): value is number =>
  typeof value === "number" && !isNaN(value);

export const isObject = (value: unknown): value is Record<string, unknown> =>
  typeof value === "object" && value !== null && !Array.isArray(value);

export const isError = (value: unknown): value is Error =>
  value instanceof Error;

export function assertDefined<T>(value: T | null | undefined, message?: string): T {
  if (value == null) throw new Error(message ?? "Expected defined value");
  return value;
}
```
