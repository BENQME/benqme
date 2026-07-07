# Icons

> Icon system, sourcing guidelines, and usage patterns.

---

## Table of Contents

1. [Icon System Overview](#icon-system-overview)
2. [Icon Library](#icon-library)
3. [Usage](#usage)
4. [Sizing](#sizing)
5. [Coloring](#coloring)
6. [Accessibility](#accessibility)
7. [Custom Icons](#custom-icons)
8. [Icon Component API](#icon-component-api)
9. [Available Icons](#available-icons)

---

## Icon System Overview

Icons are SVG-based and delivered as React components. This approach provides:
- Zero HTTP requests (inlined in JS bundle)
- `currentColor` inheritance for easy theming
- `aria-hidden` by default (decorative)
- TypeScript props for size, color, and className
- Tree-shakeable — only imported icons are bundled

The primary icon library is [Lucide React](https://lucide.dev/). Custom icons are added as one-off components in `src/components/icons/`.

---

## Icon Library

| Library | Purpose | Import |
|---------|---------|--------|
| [Lucide React](https://lucide.dev/) | General UI icons | `import { Icon } from "lucide-react"` |
| Custom icons | Brand, product-specific | `import { Icon } from "@/components/icons"` |
| [Simple Icons](https://simpleicons.org/) | Brand/technology logos | `import { Icon } from "@icons-pack/react-simple-icons"` |

---

## Usage

### Basic Usage (Lucide)
```tsx
import { Search, Settings, ChevronDown } from "lucide-react";

// Decorative icon (default)
<Search aria-hidden="true" />

// Icon with text (decorative)
<button>
  <Search aria-hidden="true" />
  Search
</button>

// Icon-only interactive element (must have label)
<button aria-label="Search">
  <Search aria-hidden="true" />
</button>
```

### Icon-Only Button Pattern
```tsx
import { X } from "lucide-react";

<button
  type="button"
  aria-label="Close dialog"
  className="icon-button"
>
  <X aria-hidden="true" size={16} />
</button>
```

---

## Sizing

Icons follow a consistent size scale:

| Token | Size | Usage |
|-------|------|-------|
| `xs` | 12px | Inline with small text |
| `sm` | 14px | Inline with `text-sm` |
| `md` | 16px | Default, inline with `text-base` |
| `lg` | 20px | Buttons, nav items |
| `xl` | 24px | Section headings, features |
| `2xl` | 32px | Empty states, illustrations |
| `3xl` | 48px | Hero illustrations |

```tsx
// Lucide uses the `size` prop
<Search size={16} />   // sm
<Search size={20} />   // lg
<Search size={24} />   // xl (default)

// Custom icons use className
<SearchIcon className="w-4 h-4" />   // 16px
<SearchIcon className="w-5 h-5" />   // 20px
```

---

## Coloring

Icons inherit text color via `currentColor`. Control color by setting `text-*` on the icon or a parent:

```tsx
// Inherit from parent
<div className="text-blue-600">
  <InfoIcon aria-hidden="true" />
  Info message
</div>

// Explicit color
<CheckIcon className="text-green-500" aria-hidden="true" />

// Status colors
<SuccessIcon className="text-success" />
<WarningIcon className="text-warning" />
<ErrorIcon className="text-danger" />
```

---

## Accessibility

### Decorative Icons (most icons)
```tsx
// Always add aria-hidden="true" to icons that are decorative
// (i.e., accompanied by text that conveys the meaning)
<button>
  <TrashIcon aria-hidden="true" />
  Delete
</button>
```

### Meaningful Icons (standalone)
```tsx
// When an icon conveys meaning without accompanying text,
// either:

// Option 1: aria-label on the interactive element
<button aria-label="Delete post">
  <TrashIcon aria-hidden="true" />
</button>

// Option 2: sr-only text
<button>
  <TrashIcon aria-hidden="true" />
  <span className="sr-only">Delete post</span>
</button>

// Option 3: title on the SVG (less reliable, avoid)
<svg><title>Delete post</title>...</svg>
```

---

## Custom Icons

For icons not in Lucide, add them as React components:

```tsx
// src/components/icons/GitHubIcon.tsx
import type { SVGProps } from "react";

export function GitHubIcon(props: SVGProps<SVGSVGElement>) {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 24 24"
      fill="currentColor"
      aria-hidden="true"
      {...props}
    >
      <path
        fillRule="evenodd"
        d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425..."
        clipRule="evenodd"
      />
    </svg>
  );
}
```

### Barrel Export
```typescript
// src/components/icons/index.ts
export { GitHubIcon } from "./GitHubIcon";
export { TwitterIcon } from "./TwitterIcon";
export { VercelIcon } from "./VercelIcon";
```

### Icon Requirements
- `viewBox` attribute set correctly
- `fill="currentColor"` or `stroke="currentColor"` (not hardcoded colors)
- `aria-hidden="true"` as default
- Spread `...props` to allow className, size overrides
- Run through [SVGO](https://svgo.dev/) for optimization
- Document in the [Available Icons](#available-icons) section below

---

## Icon Component API

Wrapper component for consistent sizing and styling:

```tsx
// src/components/icons/Icon.tsx
import { cn } from "@/utils/cn";
import type { LucideIcon } from "lucide-react";

interface IconProps {
  icon: LucideIcon;
  size?: "xs" | "sm" | "md" | "lg" | "xl" | "2xl";
  className?: string;
}

const sizeMap = {
  xs:  "w-3 h-3",
  sm:  "w-3.5 h-3.5",
  md:  "w-4 h-4",
  lg:  "w-5 h-5",
  xl:  "w-6 h-6",
  "2xl": "w-8 h-8",
} as const;

export function Icon({ icon: IconComponent, size = "md", className }: IconProps) {
  return (
    <IconComponent
      aria-hidden="true"
      className={cn(sizeMap[size], className)}
    />
  );
}
```

---

## Available Icons

### Navigation
| Name | Component | Usage |
|------|-----------|-------|
| Home | `Home` (Lucide) | Home link |
| Menu | `Menu` (Lucide) | Mobile hamburger |
| X / Close | `X` (Lucide) | Close buttons |
| ChevronDown | `ChevronDown` (Lucide) | Dropdowns |
| ChevronRight | `ChevronRight` (Lucide) | Breadcrumbs, links |
| ArrowLeft | `ArrowLeft` (Lucide) | Back navigation |

### Actions
| Name | Component | Usage |
|------|-----------|-------|
| Plus | `Plus` (Lucide) | Add/create |
| Trash | `Trash2` (Lucide) | Delete |
| Edit | `Pencil` (Lucide) | Edit |
| Copy | `Copy` (Lucide) | Copy to clipboard |
| Download | `Download` (Lucide) | Download |
| Upload | `Upload` (Lucide) | Upload |
| Share | `Share2` (Lucide) | Share |
| Search | `Search` (Lucide) | Search |

### Status
| Name | Component | Usage |
|------|-----------|-------|
| Check | `Check` (Lucide) | Success/complete |
| AlertCircle | `AlertCircle` (Lucide) | Error |
| AlertTriangle | `AlertTriangle` (Lucide) | Warning |
| Info | `Info` (Lucide) | Informational |
| Loader | `Loader2` (Lucide) | Loading (animated) |

### Brand / Social
| Name | Component | Usage |
|------|-----------|-------|
| GitHub | `GitHubIcon` (custom) | GitHub links |
| Twitter/X | `TwitterIcon` (custom) | Twitter links |
