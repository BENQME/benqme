# Typography

> Type scale, font choices, and text styling conventions.

---

## Table of Contents

1. [Font Families](#font-families)
2. [Type Scale](#type-scale)
3. [Font Weights](#font-weights)
4. [Line Heights](#line-heights)
5. [Letter Spacing](#letter-spacing)
6. [Fluid Typography](#fluid-typography)
7. [Prose Styles](#prose-styles)
8. [Usage Guidelines](#usage-guidelines)
9. [CSS Tokens](#css-tokens)

---

## Font Families

| Role | Family | Fallback Stack |
|------|--------|----------------|
| Sans (UI) | Inter | `system-ui, -apple-system, sans-serif` |
| Serif (Editorial) | Playfair Display | `Georgia, serif` |
| Mono (Code) | JetBrains Mono | `"Courier New", monospace` |

### Loading

```html
<!-- Google Fonts (self-host in production for performance) -->
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet" />
```

```css
:root {
  --font-sans:  "Inter", system-ui, -apple-system, sans-serif;
  --font-serif: "Playfair Display", Georgia, serif;
  --font-mono:  "JetBrains Mono", "Courier New", monospace;
}
```

---

## Type Scale

Based on a **Major Third** (1.25×) scale:

| Token | Size | Usage |
|-------|------|-------|
| `text-xs` | 12px / 0.75rem | Labels, captions, legal |
| `text-sm` | 14px / 0.875rem | Secondary text, table cells |
| `text-base` | 16px / 1rem | Body copy (default) |
| `text-lg` | 18px / 1.125rem | Lead paragraphs |
| `text-xl` | 20px / 1.25rem | Small headings, card titles |
| `text-2xl` | 24px / 1.5rem | Section headings (h3) |
| `text-3xl` | 30px / 1.875rem | Page headings (h2) |
| `text-4xl` | 36px / 2.25rem | Section heroes (h1 mobile) |
| `text-5xl` | 48px / 3rem | Large headings (h1 tablet) |
| `text-6xl` | 60px / 3.75rem | Display headings (h1 desktop) |
| `text-7xl` | 72px / 4.5rem | Hero display |
| `text-8xl` | 96px / 6rem | Oversized display |

---

## Font Weights

| Token | Value | Usage |
|-------|-------|-------|
| `font-thin` | 100 | Decorative display only |
| `font-light` | 300 | Large display text |
| `font-normal` | 400 | Body copy |
| `font-medium` | 500 | Emphasis, labels |
| `font-semibold` | 600 | Subheadings, strong UI elements |
| `font-bold` | 700 | Headings, primary CTAs |
| `font-extrabold` | 800 | Hero headings |
| `font-black` | 900 | Maximum emphasis display |

---

## Line Heights

| Token | Value | Usage |
|-------|-------|-------|
| `leading-none` | 1 | Single-line labels, buttons |
| `leading-tight` | 1.25 | Headings |
| `leading-snug` | 1.375 | Compact paragraphs |
| `leading-normal` | 1.5 | Body copy (default) |
| `leading-relaxed` | 1.625 | Long-form prose |
| `leading-loose` | 2 | Spacious layouts |

---

## Letter Spacing

| Token | Value | Usage |
|-------|-------|-------|
| `tracking-tighter` | -0.05em | Large display headings |
| `tracking-tight` | -0.025em | Headings |
| `tracking-normal` | 0em | Body copy (default) |
| `tracking-wide` | 0.025em | Small labels |
| `tracking-wider` | 0.05em | Uppercase labels, badges |
| `tracking-widest` | 0.1em | Caps abbreviations |

---

## Fluid Typography

Use `clamp()` for headings that scale smoothly between breakpoints:

```css
:root {
  /* h1: 36px on mobile → 60px on desktop */
  --text-h1: clamp(2.25rem, 5vw + 0.5rem, 3.75rem);

  /* h2: 24px on mobile → 36px on desktop */
  --text-h2: clamp(1.5rem, 3vw + 0.5rem, 2.25rem);

  /* h3: 20px on mobile → 24px on desktop */
  --text-h3: clamp(1.25rem, 2vw + 0.25rem, 1.5rem);
}
```

---

## Prose Styles

For long-form content (blog posts, documentation):

```css
.prose {
  font-family: var(--font-sans);
  font-size: 1.125rem;   /* 18px */
  line-height: 1.75;
  color: var(--color-text-primary);
  max-width: 65ch;       /* Optimal reading width */
}

.prose h1, .prose h2, .prose h3 {
  font-weight: 700;
  line-height: 1.25;
  letter-spacing: -0.025em;
  color: var(--color-text-heading);
}

.prose p + p {
  margin-top: 1.25em;
}

.prose code {
  font-family: var(--font-mono);
  font-size: 0.875em;
  background: var(--color-surface-2);
  padding: 0.125em 0.375em;
  border-radius: 4px;
}

.prose blockquote {
  border-left: 4px solid var(--color-border);
  padding-left: 1rem;
  color: var(--color-text-secondary);
  font-style: italic;
}
```

---

## Usage Guidelines

### Headings
- Use the semantic heading hierarchy (`h1`→`h2`→`h3`) for accessibility
- Only one `h1` per page
- Don't skip heading levels for visual reasons — adjust font size with CSS instead

### Body Copy
- Default body: `text-base` (16px), `leading-normal` (1.5), `font-normal`
- Aim for 60–80 characters per line (`max-width: 65ch`)
- Paragraph spacing: `1em` top margin on `p + p`

### Labels & UI Text
- Buttons, form labels: `text-sm` or `text-base`, `font-medium`
- Captions, metadata: `text-xs` or `text-sm`, `text-secondary`
- Do not use font-weight alone to convey importance — pair with size

### Code
- Always use `font-mono` for code snippets
- Inline code: smaller size (`0.875em`) with background highlight
- Code blocks: maintain horizontal scroll on overflow; never wrap code

---

## CSS Tokens

```css
:root {
  /* Families */
  --font-sans:  "Inter", system-ui, sans-serif;
  --font-serif: "Playfair Display", Georgia, serif;
  --font-mono:  "JetBrains Mono", monospace;

  /* Scale */
  --text-xs:   0.75rem;
  --text-sm:   0.875rem;
  --text-base: 1rem;
  --text-lg:   1.125rem;
  --text-xl:   1.25rem;
  --text-2xl:  1.5rem;
  --text-3xl:  1.875rem;
  --text-4xl:  2.25rem;
  --text-5xl:  3rem;
  --text-6xl:  3.75rem;

  /* Weights */
  --font-normal:   400;
  --font-medium:   500;
  --font-semibold: 600;
  --font-bold:     700;

  /* Leading */
  --leading-tight:   1.25;
  --leading-normal:  1.5;
  --leading-relaxed: 1.625;

  /* Tracking */
  --tracking-tight:   -0.025em;
  --tracking-normal:   0em;
  --tracking-wide:     0.025em;
  --tracking-wider:    0.05em;
}
```
