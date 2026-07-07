# Navigation

> Navigation patterns, components, and routing conventions.

---

## Table of Contents

1. [Navigation Patterns](#navigation-patterns)
2. [Navbar](#navbar)
3. [Sidebar Navigation](#sidebar-navigation)
4. [Breadcrumbs](#breadcrumbs)
5. [Tabs](#tabs)
6. [Pagination](#pagination)
7. [Routing Conventions](#routing-conventions)
8. [Active State Detection](#active-state-detection)
9. [Accessibility](#accessibility)

---

## Navigation Patterns

| Pattern | Use case |
|---------|---------|
| Top navbar | Primary site navigation |
| Sidebar | App navigation with many items |
| Breadcrumbs | Deep hierarchies (> 2 levels) |
| Tabs | Switching between peer content sections |
| Pagination | Long lists and data tables |
| Command palette | Power user navigation (⌘K) |
| Footer nav | Secondary/legal links |

---

## Navbar

### Desktop Navbar
```tsx
// src/components/layout/Header/Header.tsx
import Link from "next/link";
import { usePathname } from "next/navigation";
import { cn } from "@/utils/cn";

const navItems = [
  { label: "Home", href: "/" },
  { label: "Projects", href: "/projects" },
  { label: "Writing", href: "/writing" },
  { label: "About", href: "/about" },
];

export function Header() {
  const pathname = usePathname();

  return (
    <header className="sticky top-0 z-sticky border-b border-border bg-bg-primary/80 backdrop-blur">
      <nav aria-label="Main navigation" className="container flex h-16 items-center justify-between">
        <Link href="/" className="font-bold text-lg">
          BENQME
        </Link>

        <ul className="hidden md:flex items-center gap-1" role="list">
          {navItems.map(({ label, href }) => (
            <li key={href}>
              <Link
                href={href}
                aria-current={pathname === href ? "page" : undefined}
                className={cn(
                  "px-3 py-2 rounded-md text-sm font-medium transition-colors",
                  pathname === href
                    ? "bg-bg-secondary text-text-primary"
                    : "text-text-secondary hover:text-text-primary hover:bg-bg-secondary"
                )}
              >
                {label}
              </Link>
            </li>
          ))}
        </ul>

        <div className="flex items-center gap-2">
          {/* Theme toggle, auth buttons, etc. */}
        </div>
      </nav>
    </header>
  );
}
```

### Mobile Navigation (Hamburger Drawer)
```tsx
// State
const [isOpen, setIsOpen] = useState(false);

// Close on route change
useEffect(() => setIsOpen(false), [pathname]);

// Markup
<>
  <button
    aria-label={isOpen ? "Close menu" : "Open menu"}
    aria-expanded={isOpen}
    aria-controls="mobile-nav"
    onClick={() => setIsOpen((o) => !o)}
    className="md:hidden"
  >
    {isOpen ? <XIcon /> : <MenuIcon />}
  </button>

  {/* Drawer */}
  <div
    id="mobile-nav"
    role="dialog"
    aria-modal="true"
    aria-label="Mobile navigation"
    className={cn(
      "fixed inset-y-0 right-0 w-72 bg-bg-primary shadow-xl transition-transform",
      isOpen ? "translate-x-0" : "translate-x-full"
    )}
  >
    <nav>
      {navItems.map(({ label, href }) => (
        <Link key={href} href={href}>{label}</Link>
      ))}
    </nav>
  </div>

  {/* Backdrop */}
  {isOpen && (
    <div
      className="fixed inset-0 bg-black/40 z-overlay"
      onClick={() => setIsOpen(false)}
      aria-hidden="true"
    />
  )}
</>
```

---

## Sidebar Navigation

```tsx
// src/components/layout/Sidebar/Sidebar.tsx
const sidebarItems = [
  { label: "Dashboard", href: "/app", icon: HomeIcon },
  { label: "Projects", href: "/app/projects", icon: FolderIcon },
  {
    label: "Settings",
    href: "/app/settings",
    icon: SettingsIcon,
    children: [
      { label: "Profile", href: "/app/settings/profile" },
      { label: "Security", href: "/app/settings/security" },
    ],
  },
];

export function Sidebar() {
  const pathname = usePathname();

  return (
    <nav aria-label="Application navigation">
      <ul role="list" className="space-y-1">
        {sidebarItems.map((item) => (
          <li key={item.href}>
            <Link
              href={item.href}
              aria-current={pathname.startsWith(item.href) ? "page" : undefined}
              className={cn(
                "flex items-center gap-3 px-3 py-2 rounded-md text-sm",
                pathname.startsWith(item.href)
                  ? "bg-brand-subtle text-brand"
                  : "text-text-secondary hover:bg-bg-secondary hover:text-text-primary"
              )}
            >
              <item.icon size={16} aria-hidden="true" />
              {item.label}
            </Link>
            {/* Nested items */}
            {item.children && pathname.startsWith(item.href) && (
              <ul className="mt-1 ml-6 space-y-1">
                {item.children.map((child) => (
                  <li key={child.href}>
                    <Link href={child.href}>{child.label}</Link>
                  </li>
                ))}
              </ul>
            )}
          </li>
        ))}
      </ul>
    </nav>
  );
}
```

---

## Breadcrumbs

```tsx
import Link from "next/link";

interface BreadcrumbItem {
  label: string;
  href?: string;
}

export function Breadcrumbs({ items }: { items: BreadcrumbItem[] }) {
  return (
    <nav aria-label="Breadcrumb">
      <ol className="flex items-center gap-2 text-sm" role="list">
        {items.map((item, index) => (
          <li key={item.href ?? item.label} className="flex items-center gap-2">
            {index > 0 && (
              <ChevronRightIcon size={14} aria-hidden="true" className="text-text-tertiary" />
            )}
            {item.href && index < items.length - 1 ? (
              <Link href={item.href} className="text-text-secondary hover:text-text-primary">
                {item.label}
              </Link>
            ) : (
              <span aria-current="page" className="text-text-primary font-medium">
                {item.label}
              </span>
            )}
          </li>
        ))}
      </ol>
    </nav>
  );
}
```

---

## Tabs

See [`components.md`](./components.md) for the Tabs component API.

```tsx
// URL-based tabs (preserves state on refresh)
const searchParams = useSearchParams();
const activeTab = searchParams.get("tab") ?? "overview";

<Tabs value={activeTab} onValueChange={(tab) => router.push(`?tab=${tab}`)}>
  ...
</Tabs>
```

---

## Pagination

### URL-based pagination
```tsx
interface PaginationProps {
  page: number;
  totalPages: number;
  baseUrl: string;
}

export function Pagination({ page, totalPages, baseUrl }: PaginationProps) {
  return (
    <nav aria-label="Pagination">
      <ul className="flex items-center gap-1">
        <li>
          <Link
            href={`${baseUrl}?page=${page - 1}`}
            aria-disabled={page <= 1}
            aria-label="Previous page"
          >
            <ChevronLeftIcon size={16} />
          </Link>
        </li>
        {/* Page numbers */}
        <li>
          <Link
            href={`${baseUrl}?page=${page + 1}`}
            aria-disabled={page >= totalPages}
            aria-label="Next page"
          >
            <ChevronRightIcon size={16} />
          </Link>
        </li>
      </ul>
    </nav>
  );
}
```

---

## Routing Conventions

| Route | Page |
|-------|------|
| `/` | Home / landing page |
| `/about` | About page |
| `/projects` | Projects index |
| `/projects/[slug]` | Project detail |
| `/writing` | Blog/writing index |
| `/writing/[slug]` | Blog post |
| `/app` | App dashboard (authenticated) |
| `/app/[feature]` | App feature pages |
| `/login` | Login page |
| `/signup` | Sign up page |

---

## Active State Detection

```typescript
// Exact match (home page)
const isActive = pathname === href;

// Prefix match (section pages)
const isActive = pathname.startsWith(href) && href !== "/";

// With Next.js usePathname
import { usePathname } from "next/navigation";
const pathname = usePathname();
```

---

## Accessibility

- Use `<nav>` with `aria-label` to distinguish multiple nav regions
- Mark the current page with `aria-current="page"` on the active link
- Mobile nav that acts as a dialog needs `role="dialog"` and `aria-modal="true"`
- Skip navigation link at the top of the page:

```html
<a href="#main-content" class="sr-only focus:not-sr-only focus:fixed focus:top-4 focus:left-4 focus:z-max focus:p-3 focus:bg-bg-primary focus:rounded-md">
  Skip to main content
</a>
```

- Keyboard: all navigation must be usable with `Tab`, `Enter`, and arrow keys
- Don't open new tabs without warning the user (`aria-label="Opens in new tab"`)
