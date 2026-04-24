# JavaScript — Super Detailed Interview & Exam Notes

---

## 1. Introduction to JavaScript

JavaScript (JS) is a **lightweight, interpreted, dynamically typed, multi-paradigm** scripting language. Originally created by **Brendan Eich** in 1995 for Netscape Navigator in just 10 days (initially named "Mocha", then "LiveScript", then "JavaScript").

**Key characteristics:**
- Runs in browsers (client-side) and on servers (Node.js)
- Prototype-based OOP
- First-class functions (functions are values)
- Event-driven, non-blocking I/O (via Event Loop)
- Single-threaded (but asynchronous via callbacks/promises/async-await)
- ECMAScript standard (ES5, ES6/2015, ES2016...ES2024)

**JavaScript Engines:**

| Engine | Used in |
|--------|---------|
| V8 | Chrome, Node.js |
| SpiderMonkey | Firefox |
| JavaScriptCore | Safari |
| Chakra | Legacy Edge |

---

## 2. Variables and Declarations

### 2.1 `var`, `let`, `const`

```javascript
var x = 10;    // function-scoped, hoisted, can redeclare
let y = 20;    // block-scoped, not hoisted (TDZ), cannot redeclare
const z = 30;  // block-scoped, must initialize, cannot reassign
```

**Comparison Table:**

| Feature | `var` | `let` | `const` |
|---------|-------|-------|---------|
| Scope | Function | Block | Block |
| Hoisting | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| Re-declaration | ✅ | ❌ | ❌ |
| Re-assignment | ✅ | ✅ | ❌ |
| Global object prop | ✅ | ❌ | ❌ |

**Temporal Dead Zone (TDZ):** Period between entering scope and `let`/`const` initialization. Accessing variable in TDZ throws `ReferenceError`.

```javascript
console.log(a); // undefined (var hoisted)
console.log(b); // ReferenceError: b is not defined (TDZ)
var a = 5;
let b = 10;
```

---

## 3. Data Types

### 3.1 Primitive Types (7)

| Type | Example | Description |
|------|---------|-------------|
| `number` | `42`, `3.14`, `NaN`, `Infinity` | 64-bit float (IEEE 754) |
| `string` | `"hello"`, `'world'`, `` `template` `` | Immutable sequence of chars |
| `boolean` | `true`, `false` | Logical values |
| `null` | `null` | Intentional absence of value |
| `undefined` | `undefined` | Uninitialized variable |
| `symbol` (ES6) | `Symbol("desc")` | Unique, immutable identifier |
| `bigint` (ES2020) | `9007199254740991n` | Arbitrary precision integers |

### 3.2 Reference Type

- **`object`** — all non-primitives: `{}`, `[]`, functions, `Date`, `RegExp`, `Map`, `Set`, etc.

### 3.3 Type Coercion

```javascript
// Implicit coercion (can be surprising!)
"5" + 3;        // "53" (string concatenation)
"5" - 3;        // 2 (numeric subtraction)
"5" * "2";      // 10
true + 1;       // 2
null + 1;       // 1
undefined + 1;  // NaN

// Explicit coercion
Number("42");        // 42
Number(true);        // 1
Number(null);        // 0
Number(undefined);   // NaN
String(42);          // "42"
Boolean(0);          // false
Boolean("");         // false
Boolean(null);       // false
Boolean(undefined);  // false
Boolean(NaN);        // false
// Everything else is truthy
```

### 3.4 `typeof` and `instanceof`

```javascript
typeof 42;            // "number"
typeof "hi";          // "string"
typeof true;          // "boolean"
typeof undefined;     // "undefined"
typeof null;          // "object" ← famous bug in JS!
typeof {};            // "object"
typeof [];            // "object" (use Array.isArray())
typeof function(){}; // "function"
typeof Symbol();      // "symbol"

[] instanceof Array;  // true
{} instanceof Object; // true
```

---

## 4. Operators

```javascript
// Equality
5 == "5";    // true  (loose equality, type coercion)
5 === "5";   // false (strict equality, no coercion)
5 != "5";    // false
5 !== "5";   // true

// Logical
&&   // AND (short-circuit: returns first falsy or last value)
||   // OR (short-circuit: returns first truthy or last value)
!    // NOT
??   // Nullish coalescing (ES2020): returns right if left is null/undefined
?.   // Optional chaining (ES2020)

// Examples
null ?? "default";         // "default"
undefined ?? "default";    // "default"
0 ?? "default";            // 0 (0 is not null/undefined)
0 || "default";            // "default" (0 is falsy)

user?.address?.city;       // undefined if user or address is null/undefined
user?.getName?.();         // calls safely if method exists
```

---

## 5. Functions

### 5.1 Function Declaration vs Expression
```javascript
// Function Declaration — hoisted
function greet(name) { return "Hello, " + name; }

// Function Expression — not hoisted
const greet = function(name) { return "Hello, " + name; };

// Arrow Function (ES6) — concise; no own `this`, `arguments`, `super`
const greet = (name) => "Hello, " + name;
const square = x => x * x;                  // single param, no parens needed
const noop = () => {};                       // no params
const obj = () => ({ key: "value" });        // return object literal (wrap in parens)
```

### 5.2 Arrow Function vs Regular Function

| Feature | Regular Function | Arrow Function |
|---------|-----------------|----------------|
| `this` binding | Own `this` (dynamic) | Inherits `this` (lexical) |
| `arguments` object | ✅ Has own | ❌ No own |
| `new` keyword | ✅ Can be constructor | ❌ Cannot be constructor |
| `prototype` | ✅ Has | ❌ No |
| Hoisting | ✅ (declarations) | ❌ |

### 5.3 Default Parameters (ES6)
```javascript
function greet(name = "World", greeting = "Hello") {
    return `${greeting}, ${name}!`;
}
greet();               // "Hello, World!"
greet("Alice");        // "Hello, Alice!"
```

### 5.4 Rest Parameters and Spread Operator
```javascript
// Rest (...) — collects remaining args into array
function sum(...nums) { return nums.reduce((a, b) => a + b, 0); }
sum(1, 2, 3, 4);   // 10

// Spread (...) — expands iterable
const arr = [1, 2, 3];
const arr2 = [...arr, 4, 5];   // [1, 2, 3, 4, 5]
Math.max(...arr);               // 3

const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 };  // { a:1, b:2, c:3 }
```

### 5.5 IIFE (Immediately Invoked Function Expression)
```javascript
(function() {
    console.log("Executed immediately");
})();

// Arrow IIFE
(() => console.log("Arrow IIFE"))();
// Use: create private scope, avoid global pollution
```

---

## 6. Scope and Closures

### 6.1 Scope

```javascript
// Global scope
var global = "I'm global";

function outer() {
    var outerVar = "outer";     // function scope

    function inner() {
        var innerVar = "inner"; // function scope
        console.log(outerVar);  // can access outer ✅
        console.log(global);    // can access global ✅
    }

    inner();
    // console.log(innerVar);   // ❌ ReferenceError
}
```

**Scope Chain:** When a variable is accessed, JS looks in current scope → outer scope → ... → global scope → `ReferenceError`.

### 6.2 Closures

> A **closure** is a function that retains access to its **lexical scope** even when it executes outside that scope.

```javascript
function makeCounter() {
    let count = 0;           // enclosed variable
    return {
        increment: () => ++count,
        decrement: () => --count,
        value: () => count
    };
}
const counter = makeCounter();
counter.increment();  // 1
counter.increment();  // 2
counter.decrement();  // 1
counter.value();      // 1
// count is private — not accessible directly!
```

**Common closure pitfall:**
```javascript
// Classic bug with var in loops
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 100);  // prints 3, 3, 3
}

// Fix 1: use let
for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 100);  // prints 0, 1, 2
}

// Fix 2: IIFE closure
for (var i = 0; i < 3; i++) {
    ((j) => setTimeout(() => console.log(j), 100))(i);
}
```

---

## 7. Hoisting

JavaScript moves **declarations** to the top of their scope during compilation phase. Only declarations are hoisted, not initializations.

```javascript
// var hoisting
console.log(x);   // undefined (declared but not initialized)
var x = 5;

// function declaration hoisting (fully hoisted)
greet();          // "Hello!" (works!)
function greet() { console.log("Hello!"); }

// let/const — hoisted but in TDZ
console.log(y);   // ReferenceError
let y = 10;

// Function expression — NOT hoisted
greet2();         // TypeError: greet2 is not a function
var greet2 = function() { console.log("Hi"); };
```

---

## 8. `this` Keyword

The value of `this` depends on **how a function is called**, not where it's defined (except arrow functions).

```javascript
// 1. Global context
console.log(this);   // window (browser) / global (Node.js)

// 2. Object method
const obj = {
    name: "Alice",
    greet() { console.log(this.name); }  // this = obj
};
obj.greet();   // "Alice"

// 3. Regular function (non-strict mode)
function show() { console.log(this); }  // this = window/global

// 4. Strict mode
"use strict";
function show() { console.log(this); }  // this = undefined

// 5. Arrow function — lexical this (inherits from surrounding scope)
const obj2 = {
    name: "Bob",
    greet: () => console.log(this.name)  // this = outer scope (NOT obj2)
};
obj2.greet();  // undefined

// 6. Constructor call
function Person(name) { this.name = name; }
const p = new Person("Charlie");  // this = new object

// 7. Explicit binding
function greet(greeting) { return `${greeting}, ${this.name}`; }
const user = { name: "Dave" };
greet.call(user, "Hello");        // "Hello, Dave" — calls immediately
greet.apply(user, ["Hi"]);        // "Hi, Dave" — calls immediately (array args)
const greetDave = greet.bind(user); // returns new function with this = user
greetDave("Hey");                 // "Hey, Dave"
```

---

## 9. Prototypes and Prototype Chain

### 9.1 Prototype Basics
```javascript
// Every object has [[Prototype]] (accessed via __proto__ or Object.getPrototypeOf())
const arr = [1, 2, 3];
arr.__proto__ === Array.prototype;          // true
Array.prototype.__proto__ === Object.prototype; // true
Object.prototype.__proto__ === null;        // end of chain

// Property lookup: own → proto → proto.__proto__ → ... → null
```

### 9.2 Constructor Functions
```javascript
function Animal(name) {
    this.name = name;           // own property
}
Animal.prototype.speak = function() {   // shared method (on prototype)
    console.log(this.name + " speaks");
};

const dog = new Animal("Rex");
dog.speak();   // "Rex speaks"
dog.hasOwnProperty("name");   // true (own)
dog.hasOwnProperty("speak");  // false (on prototype)
```

**`new` keyword does:**
1. Creates a new empty object `{}`
2. Sets its `[[Prototype]]` to `Constructor.prototype`
3. Calls constructor with `this` = new object
4. Returns the new object (unless constructor returns an object)

### 9.3 ES6 Classes (syntactic sugar over prototypes)
```javascript
class Animal {
    #sound = "...";                    // private field (ES2022)

    constructor(name) {
        this.name = name;
    }

    speak() {                          // on prototype
        console.log(`${this.name}: ${this.#sound}`);
    }

    static create(name) {              // static method (on class itself)
        return new Animal(name);
    }

    get info() { return this.name; }   // getter
    set info(v) { this.name = v; }     // setter
}

class Dog extends Animal {
    constructor(name) {
        super(name);                   // must call super first
        this.#sound = "Woof";          // ❌ can't access parent's private field
    }
    speak() {
        super.speak();                 // call parent method
        console.log("Wag!");
    }
}

const d = new Dog("Rex");
d instanceof Dog;     // true
d instanceof Animal;  // true
```

---

## 10. Asynchronous JavaScript

### 10.1 Callbacks
```javascript
function fetchData(callback) {
    setTimeout(() => {
        callback(null, { data: "result" });  // (error, data) convention
    }, 1000);
}
fetchData((err, data) => {
    if (err) return console.error(err);
    console.log(data);
});

// Callback Hell — deeply nested, hard to read
getData(function(a) {
    getMoreData(a, function(b) {
        getEvenMoreData(b, function(c) {
            // ...
        });
    });
});
```

### 10.2 Promises
```javascript
const promise = new Promise((resolve, reject) => {
    const success = true;
    setTimeout(() => {
        if (success) resolve("Data fetched");
        else reject(new Error("Fetch failed"));
    }, 1000);
});

promise
    .then(data => console.log(data))       // called on resolve
    .catch(err => console.error(err))      // called on reject
    .finally(() => console.log("Done"));   // always called

// Promise states: pending → fulfilled | rejected (settled)

// Promise chaining
fetch('/api/user')
    .then(res => res.json())
    .then(user => fetch(`/api/posts/${user.id}`))
    .then(res => res.json())
    .catch(err => console.error(err));

// Promise combinators
Promise.all([p1, p2, p3])        // resolves when all resolve; rejects on first rejection
Promise.allSettled([p1, p2, p3]) // resolves when all settle (resolve or reject)
Promise.race([p1, p2, p3])       // resolves/rejects when first settles
Promise.any([p1, p2, p3])        // resolves when first resolves; rejects if all reject
Promise.resolve(value)            // creates resolved promise
Promise.reject(error)             // creates rejected promise
```

### 10.3 `async` / `await` (ES2017)
```javascript
async function fetchUser(id) {            // always returns a Promise
    try {
        const res = await fetch(`/api/users/${id}`);  // pauses until resolved
        const user = await res.json();
        return user;
    } catch (err) {
        console.error("Error:", err);
        throw err;                        // re-throw to caller
    }
}

// Parallel execution
async function main() {
    const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()]);
    console.log(users, posts);
}

// Top-level await (ES2022 — inside modules)
const data = await fetchData();
```

### 10.4 Event Loop

> The mechanism that allows JS to handle asynchronous operations despite being single-threaded.

```
Call Stack
    ↓ (empty?)
Microtask Queue  (Promises, queueMicrotask, MutationObserver)
    ↓ (empty?)
Macrotask Queue  (setTimeout, setInterval, setImmediate, I/O, UI events)
```

**Order of execution:**
1. Synchronous code (Call Stack)
2. All microtasks (Promise callbacks, then callbacks)
3. One macrotask (one setTimeout/setInterval callback)
4. Repeat from step 2

```javascript
console.log("1");                           // sync
setTimeout(() => console.log("2"), 0);     // macrotask
Promise.resolve().then(() => console.log("3"));  // microtask
console.log("4");                           // sync
// Output: 1, 4, 3, 2
```

---

## 11. Destructuring, Spread, and Rest

### 11.1 Array Destructuring
```javascript
const [a, b, c] = [1, 2, 3];
const [first, , third] = [1, 2, 3];    // skip elements
const [x = 0, y = 0] = [10];          // default values
const [head, ...tail] = [1, 2, 3, 4]; // rest: tail = [2,3,4]

// Swap
let m = 1, n = 2;
[m, n] = [n, m];   // m=2, n=1
```

### 11.2 Object Destructuring
```javascript
const { name, age } = { name: "Alice", age: 30, city: "NYC" };
const { name: n, age: a } = obj;           // rename
const { name = "Unknown", age = 0 } = {}; // defaults
const { address: { city } } = user;        // nested
const { a: x, ...rest } = obj;             // rest

// In function parameters
function display({ name, age = 0 }) {
    console.log(`${name} is ${age}`);
}
```

---

## 12. ES6+ Features Summary

### 12.1 Template Literals
```javascript
const name = "World";
console.log(`Hello, ${name}!`);
console.log(`Multi
line string`);
// Tagged templates
const result = tag`Hello ${name}`;
```

### 12.2 Symbols
```javascript
const id = Symbol("id");          // unique identifier
const id2 = Symbol("id");
id === id2;                       // false (always unique)
Symbol.for("key");                // global symbol registry
// Used for: unique property keys, well-known symbols (Symbol.iterator)
```

### 12.3 Map and Set
```javascript
// Map — any type as key, ordered, iterable
const map = new Map();
map.set("key", "value");
map.set(42, "number key");
map.get("key");            // "value"
map.has("key");            // true
map.size;                  // 2
map.delete("key");
for (const [k, v] of map) { }

// Set — unique values, ordered
const set = new Set([1, 2, 3, 2, 1]);   // Set {1, 2, 3}
set.add(4);
set.has(2);    // true
set.size;      // 4
set.delete(1);
[...set];      // [2, 3, 4]

// WeakMap / WeakSet — weak references (keys/values can be GC'd)
```

### 12.4 Iterators and Generators
```javascript
// Iterator protocol: object with next() returning {value, done}
const range = {
    from: 1, to: 5,
    [Symbol.iterator]() {
        let current = this.from;
        return {
            next: () => current <= this.to
                ? { value: current++, done: false }
                : { value: undefined, done: true }
        };
    }
};
for (const n of range) console.log(n);  // 1, 2, 3, 4, 5

// Generator function
function* counter(start = 0) {
    while (true) yield start++;   // pauses at yield, resumes on .next()
}
const gen = counter(10);
gen.next().value;  // 10
gen.next().value;  // 11

// Async generator
async function* streamData() {
    yield await fetch('/api/1').then(r => r.json());
    yield await fetch('/api/2').then(r => r.json());
}
for await (const data of streamData()) { console.log(data); }
```

### 12.5 Modules (ES6)
```javascript
// math.js — named exports
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export default class Calculator { /* ... */ }

// app.js — imports
import Calculator, { PI, add } from './math.js';
import * as math from './math.js';
import { add as sum } from './math.js';

// Dynamic import (lazy loading)
const module = await import('./math.js');
```

---

## 13. Error Handling

```javascript
try {
    JSON.parse("invalid json");   // throws SyntaxError
    null.property;                // throws TypeError
    undeclaredVar;                // throws ReferenceError
    eval("if(");                  // throws SyntaxError
}
catch (err) {
    console.error(err.name);      // "SyntaxError"
    console.error(err.message);   // error description
    console.error(err.stack);     // stack trace
}
finally {
    console.log("cleanup");
}

// Custom Error
class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = "ValidationError";
        this.field = field;
    }
}
throw new ValidationError("Invalid email", "email");

// Error types
// SyntaxError — invalid JS syntax
// TypeError — wrong type operation (null.prop, calling non-function)
// ReferenceError — accessing undeclared variable
// RangeError — value out of range (new Array(-1))
// URIError — invalid URI
// EvalError — eval() related
```

---

## 14. DOM and Browser APIs

### 14.1 DOM Manipulation
```javascript
// Selecting elements
document.getElementById("myId");
document.querySelector(".myClass");          // first match
document.querySelectorAll("div.item");       // NodeList
document.getElementsByTagName("p");
document.getElementsByClassName("cls");

// Modifying elements
elem.textContent = "New text";             // plain text
elem.innerHTML = "<b>Bold</b>";            // HTML (XSS risk!)
elem.setAttribute("class", "highlight");
elem.getAttribute("href");
elem.classList.add("active");
elem.classList.remove("active");
elem.classList.toggle("active");
elem.style.color = "red";

// Creating and appending
const div = document.createElement("div");
div.textContent = "Hello";
document.body.appendChild(div);
parent.insertBefore(newElem, referenceElem);
parent.removeChild(child);
elem.remove();

// Traversal
elem.parentElement;
elem.children;          // HTMLCollection of child elements
elem.firstElementChild; elem.lastElementChild;
elem.nextElementSibling; elem.previousElementSibling;
```

### 14.2 Events
```javascript
// Add event listener
btn.addEventListener("click", function(event) {
    console.log(event.target);
    event.preventDefault();   // prevent default action
    event.stopPropagation();  // stop bubbling
});

// Event delegation — attach to parent, handle children
document.getElementById("list").addEventListener("click", function(e) {
    if (e.target.tagName === "LI") {
        console.log(e.target.textContent);
    }
});

// Event propagation: bubbling (child → parent) and capturing (parent → child)
// addEventListener(type, handler, true) — use capturing phase
```

### 14.3 `localStorage` and `sessionStorage`
```javascript
localStorage.setItem("key", JSON.stringify(obj));
const data = JSON.parse(localStorage.getItem("key"));
localStorage.removeItem("key");
localStorage.clear();
// sessionStorage — same API but data cleared on tab close
```

---

## 15. Advanced Concepts

### 15.1 Deep vs Shallow Copy
```javascript
// Shallow copy (1 level deep)
const copy = { ...original };
const copy2 = Object.assign({}, original);
const arrCopy = [...original];

// Deep copy
const deep = JSON.parse(JSON.stringify(obj));  // fails for functions, undefined, circular
const deep2 = structuredClone(obj);            // native deep clone (modern browsers)
```

### 15.2 Currying
```javascript
// Currying: f(a,b,c) → f(a)(b)(c)
const curry = fn => a => b => fn(a, b);
const add = curry((a, b) => a + b);
add(3)(4);  // 7
const add5 = add(5);
add5(3);    // 8
```

### 15.3 Memoization
```javascript
function memoize(fn) {
    const cache = new Map();
    return function(...args) {
        const key = JSON.stringify(args);
        if (cache.has(key)) return cache.get(key);
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}
const memoFib = memoize(function fib(n) {
    return n <= 1 ? n : memoFib(n-1) + memoFib(n-2);
});
```

### 15.4 Debounce and Throttle
```javascript
// Debounce — execute after user stops calling for `delay` ms
function debounce(fn, delay) {
    let timer;
    return function(...args) {
        clearTimeout(timer);
        timer = setTimeout(() => fn.apply(this, args), delay);
    };
}
const searchDebounced = debounce(search, 300);  // search input

// Throttle — execute at most once per `limit` ms
function throttle(fn, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            fn.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}
const scrollThrottled = throttle(handleScroll, 100);  // scroll handler
```

### 15.5 Proxy and Reflect
```javascript
const handler = {
    get(target, key) {
        return key in target ? target[key] : `Property ${key} not found`;
    },
    set(target, key, value) {
        if (typeof value !== "number") throw TypeError("Only numbers!");
        target[key] = value;
        return true;
    }
};
const proxy = new Proxy({}, handler);
proxy.x = 42;   // ok
proxy.y = "hi"; // TypeError!
proxy.z;        // "Property z not found"
```

---

## 16. Frequently Asked Questions (FAQs)

**Q1. What is the difference between `null` and `undefined`?**
> `undefined` — variable declared but not assigned a value; function returns nothing; missing object property. `null` — explicitly assigned "no value" by programmer. `typeof null === "object"` is a long-standing bug.

**Q2. Explain event delegation.**
> Instead of adding event listeners to each child element, you add one listener to a parent and use `event.target` to determine which child was clicked. More efficient for dynamic elements.

**Q3. What is a closure and why is it useful?**
> A function that remembers its lexical scope even when called outside of it. Used for: data privacy (module pattern), factory functions, partial application/currying, event handlers remembering state.

**Q4. What is hoisting?**
> JavaScript moves declarations (not initializations) to the top of their scope during compilation. `var` declarations are hoisted and initialized to `undefined`. `let`/`const` are hoisted but in TDZ. Function declarations are fully hoisted.

**Q5. What is the difference between `==` and `===`?**
> `==` (loose equality) performs type coercion before comparison. `===` (strict equality) compares type AND value. Always prefer `===` to avoid unexpected coercions.

**Q6. What is the event loop?**
> Mechanism that continuously checks if the call stack is empty, then processes the microtask queue (Promises), then the macrotask queue (setTimeout/setInterval). Enables JS's non-blocking I/O despite being single-threaded.

**Q7. What are Promises and how do they differ from callbacks?**
> Promises represent the future result of an async operation. They avoid callback hell with `.then()` chaining, have better error handling with `.catch()`, and enable `async`/`await` syntax.

**Q8. What is prototypal inheritance?**
> Objects inherit directly from other objects via the `[[Prototype]]` chain. When a property is accessed, JS looks up the chain until found or reaches `null`. ES6 `class` syntax is sugar over prototype-based inheritance.

**Q9. What is `this` in JavaScript?**
> Context object set when a function is called. Value depends on call site: global/undefined (regular), object (method call), new object (`new`), explicit with `call`/`apply`/`bind`. Arrow functions have lexical `this` from enclosing scope.

**Q10. Explain `call`, `apply`, and `bind`.**
> All three explicitly set `this`. `call(thisArg, ...args)` — invokes immediately, args listed. `apply(thisArg, [args])` — invokes immediately, args as array. `bind(thisArg, ...args)` — returns new function with `this` bound.

**Q11. What is the difference between `map`, `filter`, and `reduce`?**
> `map(fn)` — transforms each element, returns new array of same length. `filter(fn)` — returns new array with elements that pass the test. `reduce(fn, init)` — reduces array to single value by accumulating.

**Q12. What is a generator function?**
> Function that can pause (with `yield`) and resume. Returns a generator object. Used for: lazy evaluation, infinite sequences, async iteration, custom iterables.

**Q13. What is the difference between `localStorage`, `sessionStorage`, and cookies?**
> `localStorage` — persistent, per origin, ~5MB, no expiry. `sessionStorage` — per tab, cleared on close, ~5MB. Cookies — per origin, ~4KB, sent with HTTP requests, can have expiry, accessible server-side.

**Q14. What is `async`/`await`?**
> Syntactic sugar over Promises. `async` function always returns a Promise. `await` pauses execution until Promise settles, makes async code look synchronous. Errors handled with `try/catch`.

**Q15. What is a WeakMap vs Map?**
> `Map` holds strong references (keys kept in memory). `WeakMap` holds weak references (keys are objects; GC'd when no other reference). `WeakMap` is not iterable. Used for: storing metadata about DOM elements without memory leaks.

---

## 17. Common Misconceptions

- ❌ *"JavaScript is multi-threaded"* → JS is single-threaded. The event loop gives appearance of concurrency. Web Workers are separate threads with limited messaging.
- ❌ *"`null == undefined` is false"* → It's `true` (loose equality). `null === undefined` is `false`.
- ❌ *"Arrow functions can be used everywhere regular functions can"* → No. Arrow functions can't be constructors, don't have `arguments`, and are unsuitable as object methods when `this` is needed.
- ❌ *"`var` is block-scoped"* → `var` is function-scoped. Only `let`/`const` are block-scoped.
- ❌ *"Closures cause memory leaks"* → Only if closures hold references to large objects unintentionally. Properly managed closures are fine.
- ❌ *"async/await eliminates the need to understand Promises"* → `async`/`await` is built on Promises. Understanding Promises is essential for `Promise.all`, error handling, etc.

---

## 18. Quick Revision Checklist

- [ ] `var` vs `let` vs `const` — scope, hoisting, TDZ
- [ ] Primitive types (7) vs reference types
- [ ] Type coercion, `==` vs `===`, `typeof` quirks
- [ ] Function declaration vs expression vs arrow function
- [ ] `this` — 6 binding rules; lexical `this` in arrows
- [ ] Closures — definition, use cases, pitfalls with `var` in loops
- [ ] Hoisting — var, functions, let/const TDZ
- [ ] Prototype chain, `Object.create`, `new` keyword
- [ ] ES6 Classes: constructor, extends, super, private fields
- [ ] Callbacks → Promises → async/await
- [ ] Event loop: call stack, microtask queue, macrotask queue
- [ ] Destructuring, spread, rest
- [ ] Map, Set, WeakMap, WeakSet
- [ ] Iterators, generators, Symbol.iterator
- [ ] ES Modules: import, export, dynamic import
- [ ] Debounce vs throttle
- [ ] `call`, `apply`, `bind`
- [ ] Error types and custom errors
- [ ] Event delegation and propagation

---

*Last updated: 2026 | Suitable for: university exams, software engineering interviews, front-end & full-stack roles*
