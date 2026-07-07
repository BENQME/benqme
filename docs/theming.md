# Theming

> Theme configuration, dark mode, and custom theme patterns.

---

## Table of Contents

1. [Overview](#overview)
2. [Theme Architecture](#theme-architecture)
3. [Dark Mode](#dark-mode)
4. [Theme Provider](#theme-provider)
5. [Creating Custom Themes](#creating-custom-themes)
6. [Tailwind Integration](#tailwind-integration)
7. [useTheme Hook](#usetheme-hook)
8. [Theme Toggle Component](#theme-toggle-component)

---

## Overview

The theming system is built on CSS custom properties (design tokens). Switching themes requires only swapping the token values — no component changes needed.

Supported themes:
- `light` (default)
- `dark`
- `system` (follows OS preference)

---

## Theme Architecture

```
CSS custom properties (tokens)
    ↓
Semantic aliases (--color-text-primary, --color-bg-page, …)
    ↓
Component styles (always reference semantic aliases)
```

Token values are defined in `src/styles/tokens.css`. The `[data-theme]` attribute on `<html>` controls which values are active:

```css
:root {
  --color-bg-page:    #fafafa;
  --color-text-primary: #111827;
}

[data-theme="dark"] {
  --color-bg-page:    #0a0a0a;
  --color-text-primary: #f9fafb;
}
```

---

## Dark Mode

### Detection Strategy

The system uses a hybrid approach:
1. Check `localStorage` for a saved user preference
2. Fall back to `prefers-color-scheme` media query
3. Apply `data-theme` attribute to `<html>` element

```typescript
function getTheme(): "light" | "dark" {
  if (typeof window === "undefined") return "light";
  const saved = localStorage.getItem("theme") as "light" | "dark" | null;
  if (saved) return saved;
  return window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";
}
```

### Preventing Flash of Unstyled Content (FOUC)

Inject a blocking script before the body to apply the theme before React hydrates:

```html
<!-- In <head>, before any CSS -->
<script>
  (function() {
    const theme = localStorage.getItem('theme') ||
      (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
    document.documentElement.setAttribute('data-theme', theme);
  })();
</script>
```

In Next.js, add this to `src/app/layout.tsx`:

```tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" suppressHydrationWarning>
      <head>
        <script dangerouslySetInnerHTML={{ __html: themeScript }} />
      </head>
      <body>{children}</body>
    </html>
  );
}
```

---

## Theme Provider

```tsx
// src/providers/ThemeProvider.tsx
"use client";

import {
  createContext,
  useContext,
  useEffect,
  useState,
  type ReactNode,
} from "react";

type Theme = "light" | "dark" | "system";

interface ThemeContextValue {
  theme: Theme;
  resolvedTheme: "light" | "dark";
  setTheme: (theme: Theme) => void;
}

const ThemeContext = createContext<ThemeContextValue | null>(null);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setThemeState] = useState<Theme>("system");
  const [resolvedTheme, setResolvedTheme] = useState<"light" | "dark">("light");

  useEffect(() => {
    const saved = localStorage.getItem("theme") as Theme | null;
    if (saved) setThemeState(saved);
  }, []);

  useEffect(() => {
    const resolved =
      theme === "system"
        ? window.matchMedia("(prefers-color-scheme: dark)").matches
          ? "dark"
          : "light"
        : theme;

    setResolvedTheme(resolved);
    document.documentElement.setAttribute("data-theme", resolved);
  }, [theme]);

  const setTheme = (newTheme: Theme) => {
    setThemeState(newTheme);
    if (newTheme === "system") {
      localStorage.removeItem("theme");
    } else {
      localStorage.setItem("theme", newTheme);
    }
  };

  return (
    <ThemeContext.Provider value={{ theme, resolvedTheme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error("useTheme must be used within ThemeProvider");
  return ctx;
}
```

---

## Creating Custom Themes

Add a new theme by defining a new `[data-theme]` block:

```css
[data-theme="high-contrast"] {
  --color-bg-page:       #000000;
  --color-bg-primary:    #000000;
  --color-text-primary:  #ffffff;
  --color-text-secondary:#cccccc;
  --color-border:        #ffffff;
  --color-brand:         #ffff00;
  --color-focus:         #ffff00;
}
```

Register the theme in the ThemeProvider's allowed values and update the toggle UI.

---

## Tailwind Integration

Configure Tailwind to use CSS custom properties for color values:

```typescript
// tailwind.config.ts
import type { Config } from "tailwindcss";

export default {
  darkMode: ["class", '[data-theme="dark"]'],
  theme: {
    extend: {
      colors: {
        bg: {
          page:      "var(--color-bg-page)",
          primary:   "var(--color-bg-primary)",
          secondary: "var(--color-bg-secondary)",
        },
        text: {
          primary:   "var(--color-text-primary)",
          secondary: "var(--color-text-secondary)",
        },
        brand: {
          DEFAULT: "var(--color-brand)",
          hover:   "var(--color-brand-hover)",
        },
        border:   "var(--color-border)",
      },
    },
  },
} satisfies Config;
```

Now use semantic Tailwind classes that respect theming:
```tsx
<div className="bg-bg-primary text-text-primary border border-border">
  Themed element
</div>
```

---

## useTheme Hook

```tsx
"use client";

import { useTheme } from "@/providers/ThemeProvider";

export function ThemeAwareComponent() {
  const { resolvedTheme, theme, setTheme } = useTheme();

  return (
    <div>
      <p>Current theme: {resolvedTheme}</p>
      <p>Preference: {theme}</p>
      <button onClick={() => setTheme("dark")}>Dark</button>
      <button onClick={() => setTheme("light")}>Light</button>
      <button onClick={() => setTheme("system")}>System</button>
    </div>
  );
}
```

---

## Theme Toggle Component

```tsx
// src/components/ui/ThemeToggle.tsx
"use client";

import { Moon, Sun, Monitor } from "lucide-react";
import { useTheme } from "@/providers/ThemeProvider";
import { Button } from "@/components/ui/Button";

export function ThemeToggle() {
  const { theme, resolvedTheme, setTheme } = useTheme();

  const toggleTheme = () => {
    if (resolvedTheme === "light") {
      setTheme("dark");
    } else {
      setTheme("light");
    }
  };

  return (
    <Button
      variant="ghost"
      size="sm"
      iconOnly
      aria-label={`Switch to ${resolvedTheme === "light" ? "dark" : "light"} mode`}
      onClick={toggleTheme}
    >
      {resolvedTheme === "light" ? (
        <Moon aria-hidden="true" size={16} />
      ) : (
        <Sun aria-hidden="true" size={16} />
      )}
    </Button>
  );
}
```
