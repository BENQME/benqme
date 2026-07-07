# Breakpoints

> Responsive breakpoint definitions, usage patterns, and conventions.

---

## Table of Contents

1. [Breakpoint Scale](#breakpoint-scale)
2. [Mobile-First Approach](#mobile-first-approach)
3. [Tailwind Usage](#tailwind-usage)
4. [CSS Media Queries](#css-media-queries)
5. [JavaScript / React Usage](#javascript--react-usage)
6. [Design Guidance](#design-guidance)
7. [Common Patterns](#common-patterns)

---

## Breakpoint Scale

| Name | Min-width | Target devices |
|------|-----------|---------------|
| `base` | 0px | Small phones (< 640px) |
| `sm` | 640px | Large phones, small tablets |
| `md` | 768px | Tablets |
| `lg` | 1024px | Small laptops |
| `xl` | 1280px | Desktops |
| `2xl` | 1536px | Large desktops |

```css
:root {
  /* For reference only — can't use CSS vars in @media queries */
  --breakpoint-sm:  640px;
  --breakpoint-md:  768px;
  --breakpoint-lg:  1024px;
  --breakpoint-xl:  1280px;
  --breakpoint-2xl: 1536px;
}
```

---

## Mobile-First Approach

Write base styles for mobile, then progressively enhance for larger screens:

```css
/* ✅ Mobile-first */
.card {
  grid-column: span 12;    /* Full width on mobile */
}

@media (min-width: 768px) {
  .card {
    grid-column: span 6;   /* Half width on tablet */
  }
}

@media (min-width: 1024px) {
  .card {
    grid-column: span 4;   /* Third width on desktop */
  }
}
```

```tsx
{/* ✅ Tailwind mobile-first */}
<div className="col-span-12 md:col-span-6 lg:col-span-4">
```

---

## Tailwind Usage

All Tailwind responsive prefixes are mobile-first (`min-width`):

```tsx
{/* Typography */}
<h1 className="text-3xl md:text-4xl lg:text-5xl xl:text-6xl">
  Heading
</h1>

{/* Layout */}
<div className="flex flex-col md:flex-row gap-6">

{/* Grid */}
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">

{/* Visibility */}
<nav className="hidden md:flex">         {/* Hidden on mobile */}
<button className="md:hidden">           {/* Only on mobile */}

{/* Spacing */}
<section className="py-12 md:py-16 lg:py-24">

{/* Container padding */}
<div className="px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto">
```

### Container Component
```tsx
// Standard responsive container
<div className="container mx-auto px-4 sm:px-6 lg:px-8">
  {children}
</div>
```

---

## CSS Media Queries

```css
/* Mobile only (max-width) */
@media (max-width: 639px) { }

/* Tablet and up */
@media (min-width: 640px) { }

/* Between tablet and desktop */
@media (min-width: 640px) and (max-width: 1023px) { }

/* Desktop and up */
@media (min-width: 1024px) { }

/* Large desktop */
@media (min-width: 1280px) { }

/* Print */
@media print { }

/* Hover capability (pointer devices) */
@media (hover: hover) { }

/* Coarse pointer (touch) */
@media (pointer: coarse) { }

/* Reduced motion */
@media (prefers-reduced-motion: reduce) { }

/* High DPI / Retina */
@media (min-resolution: 2dppx) { }
```

---

## JavaScript / React Usage

### useBreakpoint Hook
```typescript
// src/hooks/useBreakpoint.ts
import { useMediaQuery } from "./useMediaQuery";

export function useBreakpoint() {
  const isSm  = useMediaQuery("(min-width: 640px)");
  const isMd  = useMediaQuery("(min-width: 768px)");
  const isLg  = useMediaQuery("(min-width: 1024px)");
  const isXl  = useMediaQuery("(min-width: 1280px)");
  const is2xl = useMediaQuery("(min-width: 1536px)");

  return {
    isMobile:  !isSm,
    isTablet:  isSm && !isLg,
    isDesktop: isLg,
    isSm,
    isMd,
    isLg,
    isXl,
    is2xl,
  };
}
```

### Usage
```tsx
function ResponsiveComponent() {
  const { isMobile, isDesktop } = useBreakpoint();

  if (isMobile) return <MobileView />;
  return <DesktopView />;
}
```

**Prefer CSS over JS for responsive behavior** — CSS is cheaper and avoids hydration issues. Use JS breakpoints only when CSS cannot solve the problem (e.g., conditionally rendering different component structures).

---

## Design Guidance

### Content Breakpoints
Design should break at the point the content demands it, not at arbitrary device sizes. Use the standard breakpoints as starting points, but create intermediate breakpoints if the design needs them:

```css
/* Custom intermediate breakpoint */
@media (min-width: 480px) {
  /* Between mobile and sm */
}
```

### Touch Targets
At mobile breakpoints, ensure:
- Minimum touch target: 44×44px
- Sufficient spacing between tappable elements (≥ 8px gap)

### Typography Scaling
| Breakpoint | Body font size | H1 size |
|------------|---------------|---------|
| Mobile | 16px | 32–36px |
| Tablet | 16–18px | 40–48px |
| Desktop | 16–18px | 48–60px |

---

## Common Patterns

### Show/Hide
```tsx
{/* Show only on mobile */}
<div className="md:hidden">Mobile content</div>

{/* Show only on desktop */}
<div className="hidden md:block">Desktop content</div>

{/* Flex on tablet+, stacked on mobile */}
<div className="flex flex-col md:flex-row">
```

### Adaptive Navigation
```tsx
{/* Desktop nav */}
<nav className="hidden lg:flex gap-4">
  {navItems.map(({ href, label }) => (
    <Link key={href} href={href}>{label}</Link>
  ))}
</nav>

{/* Mobile hamburger */}
<button className="lg:hidden" aria-label="Open menu">
  <MenuIcon />
</button>
```

### Responsive Grid
```tsx
{/* 1 → 2 → 3 → 4 columns */}
<div className="grid gap-6
  grid-cols-1
  sm:grid-cols-2
  lg:grid-cols-3
  xl:grid-cols-4">
```

### Responsive Sidebar
```tsx
{/* Sidebar collapses below content on mobile */}
<div className="flex flex-col lg:flex-row gap-8">
  <main className="flex-1 min-w-0">{children}</main>
  <aside className="w-full lg:w-72 shrink-0">{sidebar}</aside>
</div>
```
