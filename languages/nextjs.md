# Next.js — Detailed Notes & Interview Guide

> 📌 **Focus:** Next.js App Router, rendering, routing, data fetching, caching, Server Components, Server Actions, performance, deployment, security, and interview preparation.

---

## 1. What is Next.js?

Next.js is a **React framework** for building full-stack web applications. It provides conventions and infrastructure around React for routing, rendering, data access, optimization, metadata, API endpoints, and deployment.

A useful mental model:

```text
                         Next.js application
                                |
          +---------------------+---------------------+
          |                     |                     |
       Routing              Rendering              Server
          |                     |                     |
    App Router          Server Components      Route Handlers
    layouts             Client Components      Server Actions
    dynamic routes      SSR / prerendering      Middleware/proxy
          |                     |                     |
          +---------------------+---------------------+
                                |
                         Browser + CDN + Server
```

### Why Next.js?

- File-system based routing.
- Nested layouts and loading/error UI.
- React Server Components through the App Router.
- Server-side data access without exposing secrets to the browser.
- Static generation and dynamic rendering.
- Streaming with Suspense.
- Built-in image, font, metadata, and script optimizations.
- Route Handlers for HTTP APIs.
- Server Actions for server-side mutations.
- Strong TypeScript support.
- Flexible deployment: Node.js server, containers, serverless, or static output for compatible apps.

### Next.js vs React

React is the UI library. Next.js is the application framework around React.

| React | Next.js |
|---|---|
| UI/component library | Full application framework |
| Does not define routing | File-system routing |
| Rendering architecture is chosen by app | Provides server/client rendering patterns |
| Data fetching is application-defined | Provides framework-integrated server data patterns |
| Build/deployment choices are broad | Provides Next-specific build/runtime tooling |

---

## 2. App Router vs Pages Router

Modern Next.js applications generally use the **App Router** under `app/`.

```text
app/
├── layout.tsx
├── page.tsx
├── loading.tsx
├── error.tsx
├── not-found.tsx
├── users/
│   ├── page.tsx
│   └── [id]/
│       └── page.tsx
└── api/
    └── users/
        └── route.ts
```

The older **Pages Router** uses `pages/` and APIs such as `getServerSideProps` and `getStaticProps`.

### Interview rule

Know both, but when discussing current Next.js development, lead with the App Router unless the project explicitly uses `pages/`.

---

## 3. File-System Routing

A file under `app/` becomes part of the URL structure.

```text
app/page.tsx              -> /
app/about/page.tsx        -> /about
app/blog/page.tsx         -> /blog
app/blog/[slug]/page.tsx  -> /blog/:slug
```

### Dynamic segments

```text
app/products/[id]/page.tsx
```

```tsx
export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;
  return <h1>Product {id}</h1>;
}
```

### Catch-all routes

```text
app/docs/[...slug]/page.tsx
```

Matches `/docs/a`, `/docs/a/b`, etc.

Optional catch-all:

```text
app/docs/[[...slug]]/page.tsx
```

Can also match `/docs`.

---

## 4. Layouts

Layouts persist across navigation and are ideal for shared UI.

```tsx
// app/layout.tsx
export default function RootLayout({
  children,
}: Readonly<{ children: React.ReactNode }>) {
  return (
    <html lang="en">
      <body>
        <Header />
        {children}
      </body>
    </html>
  );
}
```

Nested layout:

```text
app/
├── layout.tsx
├── page.tsx
└── dashboard/
    ├── layout.tsx
    └── page.tsx
```

The dashboard layout wraps dashboard pages while the root layout remains above it.

### Why layouts matter

- Shared navigation.
- Persistent state/UI boundaries.
- Avoid repeating expensive setup.
- Natural nesting of application sections.

---

## 5. Special Files in the App Router

| File | Purpose |
|---|---|
| `page.tsx` | Route UI |
| `layout.tsx` | Persistent shared UI |
| `loading.tsx` | Loading UI / Suspense boundary |
| `error.tsx` | Error boundary UI |
| `global-error.tsx` | Root-level error boundary |
| `not-found.tsx` | 404 UI |
| `template.tsx` | Re-created UI boundary on navigation |
| `default.tsx` | Fallback for parallel route slots |
| `route.ts` | Route Handler |
| `sitemap.ts` | Sitemap metadata route |
| `robots.ts` | Robots metadata route |
| `opengraph-image.*` | Open Graph image |

---

## 6. Server Components vs Client Components

In the App Router, components are **Server Components by default**.

Use a Client Component when you need browser-only behavior such as:

- `useState`.
- `useEffect`.
- Event handlers.
- Browser APIs.
- Client-side subscriptions.

Add:

```tsx
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Important distinction

`'use client'` does **not** mean "this component never runs on the server." It defines a client component boundary and determines which module graph is sent to the client. Client components can participate in server rendering/pre-rendering, but their interactive JavaScript is hydrated in the browser.

### Server Component advantages

- Direct access to server-side resources.
- Secrets can stay server-side.
- Less client JavaScript.
- Good default for data-heavy UI.

### Client Component cost

Once a module is part of a client boundary, its dependencies may contribute to the client bundle. Keep client boundaries as small as practical.

---

## 7. Composition Pattern

Prefer keeping the page/server tree server-side and placing interactive islands at the leaves.

```tsx
// Server Component
import LikeButton from './LikeButton';

export default async function PostPage() {
  const post = await getPost();

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
      <LikeButton postId={post.id} />
    </article>
  );
}
```

This avoids making the entire page a Client Component just because one button needs state.

---

## 8. Data Fetching in Server Components

Server Components can fetch data directly.

```tsx
export default async function UsersPage() {
  const response = await fetch('https://api.example.com/users');
  if (!response.ok) throw new Error('Failed to load users');

  const users = await response.json();

  return (
    <ul>
      {users.map((user: { id: string; name: string }) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

You can also call a database/SDK directly from trusted server-side code when the deployment environment supports it.

### Avoid this anti-pattern

Do not make a Server Component call your own Route Handler merely because the Route Handler exists.

```text
Server Component -> Route Handler -> database
```

Often this adds an unnecessary HTTP hop. Prefer:

```text
Server Component -> shared server data function -> database
```

Use Route Handlers when an actual HTTP endpoint is needed by clients or external systems.

---

## 9. Caching and Revalidation

Caching is one of the most important and most frequently misunderstood Next.js topics.

Think about four separate concerns:

1. **Request/data caching** — whether fetched data can be reused.
2. **Revalidation** — when cached data should become stale/refreshed.
3. **Full route/render caching** — whether a rendered route result can be reused.
4. **Client Router Cache** — browser-side reuse of prefetched/navigated route segments.

The exact defaults and APIs have evolved across Next.js releases, so do not memorize old Pages Router rules and apply them blindly to current App Router applications.

### Explicit caching intent

For data that can be revalidated:

```tsx
const data = await fetch('https://api.example.com/posts', {
  next: { revalidate: 60 },
});
```

For request-time data, use an explicit uncached/dynamic strategy appropriate to the current Next.js version and application.

### Cache invalidation

Common concepts include:

- `revalidatePath()` — invalidate/revalidate cached data associated with a path.
- `revalidateTag()` — invalidate data associated with a cache tag.
- `updateTag()` — immediately expire tagged data in supported current Next.js flows.
- `refresh()` — refresh the current route from a Server Action.

The important interview answer is:

> Caching is not one global switch. Next.js has multiple caching layers, and the correct invalidation strategy depends on whether the data, rendered route, or client navigation state is stale.

---

## 10. Cache Tags

Tag related data so mutations can invalidate it without knowing every URL that consumes it.

```tsx
const response = await fetch('https://api.example.com/posts', {
  next: { tags: ['posts'] },
});
```

After a mutation:

```tsx
import { revalidateTag } from 'next/cache';

export async function refreshPosts() {
  revalidateTag('posts');
}
```

Use stable domain-oriented tags such as:

```text
posts
post:123
user:42
products
products:category:shoes
```

Avoid uncontrolled tag cardinality.

---

## 11. Static vs Dynamic Rendering

A route can be pre-rendered when its output does not need request-specific information at render time.

### Static-like workloads

Good candidates:

- Marketing pages.
- Documentation.
- Public product pages with controlled revalidation.
- Content that changes periodically.

### Dynamic workloads

Examples:

- Personalized dashboards.
- Request-authenticated pages.
- Content that depends on request-specific headers/cookies.
- Real-time request-time information.

### Interview answer

Do not reduce the topic to "SSR versus SSG." Modern Next.js combines server rendering, pre-rendering, caching, revalidation, streaming, and client hydration. The useful question is: **what can be computed ahead of time, what must be request-specific, and what can be cached safely?**

---

## 12. Streaming and Suspense

Streaming lets the server send available UI progressively instead of waiting for the slowest piece.

```tsx
import { Suspense } from 'react';

export default function Dashboard() {
  return (
    <main>
      <Header />
      <Suspense fallback={<RevenueSkeleton />}>
        <Revenue />
      </Suspense>
    </main>
  );
}
```

```tsx
async function Revenue() {
  const revenue = await getRevenue();
  return <div>{revenue.total}</div>;
}
```

### Why streaming helps

Without streaming:

```text
request -> slow data -> render everything -> response
```

With streaming:

```text
request -> shell -> fast UI -> slow section when ready
```

This can improve perceived performance and Time to First Byte/First Contentful Paint behavior depending on the application.

---

## 13. `loading.tsx`

A route-level loading file provides an automatic loading boundary.

```tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return <DashboardSkeleton />;
}
```

It is useful for navigation feedback while route content is loading.

---

## 14. Error Handling

An `error.tsx` file defines a route-level error boundary.

```tsx
'use client';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <section>
      <h2>Something went wrong.</h2>
      <button onClick={() => reset()}>Try again</button>
    </section>
  );
}
```

### Expected vs unexpected errors

Handle expected validation/business errors as data when appropriate. Reserve error boundaries for unexpected failures.

Never expose sensitive server exception details to users.

---

## 15. `notFound()`

Use `notFound()` when a resource does not exist.

```tsx
import { notFound } from 'next/navigation';

export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;
  const product = await getProduct(id);

  if (!product) notFound();

  return <h1>{product.name}</h1>;
}
```

This is preferable to rendering a successful-looking page with missing data.

---

## 16. Metadata and SEO

Static metadata:

```tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Products',
  description: 'Browse our products',
};
```

Dynamic metadata:

```tsx
import type { Metadata } from 'next';

export async function generateMetadata({
  params,
}: {
  params: Promise<{ id: string }>;
}): Promise<Metadata> {
  const { id } = await params;
  const product = await getProduct(id);

  return {
    title: product?.name ?? 'Product',
    description: product?.description,
  };
}
```

### SEO checklist

- Unique titles/descriptions.
- Correct canonical URLs when needed.
- Semantic HTML.
- Crawlable content.
- Sitemap.
- Robots rules.
- Open Graph/Twitter metadata.
- Correct status codes for missing pages.
- Good Core Web Vitals.

---

## 17. Route Handlers

Route Handlers implement HTTP endpoints in the App Router.

```tsx
// app/api/users/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
  const users = await getUsers();
  return NextResponse.json(users);
}

export async function POST(request: Request) {
  const body = await request.json();
  const user = await createUser(body);
  return NextResponse.json(user, { status: 201 });
}
```

Supported methods commonly include:

```text
GET POST PUT PATCH DELETE HEAD OPTIONS
```

### When to use Route Handlers

- Public/internal HTTP API.
- Webhook receiver.
- Browser/client API endpoint.
- Integration with an external service.
- Authentication callback endpoint.

### When not to use them

Do not create an internal API endpoint just to make server-side code call itself. Share the underlying service/data function instead.

---

## 18. Server Actions / Server Functions

Server Actions allow server-side functions to be invoked from supported React/Next.js flows.

```tsx
'use server';

import { revalidatePath } from 'next/cache';

export async function createPost(formData: FormData) {
  const title = String(formData.get('title') ?? '').trim();
  if (!title) throw new Error('Title is required');

  await db.post.create({ data: { title } });
  revalidatePath('/posts');
}
```

Use with a form:

```tsx
<form action={createPost}>
  <input name="title" />
  <button type="submit">Create</button>
</form>
```

### Security rule

A Server Action is not a security boundary by itself. Treat its inputs as untrusted and perform authorization and validation inside the server function.

---

## 19. Forms and Server Mutations

A robust form flow typically looks like:

```text
User submits form
       |
       v
Client/browser
       |
       v
Server Function / Route Handler
       |
       +--> validate input
       +--> authenticate
       +--> authorize
       +--> mutate database
       +--> invalidate relevant cache
       +--> return safe result
```

Use schema validation for complex inputs and keep validation on the server even if the client validates too.

---

## 20. `useActionState`

React's action state APIs can model form submission state and returned results.

Conceptually:

```tsx
'use client';

import { useActionState } from 'react';
import { createUser } from './actions';

export function UserForm() {
  const [state, action, pending] = useActionState(createUser, {
    error: null,
  });

  return (
    <form action={action}>
      <input name="email" />
      <button disabled={pending}>
        {pending ? 'Saving...' : 'Save'}
      </button>
      {state.error && <p>{state.error}</p>}
    </form>
  );
}
```

The exact action signature depends on the React/Next.js version and chosen API. The important idea is to model mutation state rather than manually synchronizing multiple loading flags.

---

## 21. `useFormStatus`

`useFormStatus()` lets nested Client Components read the status of a parent form submission.

```tsx
'use client';

import { useFormStatus } from 'react-dom';

export function SubmitButton() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>{pending ? 'Saving...' : 'Save'}</button>;
}
```

This is useful for reusable submit buttons and pending indicators.

---

## 22. Authentication Architecture

Authentication answers **who is the user?**

Authorization answers **what may the user do?**

A secure Next.js application must enforce authorization on the server.

```text
Browser
  |
  v
Request
  |
  +--> session/token verification
  |
  +--> authorization check
  |
  +--> server data access
```

### Do not rely on

```tsx
if (!user) return <Login />;
```

alone. Hiding UI is not authorization.

A user can still call an endpoint or mutate data directly. Protect the server-side operation.

---

## 23. Cookies

Server-side code can read cookies using Next.js cookie APIs appropriate to the current runtime/version.

Important properties:

- `HttpOnly` — prevents JavaScript access.
- `Secure` — send over HTTPS.
- `SameSite` — controls cross-site cookie behavior.
- Short, appropriate expiration.
- Narrow `Path`/`Domain` when possible.

For authentication sessions, prefer secure, server-validated session designs rather than storing sensitive long-lived credentials in `localStorage`.

---

## 24. Middleware / Proxy Concepts

Next.js supports request interception for use cases such as:

- Redirects.
- Locale routing.
- Lightweight request checks.
- Rewriting.
- Edge/request preprocessing where supported.

However, request interception should not become a giant authorization/data-access layer.

Keep expensive database work and business authorization in the appropriate server-side service/action/handler.

### Interview caution

Next.js has changed terminology and runtime capabilities around middleware/proxy in recent releases. Always check the version-specific documentation before stating that a particular API name or runtime behavior is universal.

---

## 25. Environment Variables

Server-only environment variables:

```env
DATABASE_URL=...
API_SECRET=...
```

Variables intended for browser/client code typically use:

```env
NEXT_PUBLIC_API_URL=...
```

### Security rule

Never put secrets in `NEXT_PUBLIC_*` variables. Anything exposed to client code must be considered public.

Also remember that secrets can leak through:

- Client bundles.
- Logs.
- Error messages.
- Source maps/configuration.
- Serialized props.
- Accidentally returned API responses.

---

## 26. Images

Use `next/image` where appropriate.

```tsx
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Product hero"
  width={1200}
  height={700}
/>
```

Benefits include optimized image delivery, sizing behavior, and lazy loading in appropriate cases.

### Accessibility

Always provide meaningful `alt` text for informative images. Decorative images should use an empty alt value when appropriate.

---

## 27. Fonts

Next.js provides font tooling through `next/font`.

```tsx
import { Inter } from 'next/font/google';

const inter = Inter({ subsets: ['latin'] });

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

Benefits can include optimized loading and reduced layout shifting compared with ad-hoc font loading.

---

## 28. Scripts

For third-party scripts, use `next/script` when its loading strategy and lifecycle behavior are useful.

```tsx
import Script from 'next/script';

<Script
  src="https://example.com/analytics.js"
  strategy="afterInteractive"
/>
```

Do not blindly add third-party scripts. Every script increases complexity, privacy/security risk, and potentially performance cost.

---

## 29. Parallel Routes

Parallel routes allow multiple route segments to render into named slots.

Conceptually:

```text
app/dashboard/
├── layout.tsx
├── @analytics/
│   └── page.tsx
├── @team/
│   └── page.tsx
└── page.tsx
```

Layout can receive slots:

```tsx
export default function Layout({
  children,
  analytics,
  team,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <>
      {children}
      {analytics}
      {team}
    </>
  );
}
```

Useful for dashboards, modal routes, and independently navigable UI areas.

---

## 30. Intercepting Routes

Intercepting routes let a route render another route in a different UI context, commonly for modal patterns.

Typical use case:

```text
/feed
/feed/photo/123
```

A photo can open as a modal over the feed when navigated from the feed, while direct navigation to `/photo/123` can render the full page.

This is a routing composition feature, not a replacement for ordinary component state.

---

## 31. Route Groups

Parentheses create route groups without adding a URL segment.

```text
app/
├── (marketing)/
│   ├── about/page.tsx
│   └── pricing/page.tsx
└── (app)/
    └── dashboard/page.tsx
```

Useful for organizing large applications and applying different layouts.

---

## 32. `generateStaticParams`

For dynamic routes that can be pre-generated, provide known parameters.

```tsx
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map((post) => ({ slug: post.slug }));
}
```

This is especially useful for content-heavy pages where a finite set of routes can be generated ahead of time.

Do not confuse this with a universal guarantee that a route never needs runtime rendering; actual behavior depends on the route's other data and configuration.

---

## 33. `generateMetadata` and Dynamic SEO

For dynamic pages:

```tsx
export async function generateMetadata({ params }) {
  const { slug } = await params;
  const post = await getPost(slug);

  return {
    title: post.title,
    description: post.summary,
  };
}
```

Good metadata should be derived from trusted server data and should not accidentally expose private fields.

---

## 34. Client Navigation

Use Next.js navigation APIs instead of full-page reloads where appropriate.

```tsx
'use client';

import Link from 'next/link';
import { useRouter } from 'next/navigation';

export function Navigation() {
  const router = useRouter();

  return (
    <>
      <Link href="/dashboard">Dashboard</Link>
      <button onClick={() => router.push('/settings')}>Settings</button>
    </>
  );
}
```

Common APIs:

```text
Link
useRouter
usePathname
useSearchParams
redirect
permanentRedirect
notFound
```

---

## 35. Search Params

Search parameters are useful for filters, sorting, pagination, and shareable UI state.

Example URL:

```text
/products?category=shoes&page=2
```

Server page code can read search parameters through the page's props. Client components can use `useSearchParams()`.

### Rule

If state should be bookmarkable/shareable and represent navigation, consider putting it in the URL rather than only in React state.

---

## 36. Pagination

Server-driven pagination can keep large datasets out of the client.

```tsx
const page = Number(searchParams.page ?? 1);
const limit = 20;
const offset = (page - 1) * limit;

const users = await db.user.findMany({
  skip: offset,
  take: limit,
});
```

For very large datasets, cursor pagination is often more stable than large offsets.

```text
?page=2
```

is simple, while:

```text
?cursor=eyJpZCI6MTIzfQ
```

can be preferable for changing datasets.

---

## 37. API and Backend Layering

A maintainable Next.js application can use layers such as:

```text
app/
  routes + UI
server/
  services
  repositories
  validation
  auth
lib/
  shared utilities
```

Example:

```text
page.tsx
   |
   v
getUser()
   |
   v
userRepository.findById()
   |
   v
Database
```

Keep database queries and authorization logic out of presentation components where possible.

---

## 38. Database Access

A Server Component or Server Action can access a database through a server-only module.

```ts
import 'server-only';

export async function getUser(id: string) {
  return db.user.findUnique({ where: { id } });
}
```

The `server-only` package can help catch accidental imports of server-only modules into client code during development/build tooling.

### Connection management

In serverless environments, be deliberate about database connection behavior. Use a supported connection strategy/pooler for the deployment platform rather than opening unbounded connections per request.

---

## 39. Validation

Validate untrusted input on the server.

```ts
import { z } from 'zod';

const UserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
});

const result = UserSchema.safeParse(input);

if (!result.success) {
  return { error: 'Invalid input' };
}
```

Client validation improves UX; server validation protects the application.

---

## 40. Security Checklist

### Authentication

- Verify sessions/tokens server-side.
- Expire sessions appropriately.
- Protect credential cookies.

### Authorization

- Check permissions at the data mutation/access boundary.
- Do not trust hidden UI controls.
- Do not trust client-supplied user IDs.

### Input

- Validate and normalize input.
- Use parameterized queries/ORM APIs.
- Restrict file uploads.
- Validate redirect destinations.

### XSS

React escapes normal text rendering, but dangerous HTML APIs require care.

```tsx
<div dangerouslySetInnerHTML={{ __html: html }} />
```

Never pass untrusted HTML directly. Sanitize with a trusted strategy when raw HTML is genuinely required.

### CSRF

Cookie-authenticated mutations should consider CSRF defenses appropriate to the application's architecture. SameSite cookies help but are not a universal substitute for a complete security design.

### Secrets

Never expose server secrets to Client Components or `NEXT_PUBLIC_*` variables.

---

## 41. Performance Fundamentals

Measure before optimizing.

Important metrics/concepts:

- LCP — Largest Contentful Paint.
- INP — Interaction to Next Paint.
- CLS — Cumulative Layout Shift.
- TTFB — Time to First Byte.
- JavaScript bundle size.
- Server response latency.
- Database latency.
- Cache hit ratio.

### Common optimizations

- Keep Client Components small.
- Avoid unnecessary client-side JavaScript.
- Stream slow sections.
- Optimize images/fonts.
- Cache safe, reusable data.
- Avoid waterfalls.
- Parallelize independent server requests.
- Paginate large data.
- Use dynamic imports for large client-only features.

---

## 42. Avoiding Request Waterfalls

Bad:

```tsx
const user = await getUser();
const orders = await getOrders(user.id);
const recommendations = await getRecommendations(user.id);
```

If the second and third requests are independent after obtaining the ID, parallelize where possible:

```tsx
const [user, recommendations] = await Promise.all([
  getUser(),
  getRecommendations(),
]);
```

Then fetch dependent data after the prerequisite exists.

The key is not "always use Promise.all" but **model actual dependencies and parallelize independent work**.

---

## 43. React Cache and Server-Side Deduplication Concepts

If multiple components need the same server-side resource, avoid accidental duplicate work.

Possible approaches include:

- Framework-supported fetch/request memoization where applicable.
- React `cache()` for server-side function memoization patterns.
- Application-level data caching.
- Database/query caching.

Do not assume every `fetch()` or arbitrary function call has identical caching semantics. Know which layer you are using.

---

## 44. Deployment Modes

Common deployment choices:

### Node.js server

```bash
npm run build
npm run start
```

Good for applications requiring a Node runtime and broad server capabilities.

### Container

Typical flow:

```text
source -> npm ci -> next build -> production image -> next start
```

Use multi-stage Docker builds to reduce final image size.

### Serverless

Useful for independently scalable request handlers, but consider:

- Cold starts.
- Connection management.
- Execution limits.
- Regional latency.
- File-system assumptions.

### Static export

Possible for applications that can be fully static and do not need server runtime features. It is not appropriate when the app depends on dynamic server behavior.

---

## 45. Build Process

Conceptually:

```text
Source code
   |
   v
Next build
   |
   +--> analyze routes
   +--> compile/bundle
   +--> optimize assets
   +--> determine rendering/caching behavior
   +--> produce deployment output
```

Production:

```bash
npm run build
npm run start
```

Do not treat development mode performance as production performance.

---

## 46. TypeScript Patterns

Route params:

```tsx
type PageProps = {
  params: Promise<{ slug: string }>;
  searchParams: Promise<{ page?: string }>;
};

export default async function Page({ params, searchParams }: PageProps) {
  const { slug } = await params;
  const { page = '1' } = await searchParams;
  return <div>{slug} — page {page}</div>;
}
```

Prefer explicit domain types at service boundaries.

Avoid:

```ts
const data: any = await fetchData();
```

Prefer validated types:

```ts
type User = {
  id: string;
  name: string;
};
```

For external JSON, runtime validation is stronger than TypeScript types alone.

---

## 47. Testing Next.js Applications

Use multiple testing layers.

### Unit tests

Test pure utilities and business logic.

### Component tests

Test UI behavior and accessibility semantics.

### Integration tests

Test server actions, route handlers, and data flows.

### End-to-end tests

Use a browser automation tool to test complete user journeys.

Example test strategy:

```text
Unit -> business rules
Component -> UI behavior
Integration -> API/server boundaries
E2E -> critical user journeys
```

Do not rely only on snapshot tests for application correctness.

---

## 48. Accessibility

Next.js does not automatically make an application accessible.

Use:

- Semantic HTML.
- Labels for inputs.
- Keyboard navigation.
- Visible focus states.
- Meaningful button/link text.
- Correct heading hierarchy.
- Accessible error messages.
- Appropriate ARIA only when native HTML is insufficient.

Example:

```tsx
<label htmlFor="email">Email</label>
<input id="email" name="email" type="email" />
```

Prefer `<button>` for actions and `<Link>` for navigation rather than clickable `<div>` elements.

---

## 49. Internationalization

An i18n architecture often combines:

```text
locale
  |
  +--> URL routing
  +--> translation resources
  +--> number/date formatting
  +--> metadata
  +--> locale-aware content
```

Avoid hard-coding user-visible strings throughout the application.

Locale can be represented in a route such as:

```text
/en/products
/fr/products
```

The exact i18n implementation depends on product requirements and chosen library.

---

## 50. Monorepo and Shared Packages

For larger systems:

```text
apps/
  web/
  admin/
packages/
  ui/
  config/
  validation/
  database/
```

Share:

- UI components.
- Validation schemas.
- API/domain types.
- ESLint/TypeScript configuration.
- Design tokens.

Do not share server-only packages into client bundles accidentally.

---

## 51. Common Architecture Pattern

A practical Next.js architecture:

```text
app/
├── (marketing)/
├── (app)/
├── api/
└── layout.tsx

components/
├── ui/
└── features/

server/
├── auth/
├── services/
├── repositories/
└── validation/

lib/
├── utils/
└── constants/
```

### Guiding rule

```text
UI -> application service -> repository -> database
```

Do not force every project into this exact folder structure. Architecture should follow domain boundaries and team needs.

---

## 52. Common Anti-Patterns

| Anti-pattern | Problem | Better approach |
|---|---|---|
| `'use client'` at root | Huge client bundle | Keep boundary small |
| Server Component calls own API | Extra network hop | Call service directly |
| Authorization only in UI | Easily bypassed | Enforce server-side |
| Secrets in `NEXT_PUBLIC_*` | Secret becomes public | Keep server-only |
| Giant `layout.tsx` | Hard to maintain | Compose layouts/components |
| Sequential independent requests | Slow waterfall | Parallelize |
| No loading UI | Poor navigation UX | `loading.tsx`/Suspense |
| No error boundary | Poor failure UX | `error.tsx` + domain handling |
| Unbounded client state | Complex synchronization | Keep server data server-side when possible |
| Blind cache invalidation | Stale or expensive system | Design cache tags/paths deliberately |

---

## 53. Interview: Rendering Mental Model

When asked "How does Next.js render a page?", answer in layers:

1. Route matching determines the component tree.
2. Server Components can execute on the server.
3. Data can be fetched/accessed server-side.
4. The framework determines what can be pre-rendered/cached and what must be dynamic.
5. HTML/RSC payload can stream progressively.
6. Client Components hydrate/activate browser interactivity.
7. Client navigation can reuse cached/prefetched route segments.

This is much stronger than simply saying "Next.js uses SSR."

---

# 54. Next.js Interview Questions & Answers

## Q1. What is Next.js?

**Answer:** Next.js is a React framework for building full-stack web applications. It provides routing, server/client rendering patterns, data access, caching/revalidation, route handlers, server functions, metadata, asset optimization, and deployment tooling.

## Q2. What is the difference between React and Next.js?

**Answer:** React primarily provides the component/UI model. Next.js provides the application framework around React, including routing, server rendering, data/cache infrastructure, build tooling, and conventions for production applications.

## Q3. What is the App Router?

**Answer:** The App Router is Next.js's modern routing architecture based on the `app/` directory. It integrates layouts, Server Components, loading/error boundaries, streaming, route handlers, and nested routing.

## Q4. What is the Pages Router?

**Answer:** The Pages Router is the older `pages/` routing model. It uses APIs such as `getServerSideProps`, `getStaticProps`, and `getStaticPaths`. It remains relevant for existing applications but should not be confused with App Router semantics.

## Q5. What is a Server Component?

**Answer:** In the App Router, components are Server Components by default. They execute as part of the server-side component tree and can access server-side resources without shipping their implementation as interactive browser JavaScript.

## Q6. What does `'use client'` do?

**Answer:** It marks a Client Component boundary. The component can use client-only React features such as state, effects, and event handlers. Its client-side module graph becomes part of the browser bundle as appropriate.

## Q7. Does `'use client'` mean the component never renders on the server?

**Answer:** No. Client Components can participate in server rendering/pre-rendering. The directive primarily defines the client boundary and hydration/interactivity requirements.

## Q8. Can a Server Component import a Client Component?

**Answer:** Yes. This is a common composition pattern: a Server Component renders a small Client Component for interactive behavior.

## Q9. Can a Client Component import a Server Component directly?

**Answer:** Not as an ordinary component import in the same way a Server Component imports another Server Component. Instead, compose server-rendered content through supported patterns such as passing server-rendered children/slots into a Client Component.

## Q10. Why are Server Components useful?

**Answer:** They can reduce client JavaScript, keep server-only dependencies/secrets on the server, and allow data access close to the source. They are particularly useful for data-heavy, mostly static UI.

## Q11. What is SSR?

**Answer:** Server-side rendering means generating HTML on the server for a request. In modern Next.js, SSR is only one part of a broader rendering model that includes pre-rendering, caching, streaming, and Server Components.

## Q12. What is SSG?

**Answer:** Static Site Generation means producing page output ahead of the request, allowing it to be served efficiently from a cache/CDN. In modern Next.js, think in terms of which route/data can be precomputed and cached rather than treating SSG as a completely separate universe.

## Q13. What is ISR?

**Answer:** Incremental Static Regeneration is the concept of serving pre-rendered content while allowing it to be regenerated/revalidated over time. In current Next.js, the implementation is expressed through the framework's caching and revalidation mechanisms.

## Q14. What is the difference between static and dynamic rendering?

**Answer:** Static rendering can be prepared ahead of requests; dynamic rendering requires request-time computation or data. The decision depends on route inputs, data access, caching, and request-specific information.

## Q15. What is `loading.tsx`?

**Answer:** It provides a route-segment loading UI and creates a Suspense boundary for the segment, allowing users to see fallback UI while the actual content is loading.

## Q16. What is `error.tsx`?

**Answer:** It defines a route-level error boundary. It is a Client Component and receives an error plus a reset function that can retry the failed segment.

## Q17. What is `notFound()`?

**Answer:** It stops rendering the current route and displays the nearest `not-found` UI with a not-found response semantics. It is appropriate when a requested resource does not exist.

## Q18. What are Route Handlers?

**Answer:** Route Handlers are HTTP endpoints defined with `route.ts`/`route.js` in the App Router. They can implement methods such as GET, POST, PUT, PATCH, and DELETE.

## Q19. Route Handler vs Server Action?

**Answer:** A Route Handler is an HTTP endpoint and is useful when callers need an HTTP contract. A Server Action/Server Function is a server-side function integrated with React/Next.js mutation flows. Choose based on the boundary you need rather than using either for everything.

## Q20. Should a Server Component call its own Route Handler?

**Answer:** Usually no. If both are inside the same application, directly call a shared server-side service/data function. Calling the route handler adds an unnecessary HTTP hop.

## Q21. What are Server Actions?

**Answer:** Server Actions, now commonly described within React's Server Function model, let supported UI flows invoke server-side functions. They are useful for mutations, forms, cache invalidation, and server-side business operations.

## Q22. Are Server Actions automatically secure?

**Answer:** No. Their implementation is server-side, but inputs are still untrusted. Authenticate the user, authorize the operation, validate all inputs, and avoid returning sensitive information.

## Q23. What is `revalidatePath()`?

**Answer:** It invalidates/revalidates cached data associated with a path so that subsequent rendering can obtain fresh data according to the current caching model.

## Q24. What is `revalidateTag()`?

**Answer:** It invalidates cached data associated with a cache tag. It is useful when one domain mutation should refresh data consumed by multiple routes.

## Q25. What is `updateTag()`?

**Answer:** In current Next.js caching APIs, `updateTag()` is designed for immediate expiration of tagged data in supported server mutation flows. It differs from stale-while-revalidate style invalidation because it is intended to provide read-your-writes behavior for the updating user.

## Q26. What is `refresh()` in a Server Action?

**Answer:** `refresh()` can refresh the current route so the UI can obtain updated server-rendered data after a mutation.

## Q27. Explain Next.js caching layers.

**Answer:** Separate the concepts: cached data/results, cached rendered route output, and client-side Router Cache. They have different lifetimes and invalidation mechanisms. A good engineer identifies exactly which layer is stale before choosing an invalidation API.

## Q28. How do you avoid stale data after a mutation?

**Answer:** Identify the data consumed by the affected UI, tag or associate it with appropriate cache entries, then invalidate/revalidate the relevant path/tag after a successful mutation. Also refresh client navigation state when required.

## Q29. What is streaming?

**Answer:** Streaming sends parts of the rendered UI as they become available rather than waiting for all server work to finish. Suspense boundaries allow slow components to stream later while the shell appears earlier.

## Q30. Why can streaming improve UX?

**Answer:** Users can see useful content sooner, and slow sections do not necessarily block the entire page. It improves perceived responsiveness when the page has independent slow data sources.

## Q31. What are dynamic routes?

**Answer:** Dynamic routes use segments such as `[id]`, `[slug]`, `[...slug]`, and `[[...slug]]` to match variable URL paths.

## Q32. What is a route group?

**Answer:** A directory such as `(marketing)` groups routes for organization/layout purposes without adding that directory name to the URL.

## Q33. What are parallel routes?

**Answer:** Parallel routes allow multiple route segments to render into named slots simultaneously. They are useful for dashboards and complex layouts with independently managed sections.

## Q34. What are intercepting routes?

**Answer:** They allow one route to render another route in the context of the current UI, commonly enabling modal patterns where direct navigation and in-context navigation have different presentations.

## Q35. What is `generateStaticParams()`?

**Answer:** It provides known dynamic route parameters that can be generated ahead of time. It is useful when the application knows a finite set of content routes.

## Q36. How do you implement SEO in Next.js?

**Answer:** Use the Metadata API, static metadata where possible, `generateMetadata` for dynamic pages, semantic HTML, correct 404 behavior, canonical URLs when necessary, sitemap/robots metadata routes, and good performance/accessibility.

## Q37. How do you create dynamic metadata?

**Answer:** Export an async `generateMetadata()` function, fetch the necessary trusted server-side data, and return a `Metadata` object with title, description, Open Graph information, etc.

## Q38. How do you protect secrets in Next.js?

**Answer:** Keep secrets in server-only environment variables and server modules. Never expose them through `NEXT_PUBLIC_*`, Client Components, serialized props, API responses, or logs.

## Q39. What is `NEXT_PUBLIC_`?

**Answer:** It marks environment variables intended to be available to client-side code. They should therefore be treated as public configuration, not secrets.

## Q40. How do you protect an authenticated route?

**Answer:** Authenticate the request/session and authorize the requested operation on the server. UI redirects are useful for UX, but authorization must also happen at the server-side data/mutation boundary.

## Q41. Authentication vs authorization?

**Answer:** Authentication identifies the user. Authorization decides whether that user is allowed to perform a specific action on a specific resource.

## Q42. Why should authorization be checked near the database/service layer?

**Answer:** Because multiple entry points can invoke the same operation. A service-layer check prevents bypass through a different route, Server Action, background job, or API caller.

## Q43. How do you optimize a Next.js application?

**Answer:** Reduce unnecessary Client Components, reduce JavaScript, stream slow UI, parallelize independent server work, cache safe data, optimize images/fonts, paginate large data, split large client features, and measure with real performance tooling.

## Q44. What causes hydration errors?

**Answer:** Hydration errors occur when the client-rendered result does not match the server-rendered result. Common causes include non-deterministic rendering such as `Date.now()`, random values, browser-only APIs during render, invalid HTML nesting, or different data on server and client.

## Q45. How do you fix hydration mismatches?

**Answer:** Make initial rendering deterministic, move browser-only behavior into effects/client-only boundaries where appropriate, ensure the same data is used, fix invalid markup, and avoid using browser globals during server rendering.

## Q46. What is hydration?

**Answer:** Hydration is the process by which client-side React attaches interactivity/event handling to server-rendered UI. In an RSC application, React Server Component payload and Client Components work together to construct the interactive application.

## Q47. What is the client bundle?

**Answer:** It is the JavaScript that must be delivered to the browser for Client Components and their dependencies. Smaller client boundaries generally mean less JavaScript to download, parse, and execute.

## Q48. Why is putting `'use client'` at the root often bad?

**Answer:** It can move a large portion of the component graph into the client bundle, increasing JavaScript and reducing the benefit of Server Components. Keep interactive boundaries close to the components that actually need browser capabilities.

## Q49. How do you fetch data efficiently in Next.js?

**Answer:** Fetch server-side when possible, avoid unnecessary internal HTTP calls, parallelize independent requests, cache/revalidate safe data, use streaming for slow sections, and paginate large datasets.

## Q50. What is a request waterfall?

**Answer:** A waterfall occurs when independent network/database operations execute sequentially, causing total latency to approximate the sum of each operation. Parallelize independent work with `Promise.all()` or equivalent patterns.

## Q51. How do you handle forms?

**Answer:** Use native form semantics, Server Actions/Server Functions or Route Handlers for server mutations, validate on the server, authorize the operation, provide pending/error UI, and invalidate affected data after success.

## Q52. Why validate on both client and server?

**Answer:** Client validation gives immediate feedback, but it can be bypassed. Server validation is the authoritative security boundary.

## Q53. How do you prevent XSS?

**Answer:** Use React's normal escaping, avoid injecting raw HTML, sanitize trusted-required HTML, validate URLs/redirects, keep dependencies updated, and apply an appropriate Content Security Policy where practical.

## Q54. Is React escaping enough for all XSS?

**Answer:** No. React escapes normal text output, but APIs such as `dangerouslySetInnerHTML`, unsafe URL handling, third-party scripts, and DOM escape hatches can introduce vulnerabilities.

## Q55. What is the difference between `Link` and `useRouter`?

**Answer:** `Link` is the declarative navigation component and is preferred for ordinary navigation. `useRouter` is useful for imperative navigation after an action or condition.

## Q56. When should state live in the URL?

**Answer:** Put navigation-related state such as filters, sorting, pagination, and shareable selections in search parameters when users should be able to bookmark/share/back-forward through that state.

## Q57. When should state live in React state?

**Answer:** Use React state for transient UI state such as open menus, input drafts, animations, and temporary interaction state that does not need to be represented in the URL or persisted server-side.

## Q58. How do you choose between Server Components and Client Components?

**Answer:** Start with Server Components. Move a component to the client only when it needs browser interactivity, client hooks, event handlers, or browser APIs. Keep the client boundary as small as possible.

## Q59. What is a good Next.js folder architecture?

**Answer:** There is no mandatory architecture. A common scalable approach separates route/UI composition under `app/`, reusable components under `components/`, and server-side domain logic under `server/` or `lib/`. The goal is clear domain boundaries rather than a particular folder-name convention.

## Q60. How do Route Handlers differ from traditional Express routes?

**Answer:** Route Handlers are framework-integrated file-system routes using Web `Request`/`Response` APIs and Next.js runtime conventions. Express is a general-purpose Node.js HTTP framework with its own middleware/routing model.

## Q61. Can Next.js replace a backend?

**Answer:** For many applications, yes: Server Components, Route Handlers, Server Functions, database access, and authentication can form a full-stack application. For large distributed systems, dedicated services may still be appropriate.

## Q62. When should you use a separate backend service?

**Answer:** Consider one when multiple clients need a stable API, the domain is independently scalable, long-running/background workloads dominate, teams deploy services independently, or backend infrastructure requirements exceed what should live in the web application.

## Q63. How do you handle webhooks in Next.js?

**Answer:** Use a Route Handler, verify the webhook signature before trusting the payload, make processing idempotent, persist an event/idempotency key, return the expected status quickly, and move slow work to a queue/background worker when necessary.

## Q64. How do you handle file uploads securely?

**Answer:** Validate file type and size server-side, avoid trusting the filename or MIME type alone, use safe storage, generate server-side object keys, restrict executable content, scan where required, and use direct-to-object-storage uploads for large files when appropriate.

## Q65. How would you design a multi-tenant Next.js application?

**Answer:** Resolve the tenant from a trusted source, authenticate the user, authorize membership, scope every data query by tenant ID, isolate cache keys/tags by tenant, and never trust a tenant ID supplied solely by the browser.

## Q66. How would you debug a slow Next.js page?

**Answer:** Measure first. Check TTFB/server logs, database latency, request waterfalls, cache hit/miss behavior, bundle size, hydration/client work, third-party scripts, image sizes, and slow Suspense boundaries. Fix the largest measured bottleneck first.

## Q67. How would you design a high-traffic product page?

**Answer:** Pre-render/cache stable product data, use CDN-friendly assets, optimize images, cache safe queries, use incremental revalidation where appropriate, stream personalized sections separately, and keep interactive widgets as small Client Components.

## Q68. How would you design a personalized dashboard?

**Answer:** Keep the main dashboard server-rendered when possible, authenticate on the server, fetch user-specific data through server services, stream independent widgets, cache non-sensitive shared data, and use Client Components only for interactive charts/controls.

## Q69. How would you handle a mutation followed by stale UI?

**Answer:** Identify the cache/data layer that is stale. After the mutation succeeds, invalidate the appropriate tag/path and refresh the relevant client navigation state. Also verify that the mutation actually committed before invalidating.

## Q70. What is the most important Next.js interview mental model?

**Answer:** Think in **route tree + server/client boundaries + rendering + caching + invalidation + navigation**. Most advanced Next.js questions are combinations of those concepts rather than isolated APIs.

---

# 55. Scenario-Based Interview Questions

## Scenario 1: Product page is slow because reviews take 3 seconds.

**Answer:** Render the product information independently and put reviews behind a Suspense boundary. Stream the reviews when ready. Cache/revalidate reviews if freshness allows it.

## Scenario 2: Every page became a Client Component.

**Answer:** Refactor. Identify which components actually need state/effects/browser APIs, move those to client boundaries, and restore server rendering/data access for the rest.

## Scenario 3: User changes profile but header still shows old name.

**Answer:** The profile mutation changed server data but the UI is reading cached/stale data. Invalidate the relevant user tag/path and refresh the affected route state after the mutation.

## Scenario 4: API key appears in browser DevTools.

**Answer:** Treat it as compromised. Remove it from client-visible code, rotate the key, move access to server-only code, and ensure it is not prefixed with `NEXT_PUBLIC_`.

## Scenario 5: Users can edit another user's order by changing `/orders/123`.

**Answer:** Route parameters are untrusted. The server must load the order and verify that the authenticated user is authorized to access/edit that specific order.

## Scenario 6: Page works in development but hydration fails in production.

**Answer:** Look for nondeterministic rendering, environment-dependent values, browser-only APIs, invalid markup, and data that differs between server and client. Reproduce with production build and inspect the first hydration mismatch.

## Scenario 7: A Server Component calls `/api/users` and the API calls the database.

**Answer:** If both live in the same application, extract `getUsers()` into a server-side module and call it directly from the Server Component. Keep the Route Handler for actual HTTP consumers.

## Scenario 8: A search page performs five independent API calls sequentially.

**Answer:** Identify dependencies and parallelize independent calls. Use `Promise.all()` or a server data orchestration function, then stream particularly slow sections if needed.

## Scenario 9: Dashboard has a 10,000-row table.

**Answer:** Do not send/render all rows. Paginate or cursor-paginate data, use server-side filtering, and consider client virtualization for the visible window when interaction requires a large list.

## Scenario 10: A mutation must be visible immediately to the user.

**Answer:** Use an appropriate mutation/cache strategy that gives read-your-writes behavior, invalidate the affected data, and refresh the current UI. Do not rely on eventual cache expiry for a user-facing confirmation.

## Scenario 11: A third-party analytics library adds 500 KB to the client bundle.

**Answer:** Measure its real business value, load it with an appropriate script strategy, isolate it from critical UI, and consider a lighter provider. Do not make analytics part of the critical interactive bundle unnecessarily.

## Scenario 12: A Server Action accepts `{ userId, amount }` from a form.

**Answer:** Never trust `userId` or amount. Authenticate the caller, derive identity from the session where possible, validate amount server-side, authorize the operation, and perform the mutation transactionally.

## Scenario 13: Webhook provider retries the same event five times.

**Answer:** Make the handler idempotent. Store the provider event ID or idempotency key, ignore already-processed events, verify signatures, and return a successful response after safely recording/queueing the event.

## Scenario 14: Marketing pages change once per hour.

**Answer:** Use pre-rendering plus controlled revalidation/cache invalidation rather than forcing every visitor through request-time database work.

## Scenario 15: Admin page must always show current permissions.

**Answer:** Treat authorization as request-time server logic. Do not rely solely on cached permission data. Cache only data whose freshness and security characteristics are acceptable.

---

# 56. Quick Revision Sheet

### Core concepts

```text
Next.js = React framework
App Router = app/
Server Components = default
Client Component = 'use client'
Route UI = page.tsx
Shared UI = layout.tsx
Loading = loading.tsx
Errors = error.tsx
404 = not-found.tsx
HTTP API = route.ts
Mutation = Server Action / Server Function
Dynamic route = [id]
Catch-all = [...slug]
Optional catch-all = [[...slug]]
Route group = (group)
Parallel route = @slot
```

### Rendering

```text
Static/pre-rendered
Dynamic/request-time
Streaming
Suspense
Server Components
Client Components
Hydration
```

### Caching

```text
Data/cache entries
Rendered route output
Client Router Cache
Tags
Paths
Revalidation
Invalidation
```

### Security

```text
Authenticate
Authorize
Validate
Sanitize
Protect secrets
Protect cookies
Verify webhooks
Prevent XSS/CSRF
```

### Performance

```text
Small client boundaries
Parallel data fetching
Streaming
Image optimization
Font optimization
Caching
Pagination
Code splitting
Measure before optimizing
```

---

# 57. Interview Mistakes to Avoid

1. Saying "Next.js is just SSR for React."
2. Saying all App Router components are Client Components.
3. Saying `'use client'` means code never runs on a server.
4. Treating caching as one global cache.
5. Using a Route Handler for every server-to-server call inside the same app.
6. Assuming UI hiding is authorization.
7. Putting secrets in `NEXT_PUBLIC_*`.
8. Treating Server Actions as automatically secure.
9. Saying static generation and SSR are the only rendering modes.
10. Memorizing old Next.js caching behavior without checking the version.
11. Optimizing before measuring.
12. Making the entire application a Client Component because one button needs state.

---

# 58. Final Next.js Interview Checklist

Before an interview, be able to explain without notes:

- [ ] React vs Next.js.
- [ ] App Router vs Pages Router.
- [ ] File-system routing.
- [ ] Dynamic/catch-all routes.
- [ ] Layouts and nested layouts.
- [ ] Server vs Client Components.
- [ ] `'use client'` boundaries.
- [ ] Data fetching in Server Components.
- [ ] Static vs dynamic rendering.
- [ ] Streaming and Suspense.
- [ ] `loading.tsx` and `error.tsx`.
- [ ] `notFound()`.
- [ ] Metadata and `generateMetadata()`.
- [ ] Route Handlers.
- [ ] Server Actions/Server Functions.
- [ ] `revalidatePath()`.
- [ ] `revalidateTag()`.
- [ ] `updateTag()` and read-your-writes concepts.
- [ ] Client Router Cache.
- [ ] Authentication vs authorization.
- [ ] Environment variables and secrets.
- [ ] XSS/CSRF/security basics.
- [ ] Image/font optimization.
- [ ] Request waterfalls.
- [ ] Performance measurement.
- [ ] Deployment options.
- [ ] Hydration errors.
- [ ] Parallel and intercepting routes.
- [ ] Forms and validation.
- [ ] Webhooks and idempotency.
- [ ] Multi-tenant authorization/caching.

---

## One-Line Mental Model

> **Next.js is a React application framework where routing defines the UI tree, Server Components keep server work close to data, Client Components add browser interactivity, rendering determines when UI is produced, caching determines what can be reused, and invalidation keeps reused data correct.**
