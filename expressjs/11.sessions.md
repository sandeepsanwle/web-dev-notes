# Sessions

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A session is a server-side mechanism for storing per-user data across multiple requests. The server keeps the data (e.g. in memory, Redis, or a database) and gives the browser only a **session ID** in a cookie, which links each request back to its stored data.

## 2. Simple Explanation

A session is like a hotel front desk keeping a guest file. You carry a room key (session ID cookie), but all your details — bookings, charges, preferences — stay in the hotel's records. Each time you show your key, the staff pull up your file. The key itself holds nothing sensitive.

## 3. Why It Is Used

- To keep users logged in across requests without resending credentials.
- To store per-user state (cart, flash messages, wizard progress) server-side.
- To keep sensitive data on the server, not the client.
- To enable easy logout/revocation by destroying the session.

## 4. Key Points

- Implemented with `express-session`; the client only stores the session ID.
- The default `MemoryStore` is for development only — use Redis/DB in production.
- Configure a strong `secret`, and secure cookie options (`httpOnly`, `secure`, `sameSite`).
- Sessions are stateful — revoking is as easy as deleting the server record.
- Session data lives at `req.session`.

### Cookie vs Session

| Aspect | Cookie (client storage) | Session (server storage) |
|--------|--------------------------|---------------------------|
| Where data lives | Browser | Server (store) |
| Size limit | ~4 KB | Large (store-dependent) |
| Security | Visible to client | Hidden from client |
| Holds | Actual data | Just a session ID |
| Revocation | Wait for expiry | Delete server record |

## 5. Syntax

```js
const session = require('express-session');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: { httpOnly: true, secure: true, maxAge: 3600000 }
}));

req.session.userId = 1;   // store
const id = req.session.userId; // read
req.session.destroy();    // logout
```

## 6. Example

```js
const express = require('express');
const session = require('express-session');
const app = express();
app.use(express.json());

app.use(session({
  secret: process.env.SESSION_SECRET || 'dev-secret',
  resave: false,
  saveUninitialized: false,
  cookie: { httpOnly: true, sameSite: 'lax', maxAge: 60 * 60 * 1000 }
}));

app.post('/login', (req, res) => {
  // (validate credentials first in real apps)
  req.session.userId = 42;
  res.json({ message: 'Logged in' });
});

app.get('/profile', (req, res) => {
  if (!req.session.userId) {
    return res.status(401).json({ error: 'Not logged in' });
  }
  res.json({ userId: req.session.userId });
});

app.post('/logout', (req, res) => {
  req.session.destroy(() => res.json({ message: 'Logged out' }));
});

app.listen(3000);
```

Production store example (Redis):

```js
const RedisStore = require('connect-redis').default;
const { createClient } = require('redis');
const redisClient = createClient();
redisClient.connect();

app.use(session({
  store: new RedisStore({ client: redisClient }),
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false
}));
```

## 7. Real World Use Case

A traditional server-rendered admin dashboard uses sessions for login. After authentication, the user's ID and role are stored in `req.session`, backed by Redis so all app instances share session state. The browser holds only an `httpOnly` session cookie. Logging out (or an admin force-logging out a user) simply destroys the Redis record, immediately invalidating access — something stateless JWTs can't do as cleanly.

## 8. Interview Questions

**Q1:** How do sessions differ from JWTs?
**A:** Sessions store state on the server and send only an ID to the client (stateful, easily revocable). JWTs store the data in a signed token on the client (stateless, scales easily, but hard to revoke before expiry).

**Q2:** Why is the default `MemoryStore` not suitable for production?
**A:** It leaks memory over time, doesn't scale across multiple processes/servers (each has its own memory), and loses all sessions on restart. Use Redis or a database-backed store instead.

**Q3:** What does the session `secret` do?
**A:** It signs the session ID cookie so the server can detect tampering. A strong, secret value prevents attackers from forging valid session IDs.

**Q4:** What's the difference between `saveUninitialized` and `resave`?
**A:** `saveUninitialized: false` avoids storing empty sessions (good for compliance and storage). `resave: false` avoids rewriting unchanged sessions on every request, improving performance. Both are commonly set to false.

**Q5:** How do you log a user out with sessions?
**A:** Call `req.session.destroy()` to remove the session from the store and clear the cookie. Because state is server-side, the session is immediately invalid.

## 9. Common Mistakes

- Using `MemoryStore` in production (memory leaks, no scaling).
- Weak or hard-coded session secrets.
- Not setting `httpOnly`/`secure`/`sameSite` on the session cookie.
- Storing large objects in the session, bloating the store.
- Setting `saveUninitialized: true`, creating sessions for anonymous visitors needlessly.

## 10. Advanced Notes

- **Session fixation:** Regenerate the session ID on login (`req.session.regenerate`) to prevent fixation attacks.
- **Sliding expiration:** Use `rolling: true` to reset the cookie maxAge on each response.
- **Scaling:** A shared store (Redis) is required behind a load balancer; sticky sessions are a fragile alternative.
- **Hybrid auth:** Some apps combine sessions (web) with tokens (mobile/API).
- **Concurrency:** Be careful with parallel requests mutating the same session.
- Clean up expired sessions (TTL in Redis handles this automatically).
