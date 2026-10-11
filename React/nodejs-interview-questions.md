# Node.js Interview Questions & Answers

A comprehensive guide covering Node.js fundamentals through advanced concepts, the event loop, async patterns, modules, streams, performance, security, and the broader ecosystem.

---

## Table of Contents

1. [Node.js Fundamentals](#1-nodejs-fundamentals)
2. [The Event Loop & Concurrency Model](#2-the-event-loop--concurrency-model)
3. [Modules (CommonJS & ES Modules)](#3-modules-commonjs--es-modules)
4. [Asynchronous Programming](#4-asynchronous-programming)
5. [Streams & Buffers](#5-streams--buffers)
6. [File System & Path](#6-file-system--path)
7. [HTTP, Networking & Express](#7-http-networking--express)
8. [Error Handling](#8-error-handling)
9. [Process, Child Processes & Clustering](#9-process-child-processes--clustering)
10. [Worker Threads & CPU-bound Work](#10-worker-threads--cpu-bound-work)
11. [Package Management (npm/yarn/pnpm)](#11-package-management-npmyarnpnpm)
12. [Databases & ORMs](#12-databases--orms)
13. [Authentication & Security](#13-authentication--security)
14. [Testing](#14-testing)
15. [Performance & Debugging](#15-performance--debugging)
16. [Microservices & Architecture](#16-microservices--architecture)
17. [Deployment, Environments & DevOps](#17-deployment-environments--devops)
18. [TypeScript with Node.js](#18-typescript-with-nodejs)
19. [Miscellaneous & Best Practices](#19-miscellaneous--best-practices)

---

## 1. Node.js Fundamentals

### Q1. What is Node.js?
Node.js is a cross-platform, open-source JavaScript **runtime environment** built on Google Chrome's V8 engine that executes JavaScript outside the browser. It uses an event-driven, non-blocking I/O model, making it lightweight and efficient for building scalable network applications like APIs, real-time services, and CLI tools.

### Q2. Is Node.js single-threaded or multi-threaded?
Node.js's **main JavaScript execution thread is single-threaded** — your application code runs on one thread. However, Node.js itself is not purely single-threaded under the hood: I/O operations (file system, DNS, some crypto operations) are offloaded to a **libuv thread pool** (default size 4) or handled asynchronously by the OS kernel, and results are returned to the main thread via the event loop. So the concurrency model is single-threaded for your code, but multi-threaded for I/O.

### Q3. What is V8, and what is libuv?
- **V8** is Google's open-source JavaScript/WebAssembly engine (also used in Chrome) that compiles JavaScript to native machine code, providing memory management (garbage collection) and execution.
- **libuv** is a C library that provides the event loop, thread pool, and abstractions for asynchronous, non-blocking I/O across different operating systems (handling file system access, networking, DNS lookups, etc.). It's what makes Node.js's async I/O model possible.

### Q4. What makes Node.js different from browser JavaScript?
Node.js lacks browser-specific globals (`window`, `document`, `localStorage`) but adds server-side APIs (`fs`, `http`, `path`, `process`, `Buffer`) and uses **CommonJS** (`require`/`module.exports`) as its traditional module system (alongside modern ES Module support). It also gives direct access to the file system, OS, and network — capabilities intentionally restricted in browsers for security.

### Q5. What are the pros and cons of Node.js?
**Pros:**
- Non-blocking I/O makes it well-suited for I/O-heavy, real-time applications (chat apps, streaming, APIs).
- Same language (JavaScript/TypeScript) on both frontend and backend, easing code/knowledge sharing.
- Huge npm ecosystem.
- Fast startup and lightweight footprint compared to some traditional server stacks.

**Cons:**
- Not ideal for CPU-intensive tasks (heavy computation blocks the single event-loop thread) unless offloaded to worker threads/child processes.
- Callback-heavy code can lead to "callback hell" if async patterns aren't managed well (mitigated by Promises/async-await).
- Relies heavily on third-party packages, which introduces dependency and security management overhead.

### Q6. What is npm?
npm (Node Package Manager) is the default package manager bundled with Node.js. It manages project dependencies (installing, versioning, publishing packages) and provides a CLI for running scripts defined in `package.json`.

### Q7. What is `package.json`, and what are its key fields?
The manifest file for a Node.js project. Key fields: `name`, `version`, `main` (entry point), `scripts` (custom CLI commands like `start`, `test`), `dependencies` (runtime packages), `devDependencies` (build/test-only packages), `engines` (Node version constraints), and `type` (`"module"` vs `"commonjs"`).

### Q8. What is `package-lock.json`, and why is it important?
It records the **exact** dependency tree (including transitive dependencies and their resolved versions/hashes) that was installed, ensuring reproducible installs across machines and CI environments — since `package.json` version ranges (like `^1.2.0`) alone don't guarantee everyone gets identical versions.

---

## 2. The Event Loop & Concurrency Model

### Q9. What is the Event Loop?
The mechanism that allows Node.js to perform non-blocking I/O despite JavaScript being single-threaded. It continuously checks whether the call stack is empty and, if so, pulls the next callback from the appropriate queue to execute. This lets Node.js offload operations (timers, I/O, network requests) and handle their results later without blocking the main thread.

### Q10. What are the phases of the Event Loop?
1. **Timers** – executes callbacks scheduled by `setTimeout()` and `setInterval()`.
2. **Pending callbacks** – executes I/O callbacks deferred to the next loop iteration (some system-level callbacks).
3. **Idle, prepare** – internal use only.
4. **Poll** – retrieves new I/O events, executes I/O-related callbacks (e.g., reading a file); blocks here if appropriate.
5. **Check** – executes `setImmediate()` callbacks.
6. **Close callbacks** – executes close event callbacks (e.g., `socket.on('close', ...)`).

Between/after each phase, Node processes the **microtask queues**: `process.nextTick()` callbacks (highest priority) and resolved Promise callbacks.

### Q11. What is the difference between `process.nextTick()`, Promises (microtasks), `setTimeout()`, and `setImmediate()`?
- `process.nextTick()` callbacks run **immediately after the current operation completes**, before the event loop continues — even before Promise microtasks. It has the highest priority.
- **Promise callbacks** (`.then`, `async/await` continuations) run as microtasks, after `nextTick` callbacks but before moving to the next event loop phase.
- `setTimeout(fn, 0)` schedules `fn` for the **timers** phase, after at least the specified delay (subject to the event loop's timing).
- `setImmediate(fn)` schedules `fn` to run in the **check** phase, generally after I/O callbacks in the current iteration.

Order can vary depending on context (main module vs. inside an I/O callback), but within an I/O cycle, `setImmediate` reliably fires before `setTimeout(fn, 0)`.

### Q12. Why can overusing `process.nextTick()` be dangerous?
Because `nextTick` callbacks are processed to *completion* before the event loop can proceed to the next phase, recursively scheduling `nextTick` calls (e.g., a function that calls `process.nextTick()` on itself) can **starve the event loop**, preventing I/O, timers, and everything else from ever running — effectively an infinite loop that blocks the whole process.

### Q13. What does "non-blocking I/O" mean?
Instead of waiting (blocking the thread) for an I/O operation (disk read, network call, DB query) to complete before moving to the next line of code, Node.js initiates the operation, registers a callback, and immediately continues executing other code. When the I/O completes, the callback is queued and executed by the event loop — allowing a single thread to handle many concurrent operations efficiently.

### Q14. What operations *do* block the event loop, and how do you avoid them?
CPU-intensive synchronous code — large loops, synchronous JSON parsing of huge payloads, synchronous crypto/hashing, synchronous file operations (`fs.readFileSync`), or complex regular expressions (catastrophic backtracking) — blocks the single thread and delays all other pending callbacks. Mitigations: use async versions of APIs, break up heavy computation (e.g., with `setImmediate` to yield), or offload to **worker threads** or a separate **child process/microservice**.

### Q15. What is the libuv thread pool, and how do you configure its size?
A pool of worker threads (default **4**) that libuv uses to run certain operations that don't have native async OS support — such as `fs` file operations, DNS lookups (`dns.lookup`), and some `crypto` functions (like `pbkdf2`). Its size can be increased via the `UV_THREADPOOL_SIZE` environment variable (max 128), which can help throughput for I/O-heavy apps hitting thread pool contention.

---

## 3. Modules (CommonJS & ES Modules)

### Q16. What is CommonJS, and how does `require`/`module.exports` work?
CommonJS is Node's original, default module system. Each file is treated as its own module with a private scope. `module.exports` (or the shorthand `exports`) defines what a module exposes; `require('./module')` synchronously loads and caches another module's exports.
```js
// math.js
module.exports = { add: (a, b) => a + b };

// app.js
const { add } = require('./math');
```

### Q17. What are ES Modules (ESM), and how do they differ from CommonJS?
ESM is the standard JavaScript module system (`import`/`export`), supported natively in modern Node.js. Key differences:
- ESM imports are **static** (analyzed at parse time, enabling tree-shaking) vs CommonJS's **dynamic**, runtime `require()` calls.
- ESM is **asynchronous** by design at the loading level; CommonJS `require` is synchronous.
- ESM runs in **strict mode** by default; has no `require`, `module`, `__dirname`, or `__filename` (equivalents exist via `import.meta.url`).
- To use ESM, set `"type": "module"` in `package.json` or use the `.mjs` extension (and `.cjs` to force CommonJS in an ESM project).

### Q18. How does Node.js resolve/cache `require()` calls?
Node resolves module paths (core modules → relative/absolute file paths → `node_modules` lookup, walking up directories) and **caches** the loaded module object after the first `require()` — subsequent `require()` calls for the same resolved path return the cached exports object rather than re-executing the file. This means module-level state is shared across every file that requires it.

### Q19. What is the difference between `module.exports` and `exports`?
`exports` is just a reference/shorthand pointing to the same object as `module.exports` initially. Adding properties to `exports` (`exports.foo = ...`) works, but **reassigning** `exports = {...}` breaks the reference and won't affect what's actually exported — you must reassign `module.exports` directly in that case.

### Q20. Can you mix CommonJS and ES Modules?
Partially. ESM can `import` CommonJS modules (Node wraps the CJS `module.exports` as the default export). CommonJS **cannot** `require()` a genuine ESM module directly (must use dynamic `import()`, which is asynchronous), since ESM's static/async nature is fundamentally incompatible with synchronous `require`.

---

## 4. Asynchronous Programming

### Q21. What are the main patterns for handling asynchronous code in Node.js, in order of evolution?
1. **Callbacks** — a function passed as an argument, invoked upon completion (traditionally `(err, data) => {}`, the "error-first callback" convention).
2. **Promises** — objects representing eventual completion/failure of an async operation, with `.then()/.catch()/.finally()` chaining, avoiding deep callback nesting.
3. **async/await** — syntactic sugar over Promises, letting asynchronous code be written and read like synchronous code.

### Q22. What is "callback hell," and how do Promises/async-await solve it?
Deeply nested callbacks (each depending on the previous one's result) create pyramid-shaped, hard-to-read/maintain code. Promises flatten this into a chain (`.then().then().then()`), and `async/await` flattens it further into linear, synchronous-looking code with standard `try/catch` for errors.

### Q23. What is the error-first callback convention?
A Node.js convention where callback functions accept an `error` as their first argument (`null` if no error occurred) and the result as the second: `fs.readFile('file.txt', (err, data) => { if (err) return handleError(err); /* use data */ });` — establishing a consistent way to propagate errors through async operations.

### Q24. Explain `Promise.all`, `Promise.allSettled`, `Promise.race`, and `Promise.any`.
- `Promise.all(promises)` — resolves when **all** promises resolve (returns an array of results); rejects immediately if **any** promise rejects.
- `Promise.allSettled(promises)` — always resolves once all promises have settled, returning an array of `{status, value|reason}` for each — useful when you want results regardless of individual failures.
- `Promise.race(promises)` — settles as soon as the **first** promise settles (resolve or reject).
- `Promise.any(promises)` — resolves as soon as the **first** promise resolves; rejects only if **all** reject (with an `AggregateError`).

### Q25. How do you convert a callback-based function into a Promise-based one?
Using `util.promisify()`:
```js
const util = require('util');
const fs = require('fs');
const readFileAsync = util.promisify(fs.readFile);

const data = await readFileAsync('file.txt', 'utf8');
```
(Many core modules now also expose a Promise-based API directly, e.g., `fs.promises` / `require('fs/promises')`.)

### Q26. What happens if you don't handle a rejected Promise?
It becomes an **unhandled promise rejection**. Since Node.js 15+, unhandled rejections **crash the process** by default (previously just a warning), so it's important to always attach a `.catch()` or wrap `await` calls in `try/catch`, and optionally set up a global `process.on('unhandledRejection', handler)` for logging/monitoring as a safety net.

### Q27. What is the difference between synchronous and asynchronous error handling with async/await?
Synchronous errors thrown inside an `async` function are automatically converted into a rejected Promise, so they can be caught with `try/catch` around the `await` call just like a "real" exception — unifying sync and async error handling into one consistent pattern.

### Q28. What is `EventEmitter`, and why is it fundamental to Node.js?
A core class (`require('events')`) implementing the observer/pub-sub pattern — objects can emit named events, and listeners subscribe via `.on(event, handler)`. Many core Node APIs (streams, HTTP servers, child processes) are built on top of `EventEmitter`, making it a foundational pattern for event-driven architecture in Node.
```js
const EventEmitter = require('events');
const emitter = new EventEmitter();
emitter.on('greet', (name) => console.log(`Hello, ${name}`));
emitter.emit('greet', 'World');
```

---

## 5. Streams & Buffers

### Q29. What are Streams, and why are they useful?
Streams are objects for handling reading/writing data **incrementally, in chunks**, rather than loading an entire resource into memory at once. This makes them essential for working with large files, network data, or any data source too big (or continuous) to buffer fully in memory — improving both memory efficiency and time-to-first-byte.

### Q30. What are the four types of Streams?
1. **Readable** — a source of data to be consumed (e.g., `fs.createReadStream()`, an HTTP request on the server).
2. **Writable** — a destination data can be written to (e.g., `fs.createWriteStream()`, an HTTP response).
3. **Duplex** — both readable and writable (e.g., a TCP socket).
4. **Transform** — a duplex stream that modifies/transforms data as it passes through (e.g., `zlib.createGzip()` for compression).

### Q31. What is backpressure in streams, and how is it handled?
Backpressure occurs when a writable destination can't process incoming data as fast as a readable source produces it, risking excessive memory buildup. Node's stream APIs handle this automatically when using `.pipe()` — it pauses the readable source when the writable's internal buffer is full (signaled by `write()` returning `false`) and resumes once the buffer drains (`'drain'` event), keeping memory usage bounded.

### Q32. What is `.pipe()`, and why is it preferred over manually handling `data` events?
`readable.pipe(writable)` connects a readable stream's output directly to a writable stream's input, automatically managing backpressure, data flow, and (by default) ending the writable stream when the readable ends. Manually handling `'data'`/`'end'` events requires you to implement backpressure and error handling yourself, which `.pipe()` (or the modern `pipeline()` utility) does safely and consistently.

### Q33. What is `stream.pipeline()`, and why is it recommended over `.pipe()` for production code?
`pipeline(src, ...transforms, dest, callback)` (from `require('stream')`, promisify-able) chains multiple streams together like `.pipe()` but additionally handles **proper cleanup and error propagation** across the whole chain — if any stream in the chain errors or closes, all the streams are properly destroyed, avoiding memory/file-descriptor leaks that plain `.pipe()` chains can suffer from when an error occurs mid-stream.

### Q34. What is a `Buffer`?
A `Buffer` is a Node.js global class for handling raw binary data directly in memory (outside the V8 heap), used when working with things like file I/O, TCP streams, or binary protocols. It's similar to a fixed-length array of integers (bytes, 0-255) and predates the standardized `Uint8Array`/`TypedArray` (which Node's `Buffer` now extends).
```js
const buf = Buffer.from('hello', 'utf8');
console.log(buf); // <Buffer 68 65 6c 6c 6f>
console.log(buf.toString('utf8')); // 'hello'
```

---

## 6. File System & Path

### Q35. What are the different ways to work with the file system in Node.js?
The built-in `fs` module offers three API styles: **synchronous** (`fs.readFileSync`, blocks the event loop), **callback-based** (`fs.readFile(path, cb)`, traditional async), and **Promise-based** (`fs.promises.readFile()` or `require('fs/promises')`, ideal for use with `async/await`).

### Q36. Why should `fs.readFileSync`/other sync fs methods generally be avoided in server code?
Because they **block the entire event loop** until the operation completes, preventing the server from handling any other requests during that time — problematic in a request-handling server, though acceptable for one-off scripts, CLI tools, or startup-time configuration loading where blocking briefly doesn't hurt.

### Q37. What is the `path` module used for, and why is it preferred over manually concatenating path strings?
`path` provides utilities (`path.join()`, `path.resolve()`, `path.basename()`, `path.extname()`, `path.dirname()`) for working with file and directory paths in a way that's **cross-platform** (correctly handling `/` vs `\` separators on POSIX vs Windows), avoiding subtle bugs from manual string concatenation.

### Q38. What is the difference between `__dirname`/`__filename` and `process.cwd()`?
`__dirname`/`__filename` (available in CommonJS) refer to the directory/file path of the **currently executing module file**, regardless of where the process was started from. `process.cwd()` returns the **current working directory** of the Node process itself — the directory the process was launched from, which can differ from where the running script actually lives.

---

## 7. HTTP, Networking & Express

### Q39. How do you create a basic HTTP server in Node.js without a framework?
```js
const http = require('http');
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World');
});
server.listen(3000, () => console.log('Server running on port 3000'));
```

### Q40. What is Express.js, and why is it commonly used?
Express is a minimal, unopinionated web framework built on top of Node's core `http` module, providing routing, middleware support, and utilities for handling requests/responses — reducing the boilerplate needed to build REST APIs and web servers compared to using `http` directly.

### Q41. What is middleware in Express?
A function `(req, res, next)` that has access to the request and response objects and can modify them, end the request-response cycle, or pass control to the next middleware via `next()`. Middleware is used for cross-cutting concerns: logging, authentication, body parsing, error handling, CORS, etc.
```js
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});
```

### Q42. How does Express handle errors, and what is an error-handling middleware?
Error-handling middleware is defined with **four** arguments `(err, req, res, next)`, and Express routes errors to it when `next(err)` is called (or a synchronous error is thrown inside a route handler). It should be registered **last**, after all other routes/middleware.
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong' });
});
```
Note: errors thrown inside **async** route handlers aren't automatically caught by Express (pre—v5) unless you catch them yourself and call `next(err)`, or use a wrapper utility.

### Q43. What is CORS, and how do you enable it in Node/Express?
CORS (Cross-Origin Resource Sharing) is a browser security mechanism that restricts web pages from making requests to a different origin (domain/port/protocol) than the one that served the page, unless the server explicitly allows it via response headers (`Access-Control-Allow-Origin`, etc.). In Express, it's typically enabled with the `cors` npm package: `app.use(cors())`.

### Q44. What is REST, and what are the common HTTP methods used in a REST API?
REST (Representational State Transfer) is an architectural style for designing networked APIs around resources, identified by URLs, manipulated via standard HTTP methods: **GET** (read), **POST** (create), **PUT** (replace/update), **PATCH** (partial update), **DELETE** (remove) — generally aiming to be stateless, with each request containing all information needed to process it.

### Q45. What is the difference between `PUT` and `PATCH`?
`PUT` is meant to **replace** a resource entirely with the provided representation (idempotent — repeating it produces the same result). `PATCH` applies a **partial** update, modifying only the specified fields.

### Q46. What are WebSockets, and how do they differ from HTTP polling?
WebSockets provide a **persistent, full-duplex** connection between client and server over a single TCP connection, allowing both sides to send messages at any time without the overhead of repeated HTTP requests — ideal for real-time apps (chat, live notifications, games). This contrasts with HTTP polling (repeatedly requesting updates) or long-polling (holding a request open until new data arrives), both of which are less efficient. Common libraries: `ws`, `Socket.IO` (which adds fallbacks, rooms, and reconnection logic on top of raw WebSockets).

---

## 8. Error Handling

### Q47. What are the different types of errors in Node.js?
- **Operational errors** — expected runtime problems (failed DB connection, invalid user input, file not found) that should be handled gracefully.
- **Programmer errors** — bugs in the code itself (calling a function with wrong argument types, undefined variable access) — generally should not be "handled" silently; better to fix the bug or, in extreme cases, let the process crash and restart cleanly.
- **System errors** — errors reported by the underlying OS, often surfaced as exceptions with an `err.code` (e.g., `ENOENT`, `ECONNREFUSED`).

### Q48. How do you handle uncaught exceptions and unhandled rejections globally?
```js
process.on('uncaughtException', (err) => {
  console.error('Uncaught Exception:', err);
  // Log, then gracefully shut down — the process is in an undefined state
  process.exit(1);
});

process.on('unhandledRejection', (reason) => {
  console.error('Unhandled Rejection:', reason);
  process.exit(1);
});
```
These are safety nets, not primary error-handling strategies — after an uncaught exception, the process is considered to be in a potentially corrupted state, so best practice is to log the error and restart the process (often via a process manager like PM2 or an orchestrator), rather than trying to keep running.

### Q49. What is the difference between throwing an error synchronously vs passing it to a callback vs rejecting a Promise?
- Synchronous `throw` must be caught with `try/catch` in the same call stack.
- Errors in callback-based async APIs are conventionally passed as the first argument (error-first callback) rather than thrown, since a `throw` inside an async callback can't be caught by a `try/catch` wrapping the original call (it's a different point in time/stack).
- Promise rejections propagate through `.catch()` chains or are caught via `try/catch` around `await`.

### Q50. What is a custom Error class, and why create one?
Extending the built-in `Error` class lets you attach additional context (like an HTTP status code or error type) and lets calling code distinguish error types via `instanceof` checks, useful for centralized error-handling middleware that formats responses differently based on error type.
```js
class NotFoundError extends Error {
  constructor(message) {
    super(message);
    this.name = 'NotFoundError';
    this.statusCode = 404;
  }
}
```

---

## 9. Process, Child Processes & Clustering

### Q51. What is the `process` global object?
A built-in object providing information about, and control over, the current Node.js process: `process.env` (environment variables), `process.argv` (CLI arguments), `process.exit()`, `process.cwd()`, `process.on()` (for process-level events like `exit`, `SIGTERM`, `uncaughtException`), and more.

### Q52. What are the ways to spawn child processes in Node.js?
The `child_process` module offers:
- `exec()` — runs a command in a shell, buffers output, good for short commands with limited output.
- `execFile()` — like `exec` but runs a file directly without a shell (slightly more efficient/secure, since it avoids shell injection risk).
- `spawn()` — launches a new process and streams stdout/stderr, better for long-running processes or large output volumes.
- `fork()` — a specialized version of `spawn()` for launching **new Node.js processes**, establishing an IPC (inter-process communication) channel for message-passing between parent and child.

### Q53. What is the `cluster` module, and why is it used?
Since Node.js runs JavaScript on a single thread, a single Node process can't take advantage of multi-core CPUs by default. The `cluster` module lets you fork multiple worker processes (typically one per CPU core), all sharing the same server port, with the OS/Node load-balancing incoming connections across them — improving throughput and providing some fault tolerance (a crashed worker can be replaced without taking down the whole app).
```js
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  os.cpus().forEach(() => cluster.fork());
} else {
  require('./server'); // each worker runs the actual server
}
```

### Q54. What is the difference between `cluster` and `worker_threads`?
`cluster` spawns separate **OS processes**, each with its own memory space and V8 instance — good for scaling I/O-bound HTTP servers across cores, but processes can't share memory directly (must communicate via IPC/serialization). `worker_threads` run within the **same process**, can share memory via `SharedArrayBuffer`, and have lower overhead per unit — better suited for CPU-bound tasks that need to offload heavy computation without blocking the main thread.

### Q55. How do you gracefully shut down a Node.js server?
Listen for termination signals (`SIGTERM`, `SIGINT`), stop accepting new connections, let in-flight requests finish, close database connections/other resources, then exit:
```js
process.on('SIGTERM', () => {
  server.close(() => {
    db.disconnect();
    process.exit(0);
  });
});
```
This is important in containerized/orchestrated environments (Docker, Kubernetes) where the platform sends `SIGTERM` before forcibly killing a process during deploys or scaling events.

---

## 10. Worker Threads & CPU-bound Work

### Q56. What are Worker Threads, and when should you use them?
The `worker_threads` module allows running JavaScript in parallel on separate threads within the same process, each with its own V8 instance and event loop, but able to share memory via `SharedArrayBuffer`/`ArrayBuffer` transfer. Use them for genuinely **CPU-intensive** tasks (image processing, complex calculations, parsing large data sets) that would otherwise block the main event loop, keeping the main thread free to handle I/O and requests.

```js
const { Worker } = require('worker_threads');
const worker = new Worker('./heavy-task.js', { workerData: input });
worker.on('message', (result) => console.log(result));
```

### Q57. What is the difference between offloading work to a Worker Thread vs a Child Process vs an external service/queue?
- **Worker threads**: lowest overhead, shared-process, best for CPU-bound JS computation.
- **Child processes**: separate OS process, useful for running non-Node executables or isolating crashes/memory usage completely.
- **External service/message queue** (e.g., a job queue like BullMQ backed by Redis): best when work needs to be distributed across multiple machines, needs persistence/retry guarantees, or should be decoupled from the web server's lifecycle entirely.

---

## 11. Package Management (npm/yarn/pnpm)

### Q58. What is semantic versioning (semver), and what do the symbols in `package.json` mean?
Semver format is `MAJOR.MINOR.PATCH` (e.g., `2.4.1`) — **major** for breaking changes, **minor** for backward-compatible features, **patch** for backward-compatible bug fixes. In `package.json`:
- `^1.2.3` — allows updates that don't change the leftmost non-zero digit (compatible with `1.x.x`, but not `2.0.0`).
- `~1.2.3` — allows patch-level updates only (`1.2.x`).
- Exact version (`1.2.3`) — no automatic updates.

### Q59. What is the difference between `dependencies` and `devDependencies`?
`dependencies` are required for the application to **run in production** (e.g., Express, a database driver). `devDependencies` are only needed during **development/build/test** (e.g., testing frameworks, linters, bundlers) and are typically excluded when installing with `npm install --production` or `npm ci --omit=dev`.

### Q60. What is `npm ci`, and how does it differ from `npm install`?
`npm ci` performs a **clean install** strictly from `package-lock.json` (deletes `node_modules` first, and fails if the lockfile and `package.json` are out of sync), producing fully reproducible builds — designed for CI/CD pipelines. `npm install` may update the lockfile and is more forgiving/flexible, suited for local development.

### Q61. What are npm/yarn/pnpm workspaces (monorepos)?
A feature that lets you manage multiple related packages within a single repository, sharing a single top-level `node_modules` (hoisting common dependencies) and enabling cross-package linking without publishing to a registry — commonly used for monorepos containing, e.g., a shared UI library alongside multiple apps that consume it.

### Q62. How does npm handle transitive dependency resolution, and what problem can arise ("dependency hell")?
npm resolves and (where possible) deduplicates/hoists nested dependencies into a flatter `node_modules` structure. "Dependency hell" can occur when different packages require incompatible versions of the same dependency, leading to duplicated installs (increasing bundle/install size) or, in rarer cases, version conflicts that require manual resolution (`overrides`/`resolutions` fields) to fix.

---

## 12. Databases & ORMs

### Q63. How do you typically connect Node.js to a SQL database?
Using a driver (`pg` for PostgreSQL, `mysql2` for MySQL) directly, or through a query builder (`knex`) or a full **ORM** (Object-Relational Mapper) like **Sequelize**, **TypeORM**, or **Prisma**, which map database tables to JavaScript/TypeScript objects/classes and abstract raw SQL.

### Q64. What are the trade-offs of using an ORM vs raw SQL/query builders?
**ORMs** (Prisma, Sequelize, TypeORM) speed up development, provide type safety (especially Prisma with TS), and abstract database differences, but can generate inefficient queries for complex cases and add a layer of "magic" that can obscure what's actually being executed. **Raw SQL/query builders** (Knex) offer full control and often better performance for complex queries, at the cost of more boilerplate and manual type-safety/mapping work.

### Q65. How do you prevent SQL injection in Node.js?
Always use **parameterized queries / prepared statements** (passing values separately from the query string) rather than concatenating user input directly into SQL strings. Most drivers and ORMs support this natively (`?` or `$1` placeholders); avoid ever building queries via raw string concatenation with user input.
```js
// Safe
db.query('SELECT * FROM users WHERE id = $1', [userId]);
// Unsafe — vulnerable to injection
db.query(`SELECT * FROM users WHERE id = ${userId}`);
```

### Q66. How do you connect to MongoDB in Node.js?
Via the official `mongodb` driver, or more commonly through **Mongoose**, an ODM (Object-Document Mapper) that provides schema definitions, validation, middleware ("hooks"), and a more structured API over MongoDB's flexible, schema-less documents.

### Q67. What is connection pooling, and why is it important?
Rather than opening a new database connection for every query (expensive in terms of latency and resources), a **connection pool** maintains a set of reusable, already-established connections that requests borrow and return. Nearly all production database drivers/ORMs use pooling by default — critical for handling concurrent requests efficiently under load.

---

## 13. Authentication & Security

### Q68. How is authentication typically implemented in a Node.js API?
Common approaches: **session-based auth** (server stores session state, typically in a store like Redis, and issues a session ID cookie), or **token-based auth** using **JWT (JSON Web Tokens)** — a signed, self-contained token issued upon login and sent by the client on subsequent requests (typically in an `Authorization: Bearer <token>` header), verified via the server's secret/public key without needing server-side session storage.

### Q69. What is a JWT, and what are its parts?
A JWT consists of three Base64URL-encoded, dot-separated parts: **Header** (algorithm/token type), **Payload** (claims — user ID, roles, expiration, etc.), and **Signature** (created by signing the header+payload with a secret or private key), used to verify the token hasn't been tampered with. Note: JWT payloads are **encoded, not encrypted** — never put sensitive data in them, as anyone can decode and read the payload.

### Q70. What are the security trade-offs between JWTs and server-side sessions?
JWTs are **stateless** (no server-side lookup needed to validate, easing horizontal scaling) but harder to **revoke** before expiration (since the server doesn't track "valid" tokens by default — requiring extra mechanisms like short expiry + refresh tokens, or a token blocklist). Sessions are easy to revoke instantly (just delete server-side state) but require a shared session store (like Redis) to scale across multiple server instances.

### Q71. How do you securely store passwords?
Never store plaintext passwords. Use a slow, purpose-built **hashing** algorithm with built-in salting, such as **bcrypt** or **argon2** (not fast general-purpose hashes like MD5/SHA-256 alone, which are vulnerable to brute-force/rainbow-table attacks due to their speed).
```js
const bcrypt = require('bcrypt');
const hash = await bcrypt.hash(password, 10); // 10 = salt rounds
const isValid = await bcrypt.compare(inputPassword, hash);
```

### Q72. What are common Node.js/web application security vulnerabilities to guard against?
- **SQL/NoSQL Injection** — use parameterized queries; sanitize input for NoSQL query operators too.
- **XSS (Cross-Site Scripting)** — sanitize/escape user-generated content rendered in HTML.
- **CSRF (Cross-Site Request Forgery)** — use CSRF tokens or `SameSite` cookies for session-based auth.
- **Prototype pollution** — a Node/JS-specific vulnerability where attacker-controlled input can modify `Object.prototype` via crafted keys (`__proto__`), affecting the whole application; mitigated by validating/sanitizing input keys and using `Object.create(null)` or `Map` where appropriate.
- **ReDoS (Regular Expression Denial of Service)** — malicious input causing catastrophic backtracking in a poorly written regex, hanging the event loop.
- **Exposing stack traces/sensitive errors** to clients in production.
- **Insecure dependencies** — regularly run `npm audit` and keep dependencies patched.
- **Missing rate limiting**, allowing brute-force or abuse.

### Q73. What is `helmet`, and what does it do?
An Express middleware package that sets various security-related HTTP headers (like `Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security`) to help protect against common web vulnerabilities, applied simply via `app.use(helmet())`.

### Q74. How do you implement rate limiting in a Node.js API?
Using middleware like `express-rate-limit` (in-memory or backed by a shared store like Redis for multi-instance deployments) to cap the number of requests a client (by IP or API key) can make within a time window, mitigating brute-force attacks and abuse.

### Q75. How should environment variables/secrets be managed?
Never commit secrets (API keys, DB credentials) to source control. Use `.env` files (loaded via a package like `dotenv`) for local development, excluded via `.gitignore`, and a proper **secrets manager** (AWS Secrets Manager, HashiCorp Vault, environment variables injected by the deployment platform) in production.

---

## 14. Testing

### Q76. What are common testing tools/frameworks used with Node.js?
- **Jest** / **Vitest** / **Mocha + Chai** — unit/integration test runners and assertion libraries.
- **Supertest** — for testing HTTP endpoints/Express apps by simulating requests without needing a real running server/port.
- **Sinon** — for spies, stubs, and mocks.
- **Playwright/Cypress** — end-to-end testing (often paired with a frontend, but can drive full-stack flows).

### Q77. What is the difference between unit, integration, and end-to-end (E2E) tests?
- **Unit tests** — test a single function/module in isolation, mocking external dependencies (DB, network).
- **Integration tests** — test how multiple units work together (e.g., an API route hitting a real/test database).
- **E2E tests** — test the entire system as a user would experience it, from the outside in (often through the UI or a full API workflow).

### Q78. How do you test an Express route with Supertest?
```js
const request = require('supertest');
const app = require('../app');

test('GET /users returns 200', async () => {
  const res = await request(app).get('/users');
  expect(res.status).toBe(200);
  expect(Array.isArray(res.body)).toBe(true);
});
```

### Q79. How do you mock external dependencies (e.g., a database or third-party API) in tests?
Using the test framework's mocking utilities (`jest.mock()`), dependency injection (passing dependencies as parameters/constructor args rather than importing them directly, making substitution easy), or dedicated mocking libraries (`sinon`, `nock` for HTTP mocking) to simulate responses without hitting real external systems — keeping tests fast, deterministic, and independent of network/service availability.

---

## 15. Performance & Debugging

### Q80. How do you profile a Node.js application for performance issues?
- **Node's built-in profiler** (`node --prof app.js`, then `node --prof-process` to analyze).
- **Chrome DevTools** via `node --inspect` (attach via `chrome://inspect`), enabling breakpoints, CPU profiling, and heap snapshots.
- **`clinic.js`** — a suite of tools (`clinic doctor`, `clinic flame`, `clinic bubbleprof`) for diagnosing event loop delays, CPU bottlenecks, and I/O issues.
- APM (Application Performance Monitoring) tools (Datadog, New Relic) for production-level tracing and metrics.

### Q81. What is a memory leak in Node.js, and what commonly causes one?
A memory leak occurs when memory that's no longer needed isn't released, causing memory usage to grow indefinitely (eventually crashing the process). Common causes: global variables/caches that grow unbounded, forgotten `setInterval`/event listeners that are never cleared (keeping closures — and everything they reference — alive), and accumulating references in long-lived data structures (like an array that's only ever pushed to).

### Q82. How do you detect and diagnose a memory leak?
Take **heap snapshots** at different points in time (via Chrome DevTools or `node --inspect`) and compare them to see which object types are growing unexpectedly; monitor `process.memoryUsage()` over time; use tools like `clinic heapprofiler` or `memwatch-next` for automated leak detection during load testing.

### Q83. What is the difference between the V8 heap and the Node.js process's overall memory usage?
The **V8 heap** is where JavaScript objects live and is subject to V8's garbage collector and default size limits (historically capped around ~1.5–4GB depending on version/settings, though modern versions largely scale with available system memory and can be tuned via `--max-old-space-size`). Node's **total memory usage** also includes memory outside the V8 heap: `Buffer`s (allocated outside the V8 heap), native C++ addon memory, and the stack — so `process.memoryUsage()`'s `rss` (resident set size) reflects the full picture, not just `heapUsed`.

### Q84. What is `--inspect`, and how do you debug a running Node.js process?
`node --inspect app.js` (or `--inspect-brk` to pause on the first line) starts the app with the V8 Inspector protocol enabled, allowing you to attach Chrome DevTools (`chrome://inspect`) or an IDE debugger to set breakpoints, step through code, inspect variables, and profile performance on a live process.

### Q85. What strategies improve Node.js application performance?
- Use async/non-blocking APIs; avoid sync I/O in request paths.
- Cache expensive computations/DB queries (in-memory or Redis).
- Use connection pooling for databases.
- Compress responses (`gzip`/`brotli` via middleware like `compression`).
- Use clustering/horizontal scaling to utilize multiple cores/machines.
- Paginate and limit large query results rather than loading everything into memory.
- Use streams for large file/data transfers instead of buffering entirely in memory.
- Profile before optimizing — avoid premature/guessed optimization.

---

## 16. Microservices & Architecture

### Q86. What is the difference between a monolithic and microservices architecture, in the Node.js context?
A **monolith** is a single deployable application containing all functionality (routing, business logic, data access) in one codebase/process. **Microservices** split functionality into independently deployable services (often communicating via HTTP/gRPC/message queues), each potentially owning its own database — offering independent scaling and deployment at the cost of added operational complexity (service discovery, distributed transactions, network reliability).

### Q87. How do Node.js services typically communicate with each other?
- **Synchronous**: REST (HTTP/JSON) or gRPC (binary, contract-based via Protocol Buffers, generally faster/more strongly typed).
- **Asynchronous**: message queues/brokers (RabbitMQ, Kafka, Redis Streams, AWS SQS) — decoupling services in time, improving resilience to individual service downtime, and supporting event-driven architectures.

### Q88. What is an API Gateway, and why is it used in a microservices setup?
A single entry point that routes incoming requests to the appropriate backend microservice, often also handling cross-cutting concerns centrally: authentication, rate limiting, request/response transformation, and aggregating results from multiple services — simplifying what clients need to know about the underlying service topology.

### Q89. What is the 12-Factor App methodology, and how does it apply to Node.js apps?
A set of best practices for building cloud-native, scalable applications: e.g., store config in environment variables (not code), treat backing services (databases, queues) as attachable resources, keep dev/prod environments as similar as possible, log to stdout rather than managing log files internally, and design processes to be stateless and disposable (easy to start/stop, supporting horizontal scaling). Widely applicable to Node.js services deployed in containerized/cloud environments.

---

## 17. Deployment, Environments & DevOps

### Q90. What is a process manager, and why use one (e.g., PM2)?
A tool like **PM2** keeps a Node.js application running in production: automatically restarting it on crashes, supporting zero-downtime reloads, built-in clustering (spawning multiple instances across CPU cores), log management, and monitoring — handling operational concerns that raw `node app.js` doesn't address.

### Q91. How do you containerize a Node.js application with Docker?
Write a `Dockerfile` that specifies a Node base image, copies `package.json`/lockfile first (to leverage Docker's layer caching for dependency installs), runs `npm ci`, copies the rest of the source, and defines the start command:
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```
Best practices: use a slim base image (`alpine`), avoid running as root, use multi-stage builds to keep the final image small (excluding build tools/devDependencies).

### Q92. How do you manage different configurations across environments (development, staging, production)?
Via environment variables (`process.env.NODE_ENV` and others), often loaded from environment-specific `.env` files locally or injected directly by the deployment platform/orchestrator in staging/production — keeping a single codebase that behaves correctly across environments without hardcoded, environment-specific values.

### Q93. What is `NODE_ENV`, and what does it affect?
A conventional environment variable indicating the runtime environment (`development`, `production`, `test`). Many libraries (including Express) change behavior based on it — e.g., Express enables view caching and less verbose error pages when `NODE_ENV=production`. It's a convention, not a Node.js core requirement, but widely relied upon across the ecosystem.

### Q94. How do you implement structured logging in a production Node.js app?
Use a logging library like **Pino** or **Winston** that outputs structured (typically JSON) logs with consistent fields (timestamp, level, message, request ID), rather than plain `console.log` — making logs easier to search/filter/aggregate in centralized logging systems (ELK stack, Datadog, CloudWatch), and enabling log correlation across a distributed system via trace/request IDs.

### Q95. What is a health check endpoint, and why is it needed?
A lightweight endpoint (e.g., `GET /health`) that returns the service's status (and optionally checks downstream dependencies like DB connectivity), used by load balancers/orchestrators (Kubernetes liveness/readiness probes) to determine whether an instance should receive traffic or needs to be restarted.

---

## 18. TypeScript with Node.js

### Q96. Why use TypeScript with Node.js?
TypeScript adds static typing on top of JavaScript, catching type errors at compile time, improving IDE autocompletion/refactoring support, and making large codebases easier to maintain and reason about — particularly valuable in backend services where API contracts and data shapes benefit from explicit typing.

### Q97. How do you set up a basic Node.js + TypeScript project?
Install `typescript` and `@types/node` as dev dependencies, create a `tsconfig.json` (specifying `target`, `module`, `outDir`, `rootDir`, `strict` mode, etc.), write code in `.ts` files, and compile with `tsc` (or run directly in development via `ts-node` or `tsx` for faster iteration without a separate compile step).

### Q98. What are `.d.ts` files, and why are `@types/*` packages needed?
`.d.ts` (declaration) files describe the shape/types of JavaScript code without containing implementation, allowing TypeScript to type-check code that uses plain JS libraries. Many popular packages don't ship their own types, so the community-maintained `@types/*` packages (from **DefinitelyTyped**) provide these declarations separately (e.g., `@types/express`).

### Q99. How do you type Express request/response objects and middleware?
```ts
import { Request, Response, NextFunction } from 'express';

app.get('/users/:id', (req: Request<{ id: string }>, res: Response) => {
  res.json({ id: req.params.id });
});

function authMiddleware(req: Request, res: Response, next: NextFunction) {
  // ...
  next();
}
```
Generic type parameters on `Request<Params, ResBody, ReqBody, Query>` allow strongly typing route params, request body, and query strings.

---

## 19. Miscellaneous & Best Practices

### Q100. What is the difference between `spawn`, `exec`, and running a shell command directly — security implications?
`exec()` runs the command through a shell, meaning shell metacharacters in unsanitized input (e.g., `;`, `&&`, backticks) could allow **command injection** if user input is interpolated into the command string. `spawn()`/`execFile()` (without `shell: true`) pass arguments as an array directly to the executable, avoiding shell interpretation and thus this class of vulnerability — preferred whenever the command doesn't need actual shell features (pipes, globbing).

### Q101. What is the difference between `==` behavior nuances aside — how does Node.js handle uncaught synchronous errors at the top level vs inside a request handler?
An uncaught synchronous error at the top level of a script crashes the process immediately. Inside an Express route handler, a synchronous `throw` is actually caught internally by Express and forwarded to error-handling middleware (for versions 5+ this also applies more consistently to async handlers); but for other frameworks or raw `http` servers, an uncaught error inside a request callback can crash the entire server (affecting all in-flight requests), which is why try/catch or a global error handler is essential in raw setups.

### Q102. How do environment-specific `require`/`import` caching issues manifest, and how do you avoid stale module state bugs?
Since `require()` caches modules by resolved file path, two different **relative** paths that resolve to the same absolute file share the same cached instance/state (generally desired for singletons like DB connections), but this can surprise developers who expect a "fresh" module on each import — especially in tests, where module-level state (like an in-memory cache) can leak between test cases unless explicitly reset or the module cache is cleared (`delete require.cache[...]`, or using isolated test module registries in modern test runners).

### Q103. What is the difference between `Object.freeze()` at the module level and true immutability, and why might it matter in a Node service?
`Object.freeze()` provides shallow immutability (nested objects can still be mutated) and is sometimes used to protect shared config objects loaded once at startup from being accidentally mutated by consuming code elsewhere in a large codebase — useful defensively, but not a substitute for deep immutability if nested structures also need protecting (would require recursive freezing or an immutability library).

### Q104. What are some general Node.js backend best practices?
- Validate and sanitize all external input (request bodies, query params, headers).
- Keep configuration in environment variables, not hardcoded.
- Use structured logging with correlation/request IDs for traceability.
- Handle errors centrally (error-handling middleware) rather than scattering `try/catch` inconsistently.
- Avoid blocking the event loop; prefer async APIs and offload CPU-heavy work.
- Set appropriate timeouts on external calls (HTTP requests, DB queries) to avoid hanging indefinitely.
- Keep dependencies updated and regularly audited for vulnerabilities.
- Design for graceful shutdown and horizontal scalability from the start.
- Write tests at multiple levels (unit, integration) and include them in CI.

---

*This guide covers the core Node.js interview surface area. For role-specific deep dives (e.g., GraphQL server implementation with Apollo Server, gRPC service design, advanced Kubernetes deployment patterns, or specific database internals), further topic-specific preparation is recommended.*
