# Assets

> Guidelines for managing images, icons, fonts, and other static assets.

---

## Table of Contents

1. [Asset Types](#asset-types)
2. [Folder Structure](#folder-structure)
3. [Images](#images)
4. [Icons](#icons)
5. [Fonts](#fonts)
6. [Videos](#videos)
7. [Documents](#documents)
8. [Naming Conventions](#naming-conventions)
9. [Optimization Checklist](#optimization-checklist)

---

## Asset Types

| Type | Formats | Location |
|------|---------|----------|
| Raster images | AVIF, WebP, JPEG, PNG | `public/images/` |
| Vector graphics | SVG | `public/images/` or inline |
| Icons | SVG (via sprite or React component) | `src/components/icons/` |
| Fonts | WOFF2 | `public/fonts/` |
| Videos | MP4 (H.264/H.265), WebM | `public/videos/` |
| Documents | PDF | `public/docs/` |
| Favicons | ICO, PNG, SVG | `public/` |
| OG Images | PNG (1200×630) | `public/og/` or generated |

---

## Folder Structure

```
public/
├── images/
│   ├── photos/          # Photography, realistic images
│   ├── illustrations/   # Illustrations, diagrams
│   ├── og/              # Open Graph preview images
│   └── screenshots/     # App/feature screenshots
├── fonts/
│   ├── inter/           # Inter variable font
│   └── jetbrains-mono/  # JetBrains Mono
├── videos/
├── docs/                # Downloadable PDFs, etc.
└── [favicon files]
```

---

## Images

### Format Selection

```
Photo with no transparency    → JPEG (progressive)
Photo or illustration          → WebP (primary), JPEG (fallback)
Modern browser only            → AVIF (primary), WebP (fallback)
Simple graphics, logos         → SVG (always preferred)
Graphic with transparency      → PNG or WebP
```

### Sizing Guidelines

| Context | Max dimensions | Notes |
|---------|---------------|-------|
| Hero image | 1920×1080 | Serve via `srcset` |
| Card thumbnail | 800×600 | 2× for retina |
| Avatar | 256×256 | 2× for retina |
| OG image | 1200×630 | Exact |
| Favicon | 32×32, 192×192, 512×512 | Multiple sizes |
| Blog cover | 1200×675 | 16:9 ratio |

### Responsive Image Pattern

```html
<picture>
  <source
    srcset="/images/hero.avif 1x, /images/hero@2x.avif 2x"
    type="image/avif"
  />
  <source
    srcset="/images/hero.webp 1x, /images/hero@2x.webp 2x"
    type="image/webp"
  />
  <img
    src="/images/hero.jpg"
    srcset="/images/hero-400.jpg 400w,
            /images/hero-800.jpg 800w,
            /images/hero-1200.jpg 1200w"
    sizes="(max-width: 640px) 100vw,
           (max-width: 1024px) 80vw,
           1200px"
    alt="Descriptive alt text"
    width="1200"
    height="675"
    loading="eager"
    fetchpriority="high"
  />
</picture>
```

### Next.js Image Component

```tsx
import Image from "next/image";

<Image
  src="/images/avatar.jpg"
  alt="BENQME avatar"
  width={256}
  height={256}
  priority           // for above-the-fold images
  placeholder="blur"
  blurDataURL="..."  // tiny base64 preview
/>
```

---

## Icons

See [`icons.md`](./icons.md) for the full icon system documentation.

### Preferred Approach: SVG Components

```tsx
// src/components/icons/CheckIcon.tsx
export function CheckIcon({ className }: { className?: string }) {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 24 24"
      fill="none"
      stroke="currentColor"
      strokeWidth={2}
      strokeLinecap="round"
      strokeLinejoin="round"
      aria-hidden="true"
      className={className}
    >
      <polyline points="20 6 9 17 4 12" />
    </svg>
  );
}
```

### Icon Guidelines
- Always `aria-hidden="true"` on decorative icons
- Pair with text or `aria-label` on the parent for interactive icons
- Default size: `1em` (inherits from font-size context)
- Use `currentColor` for stroke/fill to inherit text color

---

## Fonts

### Self-hosted Fonts (Recommended)

```css
@font-face {
  font-family: "Inter";
  src:
    url("/fonts/inter/inter-var.woff2") format("woff2-variations"),
    url("/fonts/inter/inter-var.woff2") format("woff2");
  font-weight: 100 900;
  font-style: normal;
  font-display: swap;
}
```

### Preload Critical Fonts

```html
<link
  rel="preload"
  href="/fonts/inter/inter-var.woff2"
  as="font"
  type="font/woff2"
  crossorigin="anonymous"
/>
```

### Font File Checklist
- [ ] WOFF2 format only (best compression, universal modern browser support)
- [ ] Subsetting applied (only include needed character ranges)
- [ ] `font-display: swap` to prevent invisible text
- [ ] Variable font used where possible (one file for all weights)

---

## Videos

### Formats
- Primary: **WebM** (VP9 or AV1 codec) — smaller file
- Fallback: **MP4** (H.264 codec) — broadest compatibility

```html
<video
  autoplay
  loop
  muted
  playsinline
  preload="metadata"
  poster="/videos/demo-poster.jpg"
>
  <source src="/videos/demo.webm" type="video/webm" />
  <source src="/videos/demo.mp4" type="video/mp4" />
  Your browser does not support the video element.
</video>
```

### Rules
- Always provide a `poster` image
- Autoplay only when `muted` and `loop`
- Never autoplay with audio
- Provide captions for videos with meaningful audio (`<track>` element)
- Maximum size: 5MB for background videos; use external CDN for longer content

---

## Documents

- Store downloadable PDFs in `public/docs/`
- Link with descriptive text including file type: `Resume (PDF, 120KB)`
- Provide accessible alternatives where possible

---

## Naming Conventions

| Rule | Example |
|------|---------|
| All lowercase | `hero-image.jpg` ✅ |
| Hyphen-separated words | `my-project-screenshot.png` ✅ |
| No spaces | `my project.jpg` ❌ |
| No uppercase | `HeroImage.jpg` ❌ |
| Descriptive names | `profile-avatar-256.jpg` ✅ |
| Include dimensions for fixed-size assets | `og-image-1200x630.png` |
| Retina suffix | `icon@2x.png` |
| Locale suffix for localized assets | `banner-fr.jpg` |

---

## Optimization Checklist

Before committing any asset:

- [ ] Image compressed (target: < 100KB for typical content, < 500KB for hero)
- [ ] Image converted to WebP or AVIF where applicable
- [ ] Image dimensions match the largest display size needed
- [ ] `width` and `height` attributes set (prevent CLS)
- [ ] `alt` text written (or `alt=""` for decorative)
- [ ] Font files are WOFF2 and subsetted
- [ ] SVG optimized with SVGO
- [ ] Video compressed and under size budget
- [ ] Unused assets removed from `public/`
