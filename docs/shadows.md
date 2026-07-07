# Shadows

> Box shadow tokens, elevation system, and usage guidelines.

---

## Table of Contents

1. [Shadow Scale](#shadow-scale)
2. [Elevation System](#elevation-system)
3. [Colored Shadows](#colored-shadows)
4. [Focus Ring](#focus-ring)
5. [Dark Mode](#dark-mode)
6. [CSS Tokens](#css-tokens)
7. [Usage Guidelines](#usage-guidelines)

---

## Shadow Scale

| Token | Value | Level |
|-------|-------|-------|
| `shadow-none` | `none` | 0 — Flat |
| `shadow-xs` | `0 1px 2px 0 rgb(0 0 0 / 0.05)` | — |
| `shadow-sm` | `0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1)` | 1 — Raised |
| `shadow-md` | `0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1)` | 2 — Floating |
| `shadow-lg` | `0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1)` | 3 — Modal |
| `shadow-xl` | `0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1)` | 4 — Dialog |
| `shadow-2xl` | `0 25px 50px -12px rgb(0 0 0 / 0.25)` | 5 — Maximum |
| `shadow-inner` | `inset 0 2px 4px 0 rgb(0 0 0 / 0.05)` | Inset/recessed |

---

## Elevation System

Elevation communicates depth and layer order. Use consistently:

| Level | Token | Components |
|-------|-------|-----------|
| 0 | `shadow-none` | Page background, flat cards |
| 1 | `shadow-sm` | Default cards, list items, borders |
| 2 | `shadow-md` | Dropdowns, floating action buttons |
| 3 | `shadow-lg` | Modals, drawers |
| 4 | `shadow-xl` | Command palettes, fullscreen overlays |
| 5 | `shadow-2xl` | Tooltips over modals, maximum emphasis |

**Rule**: An element should have a shadow proportional to its z-index level. Elements at the same level have the same shadow depth.

---

## Colored Shadows

For brand-colored emphasis on buttons and highlights:

```css
.button-primary-glow {
  box-shadow:
    0 0 0 0 rgb(37 99 235 / 0),
    0 4px 14px 0 rgb(37 99 235 / 0.4);
  transition: box-shadow var(--duration-200) var(--ease-out);
}

.button-primary-glow:hover {
  box-shadow:
    0 0 0 0 rgb(37 99 235 / 0),
    0 6px 20px 0 rgb(37 99 235 / 0.5);
}
```

### Status Shadows
```css
.success-ring { box-shadow: 0 0 0 3px rgb(34 197 94 / 0.3); }
.danger-ring  { box-shadow: 0 0 0 3px rgb(239 68 68 / 0.3); }
.warning-ring { box-shadow: 0 0 0 3px rgb(234 179 8 / 0.3); }
```

---

## Focus Ring

The focus ring is a special shadow used for keyboard focus indication:

```css
:root {
  --shadow-focus: 0 0 0 3px var(--color-focus);
  --shadow-focus-inset: inset 0 0 0 2px var(--color-focus);
}

/* Applied to focusable elements */
:focus-visible {
  outline: none;
  box-shadow: var(--shadow-focus);
}

/* For elements that already have a box-shadow */
.card:focus-visible {
  box-shadow: var(--shadow-sm), var(--shadow-focus);
}
```

---

## Dark Mode

Shadows become invisible on dark backgrounds at the same opacity. Adjust for dark mode:

```css
:root {
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
}

[data-theme="dark"] {
  /* Increase opacity in dark mode; also add subtle border */
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.3), 0 1px 2px -1px rgb(0 0 0 / 0.2);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.4), 0 2px 4px -2px rgb(0 0 0 / 0.3);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.5), 0 4px 6px -4px rgb(0 0 0 / 0.4);
}
```

In dark mode, supplement or replace shadows with borders for element definition:

```css
[data-theme="dark"] .card {
  box-shadow: var(--shadow-sm);
  border: 1px solid var(--color-border);
}
```

---

## CSS Tokens

```css
:root {
  --shadow-none:   none;
  --shadow-xs:     0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm:     0 1px 3px 0 rgb(0 0 0 / 0.1),
                   0 1px 2px -1px rgb(0 0 0 / 0.1);
  --shadow-md:     0 4px 6px -1px rgb(0 0 0 / 0.1),
                   0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg:     0 10px 15px -3px rgb(0 0 0 / 0.1),
                   0 4px 6px -4px rgb(0 0 0 / 0.1);
  --shadow-xl:     0 20px 25px -5px rgb(0 0 0 / 0.1),
                   0 8px 10px -6px rgb(0 0 0 / 0.1);
  --shadow-2xl:    0 25px 50px -12px rgb(0 0 0 / 0.25);
  --shadow-inner:  inset 0 2px 4px 0 rgb(0 0 0 / 0.05);
  --shadow-focus:  0 0 0 3px var(--color-focus);
}
```

---

## Usage Guidelines

- Use shadows to establish **depth hierarchy**, not decoration
- Every elevation level change should be **meaningful** (the element is physically above others)
- Avoid more than 2–3 different shadow levels in the same view
- On **mobile**, reduce shadow depth by one level (shadows feel heavier on small screens)
- Never apply shadows to text
- Never animate `box-shadow` directly — animate `opacity` on a pseudo-element instead (better performance):

```css
.card::after {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  box-shadow: var(--shadow-lg);
  opacity: 0;
  transition: opacity var(--duration-200) var(--ease-out);
}

.card:hover::after {
  opacity: 1;
}
```
