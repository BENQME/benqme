# Spacing

> Spacing scale, padding/margin conventions, and layout gap guidelines.

---

## Table of Contents

1. [Scale](#scale)
2. [Usage Guidelines](#usage-guidelines)
3. [Component Spacing](#component-spacing)
4. [Layout Spacing](#layout-spacing)
5. [CSS Tokens](#css-tokens)
6. [Tailwind Reference](#tailwind-reference)

---

## Scale

The spacing system is based on a **4px base unit**. Every spacing value is a multiple of 4px.

| Token | Value | px | Common Use |
|-------|-------|----|-----------|
| `space-0` | 0 | 0px | Reset |
| `space-0.5` | 0.125rem | 2px | Hairline gaps |
| `space-1` | 0.25rem | 4px | Tight padding (icons) |
| `space-1.5` | 0.375rem | 6px | — |
| `space-2` | 0.5rem | 8px | Compact padding |
| `space-2.5` | 0.625rem | 10px | — |
| `space-3` | 0.75rem | 12px | — |
| `space-3.5` | 0.875rem | 14px | — |
| `space-4` | 1rem | 16px | Default element padding |
| `space-5` | 1.25rem | 20px | — |
| `space-6` | 1.5rem | 24px | Comfortable padding |
| `space-7` | 1.75rem | 28px | — |
| `space-8` | 2rem | 32px | Section padding |
| `space-10` | 2.5rem | 40px | Large section |
| `space-12` | 3rem | 48px | — |
| `space-16` | 4rem | 64px | Page section padding |
| `space-20` | 5rem | 80px | Large hero gap |
| `space-24` | 6rem | 96px | — |
| `space-32` | 8rem | 128px | Full-page sections |
| `space-48` | 12rem | 192px | Extra-large gaps |

---

## Usage Guidelines

### Principle: Use the Scale
Never use arbitrary values. If the design requires 15px, round to 16px (`space-4`). Request design adjustments for significant deviations.

### Padding vs Margin
- **Padding**: Use for internal spacing within a component
- **Margin**: Use for external spacing between components
- Prefer `gap` over margin for flex/grid children

### Directional Shorthand
When specifying different values per side, always use the most compact notation:
```css
/* ✅ Vertical / Horizontal */
padding: var(--space-4) var(--space-6);

/* ✅ Per-side when different */
padding-block: var(--space-4);
padding-inline: var(--space-6);
```

---

## Component Spacing

### Buttons
| Size | Padding | Height |
|------|---------|--------|
| `sm` | `px-3 py-1.5` | 32px |
| `md` | `px-4 py-2` | 40px |
| `lg` | `px-6 py-3` | 48px |

### Inputs
| Size | Padding | Height |
|------|---------|--------|
| `sm` | `px-3 py-1.5` | 32px |
| `md` | `px-3 py-2` | 40px |
| `lg` | `px-4 py-3` | 48px |

### Cards
| Property | Value |
|----------|-------|
| Padding | `p-4` (16px) or `p-6` (24px) |
| Gap between cards | `gap-4` or `gap-6` |
| Card header → body gap | `gap-4` |

### List Items
| Property | Value |
|----------|-------|
| Item padding | `py-3 px-4` |
| Gap between items | 0 (use dividers) or `gap-1` |

### Form Fields
| Property | Value |
|----------|-------|
| Label → input gap | `mb-1.5` (6px) |
| Field → helper text gap | `mt-1.5` (6px) |
| Field → next field gap | `gap-4` |
| Form section gap | `gap-8` |

---

## Layout Spacing

### Page Structure
| Section | Top padding | Bottom padding |
|---------|------------|----------------|
| Page header | `pt-16` | `pb-8` |
| Hero section | `py-20` to `py-32` | — |
| Content sections | `py-16` | — |
| Footer | `pt-16` | `pb-8` |

### Container Padding
| Breakpoint | Horizontal padding |
|------------|-------------------|
| Mobile | `px-4` (16px) |
| Tablet (≥ 640px) | `px-6` (24px) |
| Desktop (≥ 1024px) | `px-8` (32px) |

### Section Gap
Gap between distinct page sections:
```css
.section + .section {
  margin-top: var(--space-24); /* 96px */
}
```

### Grid Gaps
| Context | Gap |
|---------|-----|
| Tight grid (thumbnails) | `gap-2` (8px) |
| Card grid | `gap-6` (24px) |
| Feature grid | `gap-8` (32px) |
| Loose grid | `gap-10` (40px) |

---

## CSS Tokens

```css
:root {
  --space-0:    0px;
  --space-0-5:  2px;
  --space-1:    4px;
  --space-1-5:  6px;
  --space-2:    8px;
  --space-2-5:  10px;
  --space-3:    12px;
  --space-3-5:  14px;
  --space-4:    16px;
  --space-5:    20px;
  --space-6:    24px;
  --space-7:    28px;
  --space-8:    32px;
  --space-9:    36px;
  --space-10:   40px;
  --space-11:   44px;
  --space-12:   48px;
  --space-14:   56px;
  --space-16:   64px;
  --space-20:   80px;
  --space-24:   96px;
  --space-28:   112px;
  --space-32:   128px;
  --space-36:   144px;
  --space-40:   160px;
  --space-48:   192px;
  --space-56:   224px;
  --space-64:   256px;
  --space-72:   288px;
  --space-80:   320px;
  --space-96:   384px;
}
```

---

## Tailwind Reference

This project uses Tailwind CSS. The spacing tokens map directly to Tailwind's default scale (1 unit = 4px):

| Tailwind class | CSS value |
|---------------|-----------|
| `p-1` | padding: 4px |
| `p-2` | padding: 8px |
| `p-4` | padding: 16px |
| `p-6` | padding: 24px |
| `gap-4` | gap: 16px |
| `mt-8` | margin-top: 32px |
| `mb-16` | margin-bottom: 64px |

Use standard Tailwind spacing utilities rather than custom `var(--space-*)` values in component styles — they compile to the same values and benefit from Tailwind's utility class optimizations.
