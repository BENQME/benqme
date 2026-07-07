# Components

> Component library reference, patterns, and guidelines.

---

## Table of Contents

1. [Component Philosophy](#component-philosophy)
2. [Atomic Design Structure](#atomic-design-structure)
3. [Primitive Components](#primitive-components)
4. [Composite Components](#composite-components)
5. [Layout Components](#layout-components)
6. [Pattern Components](#pattern-components)
7. [Component API Conventions](#component-api-conventions)
8. [Variants](#variants)
9. [Storybook](#storybook)

---

## Component Philosophy

- **Composable over configurable** — small, focused components that compose well
- **Controlled by default** — components expose full control to consumers
- **Accessible by default** — ARIA, keyboard support, and semantic HTML are non-negotiable
- **Unstyled base + styled variant** — base behavior is separate from visual styling
- **TypeScript-first** — all props are typed; no implicit `any`

---

## Atomic Design Structure

```
src/components/
├── ui/              ← Atoms & molecules (design system primitives)
│   ├── Button/
│   ├── Input/
│   ├── Select/
│   ├── Checkbox/
│   ├── Radio/
│   ├── Switch/
│   ├── Badge/
│   ├── Avatar/
│   ├── Spinner/
│   ├── Skeleton/
│   ├── Separator/
│   ├── Tooltip/
│   ├── Popover/
│   ├── Dialog/
│   ├── Drawer/
│   ├── Dropdown/
│   ├── Tabs/
│   ├── Accordion/
│   ├── Card/
│   └── Table/
│
├── layout/          ← Layout organisms
│   ├── Header/
│   ├── Footer/
│   ├── Sidebar/
│   ├── PageLayout/
│   └── Container/
│
└── [feature]/       ← Feature-specific components (see features/)
```

---

## Primitive Components

### Button

```tsx
import { Button } from "@/components/ui/Button";

// Variants
<Button variant="primary">Submit</Button>
<Button variant="secondary">Cancel</Button>
<Button variant="ghost">Learn more</Button>
<Button variant="danger">Delete</Button>
<Button variant="link">View details</Button>

// Sizes
<Button size="sm">Small</Button>
<Button size="md">Medium (default)</Button>
<Button size="lg">Large</Button>

// States
<Button disabled>Disabled</Button>
<Button loading>Saving…</Button>

// With icon
<Button leftIcon={<PlusIcon />}>Add item</Button>
<Button rightIcon={<ArrowRightIcon />}>Continue</Button>
<Button iconOnly aria-label="Add"><PlusIcon /></Button>

// As link
<Button as="a" href="/dashboard">Go to Dashboard</Button>
```

**Props**: `variant`, `size`, `loading`, `disabled`, `leftIcon`, `rightIcon`, `iconOnly`, `as`, `className`, all native button attributes.

---

### Input

```tsx
import { Input } from "@/components/ui/Input";

<Input
  id="email"
  type="email"
  label="Email address"
  placeholder="you@example.com"
  helperText="We'll never share your email."
  error="Invalid email address"
  required
  disabled
/>
```

**Props**: `label`, `helperText`, `error`, `required`, `disabled`, `size`, all native input attributes.

---

### Badge

```tsx
import { Badge } from "@/components/ui/Badge";

<Badge variant="default">Default</Badge>
<Badge variant="success">Success</Badge>
<Badge variant="warning">Warning</Badge>
<Badge variant="danger">Error</Badge>
<Badge variant="info">Info</Badge>
<Badge variant="outline">Outline</Badge>
```

---

### Avatar

```tsx
import { Avatar } from "@/components/ui/Avatar";

<Avatar src="/images/avatar.jpg" alt="Alice" size="md" />
<Avatar fallback="AB" size="lg" />            {/* initials fallback */}
<Avatar size="sm" />                           {/* generic icon fallback */}

// With status indicator
<Avatar src="/images/avatar.jpg" alt="Alice" status="online" />
```

**Sizes**: `xs` (24px), `sm` (32px), `md` (40px), `lg` (56px), `xl` (80px)

---

### Card

```tsx
import { Card } from "@/components/ui/Card";

// Compound component
<Card>
  <Card.Header>
    <Card.Title>Title</Card.Title>
    <Card.Description>Subtitle or description</Card.Description>
  </Card.Header>
  <Card.Content>
    Body content here.
  </Card.Content>
  <Card.Footer>
    <Button>Action</Button>
  </Card.Footer>
</Card>

// Interactive card (clickable)
<Card as="a" href="/article" hoverable>...</Card>
```

---

### Dialog / Modal

```tsx
import { Dialog } from "@/components/ui/Dialog";

<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <Dialog.Trigger asChild>
    <Button>Open Dialog</Button>
  </Dialog.Trigger>
  <Dialog.Content>
    <Dialog.Title>Confirm Action</Dialog.Title>
    <Dialog.Description>
      This action cannot be undone.
    </Dialog.Description>
    <Dialog.Footer>
      <Button variant="ghost" onClick={() => setIsOpen(false)}>
        Cancel
      </Button>
      <Button variant="danger" onClick={handleConfirm}>
        Delete
      </Button>
    </Dialog.Footer>
  </Dialog.Content>
</Dialog>
```

---

### Tabs

```tsx
import { Tabs } from "@/components/ui/Tabs";

<Tabs defaultValue="overview">
  <Tabs.List>
    <Tabs.Trigger value="overview">Overview</Tabs.Trigger>
    <Tabs.Trigger value="settings">Settings</Tabs.Trigger>
    <Tabs.Trigger value="activity">Activity</Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="overview">...</Tabs.Content>
  <Tabs.Content value="settings">...</Tabs.Content>
  <Tabs.Content value="activity">...</Tabs.Content>
</Tabs>
```

---

## Composite Components

### Form Field

Combines `label`, `input`, `helper text`, and `error message`:

```tsx
import { FormField } from "@/components/ui/FormField";

<FormField
  label="Username"
  name="username"
  error={errors.username?.message}
  required
>
  <Input {...register("username")} />
</FormField>
```

### Data Table

```tsx
import { DataTable } from "@/components/ui/DataTable";

<DataTable
  columns={columns}
  data={users}
  pagination
  sorting
  filtering
  emptyState={<p>No users found.</p>}
/>
```

---

## Layout Components

### Container

```tsx
import { Container } from "@/components/layout/Container";

<Container size="md">      {/* max-width: 768px */}
  <Container size="lg">    {/* max-width: 1024px */}
  <Container size="xl">    {/* max-width: 1280px */}
  <Container fluid>        {/* full-width with padding */}
```

### PageLayout

```tsx
import { PageLayout } from "@/components/layout/PageLayout";

<PageLayout
  header={<Header />}
  sidebar={<Sidebar />}
  footer={<Footer />}
>
  <main>{children}</main>
</PageLayout>
```

---

## Pattern Components

### Empty State

```tsx
import { EmptyState } from "@/components/ui/EmptyState";

<EmptyState
  icon={<FolderIcon />}
  title="No projects yet"
  description="Create your first project to get started."
  action={<Button>New Project</Button>}
/>
```

### Loading Skeleton

```tsx
import { Skeleton } from "@/components/ui/Skeleton";

<Skeleton className="h-4 w-48" />          {/* Text line */}
<Skeleton className="h-32 w-full" />        {/* Card */}
<Skeleton variant="circular" className="h-10 w-10" />  {/* Avatar */}
```

---

## Component API Conventions

### Props Naming
| Pattern | Example |
|---------|---------|
| Boolean props: no `is`/`has` prefix | `disabled`, `loading`, `required` |
| Event handlers: `on` + PascalCase | `onClick`, `onValueChange` |
| Render props: `render` + slot name | `renderHeader`, `renderFooter` |
| Children slot: `children` | `children` |
| Custom class: `className` | `className` |

### Polymorphic `as` Prop
Components that can be rendered as different elements support the `as` prop:
```tsx
<Button as="a" href="/page">Link styled as button</Button>
<Heading as="h2">Looks like h1, semantically h2</Heading>
```

---

## Variants

Use `class-variance-authority` (CVA) for variant management:

```typescript
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva(
  // Base styles
  "inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        primary: "bg-brand text-brand-foreground hover:bg-brand-hover",
        secondary: "bg-bg-secondary text-text-primary hover:bg-bg-tertiary",
        ghost: "hover:bg-bg-secondary hover:text-text-primary",
        danger: "bg-danger text-white hover:bg-red-700",
      },
      size: {
        sm: "h-8 px-3 text-sm",
        md: "h-10 px-4",
        lg: "h-12 px-6 text-lg",
      },
    },
    defaultVariants: {
      variant: "primary",
      size: "md",
    },
  }
);

type ButtonProps = VariantProps<typeof buttonVariants> &
  React.ButtonHTMLAttributes<HTMLButtonElement>;
```

---

## Storybook

```bash
# Start Storybook
pnpm storybook

# Build static Storybook
pnpm build-storybook
```

Every component in `src/components/ui/` must have a Storybook story that:
- Documents all props via Controls
- Shows all variants
- Includes an accessibility check
- Has a `Default` story that works without any props
