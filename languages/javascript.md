# JavaScript — Detailed Interview, Exam & Practical Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

> **Focus:** modern ECMAScript fundamentals, execution model, closures, objects, async JavaScript, modules, browser APIs, Node.js concepts, performance, security, debugging, and interview preparation.

---

## 1. JavaScript at a Glance

JavaScript is a dynamically typed, garbage-collected, multi-paradigm language standardized by **ECMAScript**. It supports procedural, object-oriented, and functional programming styles.

JavaScript itself is the language specification; browsers and runtimes such as Node.js provide the host environment and APIs.

```text
JavaScript / ECMAScript
        |
        +--> language semantics
        |    variables, objects, functions, promises, modules...
        |
        +--> Host environment
             Browser: DOM, Fetch, Web APIs, events
             Node.js: filesystem, sockets, processes, timers
```

### Important characteristics

- Dynamically typed.
- Prototype-based object model.
- Functions are first-class values.
- Lexical scoping and closures.
- Automatic memory management.
- Event-loop based asynchronous programming.
- Supports synchronous and asynchronous code.
- Standardized by TC39 as ECMAScript.

### Current-version note

ECMAScript is standardized through yearly editions. Avoid writing application code that depends on a feature merely because it appears in a proposal; distinguish **standardized ECMAScript** from proposals and runtime-specific APIs.

---

## 2. Values and Data Types

JavaScript has seven primitive types plus objects:

| Type | Example | Notes |
|---|---|---|
| `undefined` | `undefined` | Missing/uninitialized value |
| `null` | `null` | Intentional absence |
| `boolean` | `true` | Logical value |
| `number` | `42`, `3.14`, `NaN` | IEEE-754 double precision |
| `bigint` | `123n` | Arbitrary-precision integer |
| `string` | `'hello'` | Immutable primitive sequence |
| `symbol` | `Symbol('id')` | Unique primitive identifier |
| `object` | `{}`, `[]`, `new Date()` | Mutable reference values |

### `NaN`

`NaN` means “not a number”, but it has type `number`.

```js
Number('hello'); // NaN
Number.isNaN(NaN); // true
NaN === NaN;       // false
Object.is(NaN, NaN); // true
```

Prefer `Number.isNaN()` when you specifically want to test the `NaN` value rather than coercing arbitrary input.

### `null` and `undefined`

```js
let x;
console.log(x); // undefined

const user = { middleName: null };
```

`undefined` commonly means a value was not supplied/found; `null` is an explicit empty value. The historical result `typeof null === 'object'` is a language quirk.

---

## 3. Primitive vs Reference Semantics

Primitives are immutable values. Objects are mutable values accessed through references.

```js
let a = 10;
let b = a;
b++;
console.log(a, b); // 10 11

const user1 = { name: 'A' };
const user2 = user1;
user2.name = 'B';
console.log(user1.name); // B
```

The second example does not mean JavaScript variables “store pointers” in a simple language-level sense; the important behavior is that both variables refer to the same object.

### Shallow vs deep copy

```js
const copy = { ...user1 }; // shallow copy
const arrCopy = [...items]; // shallow copy
```

Nested objects remain shared:

```js
const a = { profile: { city: 'Delhi' } };
const b = { ...a };
b.profile.city = 'Pune';
// a.profile.city is also 'Pune'
```

For deep cloning, `structuredClone()` is often preferable to JSON serialization because it supports more data types and preserves more semantics.

---

## 4. Variables: `var`, `let`, `const`

```js
var oldStyle = 1;
let count = 2;
const limit = 3;
```

| Feature | `var` | `let` | `const` |
|---|---|---|---|
| Scope | Function | Block | Block |
| Redeclare in same scope | Yes | No | No |
| Reassign | Yes | Yes | No |
| TDZ | No | Yes | Yes |
| Must initialize | No | No | Yes |

### Temporal Dead Zone

`let` and `const` bindings exist from the beginning of their lexical scope but cannot be accessed before initialization.

```js
console.log(value); // ReferenceError
let value = 10;
```

“Not hoisted” is an oversimplification. The better explanation is that the binding is created but remains inaccessible in the TDZ.

### `const` does not freeze objects

```js
const user = { name: 'Alice' };
user.name = 'Bob'; // allowed
// user = {};      // TypeError
```

`const` prevents reassignment of the binding, not mutation of the referenced object.

---

## 5. Equality and Type Coercion

### Strict equality

```js
5 === 5;      // true
5 === '5';    // false
```

### Loose equality

`==` performs coercion according to the ECMAScript equality algorithm and can produce surprising results.

```js
5 == '5';     // true
0 == false;   // true
null == undefined; // true
```

For most application code, prefer `===` and `!==` because the type relationship is explicit.

### `Object.is`

`Object.is()` differs from `===` for a few special cases:

```js
Object.is(NaN, NaN); // true
Object.is(0, -0);     // false
0 === -0;             // true
```

---

## 6. Truthiness and Short-Circuiting

Falsy values include:

```text
false, 0, -0, 0n, '', null, undefined, NaN
```

Most other values are truthy, including `[]` and `{}`.

```js
const name = input || 'Anonymous';
```

Be careful: `||` treats `0`, `false`, and `''` as missing.

Use nullish coalescing when only `null`/`undefined` should trigger the fallback:

```js
const page = suppliedPage ?? 1;
```

### Optional chaining

```js
const city = user?.address?.city;
const result = service?.getResult?.();
```

Optional chaining short-circuits when the left side is `null` or `undefined`.

---

## 7. Functions

Functions are first-class values: they can be stored, passed, returned, and attached to objects.

```js
function add(a, b) {
  return a + b;
}

const multiply = (a, b) => a * b;
```

### Declaration vs expression

Function declarations are initialized before ordinary code in their scope, whereas function expressions follow normal variable initialization rules.

```js
greet();
function greet() {}

// run(); // ReferenceError with const binding in TDZ
const run = () => {};
```

### Arrow functions

Arrow functions:

- Have lexical `this`.
- Do not have their own `arguments` object.
- Cannot be used as constructors with `new`.
- Do not have a `prototype` property as ordinary functions do.

```js
const square = x => x * x;
const makeUser = name => ({ name });
```

---

## 8. Parameters, Rest and Spread

### Default parameters

```js
function greet(name = 'World') {
  return `Hello, ${name}`;
}
```

### Rest parameters

```js
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
```

Rest collects remaining arguments into an array.

### Spread syntax

```js
const combined = [...a, ...b];
const user = { ...defaults, ...overrides };
```

Object spread is shallow. Later properties overwrite earlier properties.

---

## 9. Scope, Lexical Environment and Closures

JavaScript uses lexical scoping. A function can access variables from the scope where it was defined.

### Closure

A closure is a function together with access to its surrounding lexical environment.

```js
function createCounter() {
  let count = 0;
  return () => ++count;
}

const next = createCounter();
next(); // 1
next(); // 2
```

The `count` binding remains reachable because the returned function closes over it.

### Common use cases

- Data privacy.
- Function factories.
- Memoization.
- Callbacks.
- Module encapsulation.
- Event handlers.

### Loop closure pitfall

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// 3 3 3
```

`var` has function scope. `let` creates a per-iteration binding:

```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// 0 1 2
```

---

## 10. Hoisting and Execution Contexts

A useful conceptual model is:

```text
Source code
   |
   v
Creation / setup of bindings
   |
   v
Execution of statements
```

The exact ECMAScript execution model is more precise than the informal phrase “moves declarations to the top”.

Important cases:

- `var` binding is initialized to `undefined`.
- `let`/`const` are in TDZ until initialization.
- Function declarations can be called before their source position in many normal cases.

---

## 11. `this`

For ordinary functions, `this` is determined by the call form.

```js
const user = {
  name: 'Alice',
  greet() {
    return this.name;
  }
};

user.greet(); // Alice
```

### Explicit binding

```js
function greet(prefix) {
  return `${prefix} ${this.name}`;
}

const user = { name: 'Alice' };
greet.call(user, 'Hi');
greet.apply(user, ['Hello']);
const bound = greet.bind(user);
bound('Hey');
```

### Arrow functions and `this`

Arrow functions capture `this` lexically; they do not create their own receiver binding.

This makes arrows useful for callbacks but often inappropriate for object methods when the method needs the object as `this`.

---

## 12. Objects and Property Descriptors

Objects are collections of properties. Properties have attributes such as:

- `value`
- `writable`
- `enumerable`
- `configurable`
- Accessor properties may have `get`/`set`.

```js
const user = {};
Object.defineProperty(user, 'id', {
  value: 1,
  writable: false,
  enumerable: true,
  configurable: false
});
```

Useful reflection APIs:

```js
Object.keys(obj);
Object.values(obj);
Object.entries(obj);
Object.getOwnPropertyDescriptors(obj);
```

---

## 13. Prototypes and Classes

JavaScript's object model is prototype-based.

Property lookup conceptually follows:

```text
object's own properties
       ↓
[[Prototype]]
       ↓
prototype's [[Prototype]]
       ↓
...
       ↓
null
```

Use `Object.getPrototypeOf()` rather than relying on `__proto__` in application code.

### Classes

`class` provides syntax for constructing objects and defining prototype methods.

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello ${this.name}`;
  }
}
```

Methods declared in the class body are normally placed on the prototype rather than copied onto every instance.

### Inheritance

```js
class Employee extends Person {
  constructor(name, role) {
    super(name);
    this.role = role;
  }
}
```

`super()` initializes the parent portion before using `this` in a derived constructor.

### Private fields

```js
class Account {
  #balance = 0;

  deposit(amount) {
    this.#balance += amount;
  }

  get balance() {
    return this.#balance;
  }
}
```

Private fields are language-level private and are distinct from naming conventions such as `_balance`.

---

## 14. Destructuring

```js
const user = { name: 'A', age: 25 };
const { name, age } = user;

const [first, second] = [10, 20];
```

Useful with function parameters:

```js
function printUser({ name, role = 'user' }) {
  console.log(name, role);
}
```

Destructuring is a syntax feature; it does not deep-clone the values.

---

## 15. Arrays and Common Methods

Important methods:

```js
map      // transform each item
filter   // keep matching items
find     // first matching item
some     // at least one match
 every    // all match
reduce   // accumulate
forEach  // side-effect iteration
```

Example:

```js
const activeNames = users
  .filter(user => user.active)
  .map(user => user.name);
```

### `map` vs `forEach`

Use `map` when producing a new array. Use `forEach` when you intentionally perform side effects and do not need a mapped result.

### Mutation

Methods such as `sort()`, `reverse()`, `splice()`, and `push()` mutate arrays. Prefer copying when immutability matters.

```js
const sorted = [...numbers].sort((a, b) => a - b);
```

---

## 16. Map, Set, WeakMap, WeakSet

### `Map`

Stores key/value pairs and allows keys of any value type.

```js
const cache = new Map();
cache.set(user, result);
cache.get(user);
```

### `Set`

Stores unique values.

```js
const unique = [...new Set(items)];
```

### Weak collections

`WeakMap` and `WeakSet` hold object references weakly, enabling garbage collection when objects are otherwise unreachable. They are useful for metadata associated with object lifetimes.

---

## 17. Iterators and Generators

An iterable can be consumed by constructs such as `for...of` and spread.

```js
for (const value of iterable) {
  console.log(value);
}
```

A generator produces values lazily:

```js
function* ids() {
  yield 1;
  yield 2;
  yield 3;
}

for (const id of ids()) console.log(id);
```

Generators are useful for lazy sequences, custom iterators, and controlled stateful iteration.

---

## 18. Promises

A Promise represents the eventual result of an asynchronous operation.

States:

```text
pending → fulfilled
        ↘ rejected
```

Once settled, a promise cannot change to another state.

```js
fetch('/api/user')
  .then(response => response.json())
  .then(user => console.log(user))
  .catch(error => console.error(error))
  .finally(() => console.log('done'));
```

### Promise combinators

| API | Behavior |
|---|---|
| `Promise.all` | Fulfill when all fulfill; reject on first rejection |
| `Promise.allSettled` | Wait for all outcomes |
| `Promise.race` | Settle when first input settles |
| `Promise.any` | Fulfill when first input fulfills; reject if all reject |

Choose the combinator based on business semantics, not just convenience.

---

## 19. `async` / `await`

`async` functions always return a Promise.

```js
async function loadUser() {
  const response = await fetch('/api/user');
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}
```

### Error handling

```js
try {
  const user = await loadUser();
} catch (error) {
  console.error(error);
}
```

### Sequential vs parallel awaits

Bad when requests are independent:

```js
const a = await loadA();
const b = await loadB();
```

Better:

```js
const [a, b] = await Promise.all([loadA(), loadB()]);
```

The second version overlaps waiting time.

---

## 20. Event Loop and Microtasks

JavaScript execution is single-threaded at the level of an individual execution context, while the host environment can perform I/O and other work concurrently.

A simplified model:

```text
Call Stack
    |
    v
Synchronous JS
    |
    +----> Web/host APIs
    |          |
    |          v
    |       Task queues
    |
    v
Microtask queue
    |
    v
Next task / rendering opportunity
```

### Microtasks

Promise reactions and `queueMicrotask()` use the microtask queue.

```js
console.log('A');
setTimeout(() => console.log('timer'), 0);
Promise.resolve().then(() => console.log('microtask'));
console.log('B');
```

Typical output:

```text
A
B
microtask
timer
```

The precise scheduling of rendering and host tasks is environment-specific, but microtasks are generally drained at defined checkpoints before the event loop proceeds to later tasks.

### Important correction

“JavaScript is single-threaded” does **not** mean the machine can only do one thing. Browsers and runtimes use threads and system facilities behind the language execution model for I/O, timers, rendering, workers, and other operations.

---

## 21. Timers

`setTimeout(fn, 0)` does not mean “run immediately”. It schedules a task after the timer conditions are satisfied and after currently executing work and higher-priority queued work allow it to run.

```js
setTimeout(() => console.log('later'), 0);
```

Do not use timers as precise real-time scheduling mechanisms.

---

## 22. Event Handling

Browser events use an event propagation model.

```text
Capture phase
    ↓
Target
    ↓
Bubble phase
```

### Event delegation

Attach one handler to a stable ancestor instead of one handler per child.

```js
list.addEventListener('click', event => {
  const button = event.target.closest('[data-id]');
  if (!button) return;
  remove(button.dataset.id);
});
```

This is useful for dynamic lists and can reduce listener management overhead.

---

## 23. DOM and Browser APIs

The DOM is a browser host API, not part of ECMAScript itself.

Common APIs:

```js
document.querySelector()
document.createElement()
element.addEventListener()
localStorage
fetch()
URL
AbortController
```

### AbortController

Useful for canceling fetch requests and other abortable operations.

```js
const controller = new AbortController();

fetch('/api/data', { signal: controller.signal });
controller.abort();
```

---

## 24. Fetch and HTTP Errors

`fetch()` rejects for failures to perform the request at the network layer, but an HTTP 404/500 response is still a successfully received HTTP response.

Therefore:

```js
const response = await fetch(url);
if (!response.ok) {
  throw new Error(`HTTP ${response.status}`);
}
```

This distinction is a common interview question.

---

## 25. Modules

ES modules use `import` and `export`.

```js
// math.js
export function add(a, b) {
  return a + b;
}

// app.js
import { add } from './math.js';
```

### Named vs default exports

```js
export const version = '1.0';
export default function start() {}
```

Named exports make imported names explicit. Default exports allow the importer to choose a local name.

### Module benefits

- Explicit dependencies.
- File-level scope.
- Reusability.
- Static analysis.
- Better tooling and tree-shaking opportunities.

### CommonJS vs ES modules

Node.js supports both ecosystems.

```js
// CommonJS
const fs = require('node:fs');
module.exports = value;

// ESM
import fs from 'node:fs';
export default value;
```

Do not casually mix the two without understanding package/module configuration.

---

## 26. Error Handling

JavaScript errors can be handled with `try/catch/finally`.

```js
try {
  riskyOperation();
} catch (error) {
  console.error(error);
} finally {
  cleanup();
}
```

### Custom errors

```js
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}
```

Do not catch an error merely to rethrow it unchanged unless you are adding context, logging, cleanup, or transforming it meaningfully.

---

## 27. Memory Management and Garbage Collection

JavaScript engines automatically reclaim unreachable objects.

A simplified reachability model:

```text
GC roots
  |
  v
reachable objects
  |
  v
unreachable objects → collectible
```

Typical roots include active execution contexts and other runtime-managed references.

### Memory leak patterns

Garbage collection cannot reclaim an object that remains reachable accidentally.

Common causes:

- Forgotten event listeners.
- Unbounded caches.
- Long-lived closures retaining large objects.
- Global references.
- Timers/subscriptions that are never cleaned up.
- Detached DOM structures retained by JavaScript.

### WeakMap for metadata

Weak references can help associate metadata with object lifetimes without keeping objects alive solely because of the metadata.

---

## 28. Functional Programming Concepts

JavaScript supports:

- Pure functions.
- Higher-order functions.
- Immutability patterns.
- Function composition.
- Closures.

Example:

```js
const pipe = (...fns) => value =>
  fns.reduce((result, fn) => fn(result), value);
```

Functional style can improve testability, but blindly avoiding all mutation can also create unnecessary allocations. Choose the model based on correctness and performance needs.

---

## 29. Immutability and State Updates

Instead of:

```js
user.address.city = 'Delhi';
```

a state-management system may prefer:

```js
const updated = {
  ...user,
  address: {
    ...user.address,
    city: 'Delhi'
  }
};
```

This makes changes easier to reason about and works well with UI systems that use reference equality to detect updates.

---

## 30. Performance Fundamentals

### Avoid unnecessary work

- Use appropriate data structures.
- Avoid repeated expensive calculations.
- Batch independent asynchronous operations.
- Use lazy computation where appropriate.
- Avoid accidental quadratic algorithms.

### Big-O examples

```js
array[i]           // O(1)
array.includes(x)  // O(n)
map.get(key)       // average O(1)
set.has(value)     // average O(1)
```

These are typical complexity expectations, not guarantees that ignore implementation details.

### Debouncing

Run after activity stops for a period.

Useful for search inputs.

### Throttling

Limit execution frequency over time.

Useful for scroll/resize-related work.

---

## 31. Security Essentials

### XSS

Never insert untrusted HTML without sanitization.

Prefer:

```js
element.textContent = userInput;
```

over unsafe HTML insertion when HTML is not required.

### Prototype pollution

Be careful when merging attacker-controlled object keys into configuration/prototype-sensitive objects.

### CSRF

Primarily a server-side/session security concern. Browser cookie policies such as `SameSite`, CSRF tokens, and origin checks can help depending on architecture.

### Secrets

Never place private API keys or credentials in browser-delivered JavaScript. Anything shipped to the client should be treated as observable by the user.

---

## 32. Node.js-Specific JavaScript

Node.js is a JavaScript runtime, not the JavaScript language itself.

Important concepts:

- Event loop.
- Non-blocking I/O APIs.
- Streams.
- Buffers.
- Worker threads.
- Child processes.
- Modules.
- HTTP servers.

### CPU-bound work

A long synchronous CPU calculation blocks the JavaScript event loop for that execution context.

Use worker threads, child processes, or architectural decomposition when CPU-heavy work must not block latency-sensitive event handling.

---

## 33. Common Interview Questions

### Q1. Is JavaScript interpreted or compiled?

Modern engines use a mixture of parsing, bytecode/interpreter execution, profiling, JIT compilation, optimization, and deoptimization. “Purely interpreted” is an outdated simplification.

### Q2. Why is `typeof null` object?

It is a long-standing language compatibility quirk. Use explicit checks such as `value === null` when needed.

### Q3. `==` vs `===`?

`==` performs coercion; `===` compares without the usual coercion. Prefer strict equality unless you intentionally want the loose-equality semantics.

### Q4. `null` vs `undefined`?

`undefined` generally represents missing/uninitialized values; `null` is an explicit empty value.

### Q5. What is a closure?

A function retaining access to lexical variables from its defining environment even after that outer function has returned.

### Q6. Why are closures useful?

They provide encapsulation, factories, callbacks, memoization, and state that can survive across calls.

### Q7. What is the event loop?

A host scheduling mechanism that coordinates JavaScript execution with queued tasks, microtasks, timers, I/O, and other host activities.

### Q8. Microtask vs task?

Promise reactions and `queueMicrotask` are microtasks; timers and many event callbacks are scheduled as tasks. Microtasks are processed at defined checkpoints and can delay later tasks if continually generated.

### Q9. Why does `setTimeout(fn, 0)` not execute immediately?

It schedules work for a later task; current synchronous work and queued microtasks must be processed first.

### Q10. Promise vs callback?

Promises represent an eventual result and compose naturally with chaining and combinators; callbacks are a lower-level pattern that can become difficult to compose when heavily nested.

### Q11. `Promise.all` vs `Promise.allSettled`?

`all` fails fast when an input rejects; `allSettled` waits for every input and returns each outcome.

### Q12. `async` function return type?

Always a Promise, even if you return a plain value.

### Q13. Why use `Promise.all`?

To run independent asynchronous operations concurrently and wait for all results.

### Q14. Arrow function vs regular function?

The key difference is `this`: arrows capture it lexically; regular functions get `this` from their call form. Arrows also lack their own `arguments` and cannot be constructors.

### Q15. What does `new` do?

Conceptually it creates an object, connects its prototype to the constructor's prototype, calls the constructor with the new object as `this`, and returns the appropriate result.

### Q16. What is the prototype chain?

The linked chain JavaScript uses for property lookup when a property is not found directly on an object.

### Q17. Class vs prototype?

Classes provide convenient syntax over JavaScript's prototype-based inheritance model; they do not turn JavaScript into a class-based runtime in the same sense as C++ or Java.

### Q18. Shallow vs deep copy?

A shallow copy duplicates only the outer container; nested objects remain shared. A deep clone recursively separates supported nested values.

### Q19. Why doesn't `const` make an object immutable?

It prevents rebinding the variable, not mutation of the referenced object.

### Q20. Why does `fetch` not reject on HTTP 404?

HTTP errors are valid HTTP responses. `fetch` normally rejects for failures to complete the request itself; check `response.ok` or `response.status` for HTTP-level failure.

### Q21. What is event delegation?

Handling events at an ancestor and determining the actual target, reducing listener count and working well for dynamic children.

### Q22. What causes memory leaks in JS?

Unintentionally retained references such as listeners, timers, caches, globals, and closures.

### Q23. What is debouncing vs throttling?

Debouncing waits for a quiet period; throttling limits execution frequency.

### Q24. Why can a single synchronous loop make a Node.js server unresponsive?

Because it blocks the event-loop thread from processing other callbacks and I/O completions.

### Q25. What is tree shaking?

A build optimization that removes unused statically analyzable module exports, especially effective with ES module syntax.

---

## 34. Common Mistakes

| Mistake | Better approach |
|---|---|
| Saying JS is “only interpreted” | Explain modern engine execution/JIT behavior |
| Saying JS is “single-threaded, so no concurrency” | Separate language execution from host/runtime concurrency |
| Using `==` casually | Prefer `===` unless coercion is intentional |
| Treating `const` as immutable | It only prevents rebinding |
| Using `forEach(async () => ...)` for awaited sequencing | Use `for...of` or `Promise.all` depending on desired concurrency |
| Assuming `fetch` rejects on 404 | Check `response.ok` |
| Mutating shared arrays/objects unintentionally | Copy/update deliberately |
| Using `setTimeout(0)` as immediate execution | Understand task scheduling |
| Storing unbounded caches | Add eviction/limits |
| Putting secrets in frontend code | Keep secrets server-side |

---

## 35. Quick Revision Checklist

- [ ] Primitive vs object values
- [ ] `var` / `let` / `const`
- [ ] TDZ and hoisting
- [ ] Equality/coercion
- [ ] Truthiness, `??`, `?.`
- [ ] Functions and arrow functions
- [ ] Closures
- [ ] `this`, `call`, `apply`, `bind`
- [ ] Objects and property descriptors
- [ ] Prototypes and classes
- [ ] Destructuring/rest/spread
- [ ] Arrays/Map/Set
- [ ] Iterators/generators
- [ ] Promises and combinators
- [ ] `async`/`await`
- [ ] Event loop/tasks/microtasks
- [ ] DOM/events/fetch
- [ ] ES modules vs CommonJS
- [ ] Errors
- [ ] Garbage collection/leaks
- [ ] Performance/debounce/throttle
- [ ] XSS/CSRF/prototype pollution basics
- [ ] Node.js event-loop implications
- [ ] Interview questions
