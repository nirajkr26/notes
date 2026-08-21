# React — Detailed Notes & Interview Guide

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://linkedin.com/in/nirajkr26)

> **Focus:** Modern React fundamentals, rendering, Hooks, state management, effects, performance, concurrent UI, React 19+, Server Components concepts, testing, accessibility, architecture, and interview preparation.

---

## 1. What is React?

React is a JavaScript library for building user interfaces from reusable components. It uses a **declarative** programming model: you describe what the UI should look like for a given state, and React handles updating the DOM.

React is commonly used for:

- Single-page applications.
- Dashboards and complex web applications.
- Component libraries and design systems.
- Server-rendered applications through frameworks such as Next.js.
- Interactive forms and data-heavy interfaces.
- Progressive enhancement and server-driven UI architectures.

### Core mental model

```text
Props + State + Context
          |
          v
     Render phase
          |
          v
   React element tree
          |
          v
 Reconciliation / commit
          |
          v
       DOM / host UI
```

React does **not** mean "the DOM is replaced on every render." A render produces a new description of UI; React compares it with the previous result and commits the necessary host changes.

---

## 2. Why React?

### Declarative UI

Imperative code says how to change the UI. Declarative React code describes the desired result.

```jsx
function Counter({ count }) {
  return <button>{count}</button>;
}
```

When `count` changes, React re-renders the component and determines the required update.

### Component model

Components provide reusable boundaries:

```jsx
function UserCard({ user }) {
  return (
    <article>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </article>
  );
}
```

Good components usually have a clear responsibility, predictable inputs, and minimal hidden side effects.

---

## 3. JSX

JSX is syntax that lets JavaScript code describe a UI tree. It is transformed by a compiler into JavaScript calls compatible with React's element creation model.

```jsx
const element = <h1 className="title">Hello {name}</h1>;
```

### JSX rules

- Components use capitalized names: `<UserCard />`.
- DOM properties use React-compatible names such as `className` and `htmlFor`.
- JavaScript expressions go inside `{}`.
- Components return one tree; fragments can avoid an extra DOM node.
- JSX attributes use expressions for non-string values.

```jsx
const count = 3;
return (
  <>
    <h1>{count}</h1>
    <button disabled={count === 0}>Continue</button>
  </>
);
```

### JSX is an expression

You can assign JSX to variables, return it from functions, and conditionally choose it.

```jsx
const content = isLoading ? <Spinner /> : <Results />;
```

---

## 4. Components

Modern React applications primarily use function components.

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name}</h1>;
}
```

### Component purity

A component should behave like a pure function during rendering:

> Given the same inputs, rendering should produce the same result and should not mutate external state.

Bad:

```jsx
function BadComponent({ items }) {
  items.sort(); // mutates a prop during render
  return <List items={items} />;
}
```

Better:

```jsx
function GoodComponent({ items }) {
  const sorted = [...items].sort();
  return <List items={sorted} />;
}
```

Side effects belong in event handlers or appropriate effects, not in render logic.

---

## 5. Props

Props are inputs passed from a parent to a child.

```jsx
function Button({ label, disabled, onClick }) {
  return (
    <button disabled={disabled} onClick={onClick}>
      {label}
    </button>
  );
}
```

Props can contain:

- Strings.
- Numbers.
- Booleans.
- Objects and arrays.
- Functions.
- JSX elements.
- Render functions.

### Props are read-only

Never mutate props directly. If a child needs to request a change, call a callback supplied by the parent or use shared state.

### `children`

```jsx
function Card({ children }) {
  return <section className="card">{children}</section>;
}

<Card>
  <h2>Profile</h2>
  <p>Details</p>
</Card>;
```

`children` is just a prop containing the nested React nodes.

---

## 6. State with `useState`

State is data owned by a component that can trigger a re-render when updated.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(c => c + 1)}>
      {count}
    </button>
  );
}
```

### Functional state updates

Use the functional form when the next value depends on the previous value:

```jsx
setCount(c => c + 1);
```

This is especially important when multiple updates are queued in the same event or transition.

### State is a snapshot

A render sees a snapshot of state. Calling a setter schedules another render; it does not mutate the state variable already captured by the current render.

```jsx
function Example() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    console.log(count); // still the current render's value
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

### Lazy initialization

For expensive initial calculations:

```jsx
const [state, setState] = useState(() => expensiveInitialState());
```

The initializer is used to compute the initial state rather than recalculating it as an ordinary expression on every render.

---

## 7. Updating Objects and Arrays in State

React state should be treated as immutable.

```jsx
const [user, setUser] = useState({ name: '', age: 0 });

setUser(prev => ({
  ...prev,
  name: 'Alice'
}));
```

For arrays:

```jsx
setItems(prev => [...prev, newItem]);
setItems(prev => prev.filter(item => item.id !== id));
setItems(prev => prev.map(item =>
  item.id === id ? { ...item, done: true } : item
));
```

Avoid:

```jsx
items.push(newItem);
setItems(items);
```

because the array reference was mutated rather than replaced.

---

## 8. Derived State

Do not store data in state if it can be calculated from existing props/state.

Bad:

```jsx
const [firstName, setFirstName] = useState('A');
const [lastName, setLastName] = useState('B');
const [fullName, setFullName] = useState('A B');
```

Better:

```jsx
const fullName = `${firstName} ${lastName}`;
```

Duplicated state creates synchronization problems and unnecessary effects.

---

## 9. Conditional Rendering

```jsx
{isLoading && <Spinner />}

{error ? <ErrorMessage error={error} /> : <Results />}
```

For more complex conditions:

```jsx
let content;

if (isLoading) {
  content = <Spinner />;
} else if (error) {
  content = <ErrorMessage error={error} />;
} else {
  content = <Results data={data} />;
}

return <main>{content}</main>;
```

### Beware of `&&` with numbers

```jsx
{items.length && <List items={items} />}
```

If `items.length` is `0`, React can render `0`. Prefer:

```jsx
{items.length > 0 && <List items={items} />}
```

---

## 10. Lists and Keys

```jsx
<ul>
  {users.map(user => (
    <li key={user.id}>{user.name}</li>
  ))}
</ul>
```

A key helps React identify which sibling corresponds to which previous element.

### Good keys

- Stable.
- Unique among siblings.
- Based on item identity.
- Independent of array position when items can be reordered.

### Why array indexes can be dangerous

```jsx
items.map((item, index) => <Row key={index} item={item} />)
```

If items are inserted, removed, or reordered, React may associate existing component state with the wrong item.

Index keys can be acceptable for genuinely static lists where ordering and membership never change.

---

## 11. Reconciliation

Reconciliation is React's process of comparing the result of a new render with the previous render and determining what should be committed.

Important identity signals include:

- Element type.
- Key.
- Position in the tree.
- Component identity.

Example:

```jsx
{isLoggedIn ? <Dashboard /> : <Login />}
```

Switching between different component types at the same position can cause the previous component subtree to be removed and a new one mounted.

Keys can deliberately control identity:

```jsx
<Chat key={recipient.id} recipient={recipient} />
```

Changing the key tells React that this is a different component identity, which resets its local state.

---

## 12. Render Phase vs Commit Phase

React work can be thought of as two major stages.

### Render phase

React calculates what the UI should look like. Rendering should be pure and can be restarted or abandoned by modern React scheduling.

### Commit phase

React applies the necessary host environment changes and runs effects at their appropriate times.

Interview rule:

> Never rely on a render happening exactly once or in a particular timing pattern for correctness.

Development Strict Mode can intentionally invoke certain logic more than once to expose unsafe side effects.

---

## 13. `useEffect`

`useEffect` synchronizes a component with an **external system** such as:

- Network subscriptions.
- Browser APIs.
- Timers.
- Third-party widgets.
- Event listeners.
- External stores.

```jsx
useEffect(() => {
  const connection = createConnection(roomId);
  connection.connect();

  return () => connection.disconnect();
}, [roomId]);
```

### Effect lifecycle

Conceptually:

```text
Render
  |
  v
Commit
  |
  v
Run effect
  |
  +---- dependency changes ----> cleanup -> new effect
  |
  +---- unmount ----------------> cleanup
```

### Important rule

Do not use effects merely to transform data for rendering.

Bad:

```jsx
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);
```

Better:

```jsx
const fullName = `${firstName} ${lastName}`;
```

### Dependency array

```jsx
useEffect(() => {
  // synchronize with roomId
}, [roomId]);
```

The dependency list should reflect the reactive values used by the effect. Lint rules can catch many missing dependencies.

---

## 14. Effect Cleanup

Cleanup prevents stale subscriptions, timers, and event handlers.

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/users/${id}`, { signal: controller.signal })
    .then(r => r.json())
    .then(setUser)
    .catch(err => {
      if (err.name !== 'AbortError') setError(err);
    });

  return () => controller.abort();
}, [id]);
```

Cleanup runs before the effect is re-run with changed dependencies and when the component is removed.

---

## 15. `useRef`

`useRef` stores a mutable value that persists between renders without causing a re-render when `.current` changes.

### DOM reference

```jsx
const inputRef = useRef(null);

function focusInput() {
  inputRef.current?.focus();
}

return <input ref={inputRef} />;
```

### Mutable instance value

```jsx
const requestId = useRef(0);

function load() {
  requestId.current += 1;
}
```

Use refs for imperative escape hatches and mutable values that do not belong in the rendered output.

Do not use refs as a general replacement for state.

---

## 16. `useContext`

Context provides values to descendants without manually passing props through every level.

```jsx
const ThemeContext = createContext(null);

function App() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext value={{ theme, setTheme }}>
      <Page />
    </ThemeContext>
  );
}
```

Consumer:

```jsx
function Button() {
  const { theme } = useContext(ThemeContext);
  return <button className={theme}>Save</button>;
}
```

Context is useful for values such as theme, locale, authentication information, or shared configuration. It is not automatically a replacement for every form of global state management.

### Context performance

If a provider creates a new object every render, consumers may re-render when the context value identity changes.

```jsx
const value = useMemo(
  () => ({ theme, setTheme }),
  [theme]
);
```

Memoization should be used when it addresses an actual rendering cost rather than as a reflex.

---

## 17. `useReducer`

`useReducer` centralizes complex state transitions.

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 };
    case 'decrement':
      return { ...state, count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  return (
    <>
      <span>{state.count}</span>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```

Use it when state transitions are numerous, related, or easier to express as explicit actions.

---

## 18. Rules of Hooks

Hooks must be called:

1. At the top level of a React function component or custom Hook.
2. In the same order on every render.

Bad:

```jsx
if (enabled) {
  useEffect(() => {}, []);
}
```

Better:

```jsx
useEffect(() => {
  if (!enabled) return;
  // work
}, [enabled]);
```

Do not call Hooks inside loops, nested functions, event handlers, or arbitrary conditionals.

---

## 19. Custom Hooks

Custom Hooks extract reusable stateful behavior.

```jsx
function useOnlineStatus() {
  const [online, setOnline] = useState(navigator.onLine);

  useEffect(() => {
    const on = () => setOnline(true);
    const off = () => setOnline(false);

    window.addEventListener('online', on);
    window.addEventListener('offline', off);

    return () => {
      window.removeEventListener('online', on);
      window.removeEventListener('offline', off);
    };
  }, []);

  return online;
}
```

A custom Hook is a convention for reusing logic; it does not create shared state between component instances. Each call gets its own Hook state unless it connects to a shared external store.

---

## 20. Event Handling

```jsx
function Form() {
  function handleSubmit(event) {
    event.preventDefault();
    // submit
  }

  return <form onSubmit={handleSubmit}>...</form>;
}
```

Pass a function rather than invoking it during render:

```jsx
<button onClick={handleClick}>Save</button>
```

Not:

```jsx
<button onClick={handleClick()}>Save</button>
```

For dynamic arguments:

```jsx
<button onClick={() => deleteUser(user.id)}>Delete</button>
```

---

## 21. Controlled vs Uncontrolled Inputs

### Controlled

React state is the source of truth.

```jsx
function SearchBox() {
  const [query, setQuery] = useState('');

  return (
    <input
      value={query}
      onChange={e => setQuery(e.target.value)}
    />
  );
}
```

### Uncontrolled

The DOM owns the current value; a ref can read it.

```jsx
function Form() {
  const inputRef = useRef(null);

  function submit() {
    console.log(inputRef.current.value);
  }

  return <input ref={inputRef} defaultValue="hello" />;
}
```

Use controlled inputs when the UI needs to react immediately to the value. Uncontrolled inputs can be useful for simple forms or integrations where DOM state is sufficient.

---

## 22. Form Actions and Modern React Forms

Modern React includes APIs designed to simplify asynchronous form actions.

```jsx
function SignupForm() {
  async function action(formData) {
    const email = formData.get('email');
    await saveUser(email);
  }

  return (
    <form action={action}>
      <input name="email" type="email" required />
      <button type="submit">Create account</button>
    </form>
  );
}
```

In framework environments, form actions can integrate with server-side mutations. The exact server behavior depends on the framework and runtime.

---

## 23. `useActionState`

`useActionState` helps manage the result of an action, including form submissions.

Conceptually:

```jsx
const [state, formAction, isPending] = useActionState(
  async (previousState, formData) => {
    const result = await submitForm(formData);
    return result;
  },
  initialState
);
```

It is useful when an async action needs to return structured state such as validation errors or success information.

---

## 24. `useFormStatus`

`useFormStatus` lets components inside a form access submission status.

```jsx
function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button disabled={pending}>
      {pending ? 'Saving...' : 'Save'}
    </button>
  );
}
```

The Hook must be used in a component that is a descendant of the relevant `<form>`.

---

## 25. `useOptimistic`

Optimistic UI updates the interface before the server confirms the mutation.

```jsx
const [optimisticMessages, addOptimisticMessage] = useOptimistic(
  messages,
  (current, message) => [...current, { ...message, sending: true }]
);
```

A typical flow:

```text
User action
   |
   +--> update optimistic UI immediately
   |
   +--> send request
   |
   +--> success -> keep confirmed state
   |
   +--> failure -> reconcile / show error
```

Optimistic UI improves perceived latency but requires a clear failure/reconciliation strategy.

---

## 26. Transitions

Transitions mark updates as non-urgent so urgent interactions can remain responsive.

```jsx
const [isPending, startTransition] = useTransition();

function handleChange(value) {
  setInput(value);

  startTransition(() => {
    setSearchQuery(value);
  });
}
```

Use transitions for updates that can wait, such as rendering a large search result view after an input change.

Do not put urgent controlled input updates themselves inside a transition when the input needs immediate feedback.

---

## 27. `startTransition`

You can also use the standalone API:

```jsx
startTransition(() => {
  setTab(nextTab);
});
```

The goal is not to make the operation faster. It tells React that the resulting rendering work has lower urgency and may be interrupted by more important work.

---

## 28. `useDeferredValue`

`useDeferredValue` lets a component use a deferred version of a value.

```jsx
const deferredQuery = useDeferredValue(query);

return <SearchResults query={deferredQuery} />;
```

The current input can stay responsive while a heavier result tree catches up.

Difference:

- `startTransition`: you control which state update is non-urgent.
- `useDeferredValue`: you receive a deferred version of an existing value.

---

## 29. Suspense

`<Suspense>` provides a declarative loading boundary for components that suspend.

```jsx
<Suspense fallback={<Spinner />}>
  <ProfilePage />
</Suspense>
```

Suspense is not merely a generic `isLoading` replacement. It works with React mechanisms and integrations that can suspend rendering, including lazy-loaded components and framework data-loading systems.

### Lazy loading

```jsx
const Settings = lazy(() => import('./Settings.jsx'));

<Suspense fallback={<Spinner />}>
  <Settings />
</Suspense>
```

---

## 30. Error Boundaries

Error boundaries provide a fallback UI when descendant rendering throws an error.

Conceptually:

```jsx
<ErrorBoundary fallback={<ErrorPage />}>
  <Dashboard />
</ErrorBoundary>
```

Traditional error boundaries are implemented using class lifecycle APIs or provided by libraries/frameworks. A plain function component with `try/catch` around JSX does not replace an error boundary because render errors occur during React's rendering process.

Error boundaries do not automatically catch every error, such as arbitrary asynchronous errors outside rendering or event handler errors. Handle those according to the source of the error.

---

## 31. `React.memo`

`memo` can skip re-rendering a component when its props are unchanged according to the chosen comparison.

```jsx
const UserCard = memo(function UserCard({ user }) {
  return <div>{user.name}</div>;
});
```

Memoization is an optimization, not a semantic requirement.

A memoized child can still re-render when:

- Its props change.
- Its own state changes.
- A context it reads changes.
- Its parent supplies a different relevant value.

---

## 32. `useMemo`

`useMemo` caches the result of a calculation between renders when dependencies are unchanged.

```jsx
const sortedUsers = useMemo(
  () => [...users].sort(compareUsers),
  [users]
);
```

Good uses include genuinely expensive calculations or stable derived references that help a memoized child.

Do not use `useMemo` everywhere. It adds complexity and has its own cost.

---

## 33. `useCallback`

`useCallback` caches a function reference.

```jsx
const handleSelect = useCallback(
  id => setSelectedId(id),
  []
);
```

It is most useful when function identity affects a meaningful optimization, such as passing a callback to a memoized child or satisfying a dependency contract.

It does not make the function's execution itself faster.

---

## 34. React Compiler

The React Compiler is designed to automatically optimize component code by analyzing it and applying memoization where appropriate.

Important interview distinction:

> Manual `useMemo`, `useCallback`, and `memo` are optimization tools; a compiler can automate many memoization decisions, but developers still need correct component logic, state architecture, and side-effect discipline.

Compiler adoption and configuration depend on the project's React/toolchain setup. Do not assume every React application automatically uses it.

---

## 35. React 19+ Modern APIs

React 19 introduced a number of APIs and patterns aimed at reducing boilerplate around forms, actions, refs, and asynchronous UI.

Important topics to know:

| API / feature | Purpose |
|---|---|
| Actions | Model async mutations and form workflows |
| `useActionState` | Track action result/state |
| `useFormStatus` | Read pending form status from descendants |
| `useOptimistic` | Build optimistic UI |
| `use()` | Read a Promise or context during rendering where supported |
| Ref as prop | Modern function components can receive `ref` as a prop in React 19 patterns |
| Server Components | Render components on the server in supported frameworks/environments |

Always distinguish React APIs from framework-specific server behavior.

---

## 36. The `use()` API

`use()` can read resources such as Promises and Context in supported React environments.

Conceptual example:

```jsx
function Message({ messagePromise }) {
  const message = use(messagePromise);
  return <p>{message}</p>;
}
```

A component that suspends while waiting can be wrapped by Suspense.

```jsx
<Suspense fallback={<Spinner />}>
  <Message messagePromise={promise} />
</Suspense>
```

`use()` has different rules from ordinary Hooks; it can be called conditionally in ways standard Hooks cannot. Follow the current React documentation and compiler/linter rules for the supported patterns.

---

## 37. Server Components

React Server Components (RSC) allow some components to render in a server environment and send a representation of the result to the client instead of shipping all component code to the browser.

Conceptually:

```text
Server
  |
  | render server components
  v
React server payload
  |
  +---- client component references
  v
Browser
```

Potential benefits:

- Less client JavaScript for server-only UI.
- Direct server-side access to data sources in supported architectures.
- Better separation between server-only and interactive client code.

Server Components are an architecture capability, not simply "SSR." A framework such as Next.js determines much of the application-level integration.

---

## 38. Server Components vs SSR

These concepts are different.

| Concept | Meaning |
|---|---|
| SSR | Server renders HTML for a request/build scenario |
| Server Components | Components execute on the server and participate in a React server payload architecture |
| CSR | Browser performs the primary rendering work |
| SSG | HTML/output generated ahead of requests |

An application can combine several of these techniques.

---

## 39. Client Components

In RSC-based frameworks, a client component is code that must execute in the browser because it needs client-side interactivity or browser APIs.

Typical reasons:

- `useState`.
- Browser event handlers.
- `useEffect`.
- Access to browser-only APIs.
- Client-side subscriptions.

A client component boundary can increase the amount of JavaScript sent to the browser, so keep it as narrow as practical.

---

## 40. Portals

Portals render children into a different DOM location while preserving React tree relationships.

```jsx
createPortal(
  <Modal />,
  document.getElementById('modal-root')
);
```

Useful for:

- Modals.
- Tooltips.
- Dropdowns that must escape clipping containers.
- Overlay UI.

Events still participate in the React tree even though the DOM node is mounted elsewhere.

---

## 41. Refs and Imperative Handles

Sometimes a parent needs a narrow imperative API from a child.

```jsx
function Input({ ref }) {
  useImperativeHandle(ref, () => ({
    focus() {
      inputRef.current?.focus();
    }
  }));

  const inputRef = useRef(null);
  return <input ref={inputRef} />;
}
```

Use imperative APIs sparingly. Prefer declarative props when possible.

---

## 42. Lifting State Up

If two components need to coordinate the same state, move the state to their closest common owner.

```text
          Parent state
          /          \
       Child A     Child B
          |            |
       reads         updates
```

This creates a single source of truth.

Avoid moving all state to the top of the application. Keep state as close as practical to the components that own it.

---

## 43. State Ownership

A useful classification:

| State | Good owner |
|---|---|
| Input text | Form/component |
| Modal open state | Feature/component |
| Theme | Context/app shell |
| Auth session | App-level store/context/server session |
| Cached server data | Server-state library/framework cache |
| URL filters | Router/URL |
| Large global workflow | Dedicated state architecture |

A common mistake is putting server cache, URL state, and local UI state into one global store without a clear reason.

---

## 44. Server State vs Client State

Server state has properties that local UI state does not:

- Remote ownership.
- Network latency.
- Caching.
- Staleness.
- Refetching.
- Deduplication.
- Pagination.
- Mutations and invalidation.

Libraries such as TanStack Query can model these concerns explicitly. Frameworks may provide their own caching/data-fetching model.

---

## 45. State Management Choices

### Local state

Use `useState` or `useReducer` for component/feature state.

### Context

Use for values naturally shared through a tree.

### Redux Toolkit

Useful for large applications needing explicit centralized state transitions, middleware, devtools, and predictable patterns.

### Zustand and similar stores

Useful when you want a lightweight external store with selective subscriptions.

### TanStack Query

Designed for server-state caching and synchronization rather than arbitrary UI state.

### Rule of thumb

Start local. Lift when necessary. Introduce global state only when the ownership problem actually requires it.

---

## 46. Rendering Performance

Performance problems usually come from too much work, too much frequency, or too much data.

First identify the bottleneck using profiling tools rather than adding memoization blindly.

Useful techniques:

- Keep state local.
- Avoid unnecessary context updates.
- Memoize expensive calculations when justified.
- Use `memo` where prop stability makes it useful.
- Virtualize huge lists.
- Split bundles.
- Lazy-load non-critical features.
- Use transitions for non-urgent rendering.
- Avoid unnecessary effects that cause render chains.

---

## 47. React DevTools Profiler

React DevTools can help identify:

- Components that re-render.
- Render durations.
- Component update causes.
- Expensive subtrees.

A disciplined workflow is:

```text
Observe
  -> Profile
  -> Identify expensive work
  -> Change one thing
  -> Profile again
```

Do not optimize based solely on intuition.

---

## 48. Large Lists and Virtualization

Rendering 50,000 DOM nodes is expensive even if each row is simple.

Virtualization renders only the visible window plus a small buffer.

Conceptually:

```text
Dataset: 50,000 rows

Visible viewport
+----------------------+
| row 1200             |
| row 1201             |
| row 1202             |
| ...                  |
| row 1218             |
+----------------------+

DOM contains only a small window.
```

Choose a virtualization library appropriate to the project and accessibility requirements.

---

## 49. Code Splitting

Dynamic imports allow a bundle to be split.

```jsx
const Reports = lazy(() => import('./Reports.jsx'));
```

Then:

```jsx
<Suspense fallback={<Spinner />}>
  <Reports />
</Suspense>
```

Common candidates include rarely used routes, admin screens, heavy editors, charts, and large feature modules.

---

## 50. Accessibility

React does not automatically make an application accessible.

Prefer semantic HTML:

```jsx
<button onClick={save}>Save</button>
```

instead of:

```jsx
<div onClick={save}>Save</div>
```

Important practices:

- Use semantic elements.
- Label form controls.
- Manage keyboard interaction.
- Preserve visible focus.
- Use meaningful headings.
- Provide accessible names.
- Use ARIA only when native semantics are insufficient.
- Test with keyboard navigation and assistive technologies.

---

## 51. Security in React

React escapes text inserted through JSX by default, reducing many common HTML injection risks.

```jsx
<div>{userInput}</div>
```

is safer than inserting raw HTML.

### `dangerouslySetInnerHTML`

```jsx
<div dangerouslySetInnerHTML={{ __html: html }} />
```

This is dangerous when `html` contains untrusted content. Sanitize untrusted HTML with a well-maintained sanitizer and define a clear trust boundary.

Other concerns:

- Do not put secrets in client bundles.
- Validate authorization on the server.
- Treat client-side checks as UX, not security.
- Protect authentication/session flows.
- Avoid unsafe URL handling.
- Keep dependencies patched.

---

## 52. Data Fetching Pattern

A robust data-fetching UI usually models at least:

```text
idle/loading
    |
    +--> success -> data
    |
    +--> error   -> error state
```

For mutations, also consider:

- Pending.
- Validation failure.
- Network failure.
- Retry.
- Cancellation.
- Optimistic update.
- Cache invalidation.

Do not put every network request inside `useEffect` by default. Framework loaders and server components can be better suited for route-level data, while dedicated server-state libraries can handle caching and synchronization.

---

## 53. Avoiding Race Conditions

Suppose a user types:

```text
r -> re -> rea -> react
```

and requests finish out of order. A stale response can overwrite newer data.

Use cancellation or request identity checks.

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/search?q=${encodeURIComponent(query)}`, {
    signal: controller.signal
  })
    .then(r => r.json())
    .then(setResults)
    .catch(err => {
      if (err.name !== 'AbortError') setError(err);
    });

  return () => controller.abort();
}, [query]);
```

---

## 54. Avoiding Prop Drilling

Prop drilling is not inherently bad. Passing props through one or two levels can be clearer than introducing global state.

Use alternatives when the same data must pass through many unrelated layers:

- Component composition.
- Context.
- External state stores.
- Feature-specific state architecture.

Prefer the simplest dependency mechanism that makes ownership clear.

---

## 55. Composition Over Inheritance

React encourages composition.

```jsx
function Layout({ sidebar, children }) {
  return (
    <div className="layout">
      <aside>{sidebar}</aside>
      <main>{children}</main>
    </div>
  );
}
```

Composition keeps components flexible without building deep class hierarchies.

---

## 56. Compound Components

Compound components share state through context while providing a flexible API.

```jsx
<Tabs>
  <Tabs.List>
    <Tabs.Tab value="profile">Profile</Tabs.Tab>
    <Tabs.Tab value="billing">Billing</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel value="profile">...</Tabs.Panel>
  <Tabs.Panel value="billing">...</Tabs.Panel>
</Tabs>
```

This pattern is common in component libraries.

---

## 57. Higher-Order Components

A Higher-Order Component (HOC) is a function that takes a component and returns an enhanced component.

```jsx
function withAuth(Component) {
  return function Authenticated(props) {
    if (!isAuthenticated()) return <Login />;
    return <Component {...props} />;
  };
}
```

HOCs remain useful in some libraries and legacy systems, but Hooks and composition are often simpler for new application logic.

---

## 58. Render Props

A render prop passes a function that determines what UI to render.

```jsx
<DataProvider>
  {data => <Results data={data} />}
</DataProvider>
```

Hooks often replace render props for logic reuse, but the pattern remains useful when rendering needs to remain explicitly controlled by a consumer.

---

## 59. Testing React Components

A strong test strategy focuses on user-visible behavior rather than implementation details.

Typical stack:

- Unit tests for pure logic.
- React Testing Library for component behavior.
- Browser/end-to-end tests for critical flows.

Example:

```jsx
render(<LoginForm onLogin={mockLogin} />);

await user.type(screen.getByLabelText(/email/i), 'a@example.com');
await user.click(screen.getByRole('button', { name: /login/i }));

expect(mockLogin).toHaveBeenCalled();
```

Prefer accessible queries such as role, label, and visible text.

---

## 60. Strict Mode

`<StrictMode>` enables additional development-time checks.

```jsx
<StrictMode>
  <App />
</StrictMode>
```

In development, React may intentionally re-run certain lifecycle-related logic to expose unsafe side effects. This behavior is not the same as React making duplicate production requests.

If an effect breaks under Strict Mode, investigate whether its setup/cleanup logic is correctly designed instead of simply disabling Strict Mode.

---

## 61. Common React Anti-Patterns

| Anti-pattern | Problem | Better approach |
|---|---|---|
| Mutating state | React may not observe the intended change | Replace objects/arrays |
| Derived data in state | Synchronization bugs | Calculate during render |
| Effect for every calculation | Extra render chains | Derive directly |
| Index as key for reorderable data | Wrong component identity | Stable IDs |
| Giant global store | Hard ownership and coupling | Local/feature state |
| Memoizing everything | Complexity without benefit | Profile first |
| Fetching blindly in effects | Race/cache/loading complexity | Framework/server-state tools |
| Secrets in frontend code | Secrets are exposed | Keep secrets server-side |
| Clickable `<div>` for buttons | Accessibility problems | Semantic `<button>` |
| Disabling Strict Mode to hide bugs | Masks lifecycle problems | Fix setup/cleanup |

---

## 62. React Architecture for Large Applications

A scalable project can be organized by feature rather than only by technical type.

```text
src/
├── app/
│   ├── routes/
│   ├── providers/
│   └── App.jsx
├── features/
│   ├── auth/
│   ├── users/
│   └── billing/
├── components/
│   ├── ui/
│   └── layout/
├── hooks/
├── services/
├── lib/
└── tests/
```

A feature can own its components, hooks, API functions, state, and tests. This reduces cross-feature coupling.

---

## 63. React + TypeScript

Type component props explicitly.

```tsx
type UserCardProps = {
  user: {
    id: string;
    name: string;
  };
  onSelect: (id: string) => void;
};

function UserCard({ user, onSelect }: UserCardProps) {
  return (
    <button onClick={() => onSelect(user.id)}>
      {user.name}
    </button>
  );
}
```

Useful TypeScript concepts for React:

- Props types.
- Discriminated unions for component variants.
- Generic reusable components.
- Event types.
- `ReactNode` for children-like content.
- Avoiding unnecessary `any`.

---

## 64. React Router Concepts

Routing libraries manage URL-to-component relationships.

Typical concepts:

- Nested routes.
- Route parameters.
- Search parameters.
- Navigation.
- Redirects.
- Protected/authorized routes.
- Error routes.
- Data loaders/actions in supported routers.

Keep URL state in the URL when it should be shareable, bookmarkable, or navigable with browser history.

---

## 65. React and Next.js

React is the UI library. Next.js is a framework that provides application-level capabilities around React.

Next.js can provide:

- Routing.
- Server Components integration.
- Server rendering.
- Static generation.
- Data fetching patterns.
- Server actions/features.
- Image/font optimization.
- Deployment conventions.

Interview answer:

> React defines the component and rendering model; Next.js supplies a production application framework around React.

---

## 66. React Mental Model for Interviews

When debugging a React problem, ask in this order:

1. What state/props changed?
2. Which component owns that state?
3. Why did the component render?
4. What does the render produce?
5. Did element/component identity change?
6. Is an effect synchronizing with a real external system?
7. Is context causing broad updates?
8. Is expensive work happening repeatedly?
9. Would a transition, memoization, virtualization, or code split actually address the bottleneck?
10. Can profiling confirm the diagnosis?

---

# 67. React Interview Questions & Answers

## Q1. What is React?

**Answer:** React is a JavaScript library for building user interfaces from components. It uses a declarative model where UI is described as a function of state and props.

**Interview point:** React is a library, while frameworks such as Next.js add routing, server rendering, data-fetching, and deployment conventions.

---

## Q2. What is JSX?

**Answer:** JSX is syntax that lets developers write UI-like markup inside JavaScript. A compiler transforms JSX into JavaScript expressions that create React elements.

---

## Q3. What is a component?

**Answer:** A component is a reusable unit of UI and behavior. In modern React it is commonly a function that receives props and returns React elements.

---

## Q4. What is the difference between props and state?

**Answer:** Props are inputs supplied by a parent; state is data owned by a component that can change over time and trigger re-renders.

| Props | State |
|---|---|
| Passed into component | Owned by component |
| Read-only from child perspective | Updated through state APIs |
| Used for configuration/data flow | Used for changing UI |

---

## Q5. Why should state not be mutated directly?

**Answer:** React state should be treated as immutable. Replacing the state with a new object/array gives React a new identity and makes updates predictable. Direct mutation can cause stale UI and broken memoization assumptions.

---

## Q6. What is reconciliation?

**Answer:** Reconciliation is React's process of comparing a new rendered element tree with the previous one to determine which changes should be committed to the host environment.

---

## Q7. Why are keys required in lists?

**Answer:** Keys give React stable identity for sibling elements so it can correctly match items between renders, especially when items are inserted, deleted, or reordered.

---

## Q8. Why is using array index as a key sometimes bad?

**Answer:** When list order or membership changes, indexes can point to different logical items. React may then preserve component state for the wrong item. Stable item IDs are usually better.

---

## Q9. What causes a component to re-render?

**Answer:** Common causes include its state changing, its parent rendering and producing new props, a context value it consumes changing, or an external store subscription notifying it. A parent render does not necessarily mean every descendant must perform expensive work; React can bail out in appropriate cases.

---

## Q10. Does a re-render always mean the DOM changes?

**Answer:** No. React can render a new element tree and determine that the host output is effectively unchanged. A render is a calculation; DOM mutation is part of the commit work.

---

## Q11. What is the virtual DOM?

**Answer:** It is a common shorthand for React's in-memory representation of UI elements. React uses its element tree and reconciliation process to determine host updates. The virtual DOM should not be described as a literal second DOM that is always diffed wholesale.

---

## Q12. What is `useState`?

**Answer:** `useState` lets a function component retain state between renders and provides a setter that schedules an update.

---

## Q13. Why use functional updates?

**Answer:** When the next state depends on previous state, the functional form ensures the update is calculated from the correct queued state.

```jsx
setCount(c => c + 1);
```

---

## Q14. What is `useEffect` for?

**Answer:** `useEffect` is for synchronizing React with external systems. Examples include subscriptions, timers, browser APIs, and third-party widgets. It should not be the default mechanism for calculating derived values.

---

## Q15. What is an effect cleanup function?

**Answer:** Cleanup reverses the setup work. It runs before an effect is re-run due to changed dependencies and when the component is removed. Typical examples are unsubscribing, removing event listeners, aborting requests, and clearing timers.

---

## Q16. Why does an effect run twice in development sometimes?

**Answer:** Strict Mode can intentionally perform extra development-time setup/cleanup cycles to expose effects that are not resilient or that contain hidden side effects. It is a development check, not evidence that production always executes the effect twice.

---

## Q17. What is a stale closure?

**Answer:** A closure can capture values from an older render. If an async callback or effect uses an outdated value because dependencies or state update patterns are incorrect, it can observe stale data.

---

## Q18. What is `useRef` used for?

**Answer:** It stores a mutable value that persists between renders without causing a render when changed. It is commonly used for DOM references, timers, previous values, and imperative integrations.

---

## Q19. `useState` vs `useRef`?

**Answer:** State updates cause React to schedule rendering; changing `ref.current` does not. Use state for values that affect UI and refs for mutable values that should persist without driving UI updates.

---

## Q20. What is `useContext`?

**Answer:** It reads a context value from the nearest matching provider. Context is useful for values naturally shared through a component tree, but broad context updates can cause many consumers to re-render.

---

## Q21. What is `useReducer`?

**Answer:** `useReducer` manages state through explicit actions and a reducer function. It is useful when state transitions are complex or related.

---

## Q22. What are the Rules of Hooks?

**Answer:** Call Hooks only at the top level of React components or custom Hooks, and call them consistently in the same order on every render. This allows React to associate Hook state with the correct call position.

---

## Q23. What is a custom Hook?

**Answer:** A custom Hook is a function beginning with `use` that composes built-in or other custom Hooks to reuse stateful logic. Each invocation has its own Hook state unless the implementation connects to shared state.

---

## Q24. What is lifting state up?

**Answer:** Moving shared state to the closest common ancestor of components that need it, creating a single source of truth.

---

## Q25. What is prop drilling?

**Answer:** Passing props through intermediate components that do not use the values themselves. It is not always a problem; composition or context can help when the dependency becomes deep or widespread.

---

## Q26. What is `React.memo`?

**Answer:** `memo` can skip rendering a component when its props are equivalent according to its comparison. It is an optimization and does not prevent updates from the component's own state or consumed context.

---

## Q27. `useMemo` vs `useCallback`?

**Answer:** `useMemo` caches a calculated value; `useCallback` caches a function reference.

```text
useMemo     -> value
useCallback -> function
```

Neither should be added automatically without a performance or dependency reason.

---

## Q28. When should you use `useMemo`?

**Answer:** When an expensive calculation is repeated unnecessarily or when stable reference identity is useful to a meaningful optimization. Measure before and after when performance is important.

---

## Q29. What is `useTransition`?

**Answer:** It lets React know that certain state updates are non-urgent. React can keep urgent interactions responsive while rendering the transition in the background/interruption-friendly scheduling model.

---

## Q30. What is `useDeferredValue`?

**Answer:** It provides a deferred version of a value so a heavier subtree can lag behind an urgent value, such as a text input.

---

## Q31. What is Suspense?

**Answer:** Suspense defines a boundary that can display fallback UI while a child subtree is waiting on a supported suspending resource. It also integrates with lazy-loaded components and framework data-loading systems.

---

## Q32. What is an error boundary?

**Answer:** An error boundary displays fallback UI when descendant rendering throws an error. It is different from a normal JavaScript `try/catch` around JSX and does not catch every possible asynchronous or event-handler error.

---

## Q33. What is controlled input?

**Answer:** An input whose current value is driven by React state.

```jsx
<input value={value} onChange={e => setValue(e.target.value)} />
```

---

## Q34. Controlled vs uncontrolled forms?

**Answer:** Controlled forms keep values in React state; uncontrolled forms let the DOM maintain values and commonly use refs to read them. Controlled forms are useful when rendering depends on current input values.

---

## Q35. What is batching?

**Answer:** React can group multiple state updates so they produce fewer renders/commits. The exact batching behavior depends on the React version and execution context, but application code should use functional updates when updates depend on previous state rather than relying on immediate state mutation.

---

## Q36. Why doesn't `setState` immediately change the current state variable?

**Answer:** State is associated with a render snapshot. Calling a setter schedules another render; the current function continues using the values captured by its current render.

---

## Q37. How do you reset a component's state?

**Answer:** One common technique is changing its `key`, which gives React a new component identity.

```jsx
<Form key={userId} userId={userId} />
```

You can also explicitly reset individual state variables when that better represents the domain logic.

---

## Q38. What is a portal?

**Answer:** A portal renders React children into a different DOM container while keeping them in the same React tree, which is useful for modals, overlays, and tooltips.

---

## Q39. What is composition in React?

**Answer:** Composition means building components by combining smaller components and passing children/slots rather than relying on inheritance. It makes UI APIs flexible and reusable.

---

## Q40. What is a Higher-Order Component?

**Answer:** A HOC is a function that accepts a component and returns an enhanced component. Hooks and composition often provide a simpler approach for new logic reuse, but HOCs remain relevant in existing ecosystems.

---

## Q41. What is server-side rendering?

**Answer:** SSR generates HTML on the server for a request or rendering process, allowing the browser to receive useful markup before client-side JavaScript takes over the interactive parts.

---

## Q42. SSR vs Server Components?

**Answer:** SSR is a rendering strategy for producing HTML on the server. Server Components are a component architecture where selected components execute on the server and participate in a server component payload. They solve related but different problems.

---

## Q43. What are Server Components?

**Answer:** Server Components execute on the server and can reduce client-side JavaScript for UI that does not need browser interactivity. They are typically integrated through a framework or server architecture.

---

## Q44. What is a Client Component?

**Answer:** In an RSC architecture, a Client Component is code that must execute in the browser, commonly because it needs state, effects, event handlers, or browser APIs.

---

## Q45. What is `useActionState`?

**Answer:** It helps associate state with an async action, especially form submissions, and provides the action function plus its pending state in supported React patterns.

---

## Q46. What is `useOptimistic`?

**Answer:** It lets the UI show an optimistic version of state while an async action is in progress. The application must reconcile the optimistic result with success or failure.

---

## Q47. What is `useFormStatus`?

**Answer:** It provides status information for a parent form action to components rendered inside that form, commonly enabling pending submit buttons.

---

## Q48. What is the React Compiler?

**Answer:** It is a compiler technology designed to optimize React components automatically, including many memoization opportunities. It reduces the need for some manual memoization but does not remove the need for correct architecture and pure rendering.

---

## Q49. How do you optimize a slow React application?

**Answer:** First profile it. Then identify whether the bottleneck is excessive rendering, expensive calculations, large DOM trees, JavaScript bundle size, network latency, or server work. Apply targeted fixes such as state locality, memoization, virtualization, code splitting, transitions, caching, and better data loading.

---

## Q50. Why can context hurt performance?

**Answer:** When a provider's value changes identity, consumers reading that context can update. A very broad context containing frequently changing values can therefore cause widespread rendering. Split contexts or use selective external-store subscriptions when appropriate.

---

## Q51. How would you optimize a 100,000-row table?

**Answer:** Do not render all rows. Use virtualization, pagination, or windowing. Keep row components cheap, use stable keys, avoid unnecessary object/function recreation when it matters, and profile scrolling/rendering performance.

---

## Q52. How would you prevent stale search results?

**Answer:** Cancel previous requests or track request identity so older responses cannot overwrite newer results. `AbortController` is a common browser API for cancellation.

---

## Q53. Why does an effect cause an infinite loop?

**Answer:** Often because the effect updates state that changes one of its dependencies, or because a dependency is recreated on every render. Fix the state/data flow rather than merely removing dependencies.

---

## Q54. How do you share authentication state?

**Answer:** Use an appropriate session/auth architecture. A React context or external store can expose client-visible auth state, but authorization must be enforced on the server. Never trust a client boolean such as `isAdmin` as a security boundary.

---

## Q55. How do you protect secrets in React?

**Answer:** Do not ship secrets to the browser. Anything embedded in client JavaScript can be inspected by users. Keep API secrets, private credentials, and privileged operations on trusted server infrastructure.

---

## Q56. Why is `dangerouslySetInnerHTML` dangerous?

**Answer:** It bypasses React's normal text escaping and inserts raw HTML. If the HTML contains untrusted input, it can introduce XSS. Sanitize untrusted HTML and establish a strict trust boundary.

---

## Q57. How would you test a login form?

**Answer:** Test behavior from the user's perspective: enter credentials, submit, observe loading/error/success states, and verify the expected callback/navigation/result. Prefer accessible queries such as `getByRole` and `getByLabelText`.

---

## Q58. Why should tests avoid implementation details?

**Answer:** Tests coupled to internal state or private component structure break during harmless refactors. User-visible behavior is a more stable contract.

---

## Q59. What is the difference between local state and server state?

**Answer:** Local state belongs to the UI/application process. Server state is remote data with concerns such as caching, staleness, refetching, retries, synchronization, and invalidation. They benefit from different abstractions.

---

## Q60. What is your preferred React architecture?

**Answer:** Start with local state and feature-level components. Lift state only when sharing requires it. Keep server state in a data-fetching/cache abstraction or framework mechanism. Use context for naturally shared tree data, and introduce a global store only when there is a real cross-feature ownership problem.

---

# 68. Scenario-Based Interview Questions

## Scenario 1: A child re-renders every time the parent types in a search box. What do you do?

**Answer:** Profile first. If the child does not need the update, keep state lower in the tree or memoize the child if its props can remain stable. Avoid blindly wrapping everything in `memo` and `useCallback`.

---

## Scenario 2: A list displays the wrong checkbox state after sorting. What is likely wrong?

**Answer:** Check the keys. If array indexes are used as keys for a reorderable list, component state can follow positions instead of logical items. Use stable item IDs.

---

## Scenario 3: An API response from an old search overwrites a new search result.

**Answer:** There is a race condition. Cancel stale requests with `AbortController` or associate each request with an identity and only accept the latest result.

---

## Scenario 4: A `useEffect` runs continuously.

**Answer:** Inspect its dependency list. An object, array, or function created during render may have a new identity every time, or the effect may be updating one of its dependencies. Determine whether the effect is needed at all.

---

## Scenario 5: A form is slow because every keystroke renders a huge result list.

**Answer:** Keep the input update urgent and move the expensive result update into a transition or use a deferred value. Also profile the result list and consider virtualization or server-side filtering.

---

## Scenario 6: A context provider causes the entire app to render.

**Answer:** Check the provider value and consumer scope. Split unrelated contexts, keep rapidly changing values local, and use selective subscriptions when the architecture requires them. Profile before and after.

---

## Scenario 7: You need to reset a form whenever the selected user changes.

**Answer:** If the form represents a new component identity for each user, a stable key can be appropriate:

```jsx
<UserForm key={user.id} user={user} />
```

Alternatively, explicitly synchronize/reset the fields if preserving some state is required.

---

## Scenario 8: A modal is clipped by `overflow: hidden`.

**Answer:** Render the modal through a portal into an appropriate top-level DOM container. Also manage focus, keyboard interaction, and accessible dialog semantics.

---

## Scenario 9: A dashboard's first load is too large.

**Answer:** Analyze the bundle. Route-level code splitting, lazy loading, removing unnecessary dependencies, server rendering/server components where appropriate, image optimization, and reducing client-only boundaries can help.

---

## Scenario 10: Users see a button twice after clicking rapidly.

**Answer:** The UI likely allows duplicate submissions. Track pending state, disable or guard repeated submission, make the server mutation idempotent when possible, and provide a clear pending state.

---

# 69. Quick Comparison Tables

## `useState` vs `useReducer` vs `useRef`

| Feature | `useState` | `useReducer` | `useRef` |
|---|---|---|---|
| Causes render on update | Yes | Yes | No |
| Good for UI state | Yes | Yes | Usually no |
| Complex transitions | Sometimes | Excellent | No |
| Mutable persistent value | Not directly | Not directly | Yes |
| DOM reference | No | No | Yes |

## `useMemo` vs `useCallback`

| | `useMemo` | `useCallback` |
|---|---|---|
| Caches | Value | Function |
| Primary purpose | Avoid repeated calculation / stabilize reference | Stabilize function reference |
| Makes execution intrinsically faster | No | No |

## Context vs External Store

| | Context | External store |
|---|---|---|
| Tree-based dependency | Excellent | Optional |
| Selective subscriptions | Limited by usage pattern | Often strong |
| Simple shared config | Excellent | Overkill |
| Large frequently changing state | Can be costly | Often better |

---

# 70. React Interview Checklist

- [ ] Explain declarative UI and component composition.
- [ ] Explain JSX and components.
- [ ] Explain props vs state.
- [ ] Explain state snapshots and functional updates.
- [ ] Explain immutability.
- [ ] Explain keys and reconciliation.
- [ ] Explain render vs commit.
- [ ] Explain `useEffect` and cleanup.
- [ ] Explain stale closures.
- [ ] Explain `useRef`.
- [ ] Explain `useContext`.
- [ ] Explain `useReducer`.
- [ ] Explain custom Hooks and Rules of Hooks.
- [ ] Explain controlled vs uncontrolled inputs.
- [ ] Explain memoization and when not to use it.
- [ ] Explain Suspense and lazy loading.
- [ ] Explain transitions and deferred values.
- [ ] Explain Server Components vs SSR.
- [ ] Explain React 19 action/form APIs.
- [ ] Explain optimistic UI.
- [ ] Explain React Compiler at a high level.
- [ ] Explain performance profiling.
- [ ] Explain virtualization.
- [ ] Explain accessibility.
- [ ] Explain XSS risks and client/server trust boundaries.
- [ ] Explain testing from the user's perspective.
- [ ] Explain server state vs client state.
- [ ] Diagnose effect loops and race conditions.
- [ ] Design a scalable feature-based React architecture.

---

# 71. Final Interview Mental Model

When answering a React interview question, structure the answer as:

```text
1. Define the concept
        |
        v
2. Explain why it exists
        |
        v
3. Give a small example
        |
        v
4. Mention trade-offs
        |
        v
5. Give a production scenario
```

The strongest React candidates do not just memorize Hooks. They understand **state ownership, rendering identity, effects, asynchronous UI, server/client boundaries, accessibility, performance measurement, and predictable data flow**.
