# Security

> Security guidelines, best practices, and vulnerability prevention for this project.

---

## Table of Contents

1. [Security Principles](#security-principles)
2. [Input Validation](#input-validation)
3. [Authentication & Authorization](#authentication--authorization)
4. [API Security](#api-security)
5. [Data Protection](#data-protection)
6. [Cross-Site Scripting (XSS)](#cross-site-scripting-xss)
7. [Injection Attacks](#injection-attacks)
8. [HTTP Security Headers](#http-security-headers)
9. [Dependency Security](#dependency-security)
10. [Secrets Management](#secrets-management)
11. [Security Checklist](#security-checklist)
12. [Incident Response](#incident-response)

---

## Security Principles

1. **Defense in depth** — multiple layers of security; no single point of failure
2. **Least privilege** — services, users, and tokens get only the access they need
3. **Fail securely** — on error, default to denying access, not granting it
4. **Validate all input** — trust nothing from the client
5. **Never trust user data** — sanitize and validate before use or display

---

## Input Validation

All API route inputs must be validated with Zod before use:

```typescript
import { z } from "zod";

const CreatePostSchema = z.object({
  title: z.string().min(1, "Title is required").max(200, "Title too long"),
  content: z.string().min(1).max(50000),
  tags: z.array(z.string().max(50)).max(10).optional(),
  published: z.boolean().default(false),
});

// In your route handler:
const parsed = CreatePostSchema.safeParse(await request.json());
if (!parsed.success) {
  return NextResponse.json(
    { error: "Validation failed", details: parsed.error.flatten() },
    { status: 400 }
  );
}
// parsed.data is now safe to use
```

**Never use raw `request.json()` directly in business logic.**

---

## Authentication & Authorization

### Authentication
- All authenticated routes must verify the session before executing
- Use Auth.js middleware for route-level protection
- JWT tokens expire after 24 hours (access) and 7 days (refresh)

```typescript
// Always check auth first in API routes
export async function GET(request: NextRequest) {
  const session = await auth();
  if (!session?.user) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }
  // ...
}
```

### Authorization
- Check permissions, not just authentication
- Verify resource ownership before returning data

```typescript
// ✅ Check ownership
const post = await db.post.findUnique({ where: { id: params.id } });
if (!post) return notFound();
if (post.authorId !== session.user.id) {
  return NextResponse.json({ error: "Forbidden" }, { status: 403 });
}
```

### Password Security
- Hash passwords with bcrypt (cost factor ≥ 12)
- Never store plaintext passwords
- Never log passwords, even in development

---

## API Security

### Rate Limiting
Apply rate limiting to all public and auth endpoints:

```typescript
import { Ratelimit } from "@upstash/ratelimit";
import { Redis } from "@upstash/redis";

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, "10 s"),
});

export async function POST(request: NextRequest) {
  const ip = request.ip ?? "anonymous";
  const { success } = await ratelimit.limit(ip);
  if (!success) {
    return NextResponse.json({ error: "Too many requests" }, { status: 429 });
  }
  // ...
}
```

### CORS
Configure CORS explicitly — don't allow all origins in production:

```typescript
// next.config.ts
headers: async () => [
  {
    source: "/api/:path*",
    headers: [
      { key: "Access-Control-Allow-Origin", value: "https://yourdomain.com" },
      { key: "Access-Control-Allow-Methods", value: "GET,POST,PATCH,DELETE" },
    ],
  },
],
```

---

## Data Protection

### Sensitive Data in Responses
Never include sensitive fields in API responses:

```typescript
// ✅ Exclude password hash and internal fields
const { password, internalNotes, ...safeUser } = user;
return NextResponse.json(safeUser);

// Or use Prisma's select to only fetch what's needed
const user = await db.user.findUnique({
  where: { id },
  select: { id: true, name: true, email: true, createdAt: true },
});
```

### PII Handling
- Don't log personally identifiable information (PII)
- Encrypt sensitive data at rest using database-level encryption
- Use environment-specific data: dev environments must not use production PII

---

## Cross-Site Scripting (XSS)

React escapes output by default. **Never use `dangerouslySetInnerHTML`** with user-controlled data:

```tsx
// ❌ XSS vulnerability
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ Safe — React escapes this
<div>{userInput}</div>

// ✅ If you must render HTML (e.g., from a trusted CMS), sanitize first
import DOMPurify from "dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(trustedHtml) }} />
```

---

## Injection Attacks

### SQL Injection
Prisma uses parameterized queries automatically:
```typescript
// ✅ Safe — Prisma parameterizes this
const user = await db.user.findUnique({ where: { email: userInput } });

// ❌ Never build raw SQL from user input
await db.$queryRawUnsafe(`SELECT * FROM users WHERE email = '${userInput}'`);
```

If using `$queryRaw`, use template literals:
```typescript
// ✅ Safe
await db.$queryRaw`SELECT * FROM users WHERE email = ${userInput}`;
```

### Command Injection
Never pass user input to shell commands:
```typescript
// ❌ Extremely dangerous
import { exec } from "child_process";
exec(`convert ${userInput} output.png`);

// ✅ Use safe APIs instead
import sharp from "sharp";
await sharp(buffer).resize(800).toFile("output.png");
```

---

## HTTP Security Headers

Configure in `next.config.ts`:

```typescript
const securityHeaders = [
  { key: "X-DNS-Prefetch-Control", value: "on" },
  { key: "Strict-Transport-Security", value: "max-age=63072000; includeSubDomains; preload" },
  { key: "X-Frame-Options", value: "SAMEORIGIN" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
  { key: "Permissions-Policy", value: "camera=(), microphone=(), geolocation=()" },
  {
    key: "Content-Security-Policy",
    value: [
      "default-src 'self'",
      "script-src 'self' 'unsafe-inline'",  // tighten in production
      "style-src 'self' 'unsafe-inline'",
      "img-src 'self' data: https:",
      "font-src 'self'",
      "connect-src 'self' https://api.example.com",
    ].join("; "),
  },
];
```

---

## Dependency Security

```bash
# Audit for known vulnerabilities
pnpm audit

# Fix automatically where possible
pnpm audit --fix

# Check for outdated packages
pnpm outdated
```

- Run `pnpm audit` in CI on every PR
- Review all new dependencies before adding (see `checklist.md`)
- Subscribe to GitHub security advisories for key dependencies

---

## Secrets Management

### Rules
- **Never** commit secrets to git (API keys, passwords, tokens)
- **Never** expose server-side secrets to the client (no `NEXT_PUBLIC_` prefix on secrets)
- Use `.env.local` for local development (gitignored)
- Use Vercel Environment Variables / platform secrets for production

### Scanning
```bash
# Scan for accidental secret commits
npx git-secrets --scan
# Or use truffleHog
npx trufflehog git file://. --only-verified
```

### Rotation
If a secret is accidentally committed:
1. Immediately revoke/rotate the secret in the provider dashboard
2. Remove from git history using `git filter-repo` or BFG
3. Force-push to all branches
4. Notify team and review access logs

---

## Security Checklist

- [ ] All inputs validated with Zod before use
- [ ] All API routes check authentication
- [ ] Authorization verifies resource ownership
- [ ] No sensitive data in API responses or logs
- [ ] No `dangerouslySetInnerHTML` with user data
- [ ] Parameterized queries used (no string interpolation in SQL)
- [ ] Rate limiting on auth and public mutation endpoints
- [ ] Security headers configured
- [ ] Dependencies audited (`pnpm audit`)
- [ ] No secrets in source code or git history
- [ ] Environment variables validated at startup
- [ ] CORS configured for production origins only

---

## Incident Response

If a security vulnerability is discovered:

1. **Do not** disclose publicly until mitigated
2. Assess severity and impact
3. Notify maintainers privately
4. Implement a fix in a private branch
5. Deploy the fix
6. Disclose and update `CHANGELOG.md`
7. Review for related vulnerabilities

**Report vulnerabilities** by opening a private security advisory on GitHub.
