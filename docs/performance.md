# Performance

> Guidelines, budgets, and techniques for building fast user experiences.

---

## Table of Contents

1. [Performance Philosophy](#performance-philosophy)
2. [Core Web Vitals](#core-web-vitals)
3. [Performance Budgets](#performance-budgets)
4. [JavaScript](#javascript)
5. [CSS](#css)
6. [Images & Media](#images--media)
7. [Fonts](#fonts)
8. [Caching](#caching)
9. [Network](#network)
10. [Rendering](#rendering)
11. [Monitoring](#monitoring)
12. [Tooling](#tooling)

---

## Performance Philosophy

Performance is a feature, not an afterthought. Every byte sent and every millisecond delayed has a direct impact on user experience and conversion rates.

**Performance rules:**
1. Measure before optimizing — never guess
2. Optimize the critical path first
3. Defer or remove everything that isn't immediately needed
4. Budget performance like any other resource

---

## Core Web Vitals

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| **LCP** (Largest Contentful Paint) | ≤ 2.5s | 2.5–4s | > 4s |
| **INP** (Interaction to Next Paint) | ≤ 200ms | 200–500ms | > 500ms |
| **CLS** (Cumulative Layout Shift) | ≤ 0.1 | 0.1–0.25 | > 0.25 |
| **FID** (First Input Delay) | ≤ 100ms | 100–300ms | > 300ms |
| **TTFB** (Time to First Byte) | ≤ 800ms | 800–1800ms | > 1800ms |
| **FCP** (First Contentful Paint) | ≤ 1.8s | 1.8–3s | > 3s |

---

## Performance Budgets

| Asset Type | Budget |
|------------|--------|
| Total page weight (initial load) | < 500KB compressed |
| JavaScript (initial bundle) | < 150KB compressed |
| CSS (critical) | < 20KB compressed |
| Images (hero/above-fold) | < 100KB per image |
| Web fonts | < 50KB per family |
| Time to Interactive (TTI) | < 3.5s on 3G |

---

## JavaScript

### Bundle Size
```bash
# Analyze bundle
npm run build -- --analyze

# Check individual package sizes
npx bundlephobia [package-name]
```

### Code Splitting
```typescript
// ✅ Dynamic import for non-critical routes
const HeavyComponent = dynamic(() => import("./HeavyComponent"), {
  loading: () => <Skeleton />,
  ssr: false, // client-only
});

// ✅ Lazy load below-the-fold content
const LazySection = React.lazy(() => import("./LazySection"));
```

### Third-party Scripts
```html
<!-- Load non-critical scripts after page interactive -->
<script src="analytics.js" defer></script>
<script src="chat-widget.js" async></script>
```

### Tree Shaking
```typescript
// ✅ Named import — tree-shakeable
import { debounce } from "lodash-es";

// ❌ Default import — includes entire library
import _ from "lodash";
```

### Avoid Long Tasks
- Break up JavaScript work > 50ms into smaller chunks
- Use `scheduler.postTask()` or `setTimeout(fn, 0)` to yield to the browser

---

## CSS

### Critical CSS
Inline critical (above-the-fold) styles in `<head>`:
```html
<style>
  /* Only styles needed to render the visible viewport */
</style>
<link rel="preload" href="/styles.css" as="style" onload="this.rel='stylesheet'" />
```

### Avoid Layout Triggers
Avoid reading layout properties (`.offsetHeight`, `.getBoundingClientRect()`) after writes.

### Contain Layout
```css
/* Limit browser layout recalculation scope */
.card {
  contain: layout style;
}
```

### Selector Performance
- Avoid overly complex selectors
- Use class selectors; avoid universal (`*`) and attribute selectors in hot paths

---

## Images & Media

### Format Priority
1. **AVIF** — best compression, modern browsers
2. **WebP** — good compression, broad support
3. **SVG** — for icons, logos, and simple graphics
4. **JPEG** — photos without transparency
5. **PNG** — lossless with transparency

### Responsive Images
```html
<picture>
  <source srcset="hero.avif" type="image/avif" />
  <source srcset="hero.webp" type="image/webp" />
  <img src="hero.jpg"
       srcset="hero-400.jpg 400w, hero-800.jpg 800w, hero-1200.jpg 1200w"
       sizes="(max-width: 640px) 100vw, (max-width: 1024px) 80vw, 1200px"
       alt="Hero image"
       width="1200"
       height="600"
       loading="eager"
       fetchpriority="high" />
</picture>
```

### Lazy Loading
```html
<!-- All images except LCP candidate -->
<img src="photo.jpg" alt="..." loading="lazy" decoding="async" />
```

### Always Specify Dimensions
Prevent layout shift by including `width` and `height` attributes.

---

## Fonts

### Subsetting
Only load the characters you need:
```css
@font-face {
  font-family: "Inter";
  src: url("/fonts/inter.woff2") format("woff2");
  font-display: swap;
  unicode-range: U+0000-00FF; /* Latin subset */
}
```

### `font-display: swap`
Prevents invisible text while fonts load.

### Preload Critical Fonts
```html
<link rel="preload" href="/fonts/inter.woff2" as="font" type="font/woff2" crossorigin />
```

### System Font Fallback
Size-adjust fallback fonts to minimize layout shift:
```css
@font-face {
  font-family: "Inter-fallback";
  src: local("Arial");
  size-adjust: 107%;
  ascent-override: 90%;
}
```

---

## Caching

### Cache-Control Headers
```
# Static assets (hash in filename)
Cache-Control: public, max-age=31536000, immutable

# HTML pages
Cache-Control: no-cache, must-revalidate

# API responses
Cache-Control: private, max-age=60
```

### Service Worker
Use a service worker for offline support and asset caching (e.g., Workbox):
```typescript
import { precacheAndRoute, cleanupOutdatedCaches } from "workbox-precaching";
import { registerRoute } from "workbox-routing";
import { CacheFirst, StaleWhileRevalidate } from "workbox-strategies";
```

---

## Network

### Preconnect to Origins
```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="dns-prefetch" href="https://api.example.com" />
```

### HTTP/2 & HTTP/3
Ensure the server supports HTTP/2 for multiplexed requests.

### Compression
Enable Brotli (preferred) or Gzip on the server for all text assets.

### CDN
Serve static assets from a CDN with edge caching close to users.

---

## Rendering

### SSR / SSG Preference
```
Static (SSG)    → Best performance; pre-rendered at build time
ISR             → Good for semi-dynamic content; revalidates on interval
SSR             → Dynamic per-request; avoid for high-traffic pages if possible
CSR             → Avoid for initial paint; use for highly interactive sub-sections
```

### Virtualization
For long lists, use virtualization instead of rendering all items:
```typescript
import { useVirtualizer } from "@tanstack/react-virtual";
```

### Avoid Render-Blocking Resources
```html
<!-- CSS: load asynchronously if not critical -->
<link rel="preload" href="non-critical.css" as="style" />

<!-- Scripts: always defer or async -->
<script src="app.js" type="module"></script>
```

---

## Monitoring

### Real User Monitoring (RUM)
```typescript
import { onCLS, onINP, onLCP, onFCP, onTTFB } from "web-vitals";

function sendToAnalytics(metric: Metric) {
  fetch("/api/vitals", {
    method: "POST",
    body: JSON.stringify(metric),
    keepalive: true,
  });
}

onCLS(sendToAnalytics);
onINP(sendToAnalytics);
onLCP(sendToAnalytics);
onFCP(sendToAnalytics);
onTTFB(sendToAnalytics);
```

### Synthetic Monitoring
- Run Lighthouse in CI on every PR
- Alert on regressions > 10% on Core Web Vitals

---

## Tooling

| Tool | Purpose |
|------|---------|
| Lighthouse | Audit + CWV scoring |
| WebPageTest | Deep performance waterfall |
| Chrome DevTools Performance panel | CPU profiling |
| `web-vitals` library | RUM instrumentation |
| Bundlesize / Size-limit | Bundle budget enforcement in CI |
| ImageOptim / Squoosh | Image optimization |
| PurgeCSS | Remove unused CSS |
