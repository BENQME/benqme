# Z-Index

> Z-index scale, stacking context management, and layering conventions.

---

## Table of Contents

1. [Z-Index Scale](#z-index-scale)
2. [Stacking Context Rules](#stacking-context-rules)
3. [Component Layer Assignments](#component-layer-assignments)
4. [CSS Tokens](#css-tokens)
5. [Common Issues & Solutions](#common-issues--solutions)

---

## Z-Index Scale

| Token | Value | Layer | Examples |
|-------|-------|-------|---------|
| `z-hide` | -1 | Behind everything | Decorative backgrounds |
| `z-base` | 0 | Default flow | Page content |
| `z-raised` | 1 | Slightly elevated | Hover states, active cards |
| `z-dropdown` | 1000 | Above content | Dropdown menus, select options |
| `z-sticky` | 1100 | Fixed/sticky UI | Sticky headers, sticky sidebar |
| `z-overlay` | 1200 | Background overlay | Modal backdrop |
| `z-modal` | 1300 | Modal window | Dialogs, drawers |
| `z-popover` | 1400 | Above modals | Popovers within modals |
| `z-tooltip` | 1500 | Above popovers | Tooltips |
| `z-toast` | 1600 | Always on top | Toast notifications |
| `z-max` | 9999 | Emergency override | Debug panels, critical UI |

---

## Stacking Context Rules

### What Creates a New Stacking Context

A new stacking context is created by:
- `position` (non-static) + `z-index` (non-auto)
- `opacity < 1`
- `transform` (any value other than `none`)
- `filter` (any value other than `none`)
- `isolation: isolate`
- `will-change` with relevant properties
- `contain: layout` or `contain: paint`

**This means z-index values only compete within the same stacking context.**

### Debugging Stacking Issues

If a z-index value isn't working:
1. Check if the element's ancestor has `transform`, `filter`, or `opacity` that creates a new stacking context
2. Check if the element itself has `position` set (z-index only works on positioned elements)
3. Use browser DevTools layers panel to inspect stacking contexts

```css
/* Quick isolation — create a stacking context without visible effect */
.contains-z-index {
  isolation: isolate;
}
```

---

## Component Layer Assignments

### Fixed/Sticky Header
```css
.header {
  position: sticky;
  top: 0;
  z-index: var(--z-sticky); /* 1100 */
}
```

### Dropdown Menu
```css
.dropdown-content {
  position: absolute;
  z-index: var(--z-dropdown); /* 1000 */
}
```

### Modal Overlay + Dialog
```css
.modal-overlay {
  position: fixed;
  inset: 0;
  z-index: var(--z-overlay); /* 1200 */
  background: rgb(0 0 0 / 0.5);
}

.modal-dialog {
  position: fixed;
  z-index: var(--z-modal); /* 1300 */
}
```

### Tooltip
```css
.tooltip {
  position: absolute;
  z-index: var(--z-tooltip); /* 1500 */
}
```

### Toast Notifications
```css
.toast-container {
  position: fixed;
  bottom: 1rem;
  right: 1rem;
  z-index: var(--z-toast); /* 1600 */
}
```

---

## CSS Tokens

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

## Common Issues & Solutions

### Issue: Dropdown clips behind a card with `overflow: hidden`
```css
/* ❌ Clips child dropdowns */
.card {
  overflow: hidden;
  border-radius: 12px;
}

/* ✅ Use a pseudo-element for the border-radius clip instead */
.card {
  position: relative;
  border-radius: 12px;
}
.card::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  /* Background/border here instead */
}
```

### Issue: Modal appears behind sticky header
```
Root cause: The modal container is rendered inside a component with
`transform` or `will-change`, which created a new stacking context.

Solution: Render modal via a React Portal to <body>.
```
```tsx
import { createPortal } from "react-dom";

export function Modal({ children, isOpen }: ModalProps) {
  if (!isOpen) return null;
  return createPortal(
    <div className="modal-overlay">
      <div className="modal-dialog">{children}</div>
    </div>,
    document.body
  );
}
```

### Issue: Tooltip hidden behind sibling elements
```
Root cause: Sibling with z-index competes in the same stacking context.

Solution 1: Raise tooltip z-index above siblings.
Solution 2: Use isolation: isolate on the parent to create a stacking context.
Solution 3: Render tooltip via a Portal.
```

### Issue: `z-index: 9999` still not on top
```
Root cause: An ancestor element has transform/opacity/filter,
creating a stacking context with a lower z-index.

Solution: Find the ancestor creating the stacking context and remove
the property, or render the element outside of that ancestor via Portal.
```

### Tailwind Z-Index Utilities

```tsx
className="z-0"          // 0
className="z-10"         // 10 (Tailwind default)
className="z-20"         // 20
className="z-50"         // 50
className="z-auto"       // auto

// Custom values via token
className="z-[var(--z-modal)]"
```

Or extend Tailwind config:
```typescript
// tailwind.config.ts
theme: {
  extend: {
    zIndex: {
      "dropdown": "1000",
      "sticky":   "1100",
      "overlay":  "1200",
      "modal":    "1300",
      "popover":  "1400",
      "tooltip":  "1500",
      "toast":    "1600",
    }
  }
}
```

Then use: `className="z-modal"`.
