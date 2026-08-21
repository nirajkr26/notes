# Node.js — Detailed Notes & Interview Guide

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

> **Focus:** Node.js runtime, backend development, asynchronous programming, internals, production practices, and interview preparation.

---

## 1. What is Node.js?

Node.js is a **JavaScript runtime** built on the **V8 JavaScript engine**. It lets JavaScript run outside the browser, especially for servers, APIs, CLIs, automation, real-time applications, and tooling.

Node.js is not a programming language and not a framework. A useful mental model is:

```text
JavaScript code
     |
     v
Node.js runtime
     |
     +--> V8        -> executes JavaScript
     +--> libuv     -> event loop + async I/O abstraction
     +--> Node APIs -> fs, http, net, streams, crypto, etc.
     +--> npm       -> package ecosystem / package manager tooling
```

### Why Node.js is popular

- Same language across frontend and backend.
- Excellent for I/O-heavy applications.
- Non-blocking APIs encourage high concurrency.
- Large package ecosystem.
- Streams make large data processing efficient.
- Built-in HTTP, filesystem, networking, crypto, worker threads, and process APIs.
- Good fit for APIs, BFFs, real-time systems, CLIs, and event-driven services.

### When Node.js is a poor fit

Node.js can be a poor choice when the main workload is long-running CPU-bound JavaScript on the main thread. CPU-heavy work can block the event loop and delay unrelated requests. Possible solutions include **worker threads**, separate processes/services, native extensions, or moving the workload to a more suitable compute service.

---

## 2. Node.js Architecture

The most important interview concept is that Node.js can handle many concurrent I/O operations without creating one JavaScript thread per request.

### High-level architecture

```text
                Application JavaScript
                         |
                         v
                    Node.js APIs
                         |
             +-----------+-----------+
             |                       |
             v                       v
            V8                     libuv
     JS execution / GC      event loop + async I/O
                                     |
                    +----------------+----------------+
                    |                |                |
                  OS I/O        thread pool       networking
```

### V8

V8 parses and executes JavaScript. It includes mechanisms such as:

- Parsing and compilation.
- JIT optimization.
- Heap management.
- Garbage collection.

### libuv

libuv provides the cross-platform asynchronous I/O foundation used by Node.js. It manages the event loop and provides a thread pool for certain operations that cannot or should not be performed directly through non-blocking OS APIs.

### Important correction: "Node.js is single-threaded"

A better statement is:

> **JavaScript execution on the main Node.js event-loop thread is single-threaded by default, but a Node.js process can use multiple threads internally and explicitly through worker threads.**

Some APIs use the libuv thread pool, and `worker_threads` provides JavaScript execution on additional threads.

---

## 3. The Event Loop

The event loop is the mechanism that allows Node.js to coordinate asynchronous work while JavaScript execution remains on the main thread.

Conceptually:

```text
Run current JS
     |
     v
Process completed callbacks / queued work
     |
     v
Wait for or process I/O/events
     |
     v
Run more callbacks
     |
     +----> repeat
```

Node's event loop has phases. A simplified view is:

1. **Timers** — callbacks for expired `setTimeout()` / `setInterval()` timers.
2. **Pending callbacks** — selected deferred system callbacks.
3. **Idle / prepare** — internal libuv work.
4. **Poll** — retrieve/process I/O events.
5. **Check** — `setImmediate()` callbacks.
6. **Close callbacks** — close-event callbacks such as socket close handlers.

The exact ordering depends on the surrounding context and Node version, so avoid memorizing a simplistic rule such as "`setTimeout` always runs before `setImmediate`."

### `process.nextTick()` vs Promise microtasks

Node also has queues that are processed around JavaScript callbacks. In particular, `process.nextTick()` has special priority in Node and can starve I/O if recursively scheduled.

```js
console.log('A');

process.nextTick(() => console.log('nextTick'));

Promise.resolve().then(() => console.log('promise'));

setTimeout(() => console.log('timer'), 0);

console.log('B');
```

A typical output is:

```text
A
B
nextTick
promise
timer
```

**Interview point:** `process.nextTick()` is not the same thing as `setImmediate()`.

---

## 4. Blocking vs Non-Blocking Code

### Blocking

A synchronous operation keeps the JavaScript thread busy until it completes.

```js
const fs = require('node:fs');

const data = fs.readFileSync('./large.txt', 'utf8');
console.log(data);
```

If this takes a long time, other JavaScript callbacks cannot run on that thread during the operation.

### Non-blocking

```js
const fs = require('node:fs');

fs.readFile('./large.txt', 'utf8', (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});

console.log('This can run before the file callback.');
```

For servers, prefer asynchronous APIs on request paths unless a synchronous call is deliberately justified, such as small startup-time configuration work.

---

## 5. Installing and Running Node.js

```bash
node --version
npm --version
```

Run a file:

```bash
node app.js
```

Start a REPL:

```bash
node
```

Useful runtime flags include:

```bash
node --help
node --inspect app.js
node --trace-warnings app.js
```

Use a supported Node.js LTS release for production applications and pin/document the runtime version used by the project.

---

## 6. `package.json`

`package.json` describes a Node.js package/application.

Example:

```json
{
  "name": "example-api",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "node --watch src/server.js",
    "start": "node src/server.js",
    "test": "node --test"
  },
  "dependencies": {
    "express": "^5.0.0"
  }
}
```

### Important fields

| Field | Purpose |
|---|---|
| `name` | Package/application name |
| `version` | Package version |
| `private` | Prevent accidental publication when `true` |
| `type` | Controls module interpretation (`commonjs` or `module`) |
| `scripts` | Named commands |
| `dependencies` | Runtime dependencies |
| `devDependencies` | Development-only dependencies |
| `peerDependencies` | Compatibility contract for consumers |
| `engines` | Runtime/package-manager compatibility hints |
| `exports` | Defines public package entry points |

---

## 7. npm, npx, and Dependency Management

### npm

npm installs and manages packages.

```bash
npm install express
npm install -D eslint
npm uninstall express
npm update
npm audit
```

### `npm install` vs `npm ci`

- `npm install` resolves dependencies and can update the lockfile.
- `npm ci` is intended for clean, reproducible CI installs and uses the lockfile as the source of truth. It normally removes an existing `node_modules` tree first.

For CI, prefer:

```bash
npm ci
npm test
```

### `npx`

`npx` runs package binaries without requiring a global install.

```bash
npx eslint .
```

Modern npm also exposes `npm exec` for executing package commands.

### `package-lock.json`

The lockfile records the resolved dependency tree and versions. Commit it for applications so developers and CI can reproduce installs consistently.

---

## 8. CommonJS vs ES Modules

Node.js supports both CommonJS (CJS) and ECMAScript Modules (ESM).

### CommonJS

```js
// math.js
function add(a, b) {
  return a + b;
}

module.exports = { add };
```

```js
// app.js
const { add } = require('./math');
console.log(add(2, 3));
```

### ES Modules

```js
// math.js
export function add(a, b) {
  return a + b;
}
```

```js
// app.js
import { add } from './math.js';
console.log(add(2, 3));
```

Use ESM by setting:

```json
{
  "type": "module"
}
```

or by using `.mjs` files.

### Comparison

| Feature | CommonJS | ESM |
|---|---|---|
| Import | `require()` | `import` |
| Export | `module.exports` / `exports` | `export` |
| Typical evaluation model | Synchronous loading model | Static module structure with async-capable loading semantics |
| File markers | `.cjs` | `.mjs` |
| Package mode | `"type": "commonjs"` | `"type": "module"` |

**Interview advice:** do not say "ESM is always asynchronous." Module loading has different semantics and Node can resolve/load modules in ways that involve asynchronous operations, but the key distinction is the module system and its static import/export model.

---

## 9. Module Resolution Basics

When importing a package or local module, Node resolves it according to the module system and package metadata.

Useful concepts:

- Relative imports: `./utils.js`.
- Package imports: `express`.
- Package `exports` can restrict public entry points.
- `node_modules` is searched for package dependencies.
- ESM requires explicit file extensions for many relative imports.
- Built-in modules use the `node:` prefix, e.g. `node:fs`.

Prefer explicit built-in imports:

```js
import fs from 'node:fs/promises';
```

---

## 10. Core Modules You Should Know

| Module | Use |
|---|---|
| `node:fs` | Filesystem |
| `node:fs/promises` | Promise-based filesystem API |
| `node:path` | Cross-platform path manipulation |
| `node:url` | URL utilities |
| `node:http` / `node:https` | HTTP servers and clients |
| `node:net` | TCP networking |
| `node:events` | EventEmitter |
| `node:stream` | Streaming abstractions |
| `node:buffer` | Binary data |
| `node:crypto` | Cryptography and hashing primitives |
| `node:os` | OS information |
| `node:process` | Process/environment information |
| `node:child_process` | Spawn/execute processes |
| `node:worker_threads` | Parallel JS execution |
| `node:cluster` | Multi-process server patterns |
| `node:util` | General utilities |
| `node:test` | Built-in test runner |

---

## 11. Filesystem (`fs`)

### Promise API

```js
import { readFile, writeFile } from 'node:fs/promises';

const data = await readFile('./notes.txt', 'utf8');
await writeFile('./copy.txt', data);
```

### Path handling

Never manually concatenate filesystem paths when portability matters.

```js
import path from 'node:path';

const filePath = path.join(process.cwd(), 'data', 'users.json');
```

Useful APIs:

```js
path.join(...parts);
path.resolve(...parts);
path.basename(filePath);
path.dirname(filePath);
path.extname(filePath);
```

### Security note

If a path comes from a user, validate it before reading/writing. Do not assume `path.join(base, userInput)` by itself makes arbitrary path traversal impossible.

---

## 12. Buffers and Binary Data

A `Buffer` represents a sequence of bytes and is heavily used for files, network packets, streams, and binary protocols.

```js
const buffer = Buffer.from('hello', 'utf8');
console.log(buffer);
console.log(buffer.toString('utf8'));
```

Common encodings:

- `utf8`
- `base64`
- `hex`
- `ascii`
- `latin1`

Buffers are important because JavaScript strings are text abstractions, while networking and filesystem APIs often operate on bytes.

---

## 13. EventEmitter

Node uses the observer/event-emitter pattern extensively.

```js
import { EventEmitter } from 'node:events';

const bus = new EventEmitter();

bus.on('user.created', (user) => {
  console.log('Created:', user.id);
});

bus.emit('user.created', { id: 42 });
```

Useful methods:

- `on()` / `addListener()` — subscribe.
- `once()` — subscribe for one event.
- `emit()` — publish.
- `off()` / `removeListener()` — unsubscribe.
- `removeAllListeners()` — remove listeners.

### Memory-leak warning

Adding listeners repeatedly without removing them can retain references and increase memory usage. Node may emit `MaxListenersExceededWarning` as a diagnostic signal.

---

## 14. Callbacks

Node historically used error-first callbacks:

```js
function callback(err, value) {
  if (err) {
    // handle error
    return;
  }

  console.log(value);
}
```

Example:

```js
import fs from 'node:fs';

fs.readFile('./file.txt', 'utf8', (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```

The pattern is usually:

```text
callback(error, result)
```

---

## 15. Promises

A Promise represents the eventual result of an asynchronous operation.

States:

```text
pending
  |
  +--> fulfilled
  |
  +--> rejected
```

A Promise settles only once.

```js
const promise = fetch('https://example.com');

promise
  .then((response) => response.text())
  .then((body) => console.log(body))
  .catch((error) => console.error(error))
  .finally(() => console.log('finished'));
```

### `Promise.all`

Runs multiple operations concurrently and rejects when any input rejects.

```js
const [user, posts] = await Promise.all([
  getUser(),
  getPosts()
]);
```

### `Promise.allSettled`

Waits for every Promise and reports each result.

```js
const results = await Promise.allSettled([
  getUser(),
  getPosts()
]);
```

### `Promise.race`

Settles when the first input Promise settles.

### `Promise.any`

Fulfills when the first input fulfills; rejects with `AggregateError` if all inputs reject.

---

## 16. `async` / `await`

`async` functions always return a Promise. `await` pauses the current async function until the awaited Promise settles; it does **not** block the entire Node.js process.

```js
async function getUser(id) {
  const response = await fetch(`/users/${id}`);
  return response.json();
}
```

Error handling:

```js
async function load() {
  try {
    const user = await getUser(10);
    return user;
  } catch (error) {
    console.error('Failed:', error);
    throw error;
  }
}
```

### Sequential vs concurrent awaits

Avoid accidental serialization:

```js
// Sequential when independent:
const a = await getA();
const b = await getB();
```

Prefer:

```js
const [a, b] = await Promise.all([getA(), getB()]);
```

when the operations are independent and concurrency is safe.

---

## 17. Error Handling

### Synchronous errors

```js
try {
  JSON.parse('invalid');
} catch (error) {
  console.error(error.message);
}
```

### Promise errors

```js
try {
  await doSomethingAsync();
} catch (error) {
  // handle or propagate
}
```

### Operational vs programmer errors

A useful production distinction:

- **Operational errors:** expected runtime failures such as invalid input, unavailable dependency, timeout, or missing file.
- **Programmer errors:** bugs such as accessing an undefined variable or violating an invariant.

Handle expected operational errors at appropriate boundaries. Do not blindly swallow exceptions.

### Process-level events

```js
process.on('uncaughtException', (error) => {
  console.error(error);
});

process.on('unhandledRejection', (reason) => {
  console.error(reason);
});
```

These are last-resort diagnostics, not substitutes for local error handling. After a serious uncaught exception, the process may be in an unreliable state; production applications commonly log the failure, stop accepting work, and let a supervisor restart the process.

---

## 18. HTTP Server Without a Framework

Node can create an HTTP server directly.

```js
import http from 'node:http';

const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/health') {
    res.writeHead(200, { 'content-type': 'application/json' });
    res.end(JSON.stringify({ ok: true }));
    return;
  }

  res.writeHead(404, { 'content-type': 'application/json' });
  res.end(JSON.stringify({ error: 'Not found' }));
});

server.listen(3000, () => {
  console.log('Listening on :3000');
});
```

### HTTP request lifecycle

```text
Client
  |
  v
TCP connection
  |
  v
HTTP request
  |
  v
Node HTTP server
  |
  +--> routing
  +--> authentication / authorization
  +--> business logic
  +--> database / external services
  |
  v
HTTP response
```

---

## 19. Express Concepts

Express is a web framework commonly used with Node.js.

Basic application:

```js
import express from 'express';

const app = express();

app.use(express.json());

app.get('/users/:id', async (req, res, next) => {
  try {
    const user = await findUser(req.params.id);
    res.json(user);
  } catch (error) {
    next(error);
  }
});

app.use((error, req, res, next) => {
  console.error(error);
  res.status(500).json({ error: 'Internal server error' });
});

app.listen(3000);
```

### Middleware

Middleware functions sit in the request-processing chain.

```text
request
  |
  v
logger -> auth -> validation -> route -> error handler
```

A middleware can:

- Read/change request data.
- Add context.
- End the response.
- Call `next()` to continue.
- Pass errors to an error handler.

---

## 20. REST API Design Basics

Good Node backend APIs should consider:

- Resource-oriented URLs.
- Correct HTTP methods.
- Status codes.
- Validation.
- Authentication and authorization.
- Pagination for large collections.
- Consistent error responses.
- Idempotency where appropriate.
- Timeouts and cancellation.
- Logging and tracing.
- Rate limiting.

Example:

```text
GET    /users
GET    /users/:id
POST   /users
PATCH  /users/:id
DELETE /users/:id
```

Do not put secrets such as database passwords or API keys directly in source code.

---

## 21. Streams

Streams process data incrementally instead of requiring the complete payload in memory.

Types:

1. **Readable** — data can be read.
2. **Writable** — data can be written.
3. **Duplex** — both readable and writable.
4. **Transform** — duplex stream that transforms data.

Example:

```js
import { createReadStream, createWriteStream } from 'node:fs';

const input = createReadStream('./large.log');
const output = createWriteStream('./copy.log');

input.pipe(output);
```

### Why streams matter

Suppose a file is 10 GB. Reading the entire file into memory is expensive. A stream can process chunks incrementally.

### Backpressure

Backpressure occurs when the consumer cannot process data as quickly as the producer generates it. A well-designed stream pipeline slows or buffers the producer instead of allowing unbounded memory growth.

Prefer `pipeline()` for robust composition:

```js
import { pipeline } from 'node:stream/promises';
import { createReadStream, createWriteStream } from 'node:fs';

await pipeline(
  createReadStream('./input.txt'),
  createWriteStream('./output.txt')
);
```

---

## 22. Child Processes

Use child processes when you need to execute another program or process.

### `exec`

Runs a command through a shell and buffers the output.

### `execFile`

Runs a specific executable without requiring a shell for normal use.

### `spawn`

Starts a process and exposes stdout/stderr as streams, making it suitable for long-running or large-output processes.

```js
import { spawn } from 'node:child_process';

const child = spawn('node', ['worker.js']);

child.stdout.on('data', (chunk) => {
  console.log(chunk.toString());
});
```

### Security warning

Never interpolate untrusted input into shell commands.

Bad idea:

```js
exec(`git clone ${userInput}`);
```

Prefer argument arrays with `spawn`/`execFile` and strict validation.

---

## 23. Worker Threads

`worker_threads` lets JavaScript execute in additional threads within a process. It is useful for CPU-intensive work that would otherwise block the main event loop.

Conceptually:

```text
Main thread
    |
    +--> Worker 1 -> CPU-heavy task
    +--> Worker 2 -> CPU-heavy task
```

Use workers carefully because they introduce communication and memory costs.

```js
// main.js
import { Worker } from 'node:worker_threads';

const worker = new Worker(new URL('./worker.js', import.meta.url));

worker.on('message', (result) => {
  console.log('Result:', result);
});
```

### Worker threads vs child processes

| Feature | Worker Threads | Child Processes |
|---|---|---|
| Execution | Threads in same process | Separate OS processes |
| Memory | Can share selected memory | Separate memory spaces |
| Best for | CPU-heavy JS | External programs / isolation |
| Crash isolation | Lower than separate process | Stronger process isolation |
| Communication | Message passing / shared memory | IPC / stdio |

---

## 24. Cluster and Horizontal Scaling

A single Node.js process uses one main JavaScript event-loop thread. To use multiple CPU cores for independent server processes, applications can use multiple processes through orchestration tools or Node's cluster facilities.

In modern production systems, it is common to run multiple Node processes/containers behind a load balancer rather than relying solely on `cluster`.

```text
                 Load Balancer
                 /     |     \
                /      |      \
             Node     Node     Node
             #1       #2       #3
```

### Important rule

Do not keep critical application state only in process memory if requests can reach multiple instances. Use shared infrastructure such as a database, Redis, or another durable/shared store when appropriate.

---

## 25. Graceful Shutdown

A production service should stop accepting new work and finish or cancel existing work before exiting.

```js
const server = app.listen(3000);

async function shutdown(signal) {
  console.log(`${signal}: shutting down`);

  server.close(async () => {
    // Close DB pools, queues, etc.
    process.exit(0);
  });
}

process.on('SIGTERM', () => shutdown('SIGTERM'));
process.on('SIGINT', () => shutdown('SIGINT'));
```

A real implementation should also have a shutdown timeout so a stuck dependency cannot keep the process alive forever.

---

## 26. Environment Variables and Configuration

Access environment variables with `process.env`.

```js
const port = Number(process.env.PORT ?? 3000);
const databaseUrl = process.env.DATABASE_URL;
```

Production configuration should be supplied by the deployment environment or a dedicated secret/configuration system.

### Configuration principles

- Validate required configuration at startup.
- Do not log secrets.
- Separate configuration from business logic.
- Use typed/validated configuration where possible.
- Fail fast when critical configuration is missing.

---

## 27. Authentication and Authorization

### Authentication

Answers: **Who are you?**

Examples:

- Session cookies.
- JWT-based authentication.
- OAuth/OIDC.
- API keys for service-to-service use.

### Authorization

Answers: **What are you allowed to do?**

Examples:

- Role-based access control (RBAC).
- Attribute-based access control (ABAC).
- Resource ownership checks.

Never treat a decoded JWT payload as proof that the token is trustworthy; verify signature, issuer, audience, expiry, and other relevant claims according to the token's contract.

---

## 28. Security Checklist

For Node.js APIs:

- Validate and normalize input.
- Use parameterized database queries.
- Avoid command injection.
- Avoid path traversal.
- Configure secure headers where appropriate.
- Use TLS in transit.
- Store passwords using a slow password-hashing algorithm such as Argon2id or bcrypt rather than plain hashes.
- Rotate and protect secrets.
- Limit request body sizes.
- Apply rate limits to sensitive endpoints.
- Set sensible timeouts.
- Avoid leaking stack traces to clients in production.
- Keep dependencies updated and review advisories.
- Use least-privilege service credentials.
- Treat uploaded files and user-controlled URLs as untrusted.
- Log security-relevant events without logging secrets or sensitive personal data unnecessarily.

---

## 29. Database Access

Node applications commonly use:

- PostgreSQL/MySQL drivers.
- MongoDB drivers.
- Query builders.
- ORMs such as Prisma, Sequelize, or TypeORM.

A typical architecture separates database access from HTTP handlers:

```text
Route
  -> Controller
      -> Service
          -> Repository / Data Access
              -> Database
```

### Connection pooling

Creating a new database connection for every request is expensive. Use a connection pool with limits appropriate for the database and deployment size.

### Transactions

Use transactions when multiple writes must maintain an atomic invariant.

```text
BEGIN
  update A
  update B
COMMIT
```

On failure:

```text
ROLLBACK
```

---

## 30. Caching

Caching reduces latency and backend load when data can be reused safely.

Common locations:

- In-process memory.
- Redis.
- CDN.
- Reverse proxy.
- Database caches.

### Cache-aside pattern

```text
Request
  |
  v
Check cache
  |--- hit ---> return
  |
 miss
  v
Read DB
  |
  v
Write cache
  |
  v
Return response
```

Be explicit about TTLs, invalidation, stale data, and cache stampedes.

---

## 31. Logging, Metrics, and Tracing

Production observability normally includes:

### Logs

Structured logs are easier to search and aggregate.

```js
console.log(JSON.stringify({
  level: 'info',
  event: 'user.created',
  userId: 42
}));
```

In production, a dedicated logging library is often preferable for formatting, redaction, transports, and log levels.

### Metrics

Track things such as:

- Request rate.
- Error rate.
- Latency percentiles.
- CPU and memory usage.
- Event-loop lag.
- Database pool saturation.
- Queue depth.

### Tracing

Distributed tracing connects work across services using correlation/trace identifiers.

---

## 32. Performance and Event-Loop Health

A Node application can have low CPU utilization but still be slow if the event loop is blocked or downstream dependencies are slow.

Watch for:

- Large synchronous filesystem calls.
- CPU-heavy loops.
- Huge JSON serialization/parsing.
- Catastrophic regular expressions (ReDoS risk).
- Unbounded arrays/maps.
- Excessive logging.
- Slow database queries.
- Connection-pool exhaustion.
- Missing timeouts.

### Event-loop lag

If the event loop cannot process callbacks promptly, request latency rises. Monitor event-loop delay in performance-sensitive services.

---

## 33. Memory and Garbage Collection

V8 manages JavaScript memory using garbage collection.

Common causes of memory growth:

- Accidental global references.
- Unbounded caches.
- Event listeners never removed.
- Long-lived closures retaining large objects.
- Queues that grow without limits.
- Large buffers held in memory.

### Heap vs stack

- **Stack:** execution frames and local execution state.
- **Heap:** dynamically allocated objects and other managed memory.

Node also has memory outside the JavaScript heap, including Buffers and native allocations, so RSS can be larger than V8 heap usage.

Useful diagnostic ideas:

```bash
node --inspect app.js
```

Then use the inspector/DevTools to investigate CPU profiles and heap snapshots.

---

## 34. Timers

```js
const timeout = setTimeout(() => {
  console.log('later');
}, 1000);

clearTimeout(timeout);

const interval = setInterval(() => {
  console.log('tick');
}, 1000);

clearInterval(interval);
```

`setTimeout(fn, 0)` does not mean "run immediately." It schedules a timer callback that can run after the relevant event-loop scheduling conditions are met.

For recurring jobs, consider whether an external scheduler/queue is safer than keeping a timer inside one application instance.

---

## 35. AbortController and Cancellation

Modern Node APIs support `AbortSignal` in many places.

```js
const controller = new AbortController();

const timeout = setTimeout(() => controller.abort(), 5000);

try {
  const response = await fetch('https://example.com', {
    signal: controller.signal
  });
} finally {
  clearTimeout(timeout);
}
```

Cancellation is important for avoiding work that is no longer useful, especially when a client disconnects or a dependency exceeds its deadline.

---

## 36. AsyncLocalStorage

`AsyncLocalStorage` can maintain request-scoped context across asynchronous operations.

Common uses:

- Request IDs.
- Trace IDs.
- Correlation IDs.
- Request-scoped metadata.

Conceptually:

```text
Incoming request
      |
      v
AsyncLocalStorage context { requestId }
      |
      +--> service
      +--> DB call
      +--> logger
```

This can improve observability without passing a request ID through every function manually, but context propagation should still be used deliberately and tested.

---

## 37. Testing Node.js Applications

Testing levels:

1. **Unit tests** — isolated functions/classes.
2. **Integration tests** — real or realistic dependency interactions.
3. **API tests** — HTTP behavior and contracts.
4. **End-to-end tests** — complete user/business workflows.
5. **Load tests** — behavior under expected/constrained traffic.

Node provides a built-in test runner:

```js
import test from 'node:test';
import assert from 'node:assert/strict';

test('addition', () => {
  assert.equal(2 + 3, 5);
});
```

Popular ecosystem tools also include Jest, Vitest, Mocha, Supertest, and others. Choose based on the project rather than assuming one tool is universally best.

---

## 38. Dependency Injection and Project Structure

For larger services, avoid putting everything into one route file.

Example:

```text
src/
├── app.js
├── server.js
├── config/
├── routes/
├── controllers/
├── services/
├── repositories/
├── middleware/
├── schemas/
├── utils/
└── tests/
```

Dependency injection makes components easier to test:

```js
function createUserService({ userRepository, mailer }) {
  return {
    async createUser(input) {
      const user = await userRepository.create(input);
      await mailer.sendWelcome(user.email);
      return user;
    }
  };
}
```

The service does not need to know which concrete database or mail provider is used.

---

## 39. Message Queues and Background Jobs

Use a queue when work does not need to complete inside the HTTP request.

Examples:

- Email sending.
- Image/video processing.
- Report generation.
- Webhook retries.
- Large data imports.

Architecture:

```text
API
 |
 +--> DB transaction
 |
 +--> Queue ---> Worker ---> External service
```

Important production concerns:

- Retry policy.
- Dead-letter handling.
- Idempotency.
- Visibility/lease timeout.
- Duplicate delivery.
- Backpressure.
- Observability.

Do not assume "exactly once" delivery unless the infrastructure and application semantics genuinely provide it. Design consumers to tolerate duplicates when possible.

---

## 40. WebSockets and Real-Time Systems

Node's event-driven model is useful for long-lived connections.

Common use cases:

- Chat.
- Notifications.
- Collaborative editing.
- Live dashboards.
- Multiplayer/game state.

At scale, multiple instances usually need shared state/pub-sub infrastructure so an event received by one instance can reach clients connected to another.

---

## 41. HTTP Timeouts and Resilience

Every external dependency should have a bounded failure mode.

Consider:

- Connection timeout.
- Request/header timeout.
- Response timeout.
- Abort/cancellation.
- Retry with exponential backoff.
- Jitter.
- Circuit breakers where appropriate.
- Bulkheads/resource isolation.

### Retry warning

Do not blindly retry every request. Retrying a non-idempotent operation can duplicate side effects. Use idempotency keys or application-level safeguards where necessary.

---

## 42. Common Node.js Anti-Patterns

### 1. Blocking the event loop

```js
// Avoid on hot request paths
const result = expensiveSynchronousOperation();
```

### 2. Unbounded concurrency

Bad:

```js
await Promise.all(items.map(processItem));
```

for a huge list when each operation consumes a scarce resource.

Prefer bounded concurrency with a queue/semaphore when needed.

### 3. No timeouts

A request that waits forever can consume resources indefinitely.

### 4. Logging secrets

Never log passwords, access tokens, private keys, or full sensitive payloads casually.

### 5. Giant JSON payloads

Limit body sizes and stream large data where appropriate.

### 6. Global mutable state

Global caches/config objects can make tests and scaling harder.

### 7. Ignoring graceful shutdown

Containers and orchestrators routinely send termination signals.

---

## 43. Node.js Quick Comparison: Node vs Browser JavaScript

| Capability | Browser JS | Node.js |
|---|---|---|
| DOM | Yes | No built-in DOM |
| `window` | Browser global | No browser `window` |
| Filesystem | Restricted | Full OS API subject to permissions |
| TCP server | No direct general-purpose API | Yes |
| HTTP server | No | Yes |
| Browser Web APIs | Many | Different set; increasingly overlapping APIs |
| `process` | No standard browser global | Yes |
| Modules | ESM | ESM + CommonJS |
| Main use | UI/client | Servers, tools, services |

Do not assume every Web API behaves identically in every Node version or browser.

---

## 44. Quick Revision Cheat Sheet

### Runtime

- Node.js = JavaScript runtime, not framework.
- V8 executes JavaScript.
- libuv powers the event loop and async I/O abstractions.
- Main JavaScript execution is single-threaded by default.
- Worker threads enable parallel JavaScript execution.

### Async

- Prefer async APIs on request paths.
- `async` functions return Promises.
- `await` pauses an async function, not the whole process.
- `Promise.all()` is for concurrent independent work.
- `process.nextTick()` has special priority in Node.
- `setImmediate()` belongs to the event-loop check phase.

### Modules

- CJS: `require`, `module.exports`.
- ESM: `import`, `export`.
- `type: module` opts a package into ESM semantics.
- Use `node:` for built-in modules.

### Backend

- Validate input.
- Authenticate and authorize.
- Use DB connection pools.
- Set timeouts.
- Use transactions for atomic invariants.
- Use queues for asynchronous background work.
- Design for graceful shutdown.

### Performance

- Do not block the event loop.
- Stream large data.
- Bound concurrency.
- Monitor event-loop delay.
- Watch memory and unbounded caches.
- Profile before optimizing.

### Security

- Never trust input.
- Avoid shell interpolation.
- Use parameterized queries.
- Protect secrets.
- Hash passwords with a password-hashing algorithm.
- Rate-limit sensitive operations.
- Keep dependencies patched.

---

# 45. Node.js Interview Questions & Answers

## Beginner / Fundamentals

### Q1. What is Node.js?

**Answer:** Node.js is a JavaScript runtime built on V8 that allows JavaScript to execute outside the browser. It provides APIs for filesystem, networking, HTTP, streams, processes, cryptography, and other server/runtime capabilities.

### Q2. Is Node.js a programming language?

**Answer:** No. JavaScript is the language. Node.js is the runtime environment that executes JavaScript outside the browser.

### Q3. Is Node.js a framework?

**Answer:** No. Frameworks such as Express, Fastify, NestJS, and others can run on Node.js.

### Q4. Why is Node.js good for I/O-heavy applications?

**Answer:** Node's event-driven architecture can initiate asynchronous I/O and continue processing other work instead of blocking the main JavaScript thread while waiting for each I/O operation to finish.

### Q5. Why can Node.js be bad for CPU-heavy work?

**Answer:** CPU-heavy JavaScript executed on the main thread blocks the event loop. While that computation runs, other callbacks cannot be processed promptly. Worker threads or separate processes/services can move CPU-heavy work away from the main event loop.

### Q6. What is V8?

**Answer:** V8 is Google's open-source JavaScript engine. Node.js embeds V8 to parse, compile, optimize, and execute JavaScript.

### Q7. What is libuv?

**Answer:** libuv is a cross-platform library used by Node.js for its event loop and asynchronous I/O abstractions. It also provides a thread pool used by certain operations.

### Q8. Is Node.js single-threaded?

**Answer:** JavaScript execution on the main event-loop thread is single-threaded by default. However, Node processes can use multiple internal threads and explicit worker threads, and applications can use multiple processes.

---

## Event Loop / Async

### Q9. What is the event loop?

**Answer:** The event loop coordinates execution of JavaScript callbacks and asynchronous events. It allows Node.js to continue handling other work while I/O is pending instead of blocking on each operation.

### Q10. What is the difference between blocking and non-blocking I/O?

**Answer:** Blocking I/O prevents the current JavaScript thread from continuing until the operation completes. Non-blocking I/O starts the operation and allows the event loop to process other work until the result is ready.

### Q11. Does `await` block Node.js?

**Answer:** No. `await` suspends the current async function until the Promise settles. Other event-loop work can continue. However, CPU-heavy synchronous work inside the function can still block the event loop.

### Q12. What is callback hell?

**Answer:** Callback hell is deeply nested callback-based asynchronous code that becomes difficult to read, test, and handle errors in. Promises and `async`/`await` generally make asynchronous control flow clearer.

### Q13. What is a Promise?

**Answer:** A Promise represents the eventual success or failure of an asynchronous operation. It is initially pending and then becomes fulfilled or rejected.

### Q14. What is the difference between `Promise.all()` and `Promise.allSettled()`?

**Answer:** `Promise.all()` fulfills only if every input fulfills and rejects as soon as an input rejects. `Promise.allSettled()` waits for every input and returns each result's status, so it is useful when individual failures should not prevent collection of all outcomes.

### Q15. What is the difference between `process.nextTick()` and `setImmediate()`?

**Answer:** `process.nextTick()` schedules a callback in Node's special next-tick queue, which is processed with very high priority around callback execution. `setImmediate()` schedules work for the event loop's check phase. They therefore have different scheduling behavior and should not be treated as interchangeable.

### Q16. What happens if `process.nextTick()` is recursively scheduled forever?

**Answer:** It can starve the event loop because Node keeps processing next-tick callbacks before moving on to other work. This can delay timers and I/O callbacks.

### Q17. Are timers guaranteed to run exactly on time?

**Answer:** No. A timer specifies a threshold after which its callback becomes eligible to run. Event-loop load and other work can delay execution.

---

## Modules / npm

### Q18. What is the difference between CommonJS and ESM?

**Answer:** They are two module systems supported by Node. CommonJS uses `require()` and `module.exports`; ESM uses `import` and `export` and provides a statically analyzable module structure.

### Q19. What does `package.json` do?

**Answer:** It describes package/application metadata, scripts, dependencies, module configuration, compatibility information, and other package behavior.

### Q20. Why commit `package-lock.json`?

**Answer:** It records the resolved dependency tree so application installs can be reproduced more consistently across developer machines and CI environments.

### Q21. `npm install` vs `npm ci`?

**Answer:** `npm install` is a general dependency installation command that can update the lockfile. `npm ci` is designed for clean, reproducible CI installs and uses the lockfile as the authoritative dependency resolution.

### Q22. What is a peer dependency?

**Answer:** A peer dependency expresses that a package expects the consuming application to provide a compatible version of another package. It is common for plugins and libraries that integrate with a host framework.

---

## HTTP / Backend

### Q23. How do you create an HTTP server in Node.js?

**Answer:** Use the built-in `node:http` module and `http.createServer()`, then call `server.listen(port)`.

### Q24. What is middleware?

**Answer:** Middleware is a function in a request-processing chain that can inspect or modify the request/response, end the response, continue processing, or pass an error onward. Express is a common Node framework built around middleware.

### Q25. How do you handle errors in an Express application?

**Answer:** Route handlers should catch/reject asynchronous failures and pass them to the framework's error-handling middleware, which should map internal errors to safe HTTP responses and log diagnostic details server-side.

### Q26. How do you handle authentication in Node.js?

**Answer:** Common approaches include secure session cookies, JWTs, and OAuth/OIDC. Authentication verifies identity; authorization separately checks permissions. Tokens must be properly validated rather than merely decoded.

### Q27. Why use a database connection pool?

**Answer:** Establishing a database connection is relatively expensive. A pool reuses a bounded set of connections, improving latency and preventing uncontrolled connection creation.

### Q28. What is graceful shutdown?

**Answer:** Graceful shutdown means stopping acceptance of new work, allowing or cancelling in-flight work according to policy, closing servers and resource pools, and then exiting cleanly. This is important during deployments, container termination, and scaling events.

---

## Streams / Performance

### Q29. What is a stream?

**Answer:** A stream processes data incrementally in chunks. It avoids requiring the complete dataset in memory and is useful for large files, HTTP bodies, compression, and network protocols.

### Q30. What is backpressure?

**Answer:** Backpressure occurs when a data consumer is slower than its producer. Stream mechanisms can regulate the producer so buffers do not grow without bound and memory remains controlled.

### Q31. What is the difference between `spawn()` and `exec()`?

**Answer:** `spawn()` exposes process I/O as streams and is well suited to long-running or large-output commands. `exec()` runs a command through a shell and buffers output, which is convenient for smaller command output but requires extra care with untrusted input.

### Q32. How can you prevent event-loop blocking?

**Answer:** Avoid expensive synchronous APIs on request paths, move CPU-heavy work to worker threads or separate services, stream large payloads, bound concurrency, avoid pathological regexes, and profile slow paths.

### Q33. How do you investigate a Node.js memory leak?

**Answer:** Monitor memory over time, reproduce the growth, inspect heap snapshots, compare retained objects between snapshots, look for unbounded caches/listeners/closures/queues, and use profiling tools to identify the retaining references.

### Q34. What is event-loop lag?

**Answer:** Event-loop lag is the delay between when work is expected to run and when the event loop actually gets CPU time to process it. High lag means callbacks and requests can be delayed even if external dependencies are healthy.

---

## Advanced

### Q35. Worker threads vs cluster?

**Answer:** Worker threads provide additional threads for parallel JavaScript execution within a process and are useful for CPU-heavy tasks. Cluster-style approaches use multiple Node processes, which provide stronger process isolation and can distribute server workloads across CPU cores.

### Q36. What is the difference between a process and a thread?

**Answer:** A process has its own virtual address space and operating-system resources. Threads execute within a process and share its memory space. Separate processes provide stronger isolation; threads can communicate efficiently through shared memory/message passing but require careful synchronization.

### Q37. How would you scale a Node.js API?

**Answer:** First remove bottlenecks through profiling. Then use multiple application instances behind a load balancer, keep application instances stateless where possible, use database pooling and indexes, introduce caching where appropriate, move slow background work to queues/workers, add observability, and scale dependencies along with the application.

### Q38. How do you handle CPU-intensive work in Node.js?

**Answer:** Measure first, then move CPU-intensive work off the main event loop using worker threads, child processes, a background job system, or a dedicated service. Choose based on CPU/memory needs, isolation, latency, and operational complexity.

### Q39. What is the difference between `Buffer` and a string?

**Answer:** A string represents text; a Buffer represents raw bytes. Buffers are used for binary data such as file contents, network packets, and encoded payloads.

### Q40. Why are streams important for APIs?

**Answer:** Streaming lets an API process large request/response bodies incrementally, reducing peak memory usage and potentially improving time-to-first-byte and throughput.

### Q41. How do you make a Node.js service resilient to a slow downstream service?

**Answer:** Set deadlines/timeouts, propagate cancellation, use bounded concurrency, retry only safe/idempotent operations with backoff and jitter, use circuit-breaking or bulkhead patterns where justified, and monitor dependency latency/error rates.

### Q42. How do you prevent duplicate background jobs?

**Answer:** Prefer idempotent job handlers and durable job identifiers. Use deduplication/idempotency keys when the queue supports them and design the underlying database operation so repeated delivery produces the same intended state rather than duplicate side effects.

### Q43. How would you design a Node.js real-time chat backend?

**Answer:** Use WebSockets for persistent connections, authenticate connections, store durable messages in a database, use a shared pub/sub or message broker when multiple instances are involved, maintain connection/session metadata carefully, handle reconnects and ordering, and monitor connection counts and event-loop health.

### Q44. What is `AsyncLocalStorage` useful for?

**Answer:** It provides request-scoped context across asynchronous operations. A common use is carrying a correlation/request ID so logs produced deep in a call chain can be associated with the original request.

### Q45. What is the difference between authentication and authorization?

**Answer:** Authentication establishes identity; authorization determines whether that identity has permission to perform an action on a resource.

---

# 46. Scenario-Based Interview Questions

### Q46. Your Node API becomes slow under load, but CPU is only 30%. What do you investigate?

**Answer:** Check event-loop delay, database latency/pool saturation, external API latency, connection limits, queue depth, lock contention, GC behavior, and request concurrency. Low overall CPU does not prove the application is healthy; one blocked event-loop thread or a slow dependency can cause high latency.

### Q47. One endpoint processes a 2 GB file. How would you implement it?

**Answer:** Avoid loading the entire file into memory. Use streaming request/response APIs, enforce size and timeout limits, process chunks, apply backpressure, and store the result incrementally. If processing is expensive, hand it off to a background worker.

### Q48. A service crashes because of an uncaught exception. Should you simply keep running?

**Answer:** Not necessarily. An uncaught exception can leave the process in an unknown state. Log enough information for diagnosis, stop accepting new work, close resources if feasible, and let a process supervisor/container orchestrator restart the instance.

### Q49. Your API calls three independent services and waits 1 second for each. How can you reduce latency?

**Answer:** If the calls are independent, issue them concurrently with `Promise.all()` or a bounded-concurrency equivalent. Also apply timeouts and cancellation. Do not increase concurrency without considering downstream capacity.

### Q50. You need to send one million emails. Would you send them inside an HTTP request?

**Answer:** No. The API should enqueue work and return quickly. Background workers should process the queue with controlled concurrency, retries, rate limits, idempotency, and dead-letter handling.

### Q51. Multiple Node instances use an in-memory cache. What problem can occur?

**Answer:** Each instance has a different cache. A value cached on instance A may not exist on instance B, creating inconsistent behavior and poor cache hit rates. Use a shared cache such as Redis when cross-instance consistency is required.

### Q52. How would you protect a login endpoint?

**Answer:** Validate input, use a strong password-hashing algorithm, enforce TLS, rate-limit and/or progressively delay repeated attempts, avoid user-enumeration leaks, use secure session/token handling, log suspicious activity without exposing secrets, and consider MFA for sensitive applications.

---

# 47. Interview One-Liners to Remember

- **Node.js:** JavaScript runtime built around V8.
- **V8:** JavaScript engine.
- **libuv:** Event loop + async I/O abstraction layer used by Node.
- **Event loop:** Coordinates asynchronous callbacks/events.
- **Non-blocking I/O:** Start I/O and continue processing other work instead of waiting synchronously.
- **Promise:** Object representing an eventual async result.
- **`async`/`await`:** Promise-based syntax for readable async control flow.
- **Stream:** Incremental data processing abstraction.
- **Backpressure:** Producer is regulated because the consumer is slower.
- **Buffer:** Raw bytes.
- **EventEmitter:** Event subscription/publication abstraction.
- **Worker thread:** Parallel JavaScript execution for CPU-heavy work.
- **Graceful shutdown:** Stop new work, finish/cancel in-flight work, close resources, exit.
- **Connection pool:** Reusable bounded database connections.
- **Idempotency:** Repeating an operation does not create unintended additional effects.
- **Authentication:** Who are you?
- **Authorization:** What can you do?

---

# 48. Final Revision Checklist

- [ ] Explain Node.js vs JavaScript vs Express.
- [ ] Explain V8 and libuv.
- [ ] Explain the event loop at a high level.
- [ ] Explain why Node can handle many I/O operations concurrently.
- [ ] Explain why CPU-heavy work can block Node.
- [ ] Explain `process.nextTick()` vs `setImmediate()`.
- [ ] Explain callbacks, Promises, and `async`/`await`.
- [ ] Explain `Promise.all()` vs `allSettled()`.
- [ ] Explain CommonJS vs ESM.
- [ ] Explain `package.json` and lockfiles.
- [ ] Know major core modules.
- [ ] Explain Buffers and streams.
- [ ] Explain backpressure.
- [ ] Know `spawn`, `exec`, and `execFile` differences.
- [ ] Explain worker threads and multi-process scaling.
- [ ] Explain middleware and REST API basics.
- [ ] Explain DB connection pooling and transactions.
- [ ] Explain caching and cache invalidation concerns.
- [ ] Explain graceful shutdown.
- [ ] Explain authentication vs authorization.
- [ ] Explain common Node security risks.
- [ ] Explain event-loop lag and memory leaks.
- [ ] Explain queues, retries, idempotency, and dead-letter handling.
- [ ] Be able to design a scalable Node.js API.

---

## 49. Recommended Mental Model

When answering Node.js interview questions, connect concepts rather than memorizing isolated definitions:

```text
                    Node.js Application
                            |
          +-----------------+------------------+
          |                 |                  |
       HTTP/API          Business           Jobs
          |              Logic               |
          |                 |                Queue
          v                 v                  |
     Middleware        DB / Cache        Worker Process
          |                 |
          +--------+--------+
                   |
             Async Operations
                   |
             Event Loop / libuv
                   |
              Operating System
```

The key principle is:

> **Keep the event loop responsive, make I/O asynchronous, bound resource usage, move CPU-heavy work elsewhere, and design every external dependency with failure and cancellation in mind.**
