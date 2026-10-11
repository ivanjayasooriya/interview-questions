# Express.js Interview Questions & Answers

A comprehensive guide covering Express.js fundamentals through advanced concepts, middleware, routing, error handling, security, performance, testing, and the broader ecosystem.

---

## Table of Contents

1. [Express Fundamentals](#1-express-fundamentals)
2. [Application Setup & Configuration](#2-application-setup--configuration)
3. [Routing](#3-routing)
4. [Middleware](#4-middleware)
5. [Request & Response Objects](#5-request--response-objects)
6. [Templating & Static Files](#6-templating--static-files)
7. [Error Handling](#7-error-handling)
8. [Body Parsing & File Uploads](#8-body-parsing--file-uploads)
9. [Authentication & Sessions](#9-authentication--sessions)
10. [Security](#10-security)
11. [Validation](#11-validation)
12. [Database Integration](#12-database-integration)
13. [Async Patterns in Express](#13-async-patterns-in-express)
14. [Performance & Scaling](#14-performance--scaling)
15. [Testing](#15-testing)
16. [Express 5 & Version Differences](#16-express-5--version-differences)
17. [Architecture & Project Structure](#17-architecture--project-structure)
18. [TypeScript with Express](#18-typescript-with-express)
19. [Miscellaneous & Best Practices](#19-miscellaneous--best-practices)

---

## 1. Express Fundamentals

### Q1. What is Express.js?
Express is a minimal, unopinionated, and flexible web application framework for Node.js, built on top of Node's core `http` module. It provides a thin layer of fundamental web application features — routing, middleware, request/response helpers — without imposing a rigid structure, making it the de facto standard for building REST APIs and web servers in Node.js.

### Q2. Why use Express instead of Node's raw `http` module?
Node's `http` module requires manually parsing URLs, matching routes, handling different HTTP methods, parsing request bodies, and setting response headers/status codes by hand. Express abstracts all of this into a clean, declarative API (`app.get()`, `app.post()`, middleware chains), significantly reducing boilerplate while remaining a thin layer over `http` — you can still drop down to the underlying `req`/`res` objects at any time, since Express extends rather than replaces them.

### Q3. What are the key features of Express?
- **Routing** — a robust system for mapping URLs and HTTP methods to handler functions, including route parameters and pattern matching.
- **Middleware** — a pipeline of functions that process requests/responses sequentially, enabling modular cross-cutting logic (auth, logging, parsing).
- **Templating engine support** — integrates with view engines (EJS, Pug, Handlebars) for server-rendered HTML.
- **Static file serving** — built-in via `express.static()`.
- **Extensibility** — a huge ecosystem of middleware packages, and it forms the foundation for many higher-level frameworks (NestJS can use it under the hood, for example).

### Q4. How do you install and create a basic Express application?
```bash
npm install express
```
```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

### Q5. What is the relationship between Express and Node's `http` module under the hood?
`app.listen()` internally creates a standard Node `http.Server` instance and passes the Express `app` function itself as the request listener callback (since `app` is technically a function with additional properties/methods attached). Every incoming request still flows through Node's native HTTP parsing — Express just organizes how you handle what comes after.

---

## 2. Application Setup & Configuration

### Q6. What is `app.set()` / `app.get()` (for settings, not routes) used for?
`app.set(name, value)` configures application-level settings (distinct from the `app.get(path, handler)` route method — same method name, different usage based on argument count/type). Common settings: `app.set('view engine', 'ejs')`, `app.set('trust proxy', true)`, `app.set('port', 3000)`. `app.get(name)` (with one argument) retrieves a previously set setting.

### Q7. What does `trust proxy` do, and when is it needed?
When an app runs behind a reverse proxy or load balancer (Nginx, AWS ELB, Heroku), the client's real IP and protocol (http/https) are typically forwarded via headers like `X-Forwarded-For` and `X-Forwarded-Proto` rather than being directly visible to Express. Setting `app.set('trust proxy', true)` (or a specific number of hops / IP range) tells Express to trust and parse these headers, so `req.ip` and `req.secure` reflect the real client rather than the proxy.

### Q8. What is the difference between `app.listen()` and creating an `http.Server` manually?
`app.listen()` is a convenience method that internally creates an `http.Server` and calls `.listen()` on it, returning the server instance. For advanced use cases (e.g., attaching a WebSocket server via `socket.io`, or supporting HTTPS/HTTP2), you create the server explicitly and pass `app` in:
```js
const http = require('http');
const server = http.createServer(app);
server.listen(3000);
```

### Q9. How do you run an Express app over HTTPS?
```js
const https = require('https');
const fs = require('fs');
const options = {
  key: fs.readFileSync('key.pem'),
  cert: fs.readFileSync('cert.pem'),
};
https.createServer(options, app).listen(443);
```
In production, TLS termination is often handled by a reverse proxy/load balancer instead, with Express only serving plain HTTP internally.

### Q10. How do you structure environment-specific configuration in Express?
Typically via environment variables (loaded with `dotenv` locally), read through `process.env`, with `NODE_ENV` controlling environment-dependent behavior. Express itself uses `NODE_ENV` internally — e.g., view caching and terser error pages are enabled automatically when `NODE_ENV=production`.

---

## 3. Routing

### Q11. What is routing in Express?
Routing defines how an application responds to client requests to specific endpoints (a URI path and an HTTP method). Each route can have one or more handler functions, executed when the route matches.
```js
app.get('/users', (req, res) => { /* ... */ });
app.post('/users', (req, res) => { /* ... */ });
```

### Q12. What HTTP methods does Express support for routing?
All standard HTTP verbs are supported as `app` methods: `get`, `post`, `put`, `patch`, `delete`, `head`, `options`, plus `app.all()` (matches any method) and `app.use()` (matches any method, primarily intended for middleware but also matches as a path prefix).

### Q13. What are route parameters, and how do you access them?
Named URL segments prefixed with `:`, captured into `req.params`.
```js
app.get('/users/:id', (req, res) => {
  res.send(`User ID: ${req.params.id}`);
});
```
Multiple params are supported: `/users/:userId/posts/:postId`.

### Q14. How do query strings differ from route parameters?
Query strings (`?key=value`) are appended after the path and accessed via `req.query`, used for optional/filtering parameters (`/search?q=node&page=2`), whereas route parameters (`req.params`) are part of the URL's path structure itself and typically represent required resource identifiers.

### Q15. What is `express.Router()`, and why use it?
A mini, mountable instance of Express's routing system that lets you define modular, isolated sets of routes (often per-resource, e.g., `usersRouter`, `postsRouter`) in separate files, then mount them onto the main app — improving code organization and separation of concerns as an app grows.
```js
// routes/users.js
const router = express.Router();
router.get('/', getAllUsers);
router.get('/:id', getUserById);
module.exports = router;

// app.js
app.use('/users', require('./routes/users'));
```

### Q16. What are route path patterns, and what wildcard/regex support does Express provide?
Express route paths can include string patterns with wildcards (`*`) and can also be regular expressions directly for advanced matching. (Note: the exact wildcard/pattern-matching syntax changed between Express 4, which used `path-to-regexp` v0-era syntax supporting things like optional `?` and `*` wildcards more loosely, and Express 5, which uses a newer `path-to-regexp` version with stricter, more explicit syntax — see [Q90](#q90-what-are-the-breaking-changes-in-express-5-vs-express-4).)

### Q17. How do you chain multiple handlers for the same route path?
`app.route(path)` lets you chain method handlers for a single path fluently:
```js
app.route('/users/:id')
  .get(getUser)
  .put(updateUser)
  .delete(deleteUser);
```

### Q18. What is the significance of route/middleware **order** in Express?
Express matches and executes routes/middleware in the **order they're registered** — the first matching route/middleware for a request handles it (unless it calls `next()` to pass control onward). This means more specific routes should generally be registered before more general ones, and middleware that must run for all requests (like body parsers or auth checks) should be registered before the routes that depend on them.

### Q19. How do you handle a 404 (route not found) in Express?
By adding a catch-all middleware **after all other routes** (since nothing else matched):
```js
app.use((req, res) => {
  res.status(404).json({ error: 'Not Found' });
});
```

### Q20. How do you pass control from one route/middleware to the next matching route (not just the next middleware)?
Calling `next('route')` skips the remaining handlers for the *current* route and moves on to the next matching route definition — different from plain `next()`, which just moves to the next handler *within* the current route/middleware stack.

---

## 4. Middleware

### Q21. What is middleware in Express?
A function with the signature `(req, res, next)` that has access to the request object, response object, and the `next` function in the application's request-response cycle. Middleware can execute code, modify `req`/`res`, end the request-response cycle, or call `next()` to pass control to the subsequent middleware/route handler.

### Q22. What are the different types of middleware in Express?
1. **Application-level middleware** — bound to the `app` instance via `app.use()`/`app.METHOD()`.
2. **Router-level middleware** — bound to an `express.Router()` instance, same API as application-level but scoped to that router.
3. **Error-handling middleware** — defined with **four** arguments `(err, req, res, next)`.
4. **Built-in middleware** — shipped with Express, e.g., `express.json()`, `express.static()`, `express.urlencoded()`.
5. **Third-party middleware** — installed via npm, e.g., `cors`, `morgan`, `helmet`, `cookie-parser`.

### Q23. What happens if a middleware function doesn't call `next()` or send a response?
The request **hangs indefinitely** — the client's connection stays open with no response ever sent, until it eventually times out. Every middleware must either call `next()` (to continue the chain) or end the response (`res.send()`, `res.json()`, `res.end()`, etc.).

### Q24. What is the difference between `app.use()` and `app.get()`/other method-specific handlers?
`app.use(path, handler)` matches **any HTTP method** and treats `path` as a **prefix** (matching the given path and everything under it, e.g., `/api` matches `/api`, `/api/users`, `/api/users/1`). `app.get(path, handler)` (and other method-specific functions) match **only that HTTP method** and match the path more precisely (exact match unless parameters/wildcards are used).

### Q25. How do you apply middleware to a specific route only, versus globally?
```js
// Globally, for every request
app.use(loggerMiddleware);

// Only for a specific route
app.get('/admin', authMiddleware, adminHandler);

// Only for a specific path prefix
app.use('/api', apiOnlyMiddleware);
```

### Q26. How do you chain multiple middleware functions for a single route?
Pass them as successive arguments (or an array) — each calls `next()` to proceed to the following one:
```js
app.get('/profile', authenticate, authorize('user'), getProfile);
```

### Q27. What is the built-in `express.json()` middleware, and why is it needed?
Parses incoming requests with a `Content-Type: application/json` body, making the parsed object available on `req.body`. Without it (or an equivalent body parser), `req.body` is `undefined` for JSON payloads. It replaced the older, separate `body-parser` package's `bodyParser.json()` (now built directly into Express since v4.16).

### Q28. What is `express.urlencoded()`, and when do you need `extended: true`?
Parses `application/x-www-form-urlencoded` bodies (traditional HTML form submissions) into `req.body`. The `extended` option determines which underlying library parses the data: `extended: false` uses Node's built-in `querystring` library (supports only simple string/array values), while `extended: true` uses the `qs` library, supporting rich, nested objects and arrays in form data.

### Q29. How do you write a custom logging middleware?
```js
function requestLogger(req, res, next) {
  console.log(`${new Date().toISOString()} ${req.method} ${req.url}`);
  next();
}
app.use(requestLogger);
```

### Q30. What is the purpose of third-party middleware like `morgan`, `cors`, and `cookie-parser`?
- **`morgan`** — HTTP request logging middleware with configurable output formats (combined, dev, tiny, etc.).
- **`cors`** — configures Cross-Origin Resource Sharing headers, controlling which origins can make requests to the API from a browser.
- **`cookie-parser`** — parses the `Cookie` header and populates `req.cookies` (and `req.signedCookies` for signed cookies), since Express doesn't parse cookies by default.

### Q31. Can middleware be asynchronous? How do you handle errors in async middleware?
Yes — middleware can be an `async` function. However, in Express 4, if an async middleware throws or its returned promise rejects, Express does **not** automatically catch it and forward it to error-handling middleware — you must catch it manually and call `next(err)`, or use a wrapper utility (see [Q76](#q76-how-do-you-avoid-repetitive-trycatch-blocks-in-every-async-route-handler)). Express 5 fixes this by automatically forwarding rejected promises to error handlers.

### Q32. What is middleware composition, and how does it enable separation of concerns?
Because middleware functions are just composable, chainable units, complex request-handling logic (authentication, validation, logging, rate limiting, response shaping) can each live in its own small, independently testable middleware function, combined declaratively per-route rather than written as one large, monolithic handler.

---

## 5. Request & Response Objects

### Q33. What are the most commonly used properties on the `req` (Request) object?
- `req.params` — route parameters.
- `req.query` — parsed query string.
- `req.body` — parsed request body (requires a body-parsing middleware).
- `req.headers` — request headers.
- `req.method` — HTTP method.
- `req.path` / `req.originalUrl` — URL path (`originalUrl` preserves the full original URL even after internal rewrites, e.g., inside a mounted router).
- `req.cookies` — parsed cookies (requires `cookie-parser`).
- `req.ip` — client IP address.

### Q34. What are the most commonly used methods on the `res` (Response) object?
- `res.send(body)` — sends a response of various types (string, object/array as JSON, Buffer).
- `res.json(obj)` — sends a JSON response explicitly, setting the appropriate `Content-Type`.
- `res.status(code)` — sets the HTTP status code (chainable, e.g., `res.status(404).json(...)`).
- `res.redirect(url)` — issues an HTTP redirect.
- `res.sendFile(path)` — streams a file as the response.
- `res.set(field, value)` / `res.header()` — sets response headers.
- `res.cookie(name, value, options)` — sets a cookie.
- `res.end()` — ends the response without any data.

### Q35. What is the difference between `res.send()` and `res.json()`?
`res.json()` always serializes the argument to a JSON string and sets `Content-Type: application/json`. `res.send()` is more general-purpose — it can accept a string, Buffer, or object/array (in which case it internally delegates to `res.json()` anyway), automatically inferring the appropriate `Content-Type`. For sending JSON explicitly and predictably, `res.json()` is preferred/more semantically clear.

### Q36. What is the difference between `req.path`, `req.originalUrl`, and `req.baseUrl`?
- `req.path` — just the URL's path portion, without the query string (reflects the *current* router's view of the path, which can change as the request passes through nested routers).
- `req.originalUrl` — the full original path + query string as received, unaffected by any internal router mounting/rewriting.
- `req.baseUrl` — the path prefix at which a router was mounted (e.g., `/api` if a router was mounted via `app.use('/api', router)`).

### Q37. How do you set custom response headers?
```js
res.set('X-Custom-Header', 'value');
// or
res.header('X-Custom-Header', 'value');
```

### Q38. How do you handle content negotiation in Express?
`res.format()` lets you respond differently based on the client's `Accept` header:
```js
res.format({
  'text/plain': () => res.send('Hello'),
  'application/json': () => res.json({ message: 'Hello' }),
  default: () => res.status(406).send('Not Acceptable'),
});
```

### Q39. What is `req.app`?
A reference back to the Express application instance that's handling the request — useful inside route handlers/middleware defined in separate modules that need access to app-level settings (`req.app.get('someolat setting')`) or other app-level resources.

---

## 6. Templating & Static Files

### Q40. How do you serve static files (images, CSS, client-side JS) in Express?
Using the built-in `express.static()` middleware, pointing to a directory whose contents are served directly:
```js
app.use(express.static('public'));
// or mounted under a path prefix:
app.use('/static', express.static('public'));
```

### Q41. What is a view/templating engine, and how do you configure one in Express?
A templating engine (EJS, Pug, Handlebars) lets you generate dynamic HTML on the server by embedding variables/logic into template files, rendered via `res.render()`.
```js
app.set('view engine', 'ejs');
app.set('views', './views'); // optional, defaults to ./views

app.get('/', (req, res) => {
  res.render('index', { title: 'Home', user });
});
```

### Q42. What is the difference between server-side rendering (via a view engine) and building an API consumed by a separate frontend (SPA)?
Server-side rendering with a view engine generates complete HTML pages on each request, which the browser directly displays — simpler for content-heavy sites and good for SEO without extra tooling. Building a pure JSON API (consumed by a React/Vue/etc. SPA) separates concerns entirely, letting frontend and backend be developed, deployed, and scaled independently, at the cost of needing a separate client-side rendering/build pipeline.

---

## 7. Error Handling

### Q43. How does Express identify a function as error-handling middleware?
By its **arity** — it must have exactly **four** parameters `(err, req, res, next)`. Express inspects the function's declared parameter count to distinguish it from regular middleware, so all four parameters must be present even if `next` isn't used inside the function body.

### Q44. Where should error-handling middleware be registered?
**After** all other `app.use()` and route definitions, since Express processes middleware/routes in order and error handlers only get invoked when `next(err)` is called or (in Express 5) a promise rejects.
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.statusCode || 500).json({ error: err.message });
});
```

### Q45. How do you trigger the error-handling middleware from within a route handler?
By calling `next(err)` with an `Error` object (or any truthy value) — Express then skips all remaining regular middleware/routes and jumps directly to the nearest error-handling middleware.
```js
app.get('/users/:id', (req, res, next) => {
  const user = findUser(req.params.id);
  if (!user) return next(new Error('User not found'));
  res.json(user);
});
```

### Q46. Can you have multiple error-handling middleware functions?
Yes — you can chain multiple, calling `next(err)` from within one to pass to the next (e.g., one for logging, another for formatting the client response), similar to regular middleware chaining.

### Q47. What happens to synchronous errors thrown directly inside a route handler (Express 4)?
Express 4 automatically catches synchronous errors thrown inside route handlers and forwards them to error-handling middleware — no manual `try/catch` + `next(err)` needed for purely synchronous code. This automatic catching does **not** extend to asynchronous errors (rejected promises, errors in callbacks) in Express 4.

### Q48. How do you build a consistent, centralized error response format across an API?
Define a custom `AppError` class carrying a `statusCode` and operational flag, throw/pass it via `next()` from anywhere in the app, and have a single, final error-handling middleware format the response consistently (status code, error message, optionally an error code/type for client-side handling), rather than each route formatting its own error responses ad hoc.

### Q49. How should you handle "not found" (404) errors distinctly from other server errors?
Typically via a dedicated catch-all middleware placed after all routes but before the general error handler, which constructs a 404 error and passes it along:
```js
app.use((req, res, next) => {
  next(new AppError('Not Found', 404));
});
app.use(errorHandler); // formats and sends the response
```

### Q50. Should you expose internal error details (stack traces, DB errors) to API clients?
No, not in production — internal details can leak sensitive implementation info and aid attackers. Log full details server-side, but return a generic, sanitized message to the client (distinguishing "operational" errors meant to be shown to users, like validation failures, from unexpected programmer/system errors that should return a generic "Internal Server Error").

---

## 8. Body Parsing & File Uploads

### Q51. What body types can Express parse out of the box (v4.16+)?
`express.json()` (JSON payloads), `express.urlencoded()` (form-encoded payloads), and `express.raw()`/`express.text()` (raw Buffer/plain text bodies) — all built in, no longer requiring the separate `body-parser` package.

### Q52. How do you limit the size of accepted request bodies, and why is it important?
```js
app.use(express.json({ limit: '10kb' }));
```
Without a limit, an attacker (or a bug) could send an enormous payload, consuming excessive memory/bandwidth and potentially causing a denial-of-service — so setting a reasonable limit based on expected payload sizes is a basic hardening measure.

### Q53. How do you handle file uploads in Express?
Express itself doesn't parse `multipart/form-data` (used for file uploads) — a dedicated middleware like **Multer** is used:
```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('avatar'), (req, res) => {
  res.json({ file: req.file });
});
```
`upload.single(fieldName)` handles one file; `upload.array(fieldName, maxCount)` handles multiple files under the same field name; `upload.fields([...])` handles multiple named fields.

### Q54. How do you validate file uploads (type, size) with Multer?
```js
const upload = multer({
  dest: 'uploads/',
  limits: { fileSize: 5 * 1024 * 1024 }, // 5MB
  fileFilter: (req, file, cb) => {
    if (!file.mimetype.startsWith('image/')) {
      return cb(new Error('Only image files are allowed'));
    }
    cb(null, true);
  },
});
```

### Q55. What are the trade-offs of storing uploaded files on local disk vs. cloud storage (S3, etc.)?
Local disk storage is simple for development/single-server setups but doesn't scale across multiple server instances (each would have its own separate disk) and is lost on container restarts in many deployment models. Cloud object storage (S3, GCS) decouples file storage from the application server, supports horizontal scaling, and typically offers built-in durability/CDN integration — the standard choice for production.

---

## 9. Authentication & Sessions

### Q56. How do you implement session-based authentication in Express?
Using `express-session` middleware, which creates a session store (in-memory by default — **not suitable for production**, since it leaks memory and doesn't work across multiple server instances) and issues a signed session-ID cookie to the client:
```js
const session = require('express-session');
app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: false,
  cookie: { secure: true, httpOnly: true, maxAge: 3600000 },
}));

app.post('/login', (req, res) => {
  req.session.userId = user.id; // store data in the session
  res.send('Logged in');
});
```
For production, a persistent session store (Redis via `connect-redis`, or a database-backed store) is required so sessions survive restarts and work correctly across multiple app instances.

### Q57. How do you implement JWT-based authentication in Express?
```js
const jwt = require('jsonwebtoken');

app.post('/login', (req, res) => {
  // ...verify credentials...
  const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET, { expiresIn: '1h' });
  res.json({ token });
});

function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'No token provided' });
  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch {
    res.status(401).json({ error: 'Invalid token' });
  }
}

app.get('/profile', authenticate, (req, res) => res.json(req.user));
```

### Q58. What is Passport.js, and why is it commonly used with Express?
Passport is a flexible authentication middleware for Node/Express supporting numerous "strategies" (local username/password, OAuth providers like Google/GitHub/Facebook, JWT) behind a consistent API, abstracting the boilerplate of implementing each authentication method from scratch. It integrates via `passport.initialize()`/`passport.authenticate()` middleware and delegates the actual credential verification logic to whichever strategy is configured.

### Q59. How do you implement role-based access control (RBAC) middleware?
```js
function authorize(...allowedRoles) {
  return (req, res, next) => {
    if (!allowedRoles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}

app.delete('/users/:id', authenticate, authorize('admin'), deleteUser);
```

### Q60. What is the difference between authentication and authorization in this context?
**Authentication** verifies *who* the requester is (login, validating credentials/tokens). **Authorization** determines *what* an authenticated requester is allowed to do (role/permission checks). They're typically implemented as separate, sequential middleware layers.

---

## 10. Security

### Q61. What is `helmet`, and what does it protect against?
A middleware package that sets a collection of security-related HTTP response headers by default (`Content-Security-Policy`, `X-Content-Type-Options`, `Strict-Transport-Security`, `X-Frame-Options`, and others), mitigating common attack vectors like clickjacking, MIME-sniffing attacks, and enforcing HTTPS. Applied simply via `app.use(helmet())`, with individual protections configurable.

### Q62. How do you protect an Express API against CSRF (Cross-Site Request Forgery)?
For traditional session-cookie-based apps, use a CSRF-token pattern (e.g., via the `csrf-csrf`/`csurf`-style approach — note the original `csurf` package is deprecated, so a maintained alternative should be used), embedding a token in forms/requests that's validated server-side. For token-based (JWT in `Authorization` header) APIs consumed by SPAs, CSRF risk is inherently lower since the browser doesn't automatically attach custom headers cross-origin the way it does cookies — but cookie-based JWT storage still needs CSRF protection plus `SameSite` cookie attributes.

### Q63. How do you prevent NoSQL/SQL injection in an Express app?
Always use parameterized queries or an ORM/ODM's built-in query builders (never string-concatenate raw user input into a query). For MongoDB specifically, also guard against **query operator injection** (e.g., a user submitting `{ "$gt": "" }` as a "password" field to bypass a naive comparison) by validating input types/shapes strictly and using a sanitization middleware like `express-mongo-sanitize`.

### Q64. How do you implement rate limiting in Express?
```js
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per window
});
app.use(limiter);
```
For multi-instance deployments, a shared store (Redis) should back the rate limiter so limits are enforced consistently across instances rather than per-process.

### Q65. How do you configure CORS properly and securely?
```js
const cors = require('cors');
app.use(cors({
  origin: ['https://myapp.com'], // avoid '*' when credentials are involved
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
}));
```
Using a wildcard `origin: '*'` alongside `credentials: true` is invalid/insecure and rejected by browsers — an explicit allowlist of trusted origins should be used, especially when cookies/auth headers are involved.

### Q66. What security risks does exposing detailed stack traces in production error responses create?
Stack traces can reveal internal file paths, library versions, and application structure — information that helps attackers craft targeted exploits. Production error handlers should log full details server-side but return only a generic message to the client (see Q50).

### Q67. What is prototype pollution, and how is it relevant to Express apps?
An attack where crafted input (e.g., a JSON body with a key like `__proto__` or `constructor.prototype`) manipulates `Object.prototype` itself if the app naively merges/assigns user input into objects (common in some older body-parsing/merge utility bugs), potentially affecting the entire application's behavior. Mitigations: keep dependencies updated (this class of vulnerability has been patched in various libraries over time), validate/whitelist expected input shape rather than blindly merging arbitrary objects, and consider `Object.freeze(Object.prototype)` as a defense-in-depth measure in sensitive contexts.

### Q68. What are best practices for securely handling cookies in Express?
Set `httpOnly: true` (prevents JavaScript access, mitigating XSS-based cookie theft), `secure: true` (cookie only sent over HTTPS), and an appropriate `sameSite` value (`'strict'` or `'lax'`, mitigating CSRF) on session/auth cookies. Use `cookie-parser`'s signed cookie support (`res.cookie(name, value, { signed: true })`) with a strong secret to detect tampering.

### Q69. Why should you disable the `X-Powered-By` header, and how?
By default, Express sends `X-Powered-By: Express`, revealing the backend framework to any client — minor information disclosure that can help attackers target known Express vulnerabilities. Disable it via `app.disable('x-powered-by')` (or automatically handled by `helmet()`).

---

## 11. Validation

### Q70. Why is input validation important in Express APIs, and where should it happen?
Untrusted client input (body, query, params, headers) should never be trusted to match expected shapes/types — validating it at the boundary (before business logic runs) prevents malformed data from causing crashes, logic errors, or security vulnerabilities (injection, prototype pollution) further downstream.

### Q71. What libraries are commonly used for request validation in Express?
- **`express-validator`** — middleware built specifically for Express, providing chainable validation/sanitization rules applied directly to `req` fields.
- **`Joi`** — a general-purpose schema validation library, commonly used to validate `req.body`/`req.query` against a defined schema.
- **`Zod`** — a TypeScript-first schema validation library, increasingly popular for its strong type inference alongside runtime validation.

### Q72. Example: validating a request body with `express-validator`.
```js
const { body, validationResult } = require('express-validator');

app.post('/users',
  body('email').isEmail(),
  body('password').isLength({ min: 8 }),
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  },
  createUser
);
```

### Q73. Example: validating a request body with a Zod/Joi-style schema, and building it as reusable middleware.
```js
const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

function validate(schema) {
  return (req, res, next) => {
    const result = schema.safeParse(req.body);
    if (!result.success) {
      return res.status(400).json({ errors: result.error.issues });
    }
    req.body = result.data; // parsed/coerced data
    next();
  };
}

app.post('/users', validate(schema), createUser);
```

---

## 12. Database Integration

### Q74. How do you typically structure database access in an Express application?
Common layered approach: **routes** (define endpoints, delegate to controllers) → **controllers** (handle req/res, call services) → **services/models** (business logic and actual database queries via an ORM/driver) — separating HTTP concerns from data-access and business logic, improving testability and maintainability.

### Q75. How do you manage database connections in an Express app's lifecycle?
Establish the database connection (or pool) once at application startup, before calling `app.listen()`, and reuse that connection/pool across all requests rather than opening a new connection per request. On graceful shutdown (`SIGTERM`), close the connection/pool cleanly after in-flight requests finish.
```js
async function start() {
  await db.connect();
  app.listen(3000);
}
start();
```

---

## 13. Async Patterns in Express

### Q76. How do you avoid repetitive try/catch blocks in every async route handler?
Wrap async handlers in a utility function that catches rejected promises and forwards them to `next()`:
```js
const asyncHandler = (fn) => (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next);

app.get('/users/:id', asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);
  if (!user) return res.status(404).json({ error: 'Not found' });
  res.json(user);
}));
```
(Popular pre-built utilities for this include the `express-async-handler` package. Express 5 removes the need for this pattern by natively catching rejected promises — see Q90.)

### Q77. Why does an unhandled rejected promise inside an Express 4 route handler not automatically trigger the error middleware?
Because Express 4's routing layer wraps and catches only **synchronous** throws within a handler; it has no visibility into promises returned by an `async` function unless you explicitly `.catch()` and call `next(err)` — this was a well-known Express 4 gotcha requiring wrapper utilities or manual handling, addressed directly in Express 5.

### Q78. How do you run asynchronous setup logic (like connecting to a database) before the server starts accepting requests?
```js
async function bootstrap() {
  await connectToDatabase();
  await warmCache();
  app.listen(PORT, () => console.log(`Listening on ${PORT}`));
}
bootstrap().catch((err) => {
  console.error('Failed to start server', err);
  process.exit(1);
});
```

---

## 14. Performance & Scaling

### Q79. How do you enable response compression in Express?
```js
const compression = require('compression');
app.use(compression());
```
This gzip/brotli-compresses response bodies, reducing bandwidth and improving load times — typically applied early in the middleware stack, though it adds CPU overhead per request, which is a trade-off to consider under very high load (sometimes offloaded to a reverse proxy like Nginx instead).

### Q80. How do you scale an Express application across multiple CPU cores?
Using Node's `cluster` module (or a process manager like PM2 with cluster mode) to fork multiple worker processes, each running its own instance of the Express app, sharing the same listening port — since Express/Node itself is single-threaded per process. See the Node.js interview guide's clustering section for details.

### Q81. How do you cache responses in an Express app?
- **In-memory caching** (e.g., a simple `Map` or an LRU cache library) for single-instance apps with low-churn data.
- **Distributed caching** (Redis) for multi-instance deployments, so all instances share a consistent cache.
- **HTTP caching headers** (`Cache-Control`, `ETag`) to let browsers/CDNs cache responses, reducing load on the server entirely for cacheable content.
- Middleware-level caching (e.g., `apicache`) that wraps route handlers and serves cached responses for repeated identical requests within a TTL.

### Q82. What is the impact of synchronous middleware/route logic on Express performance, and how do you avoid it?
Any blocking, synchronous code (heavy computation, `fs.readFileSync`, synchronous JSON parsing of huge payloads) inside a middleware or route handler blocks Node's single event-loop thread, stalling **all** concurrent requests being handled by that process — not just the current one. Use async I/O throughout, and offload genuinely CPU-heavy work to worker threads or a separate service.

### Q83. How do you set request timeouts in Express to avoid hanging connections?
Using middleware like `connect-timeout`, or configuring the underlying `http.Server`'s `timeout`/`headersTimeout`/`requestTimeout` properties directly, ensuring requests that take too long (e.g., due to a stalled downstream dependency) are aborted rather than holding server resources indefinitely.

---

## 15. Testing

### Q84. How do you test Express routes without starting an actual server on a network port?
Using **Supertest**, which wraps the Express `app` instance directly and simulates HTTP requests against it in-memory, without binding to a real port — fast and avoids port-conflict issues in CI.
```js
const request = require('supertest');
const app = require('../app');

test('GET /health returns 200', async () => {
  const res = await request(app).get('/health');
  expect(res.status).toBe(200);
});
```

### Q85. How do you test middleware in isolation?
Call the middleware function directly with mock `req`, `res`, and a `next` spy/mock, asserting on how it mutates `req`/`res` or whether/how it calls `next()`:
```js
test('authenticate middleware rejects missing token', () => {
  const req = { headers: {} };
  const res = { status: jest.fn().mockReturnThis(), json: jest.fn() };
  const next = jest.fn();
  authenticate(req, res, next);
  expect(res.status).toHaveBeenCalledWith(401);
  expect(next).not.toHaveBeenCalled();
});
```

### Q86. How do you mock a database layer when testing Express controllers?
Use dependency injection (pass the DB/service layer as a parameter, or import a module that's easily mocked via `jest.mock('../models/user')`) so controller logic can be tested without hitting a real database, keeping unit tests fast and deterministic — reserving real database interaction for a smaller set of integration tests (often against a dedicated test database or an in-memory equivalent).

### Q87. What is the difference between testing an Express app's routes at the integration level vs. unit-testing individual controller functions?
**Integration tests** (via Supertest against the full `app`) verify the entire request pipeline — middleware, routing, controller, and response formatting — working together correctly, closer to real-world usage. **Unit tests** isolate a single controller/service function, mocking its dependencies, to verify its internal logic precisely and quickly, independent of the HTTP layer.

---

## 16. Express 5 & Version Differences

### Q88. What is the current status of Express 5, and how does it relate to Express 4?
Express 5 became the actively promoted major version (with Express 4 having been the long-standing default for many years), bringing modernization to the framework's internals, minimum Node.js version support, and several breaking API changes — while retaining the same overall philosophy and most of the day-to-day API surface developers are used to.

### Q89. What is the single biggest behavioral improvement in Express 5 regarding async code?
Express 5 route handlers and middleware that return a **rejected promise** are automatically caught and forwarded to the error-handling middleware — eliminating the need for the `asyncHandler` wrapper pattern that was essential in Express 4 to avoid unhandled rejections/hung requests from async route handlers.

### Q90. What are the breaking changes in Express 5 vs. Express 4?
- **Automatic promise rejection handling** in routes/middleware (see Q89).
- **Updated `path-to-regexp`** dependency, changing route path-matching syntax — certain previously-supported patterns (like bare unnamed wildcards `*` without a name, or some optional-character patterns) require updated syntax (e.g., named wildcards like `*splat`).
- **Removal of several deprecated methods** that had been soft-deprecated in Express 4 (e.g., `res.json(status, body)`/`res.send(status, body)` signature variants, `app.del()` alias for `app.delete()`).
- **Minimum supported Node.js version raised**, dropping support for older Node releases.
- Changes to how `req.body` defaults/behaves in some edge cases.

Because of these changes, upgrading an existing Express 4 app requires reviewing route patterns and any usage of removed/changed APIs rather than being a purely drop-in replacement.

### Q91. How should someone decide whether to start a new project on Express 4 or Express 5, or check current guidance?
Given that Express's major-version landscape and ecosystem middleware compatibility can shift, it's worth checking the current official Express documentation/migration guide at the time of starting a project to confirm the latest stable recommendation and middleware compatibility, since third-party middleware support for the newest major version can lag behind the framework itself.

---

## 17. Architecture & Project Structure

### Q92. What is a common, scalable folder structure for a larger Express application?
```
src/
  routes/        # route definitions, thin — delegate to controllers
  controllers/    # handle req/res, call services, format responses
  services/       # business logic, orchestrates data access
  models/         # database schemas/models (Mongoose, Sequelize, Prisma)
  middleware/     # custom middleware (auth, validation, error handling)
  config/         # environment/config loading, DB connection setup
  utils/          # shared helper functions
  app.js          # Express app assembly (middleware + route mounting)
  server.js       # entry point — starts the HTTP server
```
This layered separation (routes → controllers → services → models) keeps each layer focused on a single responsibility and easier to test/maintain independently.

### Q93. What is the MVC (Model-View-Controller) pattern, and how does it map onto Express?
**Model** — data and business logic (often via an ORM/ODM). **View** — presentation layer (a templating engine for server-rendered apps, or simply the JSON shape returned by an API). **Controller** — mediates between the two, handling incoming requests, invoking models, and choosing what to render/return. Express doesn't enforce MVC, but its middleware/router architecture maps naturally onto it, and many teams adopt this pattern by convention.

### Q94. How do you separate the Express `app` configuration from the server startup logic, and why does that separation matter?
Define and export the fully configured `app` (middleware, routes) from one file (`app.js`), and have a separate small entry-point file (`server.js`) that imports `app` and calls `app.listen()`. This separation makes the `app` importable and testable (via Supertest) **without** actually binding to a network port during tests, and cleanly isolates "how the app behaves" from "how/when it starts running."

### Q95. What is dependency injection, and how can it be applied in an Express app despite Express not having a built-in DI container?
Rather than modules directly `require()`-ing their concrete dependencies (making them hard to swap/mock), dependencies (database clients, config, other services) are passed in explicitly — via function parameters, factory functions that construct routers/controllers with injected dependencies, or a lightweight DI library. This decouples modules from specific implementations, easing testing and swapping implementations (e.g., a real vs. mock email service) without changing the consuming code.

---

## 18. TypeScript with Express

### Q96. How do you type Express request/response/handler signatures in TypeScript?
```ts
import { Request, Response, NextFunction } from 'express';

app.get('/users/:id', (req: Request<{ id: string }>, res: Response) => {
  res.json({ id: req.params.id });
});
```
The `Request` generic accepts up to four type parameters: `Request<Params, ResBody, ReqBody, ReqQuery>`, letting you strongly type route params, the response body shape, the request body, and query parameters respectively.

### Q97. How do you extend Express's `Request` type to add custom properties (e.g., `req.user` set by auth middleware)?
Using TypeScript's **declaration merging**, augmenting the `express-serve-static-core` (or `express`) module's `Request` interface in a `.d.ts` file:
```ts
declare global {
  namespace Express {
    interface Request {
      user?: { id: string; role: string };
    }
  }
}
```
This makes `req.user` typed and available everywhere `Request` is used, without needing to cast or redefine the type at every usage site.

### Q98. What are common typing challenges when writing Express middleware/error handlers in TypeScript, and how are they addressed?
Error-handling middleware's `err` parameter is typically typed as `any` or `unknown` by default (since a thrown value in JS can technically be anything), requiring a type guard or a custom typed `AppError` class to safely access properties like `statusCode` within the handler. Additionally, ensuring async route handlers' rejected promises are properly typed/caught (Q76) remains relevant even in TypeScript, since types alone don't change JavaScript's runtime async error-propagation behavior.

---

## 19. Miscellaneous & Best Practices

### Q99. What is the purpose of `app.disable()`/`app.enable()`?
Shorthand for setting a boolean application setting to `false`/`true` respectively — e.g., `app.disable('x-powered-by')` is equivalent to `app.set('x-powered-by', false)`.

### Q100. What is the difference between `res.redirect()` status codes (301 vs 302 vs 307)?
`res.redirect(url)` defaults to a **302** (temporary redirect). `res.redirect(301, url)` issues a **permanent** redirect (clients/search engines may cache this indefinitely and stop requesting the original URL). **307**/308 preserve the original HTTP method and body on redirect (unlike 301/302, which many clients historically convert to a GET), relevant when redirecting non-GET requests.

### Q101. How do you gracefully shut down an Express server (e.g., in a containerized deployment)?
```js
const server = app.listen(3000);

process.on('SIGTERM', () => {
  server.close(() => {
    // close DB connections, etc.
    process.exit(0);
  });
});
```
`server.close()` stops accepting new connections while allowing in-flight requests to complete before the callback fires — important for zero-downtime deploys under orchestrators like Kubernetes, which send `SIGTERM` before force-killing a pod.

### Q102. What is the significance of the order in which `express.json()`, route handlers, and error middleware are registered — walk through a full example.
```js
const app = express();

app.use(helmet());                  // 1. security headers, early
app.use(cors());                    // 2. CORS handling
app.use(express.json());            // 3. body parsing, before routes need req.body
app.use(requestLogger);             // 4. logging

app.use('/api/users', usersRouter); // 5. feature routes
app.use('/api/posts', postsRouter);

app.use(notFoundHandler);           // 6. catch-all 404, after all real routes
app.use(errorHandler);              // 7. error handler, always last
```
Each layer depends on the ones before it having already run (e.g., routes need `req.body` parsed first; the error handler must be last to catch errors from everything above it).

### Q103. What are common Express-specific anti-patterns to avoid?
- Putting business logic directly in route handlers instead of a service layer, making it hard to test or reuse.
- Forgetting to call `next()` (or send a response) in a middleware, hanging requests indefinitely.
- Not setting body size limits, exposing the app to large-payload abuse.
- Using the default in-memory session store or in-memory rate limiter in a multi-instance/production deployment.
- Registering error-handling middleware before other routes (it will never be reached correctly).
- Manually implementing `try/catch` boilerplate in every async handler instead of a shared wrapper (Express 4) or relying on the framework (Express 5).
- Ignoring `trust proxy` configuration when deployed behind a load balancer, leading to incorrect `req.ip`/rate-limiting behavior.
- Returning inconsistent error response shapes across different routes.

### Q104. What are some general Express.js best practices for production applications?
- Use `helmet`, proper CORS configuration, and rate limiting from the start.
- Validate and sanitize all external input.
- Centralize error handling with a well-defined error class hierarchy and a single formatting error-handling middleware.
- Keep route files thin; push logic into controllers/services.
- Use environment variables for all configuration/secrets.
- Add structured logging with request correlation IDs.
- Write integration tests (Supertest) covering critical routes, and unit tests for business logic.
- Enable compression and appropriate caching headers.
- Set request body size limits and reasonable timeouts.
- Plan for graceful shutdown and horizontal scalability (stateless request handling, externalized session/cache stores).

---

*This guide covers the core Express.js interview surface area. For role-specific deep dives (e.g., GraphQL integration with Apollo Server/Express, WebSocket integration with Socket.IO, advanced Express 5 migration specifics, or specific ORM internals), further topic-specific preparation is recommended.*
