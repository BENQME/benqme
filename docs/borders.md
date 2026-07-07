# Borders

> Border tokens, radius scale, and usage conventions.

---

## Table of Contents

1. [Border Width](#border-width)
2. [Border Radius](#border-radius)
3. [Border Colors](#border-colors)
4. [Border Style](#border-style)
5. [Outline vs Border](#outline-vs-border)
6. [CSS Tokens](#css-tokens)
7. [Usage Guidelines](#usage-guidelines)

---

## Border Width

| Token | Value | Usage |
|-------|-------|-------|
| `border-0` | 0px | Remove border |
| `border` | 1px | Default, hairline borders |
| `border-2` | 2px | Emphasis borders |
| `border-4` | 4px | Dividers, strong separators |
| `border-8` | 8px | Decorative thick borders |

---

## Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `rounded-none` | 0px | Sharp corners (structural) |
| `rounded-sm` | 2px | Minimal rounding |
| `rounded` | 4px | Default (inputs, tags) |
| `rounded-md` | 6px | Small cards, buttons |
| `rounded-lg` | 8px | Cards, panels |
| `rounded-xl` | 12px | Modals, large cards |
| `rounded-2xl` | 16px | Feature cards |
| `rounded-3xl` | 24px | Hero cards |
| `rounded-full` | 9999px | Pill shapes, avatars, badges |

### Radius Conventions by Component

| Component | Radius |
|-----------|--------|
| Button | `rounded-md` (6px) |
| Input / Select | `rounded-md` (6px) |
| Badge / Tag | `rounded-full` |
| Avatar | `rounded-full` |
| Card | `rounded-xl` (12px) |
| Modal / Dialog | `rounded-xl` (12px) |
| Popover / Dropdown | `rounded-lg` (8px) |
| Tooltip | `rounded-md` (6px) |
| Image (card) | Same as card container |

---

## Border Colors

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `color-border` | `#e4e4e7` | `#374151` | Default borders |
| `color-border-strong` | `#d1d5db` | `#4b5563` | Emphasis borders |
| `color-border-subtle` | `#f4f4f5` | `#1f2937` | Hairline, subtle dividers |
| `color-border-focus` | `#3b82f6` | `#3b82f6` | Focus state |
| `color-border-error` | `#ef4444` | `#ef4444` | Error state |
| `color-border-success` | `#22c55e` | `#22c55e` | Success state |

---

## Border Style

Default: `solid`. Use `dashed` for upload zones and `dotted` sparingly.

```css
/* Default card border */
.card {
  border: 1px solid var(--color-border);
  border-radius: var(--radius-xl);
}

/* Active/selected state */
.card--selected {
  border-color: var(--color-brand);
  border-width: 2px;
}

/* Error state */
.input--error {
  border-color: var(--color-border-error);
}

/* Dashed upload zone */
.upload-zone {
  border: 2px dashed var(--color-border-strong);
  border-radius: var(--radius-xl);
}
.upload-zone:hover {
  border-color: var(--color-brand);
}

/* Divider */
.divider {
  border: none;
  border-top: 1px solid var(--color-border);
}
```

---

## Outline vs Border

| Property | When to use |
|----------|------------|
| `border` | Visual structure — always present as part of the design |
| `outline` | Focus indicator — never part of the layout flow |

```css
/* ✅ Focus with outline — doesn't affect layout */
:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
}

/* ❌ Focus with border — shifts layout by 2px */
:focus {
  border: 2px solid var(--color-focus);
}
```

---

## CSS Tokens

```css
:root {
  /* Border widths */
  --border-0:  0px;
  --border-1:  1px;
  --border-2:  2px;
  --border-4:  4px;

  /* Border radii */
  --radius-none: 0px;
  --radius-sm:   2px;
  --radius-base: 4px;
  --radius-md:   6px;
  --radius-lg:   8px;
  --radius-xl:   12px;
  --radius-2xl:  16px;
  --radius-3xl:  24px;
  --radius-full: 9999px;

  /* Border colors */
  --color-border:         #e4e4e7;
  --color-border-strong:  #d1d5db;
  --color-border-subtle:  #f4f4f5;
  --color-border-focus:   #3b82f6;
  --color-border-error:   #ef4444;
  --color-border-success: #22c55e;
}

[data-theme="dark"] {
  --color-border:        #374151;
  --color-border-strong: #4b5563;
  --color-border-subtle: #1f2937;
}
```

---

## Usage Guidelines

- **Consistency**: Use the same border radius for the same component across the entire app
- **Nesting**: Use slightly smaller radius for inner elements (e.g., inner card button uses `rounded-md`, card uses `rounded-xl`)
- **Images**: Match the border radius of the image to its container
- **Avoid double borders**: When two elements with borders touch, use a negative margin or a single shared border
- **Dark mode**: In dark mode, borders often need to be more visible — consider increasing opacity or using `border-strong`

### Inner Radius Formula
When nesting rounded elements, inner radius = outer radius − padding:
```css
.card {                       /* Outer */
  border-radius: 12px;
  padding: 8px;
}
.card .inner {                /* Inner */
  border-radius: calc(12px - 8px); /* = 4px */
}
```
