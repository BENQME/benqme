# Motion Design

> Principles and guidelines for motion and transitions across the UI.

---

## Table of Contents

1. [Motion Philosophy](#motion-philosophy)
2. [Core Principles](#core-principles)
3. [Motion Roles](#motion-roles)
4. [Transition Patterns](#transition-patterns)
5. [Choreography](#choreography)
6. [Reduced Motion](#reduced-motion)
7. [Performance](#performance)
8. [Token Reference](#token-reference)

---

## Motion Philosophy

Motion in this design system is **functional first**. Every animated transition must:
- Communicate **what changed** and **why**
- Guide the user's attention to what matters
- Respect user preferences (reduced motion)
- Never delay or obstruct the user's workflow

Motion is not decoration. If removing an animation doesn't confuse the user, reconsider it.

---

## Core Principles

### 1. Purposeful
Every transition reinforces meaning. An element expanding communicates "opening"; an element fading out communicates "leaving."

### 2. Natural
Motion should feel physical. Elements accelerate and decelerate in ways that mirror real-world physics.

### 3. Responsive
Motion must not add perceived latency. Transitions should begin immediately and complete before the user needs to interact again.

### 4. Consistent
Use the same easing and duration for similar patterns across the entire product.

### 5. Restrained
Less is more. A single well-designed transition is more effective than many competing animations.

---

## Motion Roles

### Feedback
Confirms an action was received (button press ripple, checkbox tick, form submit spinner).

### Navigation
Communicates spatial movement between views (slide, push, fade).

### Hierarchy
Establishes depth and layering (modal overlay fade, drawer slide).

### State Change
Shows an element transitioning between states (toggle switch, progress bar, skeleton to content).

### Attention
Draws focus to important information (toast entry, error shake, badge pulse).

### Ambient
Subtle, looping motion that adds life without demanding attention (loading skeleton shimmer, idle indicator).

---

## Transition Patterns

### Fade
Use for elements entering/exiting without spatial context.

```css
.fade-enter {
  opacity: 0;
  transition: opacity var(--duration-200) var(--ease-out);
}
.fade-enter-active {
  opacity: 1;
}
```

### Slide
Use for navigation or drawer-style transitions with a clear direction.

```css
.slide-up-enter {
  transform: translateY(8px);
  opacity: 0;
  transition:
    transform var(--duration-300) var(--ease-out),
    opacity var(--duration-200) var(--ease-out);
}
```

### Scale
Use for elements that originate from a focal point (dropdown, popover).

```css
.scale-enter {
  transform: scale(0.95);
  opacity: 0;
  transition:
    transform var(--duration-200) var(--ease-spring),
    opacity var(--duration-150) var(--ease-out);
}
```

### Expand / Collapse
Use for accordions and disclosure widgets.

```css
.expand {
  height: 0;
  overflow: hidden;
  transition: height var(--duration-300) var(--ease-in-out);
}
```

### Skeleton Shimmer
Use for loading placeholders.

```css
@keyframes shimmer {
  from { background-position: -200% 0; }
  to   { background-position:  200% 0; }
}

.skeleton {
  background: linear-gradient(
    90deg,
    var(--color-surface-2) 25%,
    var(--color-surface-3) 50%,
    var(--color-surface-2) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}
```

---

## Choreography

When multiple elements animate together, stagger them to create a sense of flow.

### Stagger Rules
- **Entry**: Stagger child elements by 40–60ms; parent enters first
- **Exit**: Reverse stagger; parent exits last
- **Max stagger delay**: 300ms total (no single element waits more than 300ms)

### Example: List Entry
```css
.list-item:nth-child(1) { animation-delay: 0ms; }
.list-item:nth-child(2) { animation-delay: 50ms; }
.list-item:nth-child(3) { animation-delay: 100ms; }
/* Cap at nth-child(6) = 250ms */
```

---

## Reduced Motion

Always respect the `prefers-reduced-motion` media query.

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

**Do not** simply remove the animation — replace it with an instant state change. The content change should still happen; only the motion is removed.

---

## Performance

- Animate only `transform` and `opacity` to stay on the compositor thread
- Avoid animating `width`, `height`, `top`, `left`, `margin`, `padding` (triggers layout)
- Use `will-change: transform` sparingly — only when a complex animation is imminent
- Remove `will-change` after the animation completes
- Prefer CSS transitions/animations over JavaScript-driven frame loops for simple cases
- Use `requestAnimationFrame` for JavaScript animations; avoid `setTimeout`

### GPU Layers
Promoting elements to their own GPU layer improves performance for complex animations:
```css
/* Only during animation — remove after */
.animating {
  will-change: transform, opacity;
}
```

---

## Token Reference

| Token | Value | Usage |
|-------|-------|-------|
| `--duration-instant` | 0ms | Imperceptible changes |
| `--duration-fast` | 100ms | Micro-interactions |
| `--duration-200` | 200ms | UI feedback |
| `--duration-300` | 300ms | Standard transitions |
| `--duration-500` | 500ms | Page transitions |
| `--duration-slow` | 700ms | Emphasis animations |
| `--ease-linear` | `linear` | — |
| `--ease-in` | `cubic-bezier(0.4, 0, 1, 1)` | Exit |
| `--ease-out` | `cubic-bezier(0, 0, 0.2, 1)` | Enter |
| `--ease-in-out` | `cubic-bezier(0.4, 0, 0.2, 1)` | Move |
| `--ease-spring` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Bouncy/playful |

See [`animation-tokens.md`](./animation-tokens.md) for the full token set.
