# Design System

> Principles, patterns, and guidelines for visual and interaction design.

---

## Table of Contents

1. [Design Principles](#design-principles)
2. [Visual Language](#visual-language)
3. [Layout System](#layout-system)
4. [Spacing Scale](#spacing-scale)
5. [Component Patterns](#component-patterns)
6. [Interaction Design](#interaction-design)
7. [Dark Mode](#dark-mode)
8. [Responsive Design](#responsive-design)
9. [Design Tokens](#design-tokens)
10. [Tools & Resources](#tools--resources)

---

## Design Principles

### 1. Clarity First
Every element serves a purpose. Remove anything that does not help the user accomplish their goal.

### 2. Consistent Hierarchy
Use scale, weight, and space to guide the eye. Never rely on color alone to convey meaning.

### 3. Accessible by Default
Designs must meet WCAG 2.1 AA without additional effort. Accessibility is a feature, not a checkbox.

### 4. Progressive Disclosure
Show only what is needed. Reveal complexity on demand.

### 5. Delight Through Motion
Animation should reinforce meaning, not distract. Every transition should feel purposeful.

---

## Visual Language

### Shape Language
- **Rounded corners** (`border-radius: 0.5rem–1rem`) for interactive elements
- **Sharp corners** for decorative/structural containers
- **Pill shapes** for tags, badges, and status indicators

### Elevation
| Level | Usage | Shadow |
|-------|-------|--------|
| 0 | Flat surfaces | none |
| 1 | Cards, panels | `shadow-sm` |
| 2 | Dropdowns, popovers | `shadow-md` |
| 3 | Modals, dialogs | `shadow-lg` |
| 4 | Toasts, notifications | `shadow-xl` |

### Density
- **Compact**: data tables, admin interfaces
- **Regular**: standard application views
- **Comfortable**: marketing, landing pages

---

## Layout System

The layout is built on a 12-column grid with responsive breakpoints.

```
Mobile  (< 640px):   1–2 columns, 16px gutters
Tablet  (640–1024px): 4–6 columns, 24px gutters
Desktop (> 1024px):  8–12 columns, 32px gutters
```

### Container Widths
```css
--container-sm:  640px;
--container-md:  768px;
--container-lg:  1024px;
--container-xl:  1280px;
--container-2xl: 1536px;
```

---

## Spacing Scale

Based on a 4px base unit:

| Token | Value | Usage |
|-------|-------|-------|
| `space-1` | 4px | Tight spacing |
| `space-2` | 8px | Component padding |
| `space-3` | 12px | — |
| `space-4` | 16px | Default element gap |
| `space-5` | 20px | — |
| `space-6` | 24px | Section padding |
| `space-8` | 32px | — |
| `space-10` | 40px | Large section |
| `space-12` | 48px | — |
| `space-16` | 64px | Page sections |
| `space-20` | 80px | Hero areas |
| `space-24` | 96px | Full-page gaps |

---

## Component Patterns

### Atomic Structure
- **Atoms**: Button, Input, Icon, Badge, Avatar
- **Molecules**: Form field, Card, List item, Tooltip
- **Organisms**: Navigation, Modal, Data table, Form
- **Templates**: Page layouts, Section layouts
- **Pages**: Concrete instances with real data

### Composition Over Configuration
Prefer composable small components over monolithic components with many props.

```tsx
// ✅ Composable
<Card>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
  <Card.Footer>Actions</Card.Footer>
</Card>

// ❌ Overly configured
<Card title="Title" body="Content" footer="Actions" />
```

---

## Interaction Design

### States
Every interactive element must define:
- `default`
- `hover`
- `focus` (keyboard-visible ring)
- `active` / `pressed`
- `disabled`
- `loading`
- `error`

### Feedback Timing
| Interaction | Max Response Time |
|-------------|------------------|
| Button click | 100ms |
| Form validation | 200ms |
| Page transition | 300ms |
| Data loading indicator | 500ms (before showing) |

---

## Dark Mode

- Use CSS custom properties with `@media (prefers-color-scheme: dark)` and a `data-theme` attribute
- Never hard-code color values; always reference tokens
- Test all components in both modes before shipping

```css
:root {
  --bg-primary: #ffffff;
  --text-primary: #0a0a0a;
}

[data-theme="dark"] {
  --bg-primary: #0a0a0a;
  --text-primary: #fafafa;
}
```

---

## Responsive Design

- **Mobile-first**: Write base styles for mobile, enhance with `min-width` queries
- **Fluid typography**: Use `clamp()` for headings
- **Fluid spacing**: Scale padding/margin with viewport
- **Touch targets**: Minimum 44×44px on mobile

---

## Design Tokens

All design decisions are encoded as tokens. See:
- [`css-tokens.md`](./css-tokens.md) — full CSS custom properties reference
- [`color-system.md`](./color-system.md) — color palette and semantic colors
- [`typography.md`](./typography.md) — type scale and font settings
- [`animation-tokens.md`](./animation-tokens.md) — easing and duration values

---

## Tools & Resources

| Tool | Purpose |
|------|---------|
| Figma | UI design and prototyping |
| Storybook | Component development and docs |
| Chromatic | Visual regression testing |
| a11y plugin | Accessibility audit in Figma |
