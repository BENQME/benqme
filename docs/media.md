# Media

> Guidelines for images, video, audio, and other media used in the project.

---

## Table of Contents

1. [Media Types Overview](#media-types-overview)
2. [Images](#images)
3. [Video](#video)
4. [Audio](#audio)
5. [Animations & GIFs](#animations--gifs)
6. [Media Organization](#media-organization)
7. [Optimization Tools](#optimization-tools)
8. [Responsive Media](#responsive-media)
9. [Accessibility](#accessibility)
10. [Legal & Licensing](#legal--licensing)

---

## Media Types Overview

| Type | Formats | Max Size | Notes |
|------|---------|---------|-------|
| Photo | AVIF, WebP, JPEG | 500KB | Use `next/image` |
| Illustration | SVG, WebP | 200KB | Prefer SVG |
| Icon | SVG | 10KB | See `icons.md` |
| Logo | SVG | 20KB | Always SVG |
| Video (short) | WebM, MP4 | 5MB | No autoplay with sound |
| Video (long) | External (YouTube, Vimeo) | — | Embed, don't host |
| Audio | MP3, OGG | 10MB | Rare; provide transcript |
| Background video | WebM, MP4 | 3MB | Muted, looped, no audio |
| OG image | PNG | 500KB | 1200×630px |
| Favicon | SVG, ICO, PNG | 10KB | Multiple sizes |

---

## Images

### Format Selection Guide

```
Simple graphics, logos, icons → SVG
Complex illustrations → WebP
Photos → AVIF (primary) → WebP (fallback) → JPEG (legacy)
Screenshots → PNG or WebP
Transparent images → PNG or WebP
```

### Image Sizes

| Use case | Dimensions | Aspect ratio |
|----------|-----------|-------------|
| Hero / Banner | 1920×1080 | 16:9 |
| Blog cover | 1200×675 | 16:9 |
| Project thumbnail | 800×600 | 4:3 |
| Avatar | 256×256 | 1:1 |
| OG image | 1200×630 | ~1.91:1 |
| Favicon | 32×32, 192×192, 512×512 | 1:1 |
| Card image | 400×300+ | variable |

### Next.js Image Optimization

```tsx
import Image from "next/image";

// Static import (preferred — gives Webpack the dimensions)
import heroImg from "@/public/images/hero.jpg";
<Image src={heroImg} alt="Hero" priority />

// Dynamic import (requires explicit width/height)
<Image
  src="/images/avatar.jpg"
  alt="User avatar"
  width={64}
  height={64}
  className="rounded-full"
/>

// Fill mode (for unknown dimensions — parent must be positioned)
<div className="relative aspect-video">
  <Image
    src={project.coverImage}
    alt={project.title}
    fill
    className="object-cover"
    sizes="(max-width: 768px) 100vw, 50vw"
  />
</div>
```

### Sizes Attribute
Always provide a `sizes` attribute to help the browser pick the right `srcset` entry:

```html
sizes="(max-width: 640px) 100vw,
       (max-width: 1024px) 50vw,
       33vw"
```

---

## Video

### Self-hosted Video
Use for short videos only (< 5MB). Host longer content on YouTube/Vimeo.

```html
<video
  autoplay          <!-- Only if muted -->
  loop
  muted
  playsinline       <!-- Prevent fullscreen on iOS -->
  preload="metadata"
  poster="/videos/preview.jpg"
  aria-label="Demo of the feature"
>
  <source src="/videos/demo.webm" type="video/webm" />
  <source src="/videos/demo.mp4" type="video/mp4" />
  <track
    kind="captions"
    src="/videos/demo.vtt"
    srclang="en"
    label="English"
    default
  />
  <p>Your browser does not support video. <a href="/videos/demo.mp4">Download</a></p>
</video>
```

### YouTube/Vimeo Embeds
Use lazy loading and privacy-enhanced mode:

```tsx
// Lazy-loaded iframe with aspect ratio
<div className="relative aspect-video">
  <iframe
    src="https://www.youtube-nocookie.com/embed/VIDEO_ID"
    title="Video description"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope"
    allowFullScreen
    loading="lazy"
    className="absolute inset-0 w-full h-full"
  />
</div>
```

---

## Audio

Audio is rarely used. When it is:
- Always provide a visible player with play/pause controls
- Provide a text transcript
- Never autoplay
- Prefer MP3 with OGG fallback

```html
<audio controls preload="metadata">
  <source src="/audio/podcast.mp3" type="audio/mpeg" />
  <source src="/audio/podcast.ogg" type="audio/ogg" />
  <p>Your browser does not support audio. <a href="/audio/podcast.mp3">Download MP3</a></p>
</audio>
```

---

## Animations & GIFs

Avoid GIF files — they are large and cannot be paused. Use video instead:

```html
<!-- Instead of <img src="animation.gif"> -->
<video autoplay loop muted playsinline aria-hidden="true">
  <source src="/animations/demo.webm" type="video/webm" />
  <source src="/animations/demo.mp4" type="video/mp4" />
</video>
```

If GIF is unavoidable (e.g., in email):
- Compress with [Gifsicle](https://www.lcdf.org/gifsicle/)
- Max size: 1MB
- Add `prefers-reduced-motion` handling:

```css
@media (prefers-reduced-motion: reduce) {
  img[src$=".gif"],
  video[autoplay] {
    animation: none !important;
  }
}
```

---

## Media Organization

```
public/
├── images/
│   ├── photos/           # Photography
│   ├── illustrations/    # Illustrations
│   ├── screenshots/      # App screenshots
│   ├── og/               # Open Graph images (1200×630)
│   └── [page-name]/      # Page-specific images
├── videos/
│   ├── demos/            # Feature demos
│   └── backgrounds/      # Background videos
├── audio/                # Rare; podcasts, sound effects
└── fonts/                # Self-hosted fonts
```

**Naming**: lowercase, hyphen-separated.
`project-dashboard-screenshot.webp` ✅
`ProjectDashboard.png` ❌

---

## Optimization Tools

| Tool | Purpose |
|------|---------|
| [Squoosh](https://squoosh.app) | Browser-based image compression |
| [ImageOptim](https://imageoptim.com) | Mac app for image optimization |
| [SVGO](https://svgo.dev/) / svgomg | SVG optimization |
| [FFmpeg](https://ffmpeg.org/) | Video conversion and compression |
| [Gifsicle](https://www.lcdf.org/gifsicle/) | GIF optimization (avoid GIFs where possible) |
| [Cloudflare Images](https://cloudflare.com/products/cloudflare-images/) | CDN image optimization |

### FFmpeg Commands

```bash
# Convert video to WebM
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 0 -crf 33 -c:a libopus output.webm

# Convert video to MP4 (H.264)
ffmpeg -i input.mov -c:v libx264 -crf 23 -preset medium -c:a aac output.mp4

# Create a muted loop-optimized background video
ffmpeg -i input.mp4 -c:v libx264 -crf 28 -preset slow -an -movflags +faststart output.mp4

# Generate a poster image from video frame
ffmpeg -i input.mp4 -ss 00:00:01 -vframes 1 poster.jpg
```

---

## Responsive Media

```tsx
{/* Responsive image with art direction */}
<picture>
  {/* Mobile crop (square) */}
  <source
    media="(max-width: 639px)"
    srcset="/images/hero-mobile.webp"
    type="image/webp"
  />
  {/* Desktop (16:9) */}
  <source
    srcset="/images/hero-desktop.webp"
    type="image/webp"
  />
  <img
    src="/images/hero-desktop.jpg"
    alt="Project showcase"
    width="1200"
    height="675"
    loading="eager"
    fetchpriority="high"
  />
</picture>
```

---

## Accessibility

- **Images**: Every image must have `alt` text, or `alt=""` if decorative
- **Video**: Provide captions (`<track>` element) for videos with speech
- **Audio**: Provide transcripts for all audio content
- **GIFs/Animations**: Must be pausable (via controls or `prefers-reduced-motion`)
- **Color**: Images must not rely on color alone to convey meaning

---

## Legal & Licensing

| Source | License | Commercial use? |
|--------|---------|----------------|
| [Unsplash](https://unsplash.com) | Unsplash License | ✅ Free |
| [Pexels](https://pexels.com) | Pexels License | ✅ Free |
| [Pixabay](https://pixabay.com) | Pixabay Content License | ✅ Free |
| [Flaticon](https://flaticon.com) | Freepik License | ⚠️ Attribution required (free tier) |
| Own photography | — | ✅ |

**Rules:**
- Never use watermarked images
- Never hotlink — always download and self-host
- Always check the specific license for commercial use restrictions
- When attribution is required, add it in an `<figcaption>` or footer
