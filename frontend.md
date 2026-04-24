# Frontend Development (React & Next.js) — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

**Frontend development** involves building everything users directly interact with — the UI, user experience, routing, state management, and communication with backend APIs.

**React** is a JavaScript library for building UIs using a **component-based**, **declarative** model with a **virtual DOM**.  
**Next.js** is a full-stack React framework adding **server-side rendering, static generation, routing, and API routes** out of the box.

---

## 2. React Fundamentals

### 2.1 JSX

JSX is syntactic sugar for `React.createElement()`. It compiles to JavaScript.

```jsx
// JSX
const element = <h1 className="title">Hello, {name}!</h1>;

// Compiles to
const element = React.createElement('h1', { className: 'title' }, `Hello, ${name}!`);
```

**JSX Rules:**
- Must return a single root element (use `<>...</>` fragments to avoid extra DOM nodes)
- Use `className` (not `class`) and `htmlFor` (not `for`)
- JavaScript expressions inside `{}`; statements (if/for) must be converted to expressions
- Self-close tags: `<img />`, `<br />`

### 2.2 Components

```jsx
// Function component (preferred)
function Greeting({ name, age }) {
  return <p>Hello {name}, you are {age} years old.</p>;
}

// Default props
Greeting.defaultProps = { name: 'Guest' };

// Usage
<Greeting name="Alice" age={25} />
```

**Rules of Components:**
- Must start with a **capital letter** (lowercase = DOM element)
- Must be **pure** with respect to props — same props → same output
- Never mutate props

### 2.3 Props

```jsx
// Passing various prop types
<Button
  label="Click me"           // string
  count={5}                  // number
  isActive={true}            // boolean (true can be omitted: isActive)
  items={['a', 'b', 'c']}   // array
  config={{ color: 'red' }}  // object
  onClick={handleClick}      // function
  icon={<StarIcon />}        // JSX element
/>

// Spread props
const props = { id: 1, name: 'Alice' };
<User {...props} />

// children prop
<Card>
  <h2>Title</h2>
  <p>Content</p>
</Card>
```

### 2.4 Conditional Rendering

```jsx
// Ternary
{isLoggedIn ? <Dashboard /> : <Login />}

// Short-circuit (renders nothing when false)
{error && <ErrorBanner message={error} />}

// Variable
let content;
if (isLoading) content = <Spinner />;
else if (error)  content = <Error />;
else             content = <DataView data={data} />;
```

### 2.5 Lists & Keys

```jsx
const items = ['Apple', 'Banana', 'Cherry'];

// Keys must be stable, unique among siblings
<ul>
  {items.map((item, index) => (
    <li key={item}>{item}</li>  // use item value (stable) over index
  ))}
</ul>

// Key rules:
// - Use database IDs when available
// - Never use array index as key for reorderable lists (breaks reconciliation)
// - Keys must be unique among siblings, not globally unique
```

---

## 3. React Hooks

### 3.1 useState

```jsx
const [count, setCount] = useState(0);
const [user, setUser] = useState(null);
const [form, setForm] = useState({ name: '', email: '' });

// Update primitive
setCount(prev => prev + 1);  // functional update (safe with batching)

// Update object — must spread (React doesn't deep merge)
setForm(prev => ({ ...prev, email: 'new@email.com' }));

// Lazy initial state (runs once)
const [data] = useState(() => expensiveComputation());
```

### 3.2 useEffect

```jsx
// Runs after every render
useEffect(() => { document.title = `Count: ${count}`; });

// Runs once on mount
useEffect(() => {
  fetchUser(id).then(setUser);
}, []);

// Runs when dependencies change
useEffect(() => {
  const subscription = subscribe(userId);
  return () => subscription.unsubscribe();  // cleanup on unmount / before re-run
}, [userId]);

// Common patterns
useEffect(() => {
  let cancelled = false;
  fetchData(id).then(data => {
    if (!cancelled) setData(data);
  });
  return () => { cancelled = true; };  // prevent state update on unmounted component
}, [id]);
```

**Dependency Rules:**
- Include every reactive value used inside the effect
- Empty array `[]` = run once; missing deps = stale closure bugs

### 3.3 useRef

```jsx
// DOM reference
const inputRef = useRef(null);
const handleFocus = () => inputRef.current.focus();
<input ref={inputRef} />

// Mutable value that doesn't trigger re-render
const renderCount = useRef(0);
useEffect(() => { renderCount.current++; });

// Previous value pattern
const prevCount = useRef(count);
useEffect(() => { prevCount.current = count; }, [count]);
```

### 3.4 useMemo & useCallback

```jsx
// useMemo — memoize expensive computed value
const sortedList = useMemo(
  () => [...items].sort((a, b) => a.name.localeCompare(b.name)),
  [items]   // recompute only when items changes
);

// useCallback — memoize function reference (for stable prop references)
const handleSubmit = useCallback(async (data) => {
  await api.createUser(data);
  onSuccess();
}, [onSuccess]);  // recreate only when onSuccess changes
```

**When to use:**
- `useMemo`: expensive computation; object/array passed as prop to memoized child
- `useCallback`: function passed as prop to `React.memo` child; used in `useEffect` deps

### 3.5 useContext

```jsx
// Create context
const ThemeContext = createContext('light');

// Provider
function App() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <MainLayout />
    </ThemeContext.Provider>
  );
}

// Consumer
function Button() {
  const { theme, setTheme } = useContext(ThemeContext);
  return <button className={theme} onClick={() => setTheme('dark')}>Toggle</button>;
}
```

### 3.6 useReducer

```jsx
const initialState = { count: 0, error: null, loading: false };

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT': return { ...state, count: state.count + 1 };
    case 'SET_ERROR':  return { ...state, error: action.payload, loading: false };
    case 'RESET':      return initialState;
    default:           throw new Error(`Unknown action: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <div>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
    </div>
  );
}
```

**Use `useReducer` when:**
- State logic involves multiple sub-values
- Next state depends on previous state in complex ways
- You want explicit action names (easier to debug / trace)

### 3.7 Custom Hooks

```jsx
// useLocalStorage
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      return JSON.parse(localStorage.getItem(key)) ?? initialValue;
    } catch { return initialValue; }
  });

  const setStoredValue = useCallback((newValue) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  }, [key]);

  return [value, setStoredValue];
}

// useDebounce
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(handler);
  }, [value, delay]);
  return debouncedValue;
}

// useFetch
function useFetch(url) {
  const [data, setData]       = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError]     = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);
    fetch(url)
      .then(r => r.json())
      .then(d => { if (!cancelled) { setData(d); setLoading(false); } })
      .catch(e => { if (!cancelled) { setError(e); setLoading(false); } });
    return () => { cancelled = true; };
  }, [url]);

  return { data, loading, error };
}
```

---

## 4. React Performance Optimization

### 4.1 React.memo

```jsx
// Prevents re-render if props haven't changed (shallow comparison)
const UserCard = React.memo(function UserCard({ user }) {
  return <div>{user.name}</div>;
});

// Custom comparison
const UserCard = React.memo(UserCard, (prev, next) => prev.user.id === next.user.id);
```

### 4.2 Code Splitting & Lazy Loading

```jsx
import { lazy, Suspense } from 'react';

// Lazy load component (creates separate bundle chunk)
const Dashboard = lazy(() => import('./Dashboard'));
const Settings  = lazy(() => import('./Settings'));

function App() {
  return (
    <Suspense fallback={<PageSpinner />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings"  element={<Settings />} />
      </Routes>
    </Suspense>
  );
}
```

### 4.3 Virtualization

For long lists, render only visible items:

```jsx
import { FixedSizeList as List } from 'react-window';

function Row({ index, style }) {
  return <div style={style}>{data[index].name}</div>;
}

<List height={600} itemCount={10000} itemSize={50} width="100%">
  {Row}
</List>
```

### 4.4 Performance Anti-Patterns

| Anti-Pattern                      | Problem                              | Fix                                    |
|-----------------------------------|--------------------------------------|----------------------------------------|
| Inline object/array as prop       | New reference on every render        | `useMemo` or define outside component  |
| Inline function as prop           | New reference on every render        | `useCallback`                          |
| Missing `key` / using index as key| Wrong reconciliation with reorders   | Use stable unique ID as key            |
| State in every child              | Too many re-renders                  | Lift state up or use context           |
| `useEffect` with no deps          | Runs on every render                 | Add correct dependency array           |
| Giant components                  | Hard to optimize / test              | Split into smaller components          |

---

## 5. State Management

### 5.1 When to Use What

| Scale            | Solution                              |
|------------------|---------------------------------------|
| Local UI state   | `useState`, `useReducer`              |
| Shared between siblings | Lift state up + props          |
| Deep prop drilling | `useContext` + `useReducer`          |
| Complex global state | Zustand, Redux Toolkit             |
| Server state     | React Query / TanStack Query, SWR     |
| Form state       | React Hook Form, Formik               |

### 5.2 Zustand

```jsx
import { create } from 'zustand';

const useAuthStore = create((set, get) => ({
  user: null,
  token: null,
  isAuthenticated: () => get().token !== null,
  login: async (credentials) => {
    const { user, token } = await api.login(credentials);
    set({ user, token });
  },
  logout: () => set({ user: null, token: null }),
}));

// In component
function Profile() {
  const { user, logout } = useAuthStore();
  return <button onClick={logout}>{user.name}</button>;
}
```

### 5.3 TanStack Query (React Query)

```jsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Query (GET data)
function UserProfile({ userId }) {
  const { data: user, isLoading, error } = useQuery({
    queryKey: ['users', userId],
    queryFn: () => api.getUser(userId),
    staleTime: 5 * 60 * 1000,  // 5 min before refetch
    retry: 2,
  });

  if (isLoading) return <Spinner />;
  if (error) return <Error message={error.message} />;
  return <div>{user.name}</div>;
}

// Mutation (POST/PUT/DELETE data)
function CreatePost() {
  const queryClient = useQueryClient();
  const mutation = useMutation({
    mutationFn: (newPost) => api.createPost(newPost),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] });
    },
  });

  return (
    <button onClick={() => mutation.mutate({ title: 'New Post' })}>
      {mutation.isPending ? 'Creating...' : 'Create Post'}
    </button>
  );
}
```

### 5.4 Redux Toolkit

```jsx
// slice
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

export const fetchUser = createAsyncThunk('users/fetchById', async (id) => {
  return await api.getUser(id);
});

const usersSlice = createSlice({
  name: 'users',
  initialState: { entities: {}, loading: false },
  reducers: {
    userUpdated(state, action) {
      state.entities[action.payload.id] = action.payload;
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending,   (state) => { state.loading = true; })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.loading = false;
        state.entities[action.payload.id] = action.payload;
      });
  },
});
```

---

## 6. React Router v6

```jsx
import { BrowserRouter, Routes, Route, Link, useNavigate, useParams, Outlet } from 'react-router-dom';

// App routing
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Layout />}>
      <Route index element={<Home />} />
      <Route path="users" element={<Users />} />
      <Route path="users/:id" element={<UserDetail />} />
      <Route path="users/:id/edit" element={<EditUser />} />
      <Route path="*" element={<NotFound />} />
    </Route>
  </Routes>
</BrowserRouter>

// Layout with nested routes
function Layout() {
  return (
    <div>
      <nav><Link to="/">Home</Link> | <Link to="/users">Users</Link></nav>
      <Outlet />  {/* renders nested route */}
    </div>
  );
}

// Protected route
function PrivateRoute({ children }) {
  const { isAuthenticated } = useAuthStore();
  const location = useLocation();
  if (!isAuthenticated) return <Navigate to="/login" state={{ from: location }} replace />;
  return children;
}

// Programmatic navigation
const navigate = useNavigate();
navigate('/dashboard', { replace: true });
navigate(-1);  // go back

// Route params
const { id } = useParams();
const [searchParams] = useSearchParams();
const tab = searchParams.get('tab');  // ?tab=settings
```

---

## 7. Forms

### 7.1 React Hook Form

```jsx
import { useForm, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  name:     z.string().min(2, 'Name too short'),
  email:    z.string().email('Invalid email'),
  password: z.string().min(8, 'Min 8 characters'),
});

function RegisterForm() {
  const { register, handleSubmit, formState: { errors, isSubmitting }, control } = useForm({
    resolver: zodResolver(schema),
    defaultValues: { name: '', email: '', password: '' },
  });

  const onSubmit = async (data) => {
    await api.register(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} placeholder="Name" />
      {errors.name && <span>{errors.name.message}</span>}

      <input {...register('email')} type="email" />
      {errors.email && <span>{errors.email.message}</span>}

      <input {...register('password')} type="password" />
      {errors.password && <span>{errors.password.message}</span>}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Registering...' : 'Register'}
      </button>
    </form>
  );
}
```

---

## 8. Next.js

### 8.1 Overview & Key Features

| Feature                    | Description                                              |
|----------------------------|----------------------------------------------------------|
| File-based Routing         | `app/` or `pages/` directory structure defines routes    |
| Server Components          | Render on server; zero JS shipped to client by default   |
| Client Components          | Interactive components; marked with `'use client'`       |
| Server-Side Rendering (SSR)| HTML generated per request on server                     |
| Static Site Generation (SSG)| HTML pre-generated at build time                        |
| Incremental Static Regen.  | SSG pages revalidated in background after N seconds      |
| API Routes                 | Backend endpoints inside Next.js app                     |
| Image Optimization         | `<Image>` component; automatic WebP, lazy load, sizing  |
| Font Optimization          | `next/font`; zero layout shift                           |
| Middleware                 | Edge functions for auth, redirects, rewrites             |

### 8.2 App Router (Next.js 13+)

```
app/
├── layout.tsx          ← root layout (shared UI)
├── page.tsx            ← / route
├── loading.tsx         ← Suspense boundary for this route
├── error.tsx           ← error boundary for this route
├── not-found.tsx       ← 404 for this route
├── users/
│   ├── page.tsx        ← /users route
│   └── [id]/
│       ├── page.tsx    ← /users/:id route
│       └── edit/
│           └── page.tsx ← /users/:id/edit route
├── (auth)/             ← route group (no URL segment)
│   ├── login/page.tsx
│   └── register/page.tsx
└── api/
    └── users/
        └── route.ts    ← /api/users API route
```

### 8.3 Server vs Client Components

```tsx
// Server Component (default) — runs ONLY on server
// Can: async/await, access DB directly, read env vars, read files
// Cannot: useState, useEffect, event handlers, browser APIs

async function UserList() {
  const users = await db.query('SELECT * FROM users'); // direct DB access
  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}

// Client Component — runs on client (and SSR hydration)
// Must add 'use client' directive at top
'use client';

import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Composition Pattern:** Wrap server component around client component to pass server data down.

```tsx
// Server Component (page.tsx)
async function ProductPage({ params }) {
  const product = await getProduct(params.id); // server-side fetch
  return <ProductDetail product={product} />;  // passes data to client
}

// Client Component (product-detail.tsx)
'use client';
function ProductDetail({ product }) {
  const [quantity, setQuantity] = useState(1);
  return (
    <div>
      <h1>{product.name}</h1>
      <QuantitySelector value={quantity} onChange={setQuantity} />
    </div>
  );
}
```

### 8.4 Data Fetching in App Router

```tsx
// Server Component with async fetch (SSR / SSG)
async function BlogPost({ params }) {
  // fetch is extended by Next.js with caching
  const post = await fetch(`https://api.example.com/posts/${params.id}`, {
    next: { revalidate: 60 },  // ISR: revalidate every 60s
    // cache: 'no-store'       // SSR: always fresh
    // cache: 'force-cache'    // SSG: cache permanently
  }).then(r => r.json());

  return <article>{post.content}</article>;
}

// generateStaticParams for SSG
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map(post => ({ id: post.id.toString() }));
}

// generateMetadata for dynamic SEO
export async function generateMetadata({ params }) {
  const post = await getPost(params.id);
  return { title: post.title, description: post.excerpt };
}
```

### 8.5 API Routes (Route Handlers)

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url);
  const page = Number(searchParams.get('page') ?? 1);

  const users = await db.getUsers({ page });
  return NextResponse.json({ success: true, data: users });
}

export async function POST(request: NextRequest) {
  const body = await request.json();

  const result = createUserSchema.safeParse(body);
  if (!result.success) {
    return NextResponse.json({ error: result.error }, { status: 400 });
  }

  const user = await db.createUser(result.data);
  return NextResponse.json({ data: user }, { status: 201 });
}

// app/api/users/[id]/route.ts
export async function GET(request: NextRequest, { params }: { params: { id: string } }) {
  const user = await db.getUser(params.id);
  if (!user) return NextResponse.json({ error: 'Not found' }, { status: 404 });
  return NextResponse.json({ data: user });
}
```

### 8.6 Middleware

```typescript
// middleware.ts (root of project)
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const token = request.cookies.get('access-token')?.value;

  // Protect dashboard routes
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    if (!token) {
      return NextResponse.redirect(new URL('/login', request.url));
    }
  }

  // Add custom header
  const response = NextResponse.next();
  response.headers.set('X-Request-ID', crypto.randomUUID());
  return response;
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
};
```

### 8.7 Rendering Strategies

| Strategy | When HTML is Generated          | Data Freshness | Use Case                        |
|----------|---------------------------------|----------------|---------------------------------|
| SSG      | Build time                      | Stale          | Blog posts, docs, marketing     |
| ISR      | Build time + background refresh | Near-fresh     | Product pages, news             |
| SSR      | Each request                    | Always fresh   | User dashboards, personalization|
| CSR      | Client (after JS loads)         | Always fresh   | Admin panels, real-time apps    |

### 8.8 Image Optimization

```tsx
import Image from 'next/image';

// Automatically: WebP conversion, lazy loading, prevents CLS
<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority          // preload above-the-fold images
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
  sizes="(max-width: 768px) 100vw, 50vw"
/>

// Remote images — configure in next.config.js
const nextConfig = {
  images: {
    remotePatterns: [
      { protocol: 'https', hostname: 's3.amazonaws.com' },
    ],
  },
};
```

### 8.9 Server Actions

```tsx
// Server Action — form submission without API route
'use server';

async function createPost(formData: FormData) {
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;

  await db.createPost({ title, content });
  revalidatePath('/posts');  // revalidate cached page
  redirect('/posts');
}

// In component
<form action={createPost}>
  <input name="title" />
  <textarea name="content" />
  <button type="submit">Create</button>
</form>
```

### 8.10 Next.js Configuration

```javascript
// next.config.js
const nextConfig = {
  reactStrictMode: true,
  experimental: {
    serverActions: true,
  },
  redirects: async () => [
    { source: '/old-blog/:slug', destination: '/blog/:slug', permanent: true },
  ],
  headers: async () => [
    {
      source: '/(.*)',
      headers: [
        { key: 'X-Content-Type-Options', value: 'nosniff' },
        { key: 'X-Frame-Options', value: 'DENY' },
      ],
    },
  ],
};
```

---

## 9. Styling

### 9.1 Tailwind CSS

```tsx
// Utility-first CSS — no context switching
<button className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 active:scale-95 transition-all duration-200 disabled:opacity-50">
  Submit
</button>

// Responsive design
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" />

// Dark mode
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-white" />

// Dynamic classes — use full class names (not string concatenation)
const variants = {
  primary: 'bg-blue-600 text-white',
  danger:  'bg-red-600 text-white',
};
<button className={`px-4 py-2 ${variants[variant]}`} />
```

### 9.2 CSS Modules

```tsx
// Button.module.css
.button { padding: 8px 16px; border-radius: 4px; }
.primary { background: blue; color: white; }

// Button.tsx
import styles from './Button.module.css';
<button className={`${styles.button} ${styles.primary}`}>Click</button>
```

### 9.3 Styled Components / Emotion

```tsx
import styled from 'styled-components';

const Button = styled.button<{ variant: 'primary' | 'danger' }>`
  padding: 8px 16px;
  border-radius: 4px;
  background: ${({ variant }) => variant === 'danger' ? 'red' : 'blue'};
  color: white;
  &:hover { opacity: 0.9; }
`;

<Button variant="primary">Click</Button>
```

---

## 10. Testing React Components

### 10.1 React Testing Library

```tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('renders and submits login form', async () => {
  const user = userEvent.setup();
  const mockLogin = jest.fn().mockResolvedValue({ token: 'abc' });

  render(<LoginForm onLogin={mockLogin} />);

  // Query by accessible role / label
  await user.type(screen.getByLabelText(/email/i), 'alice@example.com');
  await user.type(screen.getByLabelText(/password/i), 'Secret123!');
  await user.click(screen.getByRole('button', { name: /sign in/i }));

  await waitFor(() => {
    expect(mockLogin).toHaveBeenCalledWith({
      email: 'alice@example.com',
      password: 'Secret123!',
    });
  });
});

// Testing async loading state
test('shows loading spinner then data', async () => {
  render(<UserProfile userId="1" />);
  expect(screen.getByRole('progressbar')).toBeInTheDocument();
  await screen.findByText('Alice');  // waits for element
  expect(screen.queryByRole('progressbar')).not.toBeInTheDocument();
});
```

**Query Priority (best → worst):**
1. `getByRole` — accessible role + name
2. `getByLabelText` — form inputs
3. `getByPlaceholderText` — placeholders
4. `getByText` — visible text
5. `getByTestId` — `data-testid` attribute (last resort)

### 10.2 Mocking API Calls (MSW)

```javascript
// handlers.js — Mock Service Worker
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/users', () => {
    return HttpResponse.json([{ id: 1, name: 'Alice' }]);
  }),
  http.post('/api/users', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({ id: 2, ...body }, { status: 201 });
  }),
];
```

---

## 11. Accessibility (a11y)

| Principle         | Description                                                  |
|-------------------|--------------------------------------------------------------|
| Semantic HTML     | Use `<button>`, `<nav>`, `<main>`, `<article>` correctly    |
| ARIA roles/labels | Add `aria-label`, `aria-describedby` when semantics unclear |
| Keyboard nav      | All interactive elements focusable; logical tab order        |
| Focus management  | Move focus to modal/dialog when opened; return on close      |
| Color contrast    | WCAG 2.1 AA: 4.5:1 ratio for normal text                    |
| Alt text          | All meaningful images have descriptive `alt`                 |
| Form labels       | Every input has an associated `<label>`                      |
| Live regions      | `aria-live="polite"` for dynamic status updates              |

```tsx
// Accessible button
<button
  onClick={handleDelete}
  aria-label="Delete post titled My Post"
  aria-busy={isDeleting}
  disabled={isDeleting}
>
  {isDeleting ? 'Deleting...' : 'Delete'}
</button>

// Skip link (keyboard users skip nav)
<a href="#main-content" className="sr-only focus:not-sr-only">
  Skip to main content
</a>
```

---

## 12. Web Performance

### 12.1 Core Web Vitals

| Metric | Full Name                  | Good Threshold | What It Measures               |
|--------|----------------------------|----------------|-------------------------------|
| LCP    | Largest Contentful Paint   | < 2.5s         | Loading performance           |
| FID    | First Input Delay          | < 100ms        | Interactivity responsiveness  |
| CLS    | Cumulative Layout Shift    | < 0.1          | Visual stability              |
| INP    | Interaction to Next Paint  | < 200ms        | Overall responsiveness        |
| TTFB   | Time to First Byte         | < 800ms        | Server response speed         |
| FCP    | First Contentful Paint     | < 1.8s         | Initial paint                 |

### 12.2 Performance Techniques

| Technique                  | Impact                                          |
|----------------------------|-------------------------------------------------|
| Code splitting + lazy load | Reduce initial JS bundle                        |
| Image optimization         | WebP, correct sizing, lazy loading              |
| Font preloading            | `<link rel="preload" as="font">`                |
| Prefetching                | `<Link prefetch>` in Next.js                    |
| Tree shaking               | Remove dead code (bundler eliminates unused exports) |
| Compression (gzip/brotli)  | 60-90% smaller transfer size                    |
| CDN + caching              | Static assets served from edge                  |
| `<script defer>`           | Don't block HTML parsing                        |
| React.memo + virtualization| Reduce unnecessary renders for long lists       |

---

## 13. SEO in Next.js

```tsx
// app/layout.tsx — static metadata
export const metadata = {
  title: { default: 'MyApp', template: '%s | MyApp' },
  description: 'The best app ever',
  openGraph: {
    type: 'website',
    url: 'https://myapp.com',
    images: [{ url: 'https://myapp.com/og.png', width: 1200, height: 630 }],
  },
  twitter: { card: 'summary_large_image' },
};

// app/blog/[slug]/page.tsx — dynamic metadata
export async function generateMetadata({ params }) {
  const post = await getPost(params.slug);
  return {
    title: post.title,
    description: post.excerpt,
    alternates: { canonical: `https://myapp.com/blog/${params.slug}` },
  };
}

// Structured data (JSON-LD)
<script
  type="application/ld+json"
  dangerouslySetInnerHTML={{ __html: JSON.stringify({
    '@context': 'https://schema.org',
    '@type': 'Article',
    headline: post.title,
    author: { '@type': 'Person', name: post.author },
  })}}
/>
```

---

## 14. Security in Frontend

| Threat                | Mitigation                                                    |
|-----------------------|---------------------------------------------------------------|
| XSS                   | React escapes output by default; avoid `dangerouslySetInnerHTML`; use DOMPurify for HTML |
| CSRF                  | Use `SameSite=Strict` cookies; CSRF tokens for state-changing requests |
| Clickjacking          | `X-Frame-Options: DENY` header                               |
| Sensitive data in URL | Put tokens/IDs in POST body or headers, not query params      |
| Token storage         | Store JWT in memory or HttpOnly cookie; **never** localStorage for sensitive tokens |
| Dependency vulnerabilities | Run `npm audit` regularly; use Dependabot                |
| Environment variables | Never expose secrets in `NEXT_PUBLIC_` vars; those are bundled into client JS |

---

## 15. Quick Reference — Interview Topics

| Topic                   | Key Points                                                       |
|-------------------------|------------------------------------------------------------------|
| Virtual DOM             | In-memory representation; diffing algorithm avoids full re-render|
| Reconciliation          | React compares old/new VDOM trees; updates only changed DOM nodes|
| Fiber Architecture      | Incremental rendering; pause/resume work; priority scheduling   |
| useState batching       | React 18 batches all state updates (async too) by default       |
| useEffect timing        | Runs after paint; `useLayoutEffect` runs synchronously after DOM |
| Strict Mode             | Double-invokes functions in dev to find side effects             |
| Server vs Client Components | Server: zero JS, DB access; Client: interactivity           |
| SSR vs SSG vs ISR       | SSR: per-request; SSG: build-time; ISR: SSG + revalidation      |
| Hydration               | Client attaches event listeners to server-rendered HTML          |
| React 19 features       | Actions, `use()` hook, enhanced Server Components, form actions  |
