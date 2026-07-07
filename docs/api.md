# API Reference

> API endpoint documentation, conventions, and client usage.

---

## Table of Contents

1. [Base URL & Authentication](#base-url--authentication)
2. [Request & Response Format](#request--response-format)
3. [Error Format](#error-format)
4. [Endpoints](#endpoints)
5. [Rate Limiting](#rate-limiting)
6. [Versioning](#versioning)
7. [TypeScript Client](#typescript-client)

---

## Base URL & Authentication

```
Base URL: /api
Content-Type: application/json
Accept: application/json
```

### Authentication
Authenticated endpoints require a valid session cookie (managed by Auth.js).

For API-key access (server-to-server):
```
Authorization: ******
```

---

## Request & Response Format

### Request
```http
POST /api/posts HTTP/1.1
Content-Type: application/json

{
  "title": "My Post",
  "content": "Content here",
  "published": false
}
```

### Success Response
```json
{
  "id": "clxyz123",
  "title": "My Post",
  "content": "Content here",
  "published": false,
  "createdAt": "2024-01-15T10:00:00.000Z",
  "updatedAt": "2024-01-15T10:00:00.000Z"
}
```

### List Response
```json
{
  "items": [...],
  "total": 42,
  "nextCursor": "clxyz456",
  "hasMore": true
}
```

---

## Error Format

All errors return a consistent shape:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": {
      "fieldErrors": {
        "email": ["Invalid email address"],
        "name": ["Name is required"]
      },
      "formErrors": []
    }
  }
}
```

### Error Codes

| HTTP Status | Code | Description |
|-------------|------|-------------|
| 400 | `VALIDATION_ERROR` | Request body failed validation |
| 400 | `BAD_REQUEST` | Malformed request |
| 401 | `UNAUTHORIZED` | Not authenticated |
| 403 | `FORBIDDEN` | Authenticated but not authorized |
| 404 | `NOT_FOUND` | Resource doesn't exist |
| 409 | `CONFLICT` | Resource already exists |
| 422 | `UNPROCESSABLE` | Semantically invalid |
| 429 | `RATE_LIMITED` | Too many requests |
| 500 | `INTERNAL_ERROR` | Server error |

---

## Endpoints

### Users

#### GET /api/users
List all users (admin only).

**Query parameters:**
| Param | Type | Description |
|-------|------|-------------|
| `page` | number | Page number (default: 1) |
| `limit` | number | Items per page (default: 20, max: 100) |
| `search` | string | Filter by name or email |

**Response:** `200 OK` — Array of user objects.

---

#### GET /api/users/:id
Get a specific user.

**Response:** `200 OK` — User object.

**Errors:** `404` if user not found, `403` if requesting another user's profile.

---

#### PATCH /api/users/:id
Update a user's profile.

**Body:**
```json
{
  "name": "New Name",
  "bio": "Updated bio"
}
```

**Response:** `200 OK` — Updated user object.

---

### Posts

#### GET /api/posts
List published posts.

**Query parameters:**
| Param | Type | Description |
|-------|------|-------------|
| `cursor` | string | Cursor for pagination |
| `limit` | number | Items per page (default: 20) |
| `tag` | string | Filter by tag |
| `search` | string | Full-text search |

**Response:** `200 OK` — Paginated list.

---

#### POST /api/posts
Create a new post. Requires authentication.

**Body:**
```json
{
  "title": "string (required, max 200)",
  "content": "string (required)",
  "excerpt": "string (optional, max 500)",
  "tags": ["string"] ,
  "published": "boolean (default: false)"
}
```

**Response:** `201 Created` — Created post object.

---

#### GET /api/posts/:slug
Get a post by slug.

**Response:** `200 OK` — Post with author and related posts.

---

#### PATCH /api/posts/:slug
Update a post. Requires ownership.

---

#### DELETE /api/posts/:slug
Delete a post. Requires ownership.

**Response:** `204 No Content`

---

### Auth

#### POST /api/auth/register
Register a new account.

**Body:**
```json
{
  "name": "string",
  "email": "string",
  "password": "string (min 8 chars)"
}
```

**Response:** `201 Created` — User object (session auto-created).

---

#### POST /api/auth/login
Login with email + password.

**Body:**
```json
{
  "email": "string",
  "password": "string"
}
```

**Response:** `200 OK` — Session cookie set.

---

#### POST /api/auth/logout
End the current session.

**Response:** `204 No Content`

---

#### GET /api/auth/me
Get the currently authenticated user.

**Response:** `200 OK` — Session user object, or `401` if not authenticated.

---

### Contact

#### POST /api/contact
Send a contact form message.

**Body:**
```json
{
  "name": "string",
  "email": "string",
  "message": "string (min 10 chars)"
}
```

**Response:** `204 No Content`

**Rate limit:** 5 requests per hour per IP.

---

## Rate Limiting

Rate limit headers are included in all responses:

```http
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 59
X-RateLimit-Reset: 1716220800
```

| Endpoint | Limit |
|----------|-------|
| General API | 60 requests / minute |
| Auth endpoints | 10 requests / minute |
| Contact form | 5 requests / hour |
| Public read endpoints | 100 requests / minute |

When rate limited:
```json
HTTP 429 Too Many Requests
Retry-After: 60

{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Too many requests. Try again in 60 seconds."
  }
}
```

---

## Versioning

The API is currently at v1 (implicit). Future breaking changes will be versioned:

```
/api/v2/posts
```

Non-breaking changes (new fields, new optional params) are made without versioning.

---

## TypeScript Client

```typescript
// src/lib/api.ts

import { z } from "zod";

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
  createdAt: z.string().datetime(),
});

export type User = z.infer<typeof UserSchema>;

export const api = {
  users: {
    get: (id: string) =>
      apiFetch<User>(`/users/${id}`),

    update: (id: string, data: Partial<Pick<User, "name">>) =>
      apiFetch<User>(`/users/${id}`, {
        method: "PATCH",
        body: JSON.stringify(data),
      }),
  },

  posts: {
    list: (params?: { cursor?: string; limit?: number; tag?: string }) =>
      apiFetch<{ items: Post[]; nextCursor: string | null }>(`/posts`, { params }),

    create: (data: CreatePostInput) =>
      apiFetch<Post>(`/posts`, {
        method: "POST",
        body: JSON.stringify(data),
      }),

    delete: (slug: string) =>
      apiFetch<void>(`/posts/${slug}`, { method: "DELETE" }),
  },

  contact: {
    send: (data: ContactInput) =>
      apiFetch<void>(`/contact`, {
        method: "POST",
        body: JSON.stringify(data),
      }),
  },
};
```
