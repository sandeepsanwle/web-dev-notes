# Authentication

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

**Authentication** is the process of verifying *who* a user is — confirming their identity, typically with credentials like a username/password, token, or third-party login, before granting access to a system.

## 2. Simple Explanation

Authentication is like showing your ID card to enter a building. The guard checks that you are who you claim to be. (Authorization, a separate step, then decides which rooms you're allowed into.) In web apps, the server checks your credentials and, if valid, remembers you for future requests.

## 3. Why It Is Used

- Protect private data and actions from unauthorized users.
- Identify users to personalize their experience.
- Provide accountability and audit trails (who did what).
- Foundation for authorization, billing, and user-specific features.

## 4. Key Points

- **Authentication = who you are; Authorization = what you can do.**
- Common strategies: session-based, token-based (JWT), and OAuth/OpenID.
- **Never store plaintext passwords** — hash them with `bcrypt`/`argon2`.
- Sessions are **stateful** (server stores session); JWTs are **stateless**.
- Always transmit credentials over **HTTPS**.

### Session vs Token Authentication

| Aspect | Session-based | Token-based (JWT) |
|--------|---------------|-------------------|
| State | Stored on server | Stored on client |
| Scalability | Needs shared store | Easily horizontal |
| Revocation | Easy (delete session) | Hard (need blocklist) |
| Best for | Traditional web apps | APIs, mobile, SPAs |

## 5. Syntax

```js
const bcrypt = require('bcrypt');

// Hash a password during signup
const hash = await bcrypt.hash(plainPassword, 12); // 12 = salt rounds

// Verify a password during login
const isValid = await bcrypt.compare(plainPassword, hash);
```

## 6. Example

```js
const express = require('express');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');

const app = express();
app.use(express.json());

const users = []; // demo store (use a real DB in production)
const SECRET = process.env.JWT_SECRET;

// Signup: hash the password before storing
app.post('/signup', async (req, res) => {
  const { email, password } = req.body;
  const passwordHash = await bcrypt.hash(password, 12);
  users.push({ email, passwordHash });
  res.status(201).json({ message: 'User created' });
});

// Login: verify password, issue a token
app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = users.find((u) => u.email === email);
  if (!user || !(await bcrypt.compare(password, user.passwordHash))) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  const token = jwt.sign({ sub: email }, SECRET, { expiresIn: '1h' });
  res.json({ token });
});

app.listen(3000);
```

## 7. Real World Use Case

A SaaS dashboard. Users sign up with email + password; the password is hashed with bcrypt and stored. On login, the server verifies the hash and returns a JWT, which the SPA stores and sends in the `Authorization` header on every API call. The app also offers "Sign in with Google" (OAuth 2.0) so users can authenticate without creating a new password.

## 8. Interview Questions

**Q1:** What is the difference between authentication and authorization?
**A:** Authentication verifies **identity** ("who are you?") via credentials. Authorization determines **permissions** ("what are you allowed to do?") after identity is confirmed. You authenticate first, then authorize each action.

**Q2:** Why must passwords be hashed and not encrypted or stored as plaintext?
**A:** Hashing is one-way, so even if the database leaks, attackers can't recover the original passwords. Encryption is reversible (a key could be stolen). Use slow, salted hashes like `bcrypt` or `argon2` to resist brute-force and rainbow-table attacks.

**Q3:** What is the difference between session-based and token-based authentication?
**A:** Session-based auth stores session state on the server and gives the client a session ID cookie — it's stateful and easy to revoke. Token-based auth (JWT) stores a signed token on the client; it's stateless and scales well but is harder to revoke before expiry.

**Q4:** What is a salt and why is it used in password hashing?
**A:** A salt is a unique random value added to each password before hashing. It ensures two identical passwords produce different hashes, defeating precomputed rainbow-table attacks. `bcrypt` generates and embeds the salt automatically.

**Q5:** How does OAuth 2.0 fit into authentication?
**A:** OAuth 2.0 is an **authorization** framework often used for delegated login ("Sign in with Google"). The app receives an access token from the provider on the user's behalf; combined with OpenID Connect, it also provides verified identity information.

## 9. Common Mistakes

- Storing passwords in plaintext or with fast hashes like MD5/SHA-1.
- Rolling your own crypto instead of using vetted libraries.
- Sending credentials/tokens over HTTP instead of HTTPS.
- Storing JWTs in `localStorage` (XSS-exposed) without considering risks.
- Leaking whether the email or the password was wrong (account enumeration).

## 10. Advanced Notes

- Use **refresh tokens** (long-lived, stored in HttpOnly cookies) plus short-lived access tokens to balance security and UX.
- Add **MFA/2FA** (TOTP, WebAuthn/passkeys) for stronger assurance.
- Rate-limit and add account lockout/backoff to slow brute-force attacks.
- Prefer `argon2id` (memory-hard) over bcrypt for new systems where available.
- For revocation with JWTs, maintain a token blocklist or use short expiry + refresh rotation.
- Set cookies with `HttpOnly`, `Secure`, and `SameSite` to mitigate XSS/CSRF.
