# CSS Tokens

> Complete reference of all CSS custom properties (design tokens) used in the project.

---

## Table of Contents

1. [Color Tokens](#color-tokens)
2. [Typography Tokens](#typography-tokens)
3. [Spacing Tokens](#spacing-tokens)
4. [Border Radius Tokens](#border-radius-tokens)
5. [Shadow Tokens](#shadow-tokens)
6. [Z-Index Tokens](#z-index-tokens)
7. [Breakpoint Tokens](#breakpoint-tokens)
8. [Animation Tokens](#animation-tokens)
9. [Full Token Sheet](#full-token-sheet)

---

## Color Tokens

See [`color-system.md`](./color-system.md) for the full color system documentation.

```css
:root {
  /* === Backgrounds === */
  --color-bg-page:      #fafafa;
  --color-bg-primary:   #ffffff;
  --color-bg-secondary: #f4f4f5;
  --color-bg-tertiary:  #e4e4e7;

  /* === Surfaces (elevation) === */
  --color-surface-1: #ffffff;
  --color-surface-2: #f4f4f5;
  --color-surface-3: #e4e4e7;

  /* === Text === */
  --color-text-primary:   #111827;
  --color-text-secondary: #4b5563;
  --color-text-tertiary:  #9ca3af;
  --color-text-disabled:  #d1d5db;
  --color-text-inverse:   #ffffff;
  --color-text-heading:   #030712;
  --color-text-link:      #2563eb;
  --color-text-link-hover:#1d4ed8;

  /* === Brand / Interactive === */
  --color-brand:             #2563eb;
  --color-brand-hover:       #1d4ed8;
  --color-brand-active:      #1e40af;
  --color-brand-subtle:      #eff6ff;
  --color-brand-foreground:  #ffffff;
  --color-interactive:       #2563eb;
  --color-interactive-hover: #1d4ed8;
  --color-focus:             #3b82f6;

  /* === Border === */
  --color-border:         #e4e4e7;
  --color-border-strong:  #d1d5db;
  --color-border-subtle:  #f4f4f5;
  --color-border-focus:   #3b82f6;

  /* === Status === */
  --color-success:            #22c55e;
  --color-success-bg:         #f0fdf4;
  --color-success-text:       #15803d;
  --color-success-border:     #bbf7d0;

  --color-warning:            #eab308;
  --color-warning-bg:         #fefce8;
  --color-warning-text:       #a16207;
  --color-warning-border:     #fef08a;

  --color-danger:             #ef4444;
  --color-danger-bg:          #fef2f2;
  --color-danger-text:        #b91c1c;
  --color-danger-border:      #fecaca;

  --color-info:               #3b82f6;
  --color-info-bg:            #eff6ff;
  --color-info-text:          #1d4ed8;
  --color-info-border:        #bfdbfe;
}

[data-theme="dark"] {
  --color-bg-page:      #0a0a0a;
  --color-bg-primary:   #111827;
  --color-bg-secondary: #1f2937;
  --color-bg-tertiary:  #374151;

  --color-surface-1: #111827;
  --color-surface-2: #1f2937;
  --color-surface-3: #374151;

  --color-text-primary:   #f9fafb;
  --color-text-secondary: #9ca3af;
  --color-text-tertiary:  #6b7280;
  --color-text-disabled:  #4b5563;
  --color-text-inverse:   #111827;
  --color-text-heading:   #ffffff;
  --color-text-link:      #60a5fa;
  --color-text-link-hover:#93c5fd;

  --color-brand:             #60a5fa;
  --color-brand-hover:       #93c5fd;
  --color-brand-active:      #bfdbfe;
  --color-brand-subtle:      #1e3a8a;
  --color-brand-foreground:  #0a0a0a;

  --color-border:         #374151;
  --color-border-strong:  #4b5563;
  --color-border-subtle:  #1f2937;

  --color-success-bg:     #052e16;
  --color-success-text:   #4ade80;
  --color-success-border: #166534;

  --color-warning-bg:     #1c1400;
  --color-warning-text:   #fde047;
  --color-warning-border: #a16207;

  --color-danger-bg:      #1f0000;
  --color-danger-text:    #f87171;
  --color-danger-border:  #b91c1c;

  --color-info-bg:        #0c1a3c;
  --color-info-text:      #93c5fd;
  --color-info-border:    #1d4ed8;
}
```

---

## Typography Tokens

See [`typography.md`](./typography.md) for full documentation.

```css
:root {
  /* Families */
  --font-sans:  "Inter", system-ui, -apple-system, sans-serif;
  --font-serif: "Playfair Display", Georgia, serif;
  --font-mono:  "JetBrains Mono", "Courier New", monospace;

  /* Scale */
  --text-xs:   0.75rem;    /* 12px */
  --text-sm:   0.875rem;   /* 14px */
  --text-base: 1rem;       /* 16px */
  --text-lg:   1.125rem;   /* 18px */
  --text-xl:   1.25rem;    /* 20px */
  --text-2xl:  1.5rem;     /* 24px */
  --text-3xl:  1.875rem;   /* 30px */
  --text-4xl:  2.25rem;    /* 36px */
  --text-5xl:  3rem;       /* 48px */
  --text-6xl:  3.75rem;    /* 60px */
  --text-7xl:  4.5rem;     /* 72px */
  --text-8xl:  6rem;       /* 96px */

  /* Fluid headings */
  --text-h1: clamp(2.25rem, 5vw + 0.5rem, 3.75rem);
  --text-h2: clamp(1.5rem,  3vw + 0.5rem, 2.25rem);
  --text-h3: clamp(1.25rem, 2vw + 0.25rem, 1.5rem);

  /* Weights */
  --font-thin:       100;
  --font-light:      300;
  --font-normal:     400;
  --font-medium:     500;
  --font-semibold:   600;
  --font-bold:       700;
  --font-extrabold:  800;
  --font-black:      900;

  /* Line heights */
  --leading-none:    1;
  --leading-tight:   1.25;
  --leading-snug:    1.375;
  --leading-normal:  1.5;
  --leading-relaxed: 1.625;
  --leading-loose:   2;

  /* Letter spacing */
  --tracking-tighter: -0.05em;
  --tracking-tight:   -0.025em;
  --tracking-normal:   0em;
  --tracking-wide:     0.025em;
  --tracking-wider:    0.05em;
  --tracking-widest:   0.1em;
}
```

---

## Spacing Tokens

Based on a 4px base unit:

```css
:root {
  --space-px:  1px;
  --space-0:   0px;
  --space-0-5: 2px;
  --space-1:   4px;
  --space-1-5: 6px;
  --space-2:   8px;
  --space-2-5: 10px;
  --space-3:   12px;
  --space-3-5: 14px;
  --space-4:   16px;
  --space-5:   20px;
  --space-6:   24px;
  --space-7:   28px;
  --space-8:   32px;
  --space-9:   36px;
  --space-10:  40px;
  --space-11:  44px;
  --space-12:  48px;
  --space-14:  56px;
  --space-16:  64px;
  --space-20:  80px;
  --space-24:  96px;
  --space-28:  112px;
  --space-32:  128px;
  --space-36:  144px;
  --space-40:  160px;
  --space-48:  192px;
  --space-56:  224px;
  --space-64:  256px;
  --space-72:  288px;
  --space-80:  320px;
  --space-96:  384px;
}
```

---

## Border Radius Tokens

```css
:root {
  --radius-none:  0px;
  --radius-sm:    2px;
  --radius-base:  4px;
  --radius-md:    6px;
  --radius-lg:    8px;
  --radius-xl:    12px;
  --radius-2xl:   16px;
  --radius-3xl:   24px;
  --radius-full:  9999px;
}
```

---

## Shadow Tokens

```css
:root {
  --shadow-none: none;
  --shadow-xs:   0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm:   0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  --shadow-md:   0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg:   0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --shadow-xl:   0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);
  --shadow-2xl:  0 25px 50px -12px rgb(0 0 0 / 0.25);
  --shadow-inner: inset 0 2px 4px 0 rgb(0 0 0 / 0.05);
  --shadow-focus: 0 0 0 3px var(--color-focus);
}
```

---

## Z-Index Tokens

```css
:root {
  --z-hide:     -1;
  --z-base:      0;
  --z-raised:    1;
  --z-dropdown:  1000;
  --z-sticky:    1100;
  --z-overlay:   1200;
  --z-modal:     1300;
  --z-popover:   1400;
  --z-tooltip:   1500;
  --z-toast:     1600;
  --z-max:       9999;
}
```

---

## Breakpoint Tokens

```css
/* Note: CSS custom properties cannot be used inside @media queries.
   These are for reference — use the values directly in media queries,
   or use Tailwind breakpoint utilities. */

:root {
  --screen-sm:  640px;
  --screen-md:  768px;
  --screen-lg:  1024px;
  --screen-xl:  1280px;
  --screen-2xl: 1536px;
}
```

---

## Animation Tokens

See [`animation-tokens.md`](./animation-tokens.md) for full documentation.

```css
:root {
  /* Duration */
  --duration-instant:  0ms;
  --duration-fast:     100ms;
  --duration-200:      200ms;
  --duration-300:      300ms;
  --duration-400:      400ms;
  --duration-500:      500ms;
  --duration-slow:     700ms;
  --duration-slower:   1000ms;
  --duration-slowest:  1500ms;

  /* Easing */
  --ease-linear:     linear;
  --ease-in:         cubic-bezier(0.4, 0, 1, 1);
  --ease-out:        cubic-bezier(0, 0, 0.2, 1);
  --ease-in-out:     cubic-bezier(0.4, 0, 0.2, 1);
  --ease-spring:     cubic-bezier(0.34, 1.56, 0.64, 1);
  --ease-anticipate: cubic-bezier(0.36, 0, 0.66, -0.56);
}
```

---

## Full Token Sheet

To import all tokens in your CSS entry point:

```css
/* src/styles/tokens.css */
@import "./tokens/colors.css";
@import "./tokens/typography.css";
@import "./tokens/spacing.css";
@import "./tokens/radii.css";
@import "./tokens/shadows.css";
@import "./tokens/z-index.css";
@import "./tokens/animation.css";
```
