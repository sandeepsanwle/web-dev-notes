# REST API

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **REST API (Representational State Transfer)** is an architectural style for designing networked applications, where resources are exposed via URLs and manipulated using standard HTTP methods (GET, POST, PUT, PATCH, DELETE) in a stateless way.

## 2. Simple Explanation

A REST API is like a restaurant menu for your data. Each dish (resource) has a name (URL), and you use standard actions to interact with it: GET to read, POST to create, PUT/PATCH to update, DELETE to remove. The server and client agree on this simple, predictable language built on HTTP.

## 3. Why It Is Used

- Provides a **standard, predictable** way for clients and servers to communicate.
- **Stateless** and cacheable, which makes APIs scalable.
- Language-agnostic — any client (web, mobile, IoT) can consume it.
- Decouples frontend and backend, enabling independent development.

## 4. Key Points

- **Resources** are nouns, identified by URLs (`/users`, `/users/42`).
- **HTTP methods** map to CRUD operations.
- **Stateless**: each request carries all needed info; no server session memory.
- Use proper **status codes** (200, 201, 400, 401, 404, 500).
- Responses are usually JSON; APIs should be **versioned** (`/api/v1`).

### HTTP Methods → CRUD

| Method | CRUD | Idempotent | Example |
|--------|------|------------|---------|
| GET | Read | Yes | `GET /users/42` |
| POST | Create | No | `POST /users` |
| PUT | Replace | Yes | `PUT /users/42` |
| PATCH | Update partial | No* | `PATCH /users/42` |
| DELETE | Delete | Yes | `DELETE /users/42` |

### Common Status Codes

| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 400 | Bad Request |
| 401 | Unauthorized |
| 404 | Not Found |
| 500 | Internal Server Error |

## 5. Syntax

```js
const express = require('express');
const app = express();
app.use(express.json());

// RESTful route pattern for a "users" resource
app.get('/api/v1/users', listUsers);        // list
app.get('/api/v1/users/:id', getUser);       // read one
app.post('/api/v1/users', createUser);       // create
app.put('/api/v1/users/:id', replaceUser);   // replace
app.delete('/api/v1/users/:id', deleteUser); // delete
```

## 6. Example

```js
const express = require('express');
const app = express();
app.use(express.json());

let users = [{ id: 1, name: 'Ada' }];
let nextId = 2;

// READ all
app.get('/api/v1/users', (req, res) => {
  res.status(200).json(users);
});

// READ one
app.get('/api/v1/users/:id', (req, res) => {
  const user = users.find((u) => u.id === Number(req.params.id));
  if (!user) return res.status(404).json({ error: 'Not found' });
  res.json(user);
});

// CREATE
app.post('/api/v1/users', (req, res) => {
  if (!req.body.name) return res.status(400).json({ error: 'name required' });
  const user = { id: nextId++, name: req.body.name };
  users.push(user);
  res.status(201).json(user); // 201 Created
});

// DELETE
app.delete('/api/v1/users/:id', (req, res) => {
  users = users.filter((u) => u.id !== Number(req.params.id));
  res.status(204).send(); // 204 No Content
});

app.listen(3000);
```

## 7. Real World Use Case

A mobile e-commerce app communicates with the backend entirely through a REST API: `GET /products` to browse, `POST /cart/items` to add to cart, `POST /orders` to checkout, and `GET /orders/:id` to track. Because the API is stateless and JSON-based, the same backend serves the iOS app, Android app, and web store, and can sit behind a CDN/load balancer for scale.

## 8. Interview Questions

**Q1:** What are the key principles of REST?
**A:** REST is built on statelessness (each request is self-contained), a uniform interface (resources via URLs, manipulated with standard HTTP methods), client-server separation, cacheability, and a layered system. Resources are represented (usually as JSON) and addressed by URIs.

**Q2:** What does it mean for an HTTP method to be idempotent?
**A:** An idempotent method produces the same server state no matter how many times it's called. GET, PUT, and DELETE are idempotent; POST is not (calling it repeatedly creates multiple resources). Idempotency matters for safe retries.

**Q3:** What's the difference between PUT and PATCH?
**A:** PUT **replaces** an entire resource with the provided representation (and is idempotent). PATCH applies a **partial** update, modifying only the supplied fields. Use PUT for full replacement and PATCH for small targeted changes.

**Q4:** Why is statelessness important in REST?
**A:** Because each request contains all the information needed to process it, the server stores no client session between requests. This makes the API easy to scale horizontally (any server can handle any request) and improves reliability and cacheability.

**Q5:** What status code should a successful resource creation return, and a missing resource?
**A:** A successful creation should return `201 Created` (often with the new resource and a `Location` header). A request for a resource that doesn't exist should return `404 Not Found`. Using correct status codes makes the API self-documenting.

## 9. Common Mistakes

- Using verbs in URLs (`/getUsers`) instead of nouns (`/users`).
- Returning `200` for everything instead of meaningful status codes.
- Making the API stateful (relying on server-side session between calls).
- Not versioning the API, breaking clients on changes.
- Exposing internal error details/stack traces in responses.

## 10. Advanced Notes

- **HATEOAS** (links in responses) is the highest REST maturity level (Richardson Model level 3).
- Support **pagination** (`?page`, `?limit` or cursor-based), filtering, and sorting for collections.
- Use **ETags**/`Cache-Control` and conditional requests for caching and concurrency control.
- Consider **rate limiting**, consistent error envelopes, and OpenAPI/Swagger documentation.
- REST vs GraphQL vs gRPC: REST is simple and cacheable; GraphQL avoids over/under-fetching; gRPC is fast and strongly typed for service-to-service calls.
- Secure with HTTPS, authentication (JWT/OAuth), and input validation.
