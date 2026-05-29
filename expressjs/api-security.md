# API Security

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

API security is the set of practices and tools used to protect an Express API from unauthorized access, data leaks, and attacks. It spans secure headers, authentication/authorization, input validation, rate limiting, transport encryption, and protection against common web vulnerabilities (OWASP Top 10).

## 2. Simple Explanation

Securing an API is like protecting a building: you lock the doors (authentication), check IDs at each room (authorization), install cameras and alarms (logging/monitoring), reinforce the walls (secure headers), and limit how many people can rush in at once (rate limiting). No single lock is enough — defense in depth combines many layers.

## 3. Why It Is Used

- To protect user data and maintain privacy/compliance (GDPR, etc.).
- To prevent breaches, data theft, and account takeovers.
- To defend against common attacks: injection, XSS, CSRF, DoS.
- To build user trust and meet security standards.

## 4. Key Points

- Use `helmet` to set secure HTTP headers.
- Always serve over **HTTPS/TLS**.
- Validate and sanitize **all** input; never trust the client.
- Authenticate, then authorize (least privilege).
- Rate limit, log, and keep dependencies patched (`npm audit`).
- Store secrets in environment variables, never in code.

### Common Threats & Defenses

| Threat | Defense |
|--------|---------|
| Injection (SQL/NoSQL) | Parameterized queries, validation |
| XSS | Output encoding, sanitization, CSP |
| CSRF | SameSite cookies, CSRF tokens |
| Brute force / DoS | Rate limiting, account lockout |
| Data exposure | HTTPS, minimal responses, RBAC |
| Insecure headers | `helmet` |

## 5. Syntax

```js
const helmet = require('helmet');
app.use(helmet());                 // secure headers
app.use(express.json({ limit: '10kb' })); // limit body size
app.disable('x-powered-by');       // hide framework
```

## 6. Example

```js
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const rateLimit = require('express-rate-limit');
const app = express();

app.use(helmet());                          // 1. secure headers
app.use(express.json({ limit: '10kb' }));   // 2. cap body size
app.use(cors({ origin: 'https://myapp.com', credentials: true })); // 3. restrict origins
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 })); // 4. rate limit
app.disable('x-powered-by');                // 5. hide Express

// Authentication middleware
function auth(req, res, next) {
  const token = (req.headers.authorization || '').split(' ')[1];
  if (!token) return res.status(401).json({ error: 'Unauthorized' });
  // verify token... then attach req.user
  next();
}

// Authorization (role check)
function requireRole(role) {
  return (req, res, next) =>
    req.user?.role === role ? next() : res.status(403).json({ error: 'Forbidden' });
}

app.delete('/admin/users/:id', auth, requireRole('admin'), (req, res) => {
  res.json({ message: 'User deleted' });
});

// Centralized error handler (no stack leaks)
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ error: 'Internal Server Error' });
});

app.listen(3000);
```

## 7. Real World Use Case

A fintech API layers its defenses: TLS everywhere, `helmet` for secure headers, JWT auth with short-lived tokens, role-based authorization on every sensitive endpoint, `zod` validation on all inputs, parameterized DB queries to block injection, `express-rate-limit` (Redis-backed) on auth routes, structured audit logging, and weekly `npm audit` + Dependabot updates. Secrets live in a vault, and responses expose only the minimum fields needed — so a single failure never compromises the whole system.

## 8. Interview Questions

**Q1:** What is `helmet` and why use it?
**A:** `helmet` is middleware that sets security-related HTTP headers (like `X-Content-Type-Options`, `Strict-Transport-Security`, and a Content-Security-Policy). It hardens the app against common attacks with sensible defaults.

**Q2:** How do you prevent injection attacks in an Express API?
**A:** Never build queries by concatenating user input. Use parameterized queries/prepared statements (or an ORM), validate and sanitize input, and apply least-privilege DB accounts.

**Q3:** What's the difference between authentication and authorization in security terms?
**A:** Authentication verifies identity; authorization enforces what an authenticated user may do. A secure API does both — verify the token, then check roles/permissions (least privilege) on each protected action.

**Q4:** How do you protect against CSRF in Express?
**A:** Use `SameSite` (Strict/Lax) cookies, anti-CSRF tokens for state-changing requests, and verify the `Origin`/`Referer` headers. Token-in-header auth (not cookies) is also inherently less CSRF-prone.

**Q5:** Why should you limit request body size and hide `x-powered-by`?
**A:** Capping body size (`express.json({ limit })`) mitigates DoS via huge payloads. Disabling `x-powered-by` removes a fingerprint that tells attackers you're running Express, reducing targeted exploits (security through reduced disclosure).

## 9. Common Mistakes

- Returning detailed errors/stack traces to clients in production.
- Trusting client input without validation/sanitization.
- Hard-coding secrets or committing `.env` files to git.
- Running without HTTPS or with outdated, vulnerable dependencies.
- Over-permissive CORS (`origin: '*'` with credentials) or missing rate limiting.

## 10. Advanced Notes

- **OWASP Top 10 / API Top 10:** Use these as a checklist (e.g. Broken Object Level Authorization — always verify the user owns the resource).
- **Secrets management:** Use a vault (AWS Secrets Manager, HashiCorp Vault) and rotate keys.
- **Security headers via CSP:** Tune Content-Security-Policy to block inline scripts.
- **Dependency hygiene:** Automate `npm audit`, Snyk, and Dependabot.
- **Logging & monitoring:** Centralize logs, alert on anomalies, never log secrets/PII.
- **Defense in depth:** Combine WAF, gateway auth, mTLS for service-to-service, and least-privilege IAM.
