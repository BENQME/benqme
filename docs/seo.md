# SEO

> Search engine optimization guidelines, metadata, and structured data patterns.

---

## Table of Contents

1. [SEO Fundamentals](#seo-fundamentals)
2. [Metadata](#metadata)
3. [Open Graph](#open-graph)
4. [Sitemap](#sitemap)
5. [Robots.txt](#robotstxt)
6. [Structured Data (JSON-LD)](#structured-data-json-ld)
7. [Performance & Core Web Vitals](#performance--core-web-vitals)
8. [Technical SEO Checklist](#technical-seo-checklist)

---

## SEO Fundamentals

### Priorities
1. **Crawlability**: Search engines must be able to access and index your pages
2. **Relevance**: Content must match user intent
3. **Authority**: Backlinks and engagement signals
4. **User Experience**: Core Web Vitals, mobile-friendliness, accessibility

### SEO-Friendly Rendering
Use SSR or SSG for all SEO-critical pages. CSR pages are invisible to basic crawlers.

| Method | SEO-Friendly |
|--------|-------------|
| SSG (Static) | ✅ Best |
| SSR (Dynamic) | ✅ Good |
| ISR | ✅ Good |
| CSR only | ❌ Poor |

---

## Metadata

### Using Next.js Metadata API

```typescript
// src/app/layout.tsx — Global defaults
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: {
    default: "BENQME",
    template: "%s | BENQME",
  },
  description: "Developer, builder, and explorer. Projects, writing, and more.",
  authors: [{ name: "BENQME", url: "https://github.com/BENQME" }],
  keywords: ["developer", "web development", "open source"],
  creator: "BENQME",
  metadataBase: new URL("https://benqme.com"),
  openGraph: {
    type: "website",
    locale: "en_US",
    url: "https://benqme.com",
    siteName: "BENQME",
  },
  twitter: {
    card: "summary_large_image",
    creator: "@BENQME",
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      "max-video-preview": -1,
      "max-image-preview": "large",
      "max-snippet": -1,
    },
  },
};
```

```typescript
// src/app/projects/[slug]/page.tsx — Dynamic metadata
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const project = await fetchProject(params.slug);
  if (!project) return { title: "Not Found" };

  return {
    title: project.title,
    description: project.description,
    openGraph: {
      title: project.title,
      description: project.description,
      images: [{ url: project.ogImage ?? "/og/default.png", width: 1200, height: 630 }],
    },
  };
}
```

### Essential Meta Tags

```html
<!-- Charset and viewport (always in <head>) -->
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />

<!-- Title and description -->
<title>Page Title | Site Name</title>
<meta name="description" content="160 characters max. Compelling summary." />

<!-- Canonical URL (prevents duplicate content) -->
<link rel="canonical" href="https://benqme.com/current-page" />

<!-- Language -->
<html lang="en">
```

---

## Open Graph

Open Graph tags control how pages appear when shared on social media.

```typescript
// Recommended OG image size: 1200×630px
// Minimum: 600×315px
// File format: PNG or JPG
// Max size: < 1MB

openGraph: {
  title: "Page Title",
  description: "Page description (< 200 chars)",
  url: "https://benqme.com/page",
  siteName: "BENQME",
  images: [
    {
      url: "https://benqme.com/og/page-image.png",
      width: 1200,
      height: 630,
      alt: "Descriptive alt text for the image",
    },
  ],
  type: "article",              // or "website", "profile"
  publishedTime: "2024-01-15T00:00:00Z",
  authors: ["BENQME"],
  tags: ["web development", "TypeScript"],
},

twitter: {
  card: "summary_large_image",  // or "summary"
  title: "Page Title",
  description: "Page description",
  images: ["https://benqme.com/og/page-image.png"],
  creator: "@BENQME",
},
```

### Dynamic OG Images with Next.js

```typescript
// src/app/og/route.tsx
import { ImageResponse } from "next/og";

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const title = searchParams.get("title") ?? "BENQME";

  return new ImageResponse(
    (
      <div
        style={{
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          background: "#0a0a0a",
          color: "#fafafa",
        }}
      >
        <h1 style={{ fontSize: 64, fontWeight: 700 }}>{title}</h1>
      </div>
    ),
    { width: 1200, height: 630 }
  );
}
```

---

## Sitemap

```typescript
// src/app/sitemap.ts
import type { MetadataRoute } from "next";
import { db } from "@/lib/db";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const posts = await db.post.findMany({
    where: { published: true },
    select: { slug: true, updatedAt: true },
  });

  const staticPages = [
    { url: "https://benqme.com", lastModified: new Date(), changeFrequency: "monthly" as const, priority: 1 },
    { url: "https://benqme.com/projects", lastModified: new Date(), changeFrequency: "weekly" as const, priority: 0.8 },
    { url: "https://benqme.com/writing", lastModified: new Date(), changeFrequency: "weekly" as const, priority: 0.8 },
    { url: "https://benqme.com/about", lastModified: new Date(), changeFrequency: "monthly" as const, priority: 0.6 },
  ];

  const postPages = posts.map((post) => ({
    url: `https://benqme.com/writing/${post.slug}`,
    lastModified: post.updatedAt,
    changeFrequency: "monthly" as const,
    priority: 0.7,
  }));

  return [...staticPages, ...postPages];
}
```

---

## Robots.txt

```typescript
// src/app/robots.ts
import type { MetadataRoute } from "next";

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      {
        userAgent: "*",
        allow: "/",
        disallow: ["/api/", "/app/", "/_next/"],
      },
    ],
    sitemap: "https://benqme.com/sitemap.xml",
    host: "https://benqme.com",
  };
}
```

---

## Structured Data (JSON-LD)

Add structured data to help search engines understand your content.

### Person Schema (Profile page)
```tsx
export function PersonSchema() {
  const schema = {
    "@context": "https://schema.org",
    "@type": "Person",
    name: "BENQME",
    url: "https://benqme.com",
    sameAs: [
      "https://github.com/BENQME",
    ],
    jobTitle: "Software Developer",
  };

  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(schema) }}
    />
  );
}
```

### Article Schema (Blog posts)
```tsx
export function ArticleSchema({ post }: { post: Post }) {
  const schema = {
    "@context": "https://schema.org",
    "@type": "TechArticle",
    headline: post.title,
    description: post.excerpt,
    author: { "@type": "Person", name: "BENQME", url: "https://benqme.com" },
    datePublished: post.publishedAt,
    dateModified: post.updatedAt,
    url: `https://benqme.com/writing/${post.slug}`,
    image: post.ogImage,
  };

  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(schema) }}
    />
  );
}
```

---

## Performance & Core Web Vitals

SEO and performance are closely linked. Google uses Core Web Vitals in its ranking algorithm.

| Vital | Target | Impact |
|-------|--------|--------|
| LCP | ≤ 2.5s | Critical |
| INP | ≤ 200ms | High |
| CLS | ≤ 0.1 | High |

See [`performance.md`](./performance.md) for the complete performance guide.

**Quick wins for SEO performance:**
- Use `fetchpriority="high"` on LCP images
- Preload critical fonts
- Minimize render-blocking resources
- Use SSG or ISR where possible

---

## Technical SEO Checklist

### Crawlability
- [ ] `robots.txt` correctly configured
- [ ] `sitemap.xml` generated and submitted to Google Search Console
- [ ] No important pages disallowed in robots.txt
- [ ] Canonical URLs set on all pages

### Metadata
- [ ] Unique `<title>` on every page (50–60 chars)
- [ ] Unique `<meta name="description">` on every page (150–160 chars)
- [ ] Open Graph tags on all shareable pages
- [ ] Twitter Card tags configured

### Content
- [ ] Single `<h1>` per page
- [ ] Heading hierarchy (h1 → h2 → h3)
- [ ] Alt text on all images
- [ ] Internal links use descriptive anchor text

### Technical
- [ ] HTTPS enforced
- [ ] No broken links (use a link checker)
- [ ] Core Web Vitals in green
- [ ] Mobile-friendly (test with Google's Mobile-Friendly Test)
- [ ] Structured data validated (Google Rich Results Test)
- [ ] No duplicate content (canonical tags or 301 redirects)
