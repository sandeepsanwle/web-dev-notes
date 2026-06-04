# Backend Interview Questions

> A curated bank of backend interview questions **with concise answers**, grouped by topic. Each question has a difficulty tag: 🟢 Easy · 🟡 Medium · 🔴 Hard.
>
> Focused on the Node.js/Express ecosystem, but the concepts (REST, databases, auth, caching, architecture) apply to any backend stack.

**Legend:** 🟢 Junior-friendly · 🟡 Mid-level · 🔴 Senior / tricky

---

## Node.js

**Q:** What is Node.js and what is it good at? 🟢
**A:** Node.js is a JavaScript runtime built on Chrome's V8 engine that runs JS outside the browser. Its non-blocking, event-driven I/O model makes it excellent for I/O-heavy, real-time apps (APIs, chat, streaming). It's less suited to CPU-bound work.

**Q:** Explain the Node.js event loop. 🔴
**A:** Node uses a single-threaded event loop (via libuv) with phases: **timers** (`setTimeout`), **pending callbacks**, **poll** (I/O), **check** (`setImmediate`), and **close** callbacks. Microtasks (Promises, `process.nextTick`) run between phases. This lets one thread handle many concurrent connections.

**Q:** Is Node.js single-threaded? How does it handle concurrency? 🟡
**A:** The JS execution is single-threaded, but libuv maintains a **thread pool** (default 4) for expensive operations (file I/O, crypto, DNS). For CPU-bound parallelism you can use **Worker Threads** or spawn multiple processes (`cluster`).

**Q:** What is the difference between `process.nextTick` and `setImmediate`? 🔴
**A:** `process.nextTick` callbacks run **before** the event loop continues (after the current operation, before any I/O). `setImmediate` runs in the **check** phase, after the poll phase. Overusing `nextTick` can starve the loop.

**Q:** How do you handle errors in Node.js? 🟡
**A:** Use `try/catch` for sync and `async/await` code, `.catch()` for promises, the error-first callback pattern (`(err, data) => {}`), `'error'` event listeners on streams/emitters, and a global `process.on('uncaughtException')`/`unhandledRejection` as last-resort logging before graceful shutdown.

**Q:** What are streams in Node.js? 🔴
**A:** Streams process data in chunks instead of loading it all into memory. Four types: **Readable**, **Writable**, **Duplex**, and **Transform**. They're memory-efficient for large files/network data and support `.pipe()` for composition.

**Q:** How do you scale a Node.js application? 🔴
**A:** Use the **cluster** module or a process manager (PM2) to fork one process per CPU core, run multiple instances behind a load balancer, offload CPU work to worker threads/queues, add caching (Redis), and scale horizontally with containers/orchestration (Kubernetes).

---

## Express.js

**Q:** What is Express and what is middleware? 🟢
**A:** Express is a minimal web framework for Node. **Middleware** are functions with signature `(req, res, next)` that run in order on each request — used for parsing, logging, auth, error handling. Call `next()` to pass control to the next middleware.

**Q:** How do you define routes in Express? 🟢
**A:** Via HTTP-method methods on the app or a `Router`:

```js
const router = express.Router();
router.get('/users/:id', getUser);
router.post('/users', createUser);
app.use('/api', router);
```

**Q:** What is the order of middleware execution and why does it matter? 🟡
**A:** Middleware runs **top to bottom** in the order registered. Order matters: body parsers must run before route handlers that read `req.body`; error handlers must be registered **last**; auth middleware must run before protected routes.

**Q:** How do you handle errors centrally in Express? 🟡
**A:** Define an error-handling middleware with **four** arguments; Express recognizes it by arity:

```js
app.use((err, req, res, next) => {
  console.error(err);
  res.status(err.status || 500).json({ message: err.message });
});
```
In async handlers, pass errors with `next(err)` (or use a wrapper / Express 5 auto-forwarding).

**Q:** How do you serve static files and parse JSON bodies? 🟢
**A:** `app.use(express.static('public'))` serves static assets. `app.use(express.json())` parses JSON request bodies into `req.body`. `express.urlencoded()` handles form posts.

**Q:** How do you secure an Express app? 🔴
**A:** Use `helmet` for secure headers, `cors` configured to specific origins, rate limiting (`express-rate-limit`), input validation/sanitization, parameterized queries, HTTPS, and never trust client input. Keep dependencies patched.

**Q:** What's the difference between `app.use` and `app.get`? 🟢
**A:** `app.use` mounts middleware for **all** HTTP methods (and matches path prefixes). `app.get` (and `post`, etc.) registers a handler for a **specific** method and exact path pattern.

---

## REST API Design

**Q:** What makes an API RESTful? 🟡
**A:** REST principles: **stateless** requests, **resource-based** URLs (nouns, not verbs), use of HTTP **methods** for actions, standard **status codes**, **representations** (usually JSON), and ideally HATEOAS (links). Each request carries all info needed to process it.

**Q:** Map CRUD operations to HTTP methods. 🟢
**A:** Create → `POST`, Read → `GET`, Update → `PUT`/`PATCH`, Delete → `DELETE`. `PUT` replaces the whole resource (idempotent); `PATCH` partially updates it.

**Q:** What are idempotent and safe methods? 🔴
**A:** **Safe** methods don't modify state (`GET`, `HEAD`). **Idempotent** methods produce the same result no matter how many times called (`GET`, `PUT`, `DELETE`, `HEAD`). `POST` is neither — repeating it creates duplicates.

**Q:** Explain common HTTP status codes. 🟢
**A:**

| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized (not authenticated) |
| 403 | Forbidden (no permission) |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Unprocessable Entity (validation) |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

**Q:** How do you version an API? 🟡
**A:** Common approaches: URL path (`/api/v1/users`), custom header (`Accept-Version`), or content negotiation via `Accept` header. URL versioning is the most explicit and widely used.

**Q:** How should you handle pagination, filtering, and sorting? 🟡
**A:** Use query params: pagination via `?page=2&limit=20` (offset) or **cursor-based** (`?cursor=abc`) for large/real-time data; filtering via `?status=active`; sorting via `?sort=-createdAt`. Return metadata (total count, next cursor).

**Q:** REST vs GraphQL — when to use which? 🔴
**A:** **REST** is simple, cacheable, and great for resource-oriented APIs. **GraphQL** lets clients request exactly the fields they need in one round trip — good for complex, nested data and many client types, at the cost of caching complexity and potential over-fetching on the server. Choose based on client needs.

---

## Databases (SQL & NoSQL)

**Q:** What is the difference between SQL and NoSQL databases? 🟢
**A:** **SQL** (relational, e.g. PostgreSQL, MySQL) uses structured tables with fixed schemas and strong consistency (ACID) — great for complex queries and transactions. **NoSQL** (e.g. MongoDB, Redis, Cassandra) is schema-flexible and horizontally scalable — great for large-scale, evolving, or unstructured data.

**Q:** What does ACID stand for? 🟡
**A:** **Atomicity** (all-or-nothing transactions), **Consistency** (valid state transitions), **Isolation** (concurrent transactions don't interfere), **Durability** (committed data survives crashes). Core guarantees of relational databases.

**Q:** What is database normalization? 🟡
**A:** Organizing tables to reduce redundancy and anomalies by splitting data into related tables (1NF, 2NF, 3NF...). Trade-off: cleaner writes but more joins. **Denormalization** intentionally duplicates data to speed up reads.

**Q:** What is an index and what's the trade-off? 🟡
**A:** An index (usually a B-tree) speeds up reads by letting the DB find rows without scanning the whole table. Trade-off: indexes consume storage and slow down writes (they must be updated). Index columns used in `WHERE`, `JOIN`, and `ORDER BY`.

**Q:** Explain SQL joins. 🟡
**A:** **INNER** returns matching rows in both tables. **LEFT** returns all left rows + matches (nulls otherwise). **RIGHT** is the mirror. **FULL OUTER** returns all rows from both. **CROSS** returns the Cartesian product.

**Q:** What is a transaction and when do you need one? 🟡
**A:** A transaction groups multiple operations so they succeed or fail together (`BEGIN ... COMMIT/ROLLBACK`). Needed when operations must stay consistent — e.g. transferring money between two accounts.

**Q:** What is the N+1 query problem? 🔴
**A:** Issuing 1 query to fetch a list, then 1 additional query per item (N more) — killing performance. Fix with a **JOIN**, eager loading, or batching (e.g. DataLoader). Watch for it in ORMs with lazy relations.

**Q:** When would you use Redis? 🟡
**A:** Redis is an in-memory key-value store used for caching, session storage, rate limiting, leaderboards (sorted sets), pub/sub messaging, and queues. Extremely fast but data is memory-bound (can persist optionally).

---

## Authentication & Security

**Q:** What's the difference between authentication and authorization? 🟢
**A:** **Authentication** verifies *who you are* (login). **Authorization** verifies *what you're allowed to do* (permissions/roles). AuthN comes first, then AuthZ.

**Q:** How does JWT-based authentication work? 🟡
**A:** On login the server issues a signed **JSON Web Token** (header.payload.signature). The client sends it (usually `Authorization: Bearer <token>`) on each request; the server **verifies the signature** to trust the claims without a DB lookup. Tokens are stateless and should be short-lived.

**Q:** Sessions vs JWT — trade-offs? 🔴
**A:** **Sessions** store state server-side (or in Redis); easy to revoke, but require shared session storage to scale. **JWTs** are stateless and scale easily, but are hard to revoke before expiry. Mitigate with short-lived access tokens + refresh tokens + a denylist.

**Q:** How should you store passwords? 🔴
**A:** Never store plaintext or simple hashes. Use a slow, salted adaptive hash like **bcrypt**, **scrypt**, or **Argon2**. The salt prevents rainbow-table attacks; the cost factor slows brute force.

**Q:** What is OAuth 2.0? 🔴
**A:** An authorization framework that lets an app access a user's resources on another service **without** their password, via access tokens. The user authorizes a scoped grant (e.g. "Sign in with Google"). OAuth handles authorization; **OpenID Connect** adds an identity layer on top.

**Q:** How do you prevent SQL injection? 🔴
**A:** Use **parameterized queries / prepared statements** (or an ORM) so user input is treated as data, never executable SQL. Also validate/sanitize input and apply least-privilege DB accounts.

**Q:** What is rate limiting and why is it important for security? 🟡
**A:** Capping how many requests a client can make in a time window. Prevents brute-force attacks, scraping, and abuse, and protects against DoS. Implement with a token bucket / sliding window, often in Redis or at an API gateway.

---

## Caching & Performance

**Q:** What is caching and what are the main layers? 🟢
**A:** Storing copies of data to serve future requests faster. Layers: browser cache, **CDN**, reverse-proxy cache, application cache (in-memory/Redis), and database query cache. Cache near the consumer for the biggest win.

**Q:** Explain cache invalidation strategies. 🔴
**A:** The hard part of caching. Strategies: **TTL** (expire after time), **write-through** (update cache on write), **write-behind** (async write), **cache-aside** (app loads on miss and populates), and explicit invalidation on data change. Pick based on staleness tolerance.

**Q:** What is the cache-aside (lazy loading) pattern? 🟡
**A:** The app checks the cache first; on a **miss** it reads from the DB, stores the result in the cache, then returns it. Subsequent reads hit the cache until it expires. Simple and common, but the first read is slow and data can go stale.

**Q:** What's the difference between caching and a CDN? 🟡
**A:** A **CDN** is a geographically distributed cache for **static assets** (and increasingly dynamic content) served from edge locations near users — reducing latency. General caching can apply at any layer for any data, including dynamic API responses.

**Q:** How do you handle a "thundering herd" / cache stampede? 🔴
**A:** When a popular key expires, many requests hit the DB simultaneously. Mitigate with a **lock/mutex** so only one request recomputes, **stale-while-revalidate**, randomized (jittered) TTLs, or pre-warming the cache.

**Q:** How can you find and fix a slow API endpoint? 🟡
**A:** Profile and measure first: add logging/APM (timing), inspect slow DB queries (`EXPLAIN`), check for N+1 queries, add indexes, cache hot data, paginate large responses, and parallelize independent I/O with `Promise.all`.

**Q:** What is connection pooling? 🟡
**A:** Reusing a fixed set of open DB connections instead of opening/closing one per request. Opening connections is expensive; a pool bounds concurrency and dramatically improves throughput and latency.

---

## Architecture

**Q:** Monolith vs microservices — trade-offs? 🔴
**A:** A **monolith** is one deployable unit — simpler to build, test, and deploy early on. **Microservices** split into independently deployable services — better team autonomy and scaling of hot paths, at the cost of operational complexity, network latency, and distributed-data challenges. Start monolith, split when justified.

**Q:** What is a message queue and when do you use one? 🟡
**A:** A queue (RabbitMQ, SQS, Kafka) decouples producers from consumers, enabling async processing, load leveling, and resilience. Use it for background jobs (emails, image processing), spikes, and inter-service communication.

**Q:** What is the difference between synchronous and asynchronous communication? 🟡
**A:** **Synchronous** (HTTP/RPC) — the caller waits for a response; simple but tightly coupled. **Asynchronous** (queues/events) — the caller fires and continues; resilient and scalable but eventually consistent and harder to trace.

**Q:** What are idempotency keys and why do they matter? 🔴
**A:** A client-supplied unique key the server uses to detect and ignore duplicate requests (e.g. retried payments). The server stores the key + result so re-sends return the original outcome instead of double-processing.

**Q:** What is eventual consistency? 🔴
**A:** In distributed systems, replicas may temporarily disagree but **converge** to the same state given no new writes. It trades immediate consistency for availability and scalability (common in NoSQL and microservices).

**Q:** How do you design for fault tolerance? 🔴
**A:** Use redundancy (multiple instances/AZs), **retries with backoff**, **circuit breakers**, timeouts, graceful degradation, health checks, and queues to absorb failures. Assume any component can fail and avoid single points of failure.

**Q:** What is the twelve-factor app methodology (a few key factors)? 🟡
**A:** Best practices for SaaS apps: store **config in the environment**, treat **backing services as attached resources**, keep processes **stateless**, achieve **dev/prod parity**, and treat **logs as event streams**. Improves portability and scalability.

---

### How to use this file
- Be ready to **whiteboard** data models and request flows for 🔴 questions.
- Connect answers to real trade-offs (consistency vs availability, simplicity vs scale).
- Pair with `system-design-basics.md` for the design rounds.
