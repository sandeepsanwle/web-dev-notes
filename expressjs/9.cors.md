# CORS

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

CORS (Cross-Origin Resource Sharing) is a browser security mechanism that controls whether a web page from one **origin** (scheme + host + port) is allowed to make requests to a server on a **different** origin. Servers opt in by sending specific `Access-Control-*` response headers.

## 2. Simple Explanation

By default, browsers follow the "same-origin policy" — a script from `siteA.com` can't freely call `siteB.com`. CORS is like `siteB.com` putting up a sign saying "Visitors from `siteA.com` are welcome." Without that sign, the browser blocks the response.

## 3. Why It Is Used

- To safely allow trusted frontends (e.g. a React app on another domain) to call your API.
- To control exactly which origins, methods, and headers are permitted.
- To enable credentialed cross-origin requests (cookies) securely.
- To prevent malicious sites from reading your API responses.

## 4. Key Points

- CORS is enforced by the **browser**, not the server — tools like curl/Postman ignore it.
- The `cors` npm package configures the right headers easily.
- "Non-simple" requests trigger a **preflight** `OPTIONS` request.
- For cookies/credentials, set `credentials: true` and a specific origin (not `*`).
- A wildcard origin (`*`) cannot be combined with credentials.

## 5. Syntax

```js
const cors = require('cors');

app.use(cors());                  // allow all origins
app.use(cors({ origin: 'https://myapp.com', credentials: true }));
```

## 6. Example

```js
const express = require('express');
const cors = require('cors');
const app = express();

// Allow only specific origins, with credentials (cookies)
const corsOptions = {
  origin: ['https://myapp.com', 'http://localhost:3000'],
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true
};

app.use(cors(corsOptions));

app.get('/api/data', (req, res) => {
  res.json({ message: 'CORS-enabled response' });
});

// Enable preflight for a specific route (optional)
app.options('/api/data', cors(corsOptions));

app.listen(3000);
```

## 7. Real World Use Case

A company runs its frontend at `https://app.acme.com` and its API at `https://api.acme.com` — different origins. The API uses the `cors` middleware to allow only `https://app.acme.com`, permit `Authorization` headers for JWTs, and enable `credentials: true` so the refresh-token cookie is sent. A random attacker site loading in a browser cannot read API responses because its origin isn't on the allowlist.

## 8. Interview Questions

**Q1:** What problem does CORS solve?
**A:** It lets servers safely relax the browser's same-origin policy, declaring which external origins may read their responses — enabling legitimate cross-origin API calls while blocking unauthorized ones.

**Q2:** What is a preflight request and when does it happen?
**A:** A preflight is an automatic `OPTIONS` request the browser sends before "non-simple" requests (custom headers, methods like PUT/DELETE, or non-form content types) to ask the server if the actual request is allowed.

**Q3:** Why can't you use `origin: '*'` with credentials?
**A:** The CORS spec forbids combining a wildcard origin with `Access-Control-Allow-Credentials: true` for security. You must specify exact origin(s) when sending cookies/credentials.

**Q4:** Is CORS a server-side or browser-side security feature?
**A:** It's enforced by the browser. The server only sends headers indicating its policy; non-browser clients (curl, Postman, server-to-server) aren't restricted by CORS.

**Q5:** Does CORS protect your server from attacks?
**A:** Not really — it protects users' browsers from reading cross-origin responses. It's not a substitute for authentication, authorization, or input validation on the server.

## 9. Common Mistakes

- Using `origin: '*'` with `credentials: true` (silently breaks credentialed requests).
- Thinking CORS secures the API itself (it only restricts browser reads).
- Forgetting to allow the `Authorization` header, breaking token auth.
- Not handling preflight `OPTIONS` requests for custom headers/methods.
- Hard-coding a single origin when multiple environments are needed.

## 10. Advanced Notes

- **Dynamic origin:** Pass a function to `origin` to validate against an allowlist or DB at request time.
- **`maxAge`:** Cache preflight responses to reduce repeated `OPTIONS` calls.
- **Exposed headers:** Use `exposedHeaders` to let the browser read custom response headers (e.g. pagination).
- **Vary: Origin:** Set when reflecting origins so caches don't serve the wrong CORS headers.
- CORS differs from **CSRF** protection — you still need CSRF tokens/SameSite cookies for state-changing requests.
