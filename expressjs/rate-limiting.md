# Rate Limiting

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Rate limiting controls how many requests a client (by IP, user, or API key) can make to your server within a given time window. Requests beyond the limit are rejected, usually with HTTP status **429 Too Many Requests**.

## 2. Simple Explanation

Rate limiting is like a turnstile at a stadium that only lets a certain number of people through per minute. It prevents a stampede from overwhelming the venue. Similarly, it stops a single client from flooding your server with too many requests.

## 3. Why It Is Used

- To prevent abuse and brute-force attacks (e.g. on login).
- To protect against DoS/DDoS and traffic spikes.
- To ensure fair usage among all clients.
- To control costs on metered downstream services.

## 4. Key Points

- Commonly implemented with the `express-rate-limit` middleware.
- Define a **window** (time period) and a **max** (allowed requests).
- Returns `429` and `Retry-After` / `RateLimit-*` headers when exceeded.
- Use a shared store (Redis) when running multiple server instances.
- Apply stricter limits to sensitive routes (login, password reset).

## 5. Syntax

```js
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,                 // limit each IP to 100 requests/window
});

app.use(limiter);
```

## 6. Example

```js
const express = require('express');
const rateLimit = require('express-rate-limit');
const app = express();

// Global limiter: 100 requests / 15 min per IP
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  standardHeaders: true,  // send RateLimit-* headers
  legacyHeaders: false,
  message: { error: 'Too many requests, try again later.' }
});
app.use(globalLimiter);

// Stricter limiter for login: 5 attempts / 15 min
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: { error: 'Too many login attempts.' }
});

app.post('/login', loginLimiter, (req, res) => {
  res.json({ message: 'Login attempt' });
});

app.listen(3000);
```

## 7. Real World Use Case

A public REST API offering a free tier limits anonymous users to 60 requests per minute by IP and authenticated users to 1,000 per minute by API key, backed by Redis so limits are consistent across its load-balanced servers. The login endpoint has a tighter limit of 5 attempts per 15 minutes to thwart credential-stuffing attacks, returning `429` with a `Retry-After` header so clients know when to retry.

## 8. Interview Questions

**Q1:** What HTTP status code indicates a rate limit has been exceeded?
**A:** `429 Too Many Requests`, usually accompanied by a `Retry-After` header telling the client when it can try again.

**Q2:** Why is a Redis store recommended for rate limiting in production?
**A:** With multiple server instances behind a load balancer, in-memory counters aren't shared, so a client could exceed the intended limit. A shared store like Redis keeps a single, consistent count across all instances.

**Q3:** How would you rate limit different routes differently?
**A:** Create multiple limiter instances with different `windowMs`/`max` and apply them per route — e.g. a strict limiter on `/login` and a looser global limiter via `app.use()`.

**Q4:** What are common strategies/algorithms for rate limiting?
**A:** Fixed window, sliding window log, sliding window counter, token bucket, and leaky bucket. Token bucket allows short bursts while enforcing an average rate.

**Q5:** Should you rate limit by IP or by user? 
**A:** It depends. IP-based limiting catches anonymous abuse but can unfairly group users behind NAT/proxies. Authenticated APIs often limit by user ID or API key for fairness; many systems combine both.

## 9. Common Mistakes

- Using default in-memory store across multiple instances, making limits inconsistent.
- Setting limits too low (blocking legitimate users) or too high (ineffective).
- Not applying stricter limits to auth endpoints.
- Trusting `req.ip` without configuring `app.set('trust proxy', ...)` behind a proxy.
- Forgetting to inform clients via `Retry-After`/`RateLimit-*` headers.

## 10. Advanced Notes

- **`trust proxy`:** Behind Nginx/Cloudflare, set it correctly so `req.ip` is the real client IP, not the proxy's.
- **Tiered limits:** Different quotas per plan (free vs paid) using key generators.
- **Token bucket** allows bursts while capping the sustained rate — good for APIs.
- **Distributed limiting:** Use `rate-limit-redis` or a gateway (Kong, API Gateway) for cross-service consistency.
- **Slow down vs block:** `express-slow-down` adds incremental delays instead of hard blocks.
- Combine with **WAF/CDN** rate limiting for defense in depth against DDoS.
