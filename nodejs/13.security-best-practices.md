# Security Best Practices

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

**Security best practices** in Node.js are the set of techniques and habits used to protect applications from common vulnerabilities — such as injection, broken authentication, data exposure, and dependency risks — across code, configuration, and deployment.

## 2. Simple Explanation

Securing a Node.js app is like protecting a house: lock the doors (validate input), don't hand out master keys (least privilege), check who comes in (authentication), patch broken windows (update dependencies), and don't leave valuables in plain sight (no secrets in code). Many small precautions together keep attackers out.

## 3. Why It Is Used

- Prevent data breaches, financial loss, and reputational damage.
- Protect users' sensitive data (passwords, payment info).
- Meet compliance requirements (GDPR, PCI-DSS, HIPAA).
- Maintain availability and integrity against attacks.

## 4. Key Points

- **Validate and sanitize all input** — never trust the client.
- Use **HTTPS/TLS** everywhere; never send credentials over plain HTTP.
- Store secrets in **environment variables / secret managers**, not in code.
- Keep dependencies updated; run `npm audit`.
- Apply **least privilege** to DB users, file permissions, and tokens.
- Set secure HTTP headers with **helmet** and limit abuse with **rate limiting**.

### Common Threats and Mitigations

| Threat | Mitigation |
|--------|------------|
| SQL/NoSQL Injection | Parameterized queries / ORM |
| XSS | Output encoding, CSP, sanitize input |
| CSRF | CSRF tokens, `SameSite` cookies |
| Brute force | Rate limiting, account lockout |
| Insecure deps | `npm audit`, regular updates |
| Secret leakage | Env vars, secret managers, `.gitignore` |

## 5. Syntax

```js
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');

app.use(helmet()); // secure HTTP headers

app.use(
  rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100,                 // limit each IP to 100 requests/window
  })
);

// Parameterized query (prevents SQL injection)
db.query('SELECT * FROM users WHERE email = $1', [email]);
```

## 6. Example

```js
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');

const app = express();

// 1. Secure headers
app.use(helmet());

// 2. Body size limit (mitigates DoS via huge payloads)
app.use(express.json({ limit: '10kb' }));

// 3. Rate limiting on auth routes
const loginLimiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 5 });

// 4. Input validation before use
app.post('/login', loginLimiter, (req, res) => {
  const { email, password } = req.body;
  if (typeof email !== 'string' || typeof password !== 'string') {
    return res.status(400).json({ error: 'Invalid input' });
  }
  // ... use parameterized DB query + bcrypt.compare ...
  res.json({ ok: true });
});

// 5. Never leak stack traces in production
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ error: 'Internal Server Error' });
});

app.listen(3000);
```

## 7. Real World Use Case

A fintech API handling money must layer defenses: TLS for all traffic, `helmet` for headers, parameterized queries to block SQL injection, bcrypt-hashed passwords, JWTs with short expiry, rate limiting and account lockout on login, secrets pulled from a vault (not the repo), strict input validation with a schema library (Joi/Zod), and automated `npm audit` in CI to catch vulnerable dependencies before deploy.

## 8. Interview Questions

**Q1:** How do you prevent SQL/NoSQL injection in Node.js?
**A:** Never build queries by concatenating user input. Use **parameterized queries** / prepared statements or an ORM/ODM (Sequelize, Prisma, Mongoose) that escapes values. For MongoDB, also sanitize input to strip operators (`$gt`, `$ne`) and validate types.

**Q2:** Where should you store secrets like API keys and DB passwords?
**A:** In environment variables loaded from a `.env` file (git-ignored) for local dev, and in a dedicated **secret manager** (AWS Secrets Manager, Vault) in production. Never hardcode secrets in source code or commit them to version control.

**Q3:** What does the `helmet` middleware do?
**A:** It sets a collection of security-related HTTP headers — like `Content-Security-Policy`, `X-Content-Type-Options`, `Strict-Transport-Security`, and `X-Frame-Options` — to mitigate attacks such as XSS, clickjacking, and MIME sniffing with sensible defaults.

**Q4:** How do you protect against brute-force and DoS attacks?
**A:** Apply **rate limiting** (e.g. `express-rate-limit`) per IP/account, add account lockout/backoff after failed logins, limit request body size, set timeouts, and put the app behind a reverse proxy/WAF or CDN that can absorb and filter traffic.

**Q5:** Why is `npm audit` important and how do you handle vulnerable dependencies?
**A:** `npm audit` scans your dependency tree against a vulnerability database and reports known issues. You handle them with `npm audit fix`, upgrading packages, replacing unmaintained libraries, and integrating audits into CI so vulnerable dependencies block deployment.

## 9. Common Mistakes

- Trusting client input without validation/sanitization.
- Committing secrets (`.env`, keys) to git.
- Returning detailed error messages/stack traces to clients.
- Running the app/DB as a privileged (root/admin) user.
- Ignoring `npm audit` warnings and outdated dependencies.
- Disabling TLS certificate verification for convenience.

## 10. Advanced Notes

- Follow the **OWASP Top 10** as a checklist (injection, broken auth, etc.).
- Add a **Content Security Policy (CSP)** to limit script sources and reduce XSS impact.
- Use **HSTS** to force HTTPS and prevent protocol downgrade.
- Avoid prototype pollution: validate/whitelist object keys and use `Object.create(null)` or `Map` for untrusted data.
- Run containers with read-only filesystems, non-root users, and minimal base images.
- Implement **logging, monitoring, and alerting** to detect intrusions, plus dependency scanning (Snyk/Dependabot) and SAST in CI.
- Set secure cookies (`HttpOnly`, `Secure`, `SameSite`) and rotate/expire tokens.
