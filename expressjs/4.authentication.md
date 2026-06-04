# Authentication

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

Authentication is the process of verifying **who** a user is — confirming their identity, typically via credentials (email/password), tokens (JWT), or third-party providers (OAuth). It answers "Are you who you claim to be?" (distinct from **authorization**, which is "What are you allowed to do?").

## 2. Simple Explanation

Authentication is like showing your ID card at a building entrance. The guard checks the card to confirm you are really you. Once verified, you get a visitor badge (a token or session) so you don't have to show ID at every door inside the building.

## 3. Why It Is Used

- To protect private routes and user-specific data.
- To identify the current user across requests (HTTP is stateless).
- To enable personalization (profiles, preferences, history).
- To form the basis for authorization and access control.

## 4. Key Points

- **Never store plain-text passwords** — hash them with `bcrypt` or `argon2`.
- Two common strategies: **session-based** (server stores state) and **token-based / JWT** (stateless).
- JWTs are signed, not encrypted — don't put secrets in the payload.
- Store tokens securely: HTTP-only cookies are safer than `localStorage` (XSS).
- Always use HTTPS so credentials/tokens aren't sniffed.

### Session vs JWT

| Aspect | Session-based | JWT (token-based) |
|--------|---------------|-------------------|
| State | Stored on server | Stateless (in token) |
| Scaling | Needs shared store (Redis) | Scales easily |
| Revocation | Easy (delete session) | Hard (needs blacklist) |
| Storage | Cookie holds session ID | Cookie or header holds token |

## 5. Syntax

```js
// Hash a password
const hash = await bcrypt.hash(plainPassword, 10);

// Verify a password
const ok = await bcrypt.compare(plainPassword, hash);

// Sign a JWT
const token = jwt.sign({ id: user.id }, SECRET, { expiresIn: '1h' });

// Verify a JWT
const payload = jwt.verify(token, SECRET);
```

## 6. Example

```js
const express = require('express');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const app = express();
app.use(express.json());

const SECRET = process.env.JWT_SECRET;
const users = []; // demo store

app.post('/register', async (req, res) => {
  const { email, password } = req.body;
  const hash = await bcrypt.hash(password, 10);
  users.push({ id: users.length + 1, email, password: hash });
  res.status(201).json({ message: 'Registered' });
});

app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = users.find(u => u.email === email);
  if (!user || !(await bcrypt.compare(password, user.password))) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  const token = jwt.sign({ id: user.id }, SECRET, { expiresIn: '1h' });
  res.json({ token });
});

// Auth middleware
function auth(req, res, next) {
  const header = req.headers.authorization || '';
  const token = header.split(' ')[1]; // "Bearer <token>"
  if (!token) return res.status(401).json({ error: 'No token' });
  try {
    req.user = jwt.verify(token, SECRET);
    next();
  } catch {
    res.status(401).json({ error: 'Invalid token' });
  }
}

app.get('/profile', auth, (req, res) => {
  res.json({ userId: req.user.id });
});

app.listen(3000);
```

## 7. Real World Use Case

A mobile banking app uses JWT authentication. On login the server returns a short-lived access token (15 min) and a long-lived refresh token (7 days, stored in an HTTP-only cookie). Each API call sends the access token in the `Authorization` header. When it expires, the client silently uses the refresh token to get a new one — keeping the user logged in securely without re-entering their password.

## 8. Interview Questions

**Q1:** What's the difference between authentication and authorization?
**A:** Authentication verifies identity ("who are you?"), while authorization determines permissions ("what can you do?"). Authentication happens first; authorization uses the verified identity to grant or deny access.

**Q2:** Why hash passwords instead of encrypting them?
**A:** Hashing is one-way — you can't reverse it to get the original password, so even a database breach doesn't reveal passwords. Use a slow, salted hash like bcrypt to resist brute-force attacks.

**Q3:** What are the trade-offs of JWT vs sessions?
**A:** JWTs are stateless and scale well but are hard to revoke before expiry. Sessions are easy to revoke but require server-side storage (often Redis) and can be harder to scale horizontally.

**Q4:** Where should you store a JWT on the client?
**A:** Preferably in an HTTP-only, Secure, SameSite cookie to mitigate XSS token theft. `localStorage` is convenient but vulnerable to XSS.

**Q5:** What is a refresh token and why use one?
**A:** A long-lived token used to obtain new short-lived access tokens without re-login. It limits the damage window if an access token leaks, since access tokens expire quickly.

## 9. Common Mistakes

- Storing passwords in plain text or with weak/fast hashes (MD5/SHA1).
- Putting sensitive data in a JWT payload (it's only base64-encoded, not encrypted).
- Using long-lived access tokens with no refresh/expiry strategy.
- Storing tokens in `localStorage` without considering XSS.
- Hard-coding the JWT secret instead of using environment variables.

## 10. Advanced Notes

- **Passport.js** provides 500+ strategies (Google, GitHub, JWT, local) with a unified API.
- **Token rotation & blacklisting:** store refresh tokens server-side to allow revocation.
- **OAuth 2.0 / OIDC:** delegate authentication to providers; never handle their passwords.
- **MFA / 2FA:** add a second factor (TOTP via `speakeasy`) for sensitive apps.
- **Timing-safe comparison:** bcrypt's compare is constant-time, preventing timing attacks.
- **Rate limit** login routes to slow brute-force attempts.
