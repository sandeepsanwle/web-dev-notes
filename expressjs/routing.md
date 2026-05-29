# Routing

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

Routing is the mechanism by which an Express application decides how to respond to a client request for a particular **endpoint** — a combination of a URL **path** (e.g. `/users`) and an HTTP **method** (GET, POST, PUT, DELETE, etc.).

## 2. Simple Explanation

Think of routing as a receptionist in an office. When a request comes in, the receptionist looks at *where* it wants to go (the path) and *what* it wants to do (the method), then sends it to the right handler function. Each route says: "When a `GET` request hits `/about`, run this function."

## 3. Why It Is Used

- To map incoming requests to specific logic in your app.
- To build clean, predictable REST APIs.
- To separate concerns (each route can do one thing).
- To support dynamic URLs via route parameters (e.g. `/users/:id`).

## 4. Key Points

- A route has three parts: **method**, **path**, and **handler**.
- `req` (request) and `res` (response) are passed to every handler.
- Route parameters are accessed via `req.params`, query strings via `req.query`.
- `express.Router()` lets you group related routes into modular files.
- Route order matters — Express matches top to bottom.

## 5. Syntax

```js
app.METHOD(PATH, HANDLER);
// METHOD: get, post, put, delete, patch, all...
// PATH:   a string or pattern
// HANDLER: (req, res, next) => { ... }
```

## 6. Example

```js
const express = require('express');
const app = express();

// Static route
app.get('/', (req, res) => {
  res.send('Home Page');
});

// Route parameter
app.get('/users/:id', (req, res) => {
  res.json({ userId: req.params.id });
});

// Query string: /search?q=express
app.get('/search', (req, res) => {
  res.send(`Searching for: ${req.query.q}`);
});

app.listen(3000, () => console.log('Server on http://localhost:3000'));
```

Using a modular router:

```js
// routes/users.js
const router = require('express').Router();

router.get('/', (req, res) => res.send('All users'));
router.get('/:id', (req, res) => res.send(`User ${req.params.id}`));

module.exports = router;

// app.js
app.use('/users', require('./routes/users'));
```

## 7. Real World Use Case

An e-commerce API exposes `GET /products` to list items, `GET /products/:id` to view one product, `POST /products` to add a new one (admin only), and `DELETE /products/:id` to remove it. Each route maps cleanly to a database operation, keeping the API RESTful and easy to consume by the frontend.

## 8. Interview Questions

**Q1:** What are the three components of an Express route?
**A:** An HTTP method (e.g. GET/POST), a path (URL pattern), and a handler function `(req, res, next)` that runs when the route matches.

**Q2:** How do route parameters differ from query strings?
**A:** Route parameters are part of the path (`/users/:id` → `req.params.id`) and usually identify a resource. Query strings come after `?` (`/search?q=x` → `req.query.q`) and are typically for filtering, sorting, or optional data.

**Q3:** What is `express.Router()` and why use it?
**A:** It's a mini, modular router instance you can attach to a path with `app.use()`. It lets you split routes across files for cleaner, maintainable, and reusable code.

**Q4:** Does the order of route definitions matter?
**A:** Yes. Express matches routes top to bottom and stops at the first match. A broad route like `app.get('*')` placed early can shadow more specific routes below it.

**Q5:** How do you handle multiple HTTP methods on the same path concisely?
**A:** Use `app.route('/path')` chaining: `app.route('/book').get(...).post(...).put(...)`, which avoids repeating the path string.

## 9. Common Mistakes

- Placing a catch-all (`*`) or generic route before specific ones, shadowing them.
- Forgetting to call `res.send()`/`res.json()`, leaving the request hanging.
- Confusing `req.params` (path) with `req.query` (query string) and `req.body` (POST data).
- Sending a response twice (`Error: Cannot set headers after they are sent`).
- Not mounting routers with `app.use()` after defining them.

## 10. Advanced Notes

- **Route patterns:** Express supports string patterns and regex, e.g. `app.get(/.*fly$/, ...)`.
- **Chained handlers:** A route can take multiple handlers; call `next()` to pass control.
- **`app.all()`** matches every HTTP method — useful for global middleware on a path.
- **Express 5** changed wildcard syntax (named wildcards like `/*splat`) and returns rejected promises to error handlers automatically.
- For large APIs, consider versioned routers: `app.use('/api/v1', v1Router)`.
