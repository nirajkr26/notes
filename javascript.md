# JavaScript — Super Detailed Notes for Exams & Technical Interviews

---

## 1) JavaScript Overview

JavaScript is a high-level, prototype-based, single-threaded language with event-driven, asynchronous programming capabilities.

### Where used
- Browser frontend
- Node.js backend
- Mobile and desktop frameworks

---

## 2) Execution Model

### Engine pipeline
- Parse -> AST -> bytecode/JIT optimization

### Runtime concepts
- Call stack
- Web APIs / Node APIs
- Callback queue (macrotasks)
- Microtask queue (promises)
- Event loop

---

## 3) Variables and Scope

- `var` (function scoped, hoisted)
- `let` (block scoped)
- `const` (block scoped, no reassignment)

### Hoisting
Declarations are hoisted; initialization behavior differs by keyword.

### Temporal Dead Zone
`let/const` cannot be accessed before declaration.

---

## 4) Data Types

### Primitive
- string, number, bigint, boolean, undefined, null, symbol

### Non-primitive
- object, array, function

### Equality
- `==` loose equality with coercion
- `===` strict equality (preferred)

---

## 5) Functions and `this`

- Function declaration vs expression
- Arrow functions and lexical `this`
- `call`, `apply`, `bind`
- Closures and lexical environment

---

## 6) Objects and Prototypes

- Prototype chain lookup
- Constructor functions and classes
- Inheritance via `extends`
- `Object.create`, `hasOwnProperty`

---

## 7) Asynchronous JavaScript

- Callbacks
- Promises (`then/catch/finally`)
- `async/await`
- Error handling in async flows

### Promise combinators
- `Promise.all`
- `Promise.allSettled`
- `Promise.race`
- `Promise.any`

---

## 8) DOM and Browser APIs

- DOM selection/manipulation
- Event propagation: capture -> target -> bubble
- Event delegation
- `localStorage` vs `sessionStorage`
- Fetch API basics

---

## 9) ES6+ Features

- Destructuring
- Spread/rest operators
- Template literals
- Default parameters
- Modules (`import/export`)
- Optional chaining and nullish coalescing

---

## 10) Error Handling and Debugging

- `try/catch/finally`
- Throwing custom errors
- Stack traces and source maps
- Debugger and breakpoints

---

## 11) Performance and Memory

- Garbage collection (mark and sweep)
- Avoid memory leaks from retained closures/listeners
- Debounce/throttle techniques
- Minimize reflows/repaints in browser

---

## 12) Security Essentials

- XSS prevention (escape/sanitize)
- CSRF basics
- CORS policy understanding
- Secure cookies and tokens

---

## 13) JavaScript in Node.js

- Module systems (CommonJS vs ESM)
- EventEmitter
- Streams and buffers
- Non-blocking I/O
- Express middleware concept

---

## 14) Frequently Asked Interview Questions

1. Explain event loop with microtasks/macrotasks.
2. Difference between `var`, `let`, and `const`?
3. What is closure and practical use case?
4. `==` vs `===`?
5. How does prototype inheritance work?
6. Why arrow functions differ in `this` binding?
7. Promise vs async/await?
8. What is debouncing and throttling?
9. Explain call/apply/bind.
10. What causes memory leaks in JS apps?

---

## 15) Exam-Focused Topics

- Scope and hoisting examples
- Event loop sequence-based questions
- Closures and lexical scope
- Promise chain/error propagation
- Prototype chain diagrams
- DOM event flow

---

## 16) JavaScript Quick Revision Checklist

- [ ] var/let/const + TDZ
- [ ] Primitive vs reference types
- [ ] this/call/apply/bind
- [ ] Closures and scope chain
- [ ] Promise and async/await
- [ ] Event loop and task queues
- [ ] Prototypes and classes
- [ ] Security basics (XSS/CSRF/CORS)

---

*Last updated: 2026 | Target: frontend/backend interviews and university exams*
