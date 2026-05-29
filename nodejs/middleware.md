# Middleware

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

**Middleware** is a function that sits in the request–response cycle and has access to the request object (`req`), the response object (`res`), and the `next` function. It can run code, modify req/res, end the response, or pass control to the next middleware.

## 2. Simple Explanation

Middleware is like a series of security checkpoints at an airport. Each request passes through checkpoints in order — one checks your ticket (auth), one scans bags (validation), one logs your entry (logging) — before reaching the gate (your route handler). Each checkpoint can let you through, modify your stuff, or stop you.

## 3. Why It Is Used

- Run shared logic (logging, auth, parsing) in one place, not in every route.
- Process and transform requests before they hit handlers.
- Handle cross-cutting concerns: CORS, body parsing, error handling.
- Keep route handlers clean and focused on business logic.

## 4. Key Points

- Signature: `(req, res, next)` — and error middleware uses `(err, req, res, next)`.
- Middleware runs **in the order it is registered**.
- You must call `next()` to pass control, or send a response to end the cycle.
- Types: application-level, router-level, built-in, third-party, and error-handling.
- Forgetting `next()` (without responding) leaves the request **hanging**.

### Middleware Types

| Type | Registered with | Example |
|------|-----------------|---------|
| Application-level | `app.use()` | logging for all routes |
| Router-level | `router.use()` | auth for a route group |
| Built-in | provided by Express | `express.json()` |
| Third-party | npm packages | `cors`, `helmet`, `morgan` |
| Error-handling | 4-arg function | central error handler |

## 5. Syntax

```js
// Standard middleware
function middleware(req, res, next) {
  // ... do something ...
  next(); // pass control to the next middleware/handler
}

app.use(middleware); // applies to all routes

// Error-handling middleware (4 args)
function errorHandler(err, req, res, next) {
  res.status(500).json({ error: err.message });
}
```

## 6. Example

```js
const express = require('express');
const app = express();

// 1. Built-in middleware: parse JSON bodies
app.use(express.json());

// 2. Custom logging middleware (runs for every request)
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next();
});

// 3. Auth middleware applied to a single route
function requireApiKey(req, res, next) {
  if (req.headers['x-api-key'] !== 'secret') {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
}

app.get('/data', requireApiKey, (req, res) => {
  res.json({ data: [1, 2, 3] });
});

// 4. Error-handling middleware (must be LAST, has 4 args)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something broke' });
});

app.listen(3000);
```

## 7. Real World Use Case

A production Express API uses a middleware stack: `helmet()` for secure headers, `cors()` for cross-origin rules, `express.json()` for body parsing, `morgan()` for request logging, a custom `authenticate` middleware to verify JWTs, a `rateLimiter` to prevent abuse, and finally a centralized error-handling middleware so all errors return a consistent JSON shape. Each concern is isolated and reusable.

## 8. Interview Questions

**Q1:** What is middleware in Express and what arguments does it receive?
**A:** Middleware is a function executed during the request-response cycle that receives `(req, res, next)`. It can read/modify `req` and `res`, end the response, or call `next()` to pass control to the next middleware. Error middleware takes a fourth argument: `(err, req, res, next)`.

**Q2:** What happens if you don't call `next()` in a middleware?
**A:** If you neither call `next()` nor send a response, the request hangs indefinitely because control is never passed on and the client never gets a reply. You must either respond or call `next()` (optionally with an error).

**Q3:** How does the order of middleware registration matter?
**A:** Middleware executes in the exact order it's registered with `app.use()`/route definitions. For example, body-parsing middleware must come before handlers that read `req.body`, and error-handling middleware must be registered **last**.

**Q4:** How do you define error-handling middleware?
**A:** By writing a middleware function with **four** parameters: `(err, req, res, next)`. Express recognizes the four-argument signature as an error handler and routes errors (passed via `next(err)` or thrown in async handlers) to it.

**Q5:** What is the difference between application-level and router-level middleware?
**A:** Application-level middleware is bound to the app via `app.use()` and runs for matching requests across the whole app. Router-level middleware is bound to an `express.Router()` instance and runs only for routes on that router, allowing scoped logic like auth for an admin route group.

## 9. Common Mistakes

- Forgetting to call `next()` (and not responding), hanging the request.
- Registering error middleware before routes, so it never catches errors.
- Putting `express.json()` after handlers that need `req.body`.
- Calling `next()` after already sending a response (double response error).
- Not forwarding async errors with `next(err)` (or a wrapper), so they go uncaught.

## 10. Advanced Notes

- In Express 4, async errors aren't auto-caught — wrap handlers (`express-async-errors` or a try/catch wrapper) or use Express 5 which forwards rejected promises.
- Middleware can be **mounted on paths**: `app.use('/admin', adminMiddleware)`.
- `next('route')` skips remaining middleware in the current route and moves to the next matching route.
- Order matters for performance: cheap checks (auth, rate limit) before expensive parsing.
- The same pattern exists in Koa (async, with `await next()`), Fastify (hooks), and Connect.
