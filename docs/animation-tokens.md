# Animation Tokens

> Duration, easing, and motion configuration tokens.

---

## Table of Contents

1. [Duration](#duration)
2. [Easing](#easing)
3. [Spring Presets](#spring-presets)
4. [Delay](#delay)
5. [Stagger](#stagger)
6. [CSS Custom Properties](#css-custom-properties)
7. [Framer Motion Config](#framer-motion-config)
8. [Usage Guide](#usage-guide)

---

## Duration

| Token | Value | Usage |
|-------|-------|-------|
| `duration-instant` | 0ms | Immediate changes (no perceived animation) |
| `duration-fast` | 100ms | Micro-interactions: button press, checkbox tick |
| `duration-200` | 200ms | UI feedback: color change, icon swap |
| `duration-300` | 300ms | Standard transitions: modal open, dropdown |
| `duration-400` | 400ms | Richer transitions: page slide, drawer |
| `duration-500` | 500ms | Page-level transitions |
| `duration-slow` | 700ms | Emphasis animations: hero entrance |
| `duration-slower` | 1000ms | Loading states, ambient animations |
| `duration-slowest` | 1500ms | Skeleton shimmer loop cycle |

### Rules
- Never animate for longer than needed to communicate the change
- Animations > 400ms feel slow unless explicitly meant to be impactful
- Loading indicators should use loops ≤ 1500ms to feel responsive

---

## Easing

### Named Easing Curves

| Token | Cubic Bezier | Usage |
|-------|-------------|-------|
| `ease-linear` | `linear` | Opacity, color — properties with no spatial direction |
| `ease-in` | `cubic-bezier(0.4, 0, 1, 1)` | Elements exiting the screen |
| `ease-out` | `cubic-bezier(0, 0, 0.2, 1)` | Elements entering the screen |
| `ease-in-out` | `cubic-bezier(0.4, 0, 0.2, 1)` | Elements moving within the screen |
| `ease-spring` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Playful/elastic feel (slight overshoot) |
| `ease-anticipate` | `cubic-bezier(0.36, 0, 0.66, -0.56)` | Anticipation (pull-back before action) |
| `ease-bounce` | `cubic-bezier(0.87, 0, 0.13, 1)` | Bouncy finish |
| `ease-sharp` | `cubic-bezier(0.4, 0, 0.6, 1)` | Fast, snappy transitions |

### Choosing the Right Easing

| Pattern | Easing |
|---------|--------|
| Element enters viewport | `ease-out` |
| Element exits viewport | `ease-in` |
| Element moves between positions | `ease-in-out` |
| Bounce/spring feedback | `ease-spring` |
| Color/opacity change (no movement) | `ease-linear` |
| Collapsing/closing | `ease-in` |
| Expanding/opening | `ease-out` |

---

## Spring Presets

Physics-based springs for Framer Motion:

```typescript
export const springs = {
  /** Snappy, responsive spring for most UI transitions */
  default: {
    type: "spring",
    stiffness: 400,
    damping: 30,
    mass: 1,
  },
  /** Slower, heavier spring for larger elements */
  slow: {
    type: "spring",
    stiffness: 200,
    damping: 25,
    mass: 1.5,
  },
  /** Bouncy spring for playful interactions */
  bouncy: {
    type: "spring",
    stiffness: 500,
    damping: 15,
    mass: 0.8,
  },
  /** Tight spring for subtle micro-interactions */
  tight: {
    type: "spring",
    stiffness: 700,
    damping: 40,
    mass: 0.8,
  },
  /** No bounce — pure spring decay */
  gentle: {
    type: "spring",
    stiffness: 120,
    damping: 30,
    mass: 1,
  },
} as const;
```

---

## Delay

| Token | Value | Usage |
|-------|-------|-------|
| `delay-none` | 0ms | No delay |
| `delay-fast` | 50ms | Very short delay for staggered children |
| `delay-100` | 100ms | Short delay |
| `delay-200` | 200ms | Standard delay |
| `delay-300` | 300ms | Wait for previous transition to complete |
| `delay-500` | 500ms | Long intentional delay |

---

## Stagger

Stagger delay between children in a list or group:

| Token | Value | Usage |
|-------|-------|-------|
| `stagger-xs` | 30ms | Dense lists (> 8 items) |
| `stagger-sm` | 50ms | Standard lists (4–8 items) |
| `stagger-md` | 80ms | Feature cards, highlights |
| `stagger-lg` | 120ms | Hero sections, key highlights |

**Max stagger delay**: Total stagger time should not exceed 400ms for any list.

```typescript
// Cap stagger at 6 children
const staggerDelay = (index: number, unit = 50) =>
  Math.min(index, 6) * unit;
```

---

## CSS Custom Properties

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
  --ease-bounce:     cubic-bezier(0.87, 0, 0.13, 1);
  --ease-sharp:      cubic-bezier(0.4, 0, 0.6, 1);

  /* Stagger */
  --stagger-xs: 30ms;
  --stagger-sm: 50ms;
  --stagger-md: 80ms;
  --stagger-lg: 120ms;
}

/* Reduced motion: collapse all durations */
@media (prefers-reduced-motion: reduce) {
  :root {
    --duration-fast:    0.01ms;
    --duration-200:     0.01ms;
    --duration-300:     0.01ms;
    --duration-400:     0.01ms;
    --duration-500:     0.01ms;
    --duration-slow:    0.01ms;
    --duration-slower:  0.01ms;
    --duration-slowest: 0.01ms;
  }
}
```

---

## Framer Motion Config

```typescript
// src/lib/motion.ts

export const transition = {
  fast: { duration: 0.1, ease: [0, 0, 0.2, 1] },
  default: { duration: 0.3, ease: [0.4, 0, 0.2, 1] },
  slow: { duration: 0.7, ease: [0.4, 0, 0.2, 1] },
  spring: springs.default,
  springBouncy: springs.bouncy,
} as const;

export const variants = {
  fadeIn: {
    hidden: { opacity: 0 },
    visible: { opacity: 1, transition: transition.default },
  },
  fadeUp: {
    hidden: { opacity: 0, y: 8 },
    visible: { opacity: 1, y: 0, transition: transition.default },
  },
  scaleIn: {
    hidden: { opacity: 0, scale: 0.95 },
    visible: { opacity: 1, scale: 1, transition: transition.spring },
  },
  slideInLeft: {
    hidden: { opacity: 0, x: -16 },
    visible: { opacity: 1, x: 0, transition: transition.default },
  },
  staggerContainer: {
    hidden: {},
    visible: { transition: { staggerChildren: 0.05 } },
  },
} as const;
```

---

## Usage Guide

### CSS Transition
```css
.button {
  transition:
    background-color var(--duration-fast) var(--ease-out),
    transform var(--duration-200) var(--ease-spring);
}
```

### Framer Motion Component
```tsx
import { motion } from "framer-motion";
import { variants } from "@/lib/motion";

export function Card({ children }: { children: React.ReactNode }) {
  return (
    <motion.div
      variants={variants.fadeUp}
      initial="hidden"
      animate="visible"
    >
      {children}
    </motion.div>
  );
}
```

### Staggered List
```tsx
<motion.ul variants={variants.staggerContainer} initial="hidden" animate="visible">
  {items.map((item) => (
    <motion.li key={item.id} variants={variants.fadeUp}>
      {item.label}
    </motion.li>
  ))}
</motion.ul>
```
