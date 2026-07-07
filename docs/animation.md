# Animation

> Implementation guide for CSS and JavaScript animations used in this project.

---

## Table of Contents

1. [Overview](#overview)
2. [CSS Animations](#css-animations)
3. [CSS Transitions](#css-transitions)
4. [JavaScript Animations](#javascript-animations)
5. [Framer Motion Patterns](#framer-motion-patterns)
6. [Keyframe Library](#keyframe-library)
7. [Compound Animations](#compound-animations)
8. [Debug & Testing](#debug--testing)

---

## Overview

Animations are implemented using three layers:
1. **CSS Transitions** — simple state changes (hover, focus, active)
2. **CSS Keyframe Animations** — looping or one-shot sequences (shimmer, spin, pulse)
3. **JavaScript Animations** — complex, physics-based, or scroll-driven motion (Framer Motion)

Always prefer CSS over JavaScript for performance. Use JS only when CSS cannot express the required behavior.

---

## CSS Animations

### Naming Convention
```css
@keyframes [category]-[name] {
  /* e.g. enter-fade-up, loop-spin, exit-scale-down */
}
```

### Standard Keyframes

#### Fade In
```css
@keyframes enter-fade {
  from { opacity: 0; }
  to   { opacity: 1; }
}
```

#### Fade Up
```css
@keyframes enter-fade-up {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

#### Fade Down
```css
@keyframes enter-fade-down {
  from {
    opacity: 0;
    transform: translateY(-8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

#### Scale In
```css
@keyframes enter-scale {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
```

#### Spin
```css
@keyframes loop-spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}
```

#### Pulse
```css
@keyframes loop-pulse {
  0%, 100% { opacity: 1; }
  50%       { opacity: 0.5; }
}
```

#### Bounce
```css
@keyframes loop-bounce {
  0%, 100% { transform: translateY(0); animation-timing-function: var(--ease-out); }
  50%       { transform: translateY(-8px); animation-timing-function: var(--ease-in); }
}
```

#### Shake (Error)
```css
@keyframes feedback-shake {
  0%, 100% { transform: translateX(0); }
  20%       { transform: translateX(-6px); }
  40%       { transform: translateX(6px); }
  60%       { transform: translateX(-4px); }
  80%       { transform: translateX(4px); }
}
```

---

## CSS Transitions

### Base Rule
```css
/* Applied globally to interactive elements */
.interactive {
  transition:
    color var(--duration-fast) var(--ease-out),
    background-color var(--duration-fast) var(--ease-out),
    border-color var(--duration-fast) var(--ease-out),
    box-shadow var(--duration-200) var(--ease-out),
    opacity var(--duration-200) var(--ease-out),
    transform var(--duration-200) var(--ease-out);
}
```

### Hover Lift
```css
.card {
  transition: transform var(--duration-200) var(--ease-out),
              box-shadow var(--duration-200) var(--ease-out);
}
.card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}
```

### Focus Ring
```css
:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
  transition: outline-offset var(--duration-fast) var(--ease-out);
}
```

---

## JavaScript Animations

Use JavaScript animations when:
- Physics (spring, inertia) are required
- Gesture-driven (drag, swipe)
- Scroll-linked progress
- Dynamic values computed at runtime

### requestAnimationFrame Loop
```typescript
function animate(
  from: number,
  to: number,
  duration: number,
  onUpdate: (value: number) => void,
  onComplete?: () => void,
) {
  const start = performance.now();

  function frame(now: number) {
    const elapsed = now - start;
    const progress = Math.min(elapsed / duration, 1);
    const eased = easeOutCubic(progress);
    onUpdate(from + (to - from) * eased);

    if (progress < 1) {
      requestAnimationFrame(frame);
    } else {
      onComplete?.();
    }
  }

  requestAnimationFrame(frame);
}

function easeOutCubic(t: number): number {
  return 1 - Math.pow(1 - t, 3);
}
```

---

## Framer Motion Patterns

### Fade Up (Component Entry)
```tsx
import { motion } from "framer-motion";

const fadeUp = {
  hidden: { opacity: 0, y: 8 },
  visible: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.3, ease: [0, 0, 0.2, 1] },
  },
};

export function FadeUp({ children }: { children: React.ReactNode }) {
  return (
    <motion.div variants={fadeUp} initial="hidden" animate="visible">
      {children}
    </motion.div>
  );
}
```

### Staggered List
```tsx
const container = {
  hidden: {},
  visible: {
    transition: { staggerChildren: 0.05 },
  },
};

const item = {
  hidden: { opacity: 0, y: 8 },
  visible: { opacity: 1, y: 0 },
};

export function StaggeredList({ items }: { items: React.ReactNode[] }) {
  return (
    <motion.ul variants={container} initial="hidden" animate="visible">
      {items.map((child, i) => (
        <motion.li key={i} variants={item}>
          {child}
        </motion.li>
      ))}
    </motion.ul>
  );
}
```

### Page Transition
```tsx
const pageVariants = {
  initial: { opacity: 0, x: -8 },
  in:      { opacity: 1, x: 0 },
  out:     { opacity: 0, x: 8 },
};

const pageTransition = {
  type: "tween",
  ease: [0.4, 0, 0.2, 1],
  duration: 0.3,
};
```

---

## Keyframe Library

Import pre-built keyframes from the animation utilities:

```typescript
import {
  enterFade,
  enterFadeUp,
  enterScale,
  exitFade,
  exitFadeDown,
  loopSpin,
  loopPulse,
  feedbackShake,
} from "@/lib/animations";
```

---

## Compound Animations

When orchestrating multiple elements:

```tsx
const sequence = async () => {
  await controls.start("step1");
  await controls.start("step2");
  controls.start("step3"); // fire and forget
};
```

---

## Debug & Testing

```bash
# Slow down all animations for debugging (browser DevTools)
# Application tab > Animations panel > throttle to 0.25x

# Test with prefers-reduced-motion
# Emulate via DevTools > Rendering > Emulate CSS media
```

- Snapshot test static states, not animated ones
- Use `jest-framer-motion` or mock Framer Motion in unit tests
- Visual regression tests (Chromatic) capture both resting and animated frames
