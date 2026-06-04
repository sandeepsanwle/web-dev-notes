# Error Handling

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Error handling in Express is the practice of catching errors (synchronous and asynchronous) that occur during request processing and responding gracefully, usually through a dedicated **error-handling middleware** with the signature `(err, req, res, next)`.

## 2. Simple Explanation

Imagine a safety net under a tightrope walker. No matter where on the rope they slip, they fall into the same net. In Express, errors from any route or middleware are passed to one central "net" — the error handler — which decides what message and status code to send back, instead of crashing the app.

## 3. Why It Is Used

- To prevent the server from crashing on unexpected failures.
- To return consistent, clean error responses to clients.
- To centralize error logic instead of repeating try/catch everywhere.
- To hide sensitive details (stack traces) from end users in production.

## 4. Key Points

- Error middleware has **four** parameters: `(err, req, res, next)`.
- It must be defined **last**, after all routes and other middleware.
- Pass errors with `next(err)`; thrown sync errors are auto-caught.
- In Express 4, async errors must be passed manually; Express 5 auto-forwards rejected promises.
- Always set an appropriate HTTP status code (`res.status(500)`).

## 5. Syntax

```js
// Error-handling middleware MUST have 4 args
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({ error: err.message });
});
```

## 6. Example

```js
const express = require('express');
const app = express();
app.use(express.json());

// A route that throws synchronously
app.get('/sync', (req, res) => {
  throw new Error('Something broke!'); // auto-caught by Express
});

// An async route — pass errors via next()
app.get('/async', async (req, res, next) => {
  try {
    const data = await fetchData();
    res.json(data);
  } catch (err) {
    next(err); // forward to error handler
  }
});

// 404 handler (no route matched)
app.use((req, res, next) => {
  res.status(404).json({ error: 'Not Found' });
});

// Centralized error handler — defined LAST
app.use((err, req, res, next) => {
  const status = err.status || 500;
  res.status(status).json({
    error: err.message,
    ...(process.env.NODE_ENV !== 'production' && { stack: err.stack })
  });
});

app.listen(3000);
```

## 7. Real World Use Case

A payment API wraps all controllers so any failure — invalid card, DB timeout, third-party gateway error — bubbles up to a single error handler. The handler logs the full error for the dev team, maps known errors to friendly messages and proper status codes (402, 400, 503), and returns a clean JSON shape `{ error, code }` that the frontend can reliably parse and display.

## 8. Interview Questions

**Q1:** How does Express recognize error-handling middleware?
**A:** By its four-argument signature `(err, req, res, next)`. Express invokes it only when an error is passed via `next(err)` or thrown synchronously.

**Q2:** Where should the error handler be placed?
**A:** Last, after all routes and other middleware, so any error from earlier in the chain can reach it.

**Q3:** How do you handle errors in async route handlers?
**A:** In Express 4, wrap the logic in try/catch and call `next(err)`, or use a wrapper like `express-async-handler`. In Express 5, rejected promises are forwarded to the error handler automatically.

**Q4:** Why shouldn't you send stack traces in production?
**A:** They expose internal implementation details and file paths, which is a security risk. Send generic messages to clients and log full details server-side.

**Q5:** How do you handle 404 (route not found)?
**A:** Add a middleware with no path after all routes that sends `res.status(404)`. It runs only when no earlier route matched.

## 9. Common Mistakes

- Writing the error handler with 3 args, so Express treats it as normal middleware.
- Placing the error handler before routes, so it never catches them.
- Forgetting `next(err)` in async code, so errors silently disappear.
- Calling `next(err)` after already sending a response.
- Leaking stack traces or DB errors directly to clients.

## 10. Advanced Notes

- **Custom error classes:** Create an `AppError extends Error` with `statusCode` and `isOperational` flags to distinguish expected vs. programmer errors.
- **Async wrapper:** `const wrap = fn => (req,res,next) => Promise.resolve(fn(req,res,next)).catch(next);` removes repetitive try/catch.
- **Process-level safety:** Handle `process.on('unhandledRejection')` and `uncaughtException` to log and gracefully shut down.
- **Status mapping:** Maintain a map of error types → HTTP codes for consistency.
- **Logging:** Integrate Winston/Pino in the error handler for structured, searchable logs.
