# Middleware

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Middleware are functions that run **between** receiving a request and sending a response. They have access to the request object (`req`), the response object (`res`), and the `next` function, and they can run code, modify `req`/`res`, end the request, or pass control to the next middleware.

## 2. Simple Explanation

Picture an assembly line. A request travels down the line, and each station (middleware) can inspect it, stamp it, modify it, or stop it. When a station is done, it calls `next()` to send the request to the next station — until one of them sends a response.

## 3. Why It Is Used

- To run cross-cutting logic like logging, authentication, and parsing.
- To avoid repeating the same code in every route handler.
- To transform requests (e.g. parse JSON bodies) before they reach handlers.
- To handle errors in one centralized place.

## 4. Key Points

- Signature is `(req, res, next)`; error middleware is `(err, req, res, next)`.
- Call `next()` to continue, or end the cycle with `res.send()`/`res.json()`.
- Middleware runs **in the order it is declared**.
- `app.use()` registers middleware globally or for a path prefix.
- Middleware can be application-level, router-level, built-in, third-party, or error-handling.

### Types of Middleware

| Type | Example | Purpose |
|------|---------|---------|
| Application-level | `app.use(logger)` | Runs for all/specified requests |
| Router-level | `router.use(...)` | Scoped to a specific router |
| Built-in | `express.json()`, `express.static()` | Shipped with Express |
| Third-party | `cors()`, `morgan()`, `helmet()` | Installed via npm |
| Error-handling | `(err, req, res, next)` | Catches errors (4 args) |

## 5. Syntax

```js
function myMiddleware(req, res, next) {
  // do something
  next(); // pass control to the next middleware
}

app.use(myMiddleware);            // global
app.use('/api', myMiddleware);    // path-specific
app.get('/x', myMiddleware, handler); // route-specific
```

## 6. Example

```js
const express = require('express');
const app = express();

// Built-in: parse JSON bodies
app.use(express.json());

// Custom logger middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next();
});

// Route-specific middleware
function requireApiKey(req, res, next) {
  if (req.headers['x-api-key'] !== 'secret123') {
    return res.status(401).json({ error: 'Invalid API key' });
  }
  next();
}

app.get('/data', requireApiKey, (req, res) => {
  res.json({ data: [1, 2, 3] });
});

app.listen(3000);
```

## 7. Real World Use Case

A SaaS API uses a stack of middleware on every request: `helmet()` for security headers, `cors()` for cross-origin access, `express.json()` to parse bodies, `morgan()` to log traffic, a custom `authenticate` middleware to verify JWTs, and finally a centralized error handler. Adding a new feature means simply slotting a new middleware in — no route changes needed.

## 8. Interview Questions

**Q1:** What is middleware in Express?
**A:** A function with access to `req`, `res`, and `next` that runs during the request-response cycle. It can execute code, modify the objects, end the cycle, or call `next()` to pass control onward.

**Q2:** What happens if you forget to call `next()`?
**A:** The request hangs because control never passes to the next middleware or route handler, and no response is sent (unless that middleware itself sends one). The client eventually times out.

**Q3:** How does Express identify error-handling middleware?
**A:** By its **four** arguments `(err, req, res, next)`. Express only calls it when an error is passed via `next(err)` or thrown in a sync handler.

**Q4:** Does the order of middleware matter?
**A:** Yes. Middleware executes in declaration order. For example, `express.json()` must be registered before routes that read `req.body`.

**Q5:** Difference between `app.use()` and `app.get()` for middleware?
**A:** `app.use()` matches all HTTP methods and treats the path as a prefix; `app.get()` matches only GET and an exact path pattern. `app.use()` is typical for general middleware.

## 9. Common Mistakes

- Forgetting `next()`, causing requests to hang.
- Registering `express.json()` *after* routes that need `req.body`.
- Sending a response and then calling `next()` (headers-already-sent error).
- Defining the error handler too early (it must be **last**).
- Writing error middleware with only 3 args so Express treats it as normal middleware.

## 10. Advanced Notes

- **Async middleware:** In Express 4, you must wrap async functions to catch rejections (`next(err)`); Express 5 forwards rejected promises automatically.
- **Short-circuiting:** Returning early with `return next()` or `return res.send()` prevents the rest of the function from running.
- **Composing middleware:** Libraries like `express-async-handler` reduce try/catch boilerplate.
- **Sub-app mounting:** Middleware on a mounted sub-app only runs for that mount path.
- **Performance:** Keep heavy middleware path-scoped rather than global to avoid unnecessary work.
