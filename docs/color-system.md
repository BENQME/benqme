# Color System

> Palette, semantic colors, and theming conventions.

---

## Table of Contents

1. [Color Philosophy](#color-philosophy)
2. [Palette](#palette)
3. [Semantic Colors](#semantic-colors)
4. [Surface & Background](#surface--background)
5. [Text Colors](#text-colors)
6. [Border Colors](#border-colors)
7. [Status Colors](#status-colors)
8. [Dark Mode](#dark-mode)
9. [Usage Guidelines](#usage-guidelines)
10. [CSS Tokens](#css-tokens)

---

## Color Philosophy

Colors in this system are defined in two layers:

1. **Primitive palette** — raw color values with numeric scales (50–950)
2. **Semantic tokens** — purpose-named aliases that reference primitives

Always use semantic tokens in components. Never reference primitive values directly.

```css
/* ✅ Correct */
background: var(--color-bg-primary);

/* ❌ Wrong */
background: #0a0a0a;
background: var(--gray-950);
```

---

## Palette

### Gray (Neutral)
| Token | Light | Dark |
|-------|-------|------|
| `gray-50` | `#fafafa` | — |
| `gray-100` | `#f4f4f5` | — |
| `gray-200` | `#e4e4e7` | — |
| `gray-300` | `#d1d5db` | — |
| `gray-400` | `#9ca3af` | — |
| `gray-500` | `#6b7280` | — |
| `gray-600` | `#4b5563` | — |
| `gray-700` | `#374151` | — |
| `gray-800` | `#1f2937` | — |
| `gray-900` | `#111827` | — |
| `gray-950` | `#0a0a0a` | — |

### Blue (Primary)
| Token | Value |
|-------|-------|
| `blue-50` | `#eff6ff` |
| `blue-100` | `#dbeafe` |
| `blue-200` | `#bfdbfe` |
| `blue-300` | `#93c5fd` |
| `blue-400` | `#60a5fa` |
| `blue-500` | `#3b82f6` |
| `blue-600` | `#2563eb` |
| `blue-700` | `#1d4ed8` |
| `blue-800` | `#1e40af` |
| `blue-900` | `#1e3a8a` |

### Green (Success)
| Token | Value |
|-------|-------|
| `green-50` | `#f0fdf4` |
| `green-500` | `#22c55e` |
| `green-700` | `#15803d` |

### Yellow (Warning)
| Token | Value |
|-------|-------|
| `yellow-50` | `#fefce8` |
| `yellow-500` | `#eab308` |
| `yellow-700` | `#a16207` |

### Red (Danger)
| Token | Value |
|-------|-------|
| `red-50` | `#fef2f2` |
| `red-500` | `#ef4444` |
| `red-700` | `#b91c1c` |

### Purple (Accent)
| Token | Value |
|-------|-------|
| `purple-50` | `#faf5ff` |
| `purple-500` | `#a855f7` |
| `purple-700` | `#7e22ce` |

---

## Semantic Colors

### Brand
| Token | Light | Dark |
|-------|-------|------|
| `color-brand` | `blue-600` | `blue-400` |
| `color-brand-hover` | `blue-700` | `blue-300` |
| `color-brand-subtle` | `blue-50` | `blue-900` |
| `color-brand-foreground` | `white` | `gray-950` |

### Interactive
| Token | Light | Dark |
|-------|-------|------|
| `color-interactive` | `blue-600` | `blue-400` |
| `color-interactive-hover` | `blue-700` | `blue-300` |
| `color-focus` | `blue-500` | `blue-400` |

---

## Surface & Background

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `color-bg-page` | `gray-50` | `gray-950` | Page background |
| `color-bg-primary` | `white` | `gray-900` | Cards, panels |
| `color-bg-secondary` | `gray-50` | `gray-800` | Sections |
| `color-bg-tertiary` | `gray-100` | `gray-700` | Subtle fills |
| `color-surface-1` | `white` | `gray-900` | Elevation 1 |
| `color-surface-2` | `gray-50` | `gray-800` | Elevation 2 |
| `color-surface-3` | `gray-100` | `gray-700` | Elevation 3 |

---

## Text Colors

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `color-text-primary` | `gray-900` | `gray-50` | Headings, primary body |
| `color-text-secondary` | `gray-600` | `gray-400` | Descriptions, metadata |
| `color-text-tertiary` | `gray-400` | `gray-600` | Placeholders, disabled |
| `color-text-inverse` | `white` | `gray-900` | On dark backgrounds |
| `color-text-link` | `blue-600` | `blue-400` | Links |
| `color-text-link-hover` | `blue-700` | `blue-300` | Link hover state |
| `color-text-heading` | `gray-950` | `gray-50` | Display headings |

---

## Border Colors

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `color-border` | `gray-200` | `gray-700` | Default borders |
| `color-border-strong` | `gray-300` | `gray-600` | Emphasis borders |
| `color-border-subtle` | `gray-100` | `gray-800` | Hairline borders |

---

## Status Colors

| Status | Background | Text | Border |
|--------|-----------|------|--------|
| Success | `green-50` / `green-950` | `green-700` / `green-300` | `green-200` / `green-800` |
| Warning | `yellow-50` / `yellow-950` | `yellow-700` / `yellow-300` | `yellow-200` / `yellow-800` |
| Danger/Error | `red-50` / `red-950` | `red-700` / `red-300` | `red-200` / `red-800` |
| Info | `blue-50` / `blue-950` | `blue-700` / `blue-300` | `blue-200` / `blue-800` |
| Neutral | `gray-50` / `gray-900` | `gray-700` / `gray-300` | `gray-200` / `gray-700` |

---

## Dark Mode

```css
:root {
  --color-bg-page: #fafafa;
  --color-bg-primary: #ffffff;
  --color-text-primary: #111827;
  --color-text-secondary: #4b5563;
  --color-border: #e4e4e7;
  --color-brand: #2563eb;
}

[data-theme="dark"] {
  --color-bg-page: #0a0a0a;
  --color-bg-primary: #111827;
  --color-text-primary: #fafafa;
  --color-text-secondary: #9ca3af;
  --color-border: #374151;
  --color-brand: #60a5fa;
}
```

---

## Usage Guidelines

- **Never use raw color values** — always reference semantic tokens
- **Don't rely on color alone** to convey meaning (use icons, labels, patterns too)
- **Check contrast ratios**: minimum 4.5:1 for text, 3:1 for large text and UI components
- Brand colors should appear sparingly — on primary actions and key highlights only
- Avoid using more than 3–4 distinct hues in a single view

### Contrast Requirements

| Text size | Minimum contrast |
|-----------|-----------------|
| < 18px normal / < 14px bold | 4.5:1 |
| ≥ 18px normal / ≥ 14px bold | 3:1 |
| Decorative / disabled | None required |

---

## CSS Tokens

Full token definitions in [`css-tokens.md`](./css-tokens.md).

```css
:root {
  /* Backgrounds */
  --color-bg-page:      #fafafa;
  --color-bg-primary:   #ffffff;
  --color-bg-secondary: #f4f4f5;

  /* Text */
  --color-text-primary:   #111827;
  --color-text-secondary: #4b5563;
  --color-text-tertiary:  #9ca3af;

  /* Brand */
  --color-brand:            #2563eb;
  --color-brand-hover:      #1d4ed8;
  --color-brand-subtle:     #eff6ff;
  --color-brand-foreground: #ffffff;

  /* Border */
  --color-border:        #e4e4e7;
  --color-border-strong: #d1d5db;

  /* Status */
  --color-success: #22c55e;
  --color-warning: #eab308;
  --color-danger:  #ef4444;
  --color-info:    #3b82f6;
}
```
