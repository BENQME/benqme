# Forms

> Form design patterns, validation, accessibility, and implementation guide.

---

## Table of Contents

1. [Form Philosophy](#form-philosophy)
2. [Form Structure](#form-structure)
3. [Field Types](#field-types)
4. [Validation](#validation)
5. [Error Handling](#error-handling)
6. [Loading & Success States](#loading--success-states)
7. [Accessibility](#accessibility)
8. [React Hook Form + Zod](#react-hook-form--zod)
9. [Form Patterns](#form-patterns)

---

## Form Philosophy

- **Progressive disclosure** — ask for only what's needed, when it's needed
- **Inline validation** — validate on blur; show errors immediately, not only on submit
- **Clear feedback** — every state (loading, error, success) must be communicated
- **Accessible by default** — every input has a label; errors are linked to inputs

---

## Form Structure

```
<form>
  ├── [Form section heading]
  ├── <FormField>
  │     ├── <label>
  │     ├── <input> / <select> / <textarea>
  │     ├── [helper text]
  │     └── [error message]
  ├── <FormField>
  │     └── ...
  └── <footer>
        ├── [Cancel button]
        └── [Submit button]
```

---

## Field Types

### Text Input
```tsx
<FormField label="Full name" name="name" required error={errors.name}>
  <Input
    id="name"
    type="text"
    placeholder="Jane Smith"
    autoComplete="name"
    {...register("name")}
  />
</FormField>
```

### Email
```tsx
<FormField label="Email address" name="email" required error={errors.email}>
  <Input
    id="email"
    type="email"
    placeholder="you@example.com"
    autoComplete="email"
    inputMode="email"
    {...register("email")}
  />
</FormField>
```

### Password
```tsx
<FormField label="Password" name="password" required error={errors.password}>
  <PasswordInput
    id="password"
    autoComplete="new-password"
    {...register("password")}
  />
</FormField>
```

### Textarea
```tsx
<FormField label="Message" name="message" error={errors.message}>
  <Textarea
    id="message"
    rows={4}
    placeholder="Tell us more..."
    {...register("message")}
  />
</FormField>
```

### Select
```tsx
<FormField label="Country" name="country" required error={errors.country}>
  <Select id="country" {...register("country")}>
    <option value="">Select a country</option>
    <option value="us">United States</option>
    <option value="gb">United Kingdom</option>
  </Select>
</FormField>
```

### Checkbox
```tsx
<FormField name="terms" error={errors.terms} horizontal>
  <Checkbox id="terms" {...register("terms")} />
  <label htmlFor="terms">
    I agree to the <a href="/terms">Terms of Service</a>
  </label>
</FormField>
```

### Radio Group
```tsx
<fieldset>
  <legend className="text-sm font-medium">Notification preference</legend>
  <div className="space-y-2 mt-2">
    {["email", "sms", "none"].map((value) => (
      <label key={value} className="flex items-center gap-2">
        <input type="radio" value={value} {...register("notifications")} />
        {value.charAt(0).toUpperCase() + value.slice(1)}
      </label>
    ))}
  </div>
  {errors.notifications && (
    <span role="alert" className="text-danger text-sm">
      {errors.notifications.message}
    </span>
  )}
</fieldset>
```

---

## Validation

### Zod Schema
```typescript
import { z } from "zod";

export const SignUpSchema = z
  .object({
    name: z
      .string()
      .min(1, "Name is required")
      .max(100, "Name is too long"),

    email: z
      .string()
      .email("Enter a valid email address"),

    password: z
      .string()
      .min(8, "Password must be at least 8 characters")
      .regex(/[A-Z]/, "Must contain at least one uppercase letter")
      .regex(/[0-9]/, "Must contain at least one number"),

    confirmPassword: z.string(),

    terms: z
      .boolean()
      .refine((val) => val === true, "You must accept the terms"),
  })
  .refine(
    (data) => data.password === data.confirmPassword,
    {
      message: "Passwords do not match",
      path: ["confirmPassword"],
    }
  );

export type SignUpInput = z.infer<typeof SignUpSchema>;
```

### Validation Timing
| Event | Action |
|-------|--------|
| `onChange` | Clear error if field was previously invalid |
| `onBlur` | Validate the field |
| `onSubmit` | Validate all fields |

---

## Error Handling

### Field-level errors
```tsx
{errors.email && (
  <p
    id="email-error"
    role="alert"
    className="mt-1.5 text-sm text-danger flex items-center gap-1"
  >
    <AlertCircleIcon size={12} aria-hidden="true" />
    {errors.email.message}
  </p>
)}
```

### Form-level errors (server errors)
```tsx
{formError && (
  <div role="alert" className="p-4 rounded-lg bg-danger-bg border border-danger-border">
    <p className="text-sm text-danger-text font-medium">{formError}</p>
  </div>
)}
```

---

## Loading & Success States

### Submit button states
```tsx
<Button
  type="submit"
  loading={isSubmitting}
  disabled={isSubmitting || !isValid}
>
  {isSubmitting ? "Saving…" : "Save changes"}
</Button>
```

### Success state
```tsx
{isSubmitSuccessful && (
  <div role="status" className="p-4 rounded-lg bg-success-bg border border-success-border">
    <p className="text-sm text-success-text">
      ✓ Your changes have been saved.
    </p>
  </div>
)}
```

---

## Accessibility

- Every `<input>`, `<select>`, and `<textarea>` must have an associated `<label>`
- Use `htmlFor` + `id` to associate labels programmatically
- Required fields: use `aria-required="true"` and a visual indicator (`*`)
- Error messages: link with `aria-describedby` on the input
- Error role: use `role="alert"` for immediate announcement on screen readers
- Group related fields with `<fieldset>` + `<legend>`
- Don't disable submit buttons before the user has tried to submit — it's confusing

```tsx
// Accessible field pattern
<div>
  <label htmlFor="email">
    Email address
    <span aria-hidden="true" className="text-danger ml-0.5">*</span>
  </label>
  <input
    id="email"
    type="email"
    required
    aria-required="true"
    aria-describedby={errors.email ? "email-error" : undefined}
    aria-invalid={!!errors.email}
  />
  {errors.email && (
    <p id="email-error" role="alert">{errors.email.message}</p>
  )}
</div>
```

---

## React Hook Form + Zod

### Complete Form Example
```tsx
"use client";

import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { useState } from "react";

const ContactSchema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Enter a valid email"),
  message: z.string().min(10, "Message must be at least 10 characters"),
});

type ContactInput = z.infer<typeof ContactSchema>;

export function ContactForm() {
  const [serverError, setServerError] = useState<string | null>(null);
  const [success, setSuccess] = useState(false);

  const {
    register,
    handleSubmit,
    reset,
    formState: { errors, isSubmitting, isValid },
  } = useForm<ContactInput>({
    resolver: zodResolver(ContactSchema),
    mode: "onBlur",
  });

  const onSubmit = async (data: ContactInput) => {
    setServerError(null);
    try {
      const res = await fetch("/api/contact", {
        method: "POST",
        body: JSON.stringify(data),
        headers: { "Content-Type": "application/json" },
      });
      if (!res.ok) throw new Error(await res.text());
      setSuccess(true);
      reset();
    } catch {
      setServerError("Something went wrong. Please try again.");
    }
  };

  if (success) {
    return <div role="status">Message sent! We'll get back to you soon.</div>;
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} noValidate>
      {serverError && <div role="alert">{serverError}</div>}

      <div>
        <label htmlFor="name">Name *</label>
        <input id="name" {...register("name")} aria-describedby={errors.name ? "name-error" : undefined} />
        {errors.name && <p id="name-error" role="alert">{errors.name.message}</p>}
      </div>

      <div>
        <label htmlFor="email">Email *</label>
        <input id="email" type="email" {...register("email")} aria-describedby={errors.email ? "email-error" : undefined} />
        {errors.email && <p id="email-error" role="alert">{errors.email.message}</p>}
      </div>

      <div>
        <label htmlFor="message">Message *</label>
        <textarea id="message" rows={5} {...register("message")} aria-describedby={errors.message ? "message-error" : undefined} />
        {errors.message && <p id="message-error" role="alert">{errors.message.message}</p>}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Sending…" : "Send message"}
      </button>
    </form>
  );
}
```

---

## Form Patterns

### Multi-step Form
```tsx
const steps = ["Personal", "Contact", "Review"];
const [step, setStep] = useState(0);

// Validate only current step fields before proceeding
const handleNext = async () => {
  const isValid = await trigger(stepFields[step]);
  if (isValid) setStep((s) => s + 1);
};
```

### Optimistic Update
```tsx
const mutation = useMutation({
  mutationFn: updateUser,
  onMutate: (newData) => {
    // Optimistically update UI immediately
    queryClient.setQueryData(["user"], (old) => ({ ...old, ...newData }));
  },
  onError: () => {
    // Roll back on error
    queryClient.invalidateQueries({ queryKey: ["user"] });
  },
});
```
