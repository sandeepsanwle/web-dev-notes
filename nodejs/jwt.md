# JWT

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **JWT (JSON Web Token)** is a compact, URL-safe, digitally signed token that securely transmits claims (information) between two parties as a JSON object. It is commonly used for stateless authentication.

## 2. Simple Explanation

A JWT is like a tamper-proof wristband at a concert. Once you've been verified at the gate, you get a wristband encoding who you are. For the rest of the event, staff just glance at the band instead of re-checking your ID. The signature makes it impossible to forge without being caught.

## 3. Why It Is Used

- **Stateless authentication**: server doesn't need to store sessions.
- **Scalable**: any server with the secret/key can verify the token.
- **Portable**: works across services, mobile apps, and APIs.
- Carries verifiable claims (user ID, roles, expiry) in the token itself.

## 4. Key Points

- A JWT has **three parts**: `header.payload.signature`, base64url-encoded, dot-separated.
- The signature verifies **integrity** — but the payload is **not encrypted** (only encoded).
- Signing uses a secret (HMAC, `HS256`) or a key pair (RSA/ECDSA, `RS256`).
- Always set an **expiry** (`exp`) and verify it on each request.
- Never put secrets/passwords in the payload — anyone can decode it.

### JWT Structure

| Part | Contains | Example fields |
|------|----------|----------------|
| Header | Token type + algorithm | `alg`, `typ` |
| Payload | Claims (data) | `sub`, `iat`, `exp`, `role` |
| Signature | Verification hash | HMAC/RSA over header+payload |

## 5. Syntax

```js
const jwt = require('jsonwebtoken');

// Sign (create) a token
const token = jwt.sign({ sub: userId, role: 'admin' }, SECRET, {
  expiresIn: '1h',
});

// Verify (and decode) a token
const payload = jwt.verify(token, SECRET); // throws if invalid/expired
```

## 6. Example

```js
const jwt = require('jsonwebtoken');
const SECRET = process.env.JWT_SECRET;

// Middleware to protect routes
function authenticate(req, res, next) {
  const header = req.headers.authorization || '';
  const token = header.startsWith('Bearer ') ? header.slice(7) : null;

  if (!token) {
    return res.status(401).json({ error: 'Missing token' });
  }

  try {
    const payload = jwt.verify(token, SECRET);
    req.user = payload; // attach decoded claims for downstream handlers
    next();
  } catch (err) {
    return res.status(401).json({ error: 'Invalid or expired token' });
  }
}

// Usage: app.get('/profile', authenticate, (req, res) => res.json(req.user));
```

## 7. Real World Use Case

A single-page app (React) talking to a Node REST API. On login the API returns a JWT. The SPA sends it in the `Authorization: Bearer <token>` header on every request. Each API server independently verifies the signature and expiry without a shared session store — so the API can scale horizontally behind a load balancer, and even a separate microservice can validate the same token.

## 8. Interview Questions

**Q1:** What are the three parts of a JWT?
**A:** Header (declares the type and signing algorithm), Payload (the claims/data like `sub`, `exp`, `role`), and Signature (a hash of the header and payload signed with a secret or private key to guarantee integrity). They are base64url-encoded and joined with dots.

**Q2:** Is the data in a JWT encrypted?
**A:** No. The header and payload are only base64url-**encoded**, not encrypted, so anyone can decode and read them. The signature only guarantees the token wasn't tampered with. Never store sensitive data like passwords in the payload.

**Q3:** How do you handle JWT expiration and revocation?
**A:** Set a short `exp` claim and verify it on each request. Since JWTs are stateless, true revocation requires extra mechanisms: a server-side blocklist of token IDs (`jti`), short-lived access tokens paired with refresh tokens, or rotating the signing secret.

**Q4:** What's the difference between `HS256` and `RS256`?
**A:** `HS256` is symmetric — the same secret signs and verifies, so every verifier must hold the secret. `RS256` is asymmetric — a private key signs and a public key verifies, so you can distribute the public key to many services without exposing the signing key.

**Q5:** Where should you store a JWT on the client and why?
**A:** Common options are `localStorage` (simple but vulnerable to XSS) or an `HttpOnly`, `Secure`, `SameSite` cookie (protected from JS access, but needs CSRF protection). HttpOnly cookies are generally safer against XSS-based token theft.

## 9. Common Mistakes

- Storing sensitive data in the payload thinking it's encrypted.
- Not validating the `exp` claim (or using overly long expiry).
- Accepting `alg: none` or not pinning the expected algorithm (algorithm confusion attack).
- Using a weak or hardcoded signing secret.
- Trying to "log out" a stateless JWT without a blocklist or refresh strategy.

## 10. Advanced Notes

- **Algorithm confusion attack**: always specify `algorithms: ['RS256']` in `verify` to stop attackers downgrading to `HS256` or `none`.
- Use **refresh token rotation**: short access tokens + longer refresh tokens stored securely, rotated on use.
- Standard registered claims: `iss`, `sub`, `aud`, `exp`, `nbf`, `iat`, `jti`.
- For confidential data, use **JWE** (JSON Web Encryption) instead of plain JWS.
- Keep payloads small — they're sent on every request and add overhead.
- Validate `aud` and `iss` to ensure the token was meant for your service.
