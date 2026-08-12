---
description: "Use when working on authentication, sign in / sign up, login, protected routes, session/user checks, middleware, or route redirects. Enforces Clerk as the ONLY auth provider, the protected /dashboard route, homepage-to-dashboard redirect for signed-in users, and modal sign in/up."
name: "Clerk Authentication Rules"
applyTo: ["app/**", "proxy.ts", "middleware.ts", "components/**"]
---

# Clerk Authentication Rules

Authentication in this app is handled **exclusively by Clerk** (`@clerk/nextjs`).
Do not introduce, suggest, or scaffold any other auth method (NextAuth/Auth.js,
Lucia, Passport, custom JWT/session/cookie logic, OAuth handlers, credentials
tables, etc.). All auth flows go through Clerk.

## Provider Setup

- `<ClerkProvider>` wraps the app inside `<body>` in [app/layout.tsx](../../app/layout.tsx).
- Read auth state on the server with `auth()` from `@clerk/nextjs/server`.
- Middleware lives in `proxy.ts` (Next.js 16), not `middleware.ts`.

## Protected Routes

`/dashboard` (and everything under it) is a protected route — the user **must be
signed in** to access it. Protect it in the middleware:

```ts
// proxy.ts
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server'

const isProtectedRoute = createRouteMatcher(['/dashboard(.*)'])

export default clerkMiddleware(async (auth, req) => {
  if (isProtectedRoute(req)) {
    await auth.protect()
  }
})
```

When adding new protected pages, extend the `createRouteMatcher` list rather than
adding per-page auth checks as the primary guard.

## Redirects

Signed-in users must not see the marketing homepage — redirect them to `/dashboard`:

```tsx
// app/page.tsx (Server Component)
import { redirect } from "next/navigation";
import { auth } from "@clerk/nextjs/server";

export default async function Home() {
  const { userId } = await auth();
  if (userId) redirect("/dashboard");
  // ...render public homepage
}
```

## Sign In / Sign Up UI

Sign in and sign up must **always launch as a modal** — never as a full-page
redirect. Always pass `mode="modal"`:

```tsx
import { SignInButton, SignUpButton } from "@clerk/nextjs";

<SignInButton mode="modal" />
<SignUpButton mode="modal" />
```

Do not remove `mode="modal"` or replace these with links to standalone
`/sign-in` or `/sign-up` pages.
