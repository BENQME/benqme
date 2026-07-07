# Grid System

> Layout grid, columns, gutters, and responsive patterns.

---

## Table of Contents

1. [Grid Basics](#grid-basics)
2. [Column System](#column-system)
3. [Gutters](#gutters)
4. [Breakpoints](#breakpoints)
5. [Common Layouts](#common-layouts)
6. [Auto Grid Patterns](#auto-grid-patterns)
7. [CSS Grid Reference](#css-grid-reference)
8. [Tailwind Patterns](#tailwind-patterns)

---

## Grid Basics

The layout system is built on CSS Grid with a 12-column base. Gutters scale with breakpoints.

```
Mobile:   1–2 columns | 16px gutters | 16px side padding
Tablet:   4–6 columns | 24px gutters | 24px side padding
Desktop: 8–12 columns | 32px gutters | 32px side padding
```

Use the `Container` component for consistent max-width and horizontal padding:

```tsx
import { Container } from "@/components/layout/Container";

<Container>
  <div className="grid grid-cols-12 gap-6">
    {/* grid items */}
  </div>
</Container>
```

---

## Column System

### 12-Column Grid

```
|—col—|—col—|—col—|—col—|—col—|—col—|—col—|—col—|—col—|—col—|—col—|—col—|
  1     2     3     4     5     6     7     8     9    10    11    12
```

Common spans:
| Span | Tailwind | Use case |
|------|---------|---------|
| Full width | `col-span-12` | Full-width elements |
| Two-thirds | `col-span-8` | Main content |
| Half | `col-span-6` | Two-column layout |
| One-third | `col-span-4` | Sidebar, tertiary |
| One-quarter | `col-span-3` | 4-column grid |

### Responsive Column Spans

```tsx
<div className="grid grid-cols-12 gap-6">
  {/* Sidebar: full width mobile, 1/3 desktop */}
  <aside className="col-span-12 md:col-span-4 lg:col-span-3">
    Sidebar
  </aside>

  {/* Main: full width mobile, 2/3 desktop */}
  <main className="col-span-12 md:col-span-8 lg:col-span-9">
    Content
  </main>
</div>
```

---

## Gutters

| Breakpoint | Gutter size | Tailwind |
|------------|------------|---------|
| Mobile (default) | 16px | `gap-4` |
| Tablet (≥ 640px) | 24px | `sm:gap-6` |
| Desktop (≥ 1024px) | 32px | `lg:gap-8` |

```tsx
<div className="grid grid-cols-12 gap-4 sm:gap-6 lg:gap-8">
```

---

## Breakpoints

| Name | Min-width | Tailwind prefix |
|------|-----------|----------------|
| Mobile | 0px | (default, no prefix) |
| `sm` | 640px | `sm:` |
| `md` | 768px | `md:` |
| `lg` | 1024px | `lg:` |
| `xl` | 1280px | `xl:` |
| `2xl` | 1536px | `2xl:` |

---

## Common Layouts

### Two-Column (Content + Sidebar)
```tsx
<div className="grid grid-cols-1 lg:grid-cols-[1fr_280px] gap-8">
  <main>Content</main>
  <aside>Sidebar</aside>
</div>
```

### Three-Column
```tsx
<div className="grid grid-cols-1 md:grid-cols-3 gap-6">
  <div>Column 1</div>
  <div>Column 2</div>
  <div>Column 3</div>
</div>
```

### Holy Grail (Header / Sidebar / Main / Aside / Footer)
```tsx
<div className="grid grid-rows-[auto_1fr_auto] min-h-screen">
  <header>Header</header>
  <div className="grid grid-cols-[200px_1fr_200px]">
    <nav>Left nav</nav>
    <main>Content</main>
    <aside>Right aside</aside>
  </div>
  <footer>Footer</footer>
</div>
```

### Feature Highlight (Big + Small)
```tsx
<div className="grid grid-cols-12 gap-6">
  <div className="col-span-12 md:col-span-7">Featured item</div>
  <div className="col-span-12 md:col-span-5 grid gap-6">
    <div>Small item 1</div>
    <div>Small item 2</div>
  </div>
</div>
```

---

## Auto Grid Patterns

### Responsive Auto-Fill
Automatically fills as many columns as fit, with a minimum column width:

```tsx
{/* Min 280px per column — fills automatically */}
<div className="grid grid-cols-[repeat(auto-fill,minmax(280px,1fr))] gap-6">
  {items.map((item) => <Card key={item.id} />)}
</div>
```

### Masonry Layout
```css
/* CSS-only masonry (no JS needed in modern browsers) */
.masonry {
  columns: 3 280px;
  gap: 1.5rem;
}

.masonry > * {
  break-inside: avoid;
  margin-bottom: 1.5rem;
}
```

### Equal-Height Cards
```tsx
{/* Flexbox row that stretches cards to equal height */}
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
  {cards.map((card) => (
    <Card key={card.id} className="flex flex-col">
      <Card.Content className="flex-1">...</Card.Content>
      <Card.Footer>...</Card.Footer>
    </Card>
  ))}
</div>
```

---

## CSS Grid Reference

```css
/* 12-column grid */
.grid-12 {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  gap: var(--space-6);
}

/* Fluid auto-fill grid */
.grid-auto {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(280px, 100%), 1fr));
  gap: var(--space-6);
}

/* Fixed sidebar layout */
.layout-sidebar {
  display: grid;
  grid-template-columns: 240px 1fr;
  gap: var(--space-8);
}

@media (max-width: 1023px) {
  .layout-sidebar {
    grid-template-columns: 1fr;
  }
}
```

---

## Tailwind Patterns

```tsx
// Responsive 3-col to 1-col
className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6"

// Centered single column
className="mx-auto max-w-2xl"

// Full bleed on mobile, constrained on desktop
className="container mx-auto px-4 sm:px-6 lg:px-8"

// Stacked on mobile, side-by-side on desktop
className="flex flex-col md:flex-row gap-8"

// Sidebar that collapses below content on mobile
className="grid grid-cols-1 lg:grid-cols-[240px_1fr] gap-8"
```
