# Cookies

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A cookie is a small piece of data the server sends to the browser via the `Set-Cookie` header. The browser stores it and automatically sends it back on subsequent requests to the same domain, letting the (stateless) server remember information about the client.

## 2. Simple Explanation

A cookie is like a coat-check ticket. When you enter, you get a small ticket (cookie) to keep. Every time you return to the counter, you hand over the ticket so the staff know which coat (data/session) is yours — without you having to re-explain who you are each time.

## 3. Why It Is Used

- To maintain state across stateless HTTP requests.
- To store session identifiers for logged-in users.
- To remember preferences (theme, language).
- To support tracking/analytics and "remember me" features.

## 4. Key Points

- Set with `res.cookie(name, value, options)`; read with `req.cookies` (needs `cookie-parser`).
- Key options: `httpOnly`, `secure`, `sameSite`, `maxAge`/`expires`, `domain`, `path`.
- `httpOnly` cookies are inaccessible to JavaScript — protects against XSS theft.
- `secure` ensures the cookie is only sent over HTTPS.
- Cookies have a ~4 KB size limit and are sent with **every** matching request.

## 5. Syntax

```js
const cookieParser = require('cookie-parser');
app.use(cookieParser());

res.cookie('name', 'value', {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 24 * 60 * 60 * 1000 // 1 day
});

const value = req.cookies.name;   // read
res.clearCookie('name');          // delete
```

## 6. Example

```js
const express = require('express');
const cookieParser = require('cookie-parser');
const app = express();
app.use(cookieParser());

// Set a cookie
app.get('/set', (req, res) => {
  res.cookie('theme', 'dark', {
    httpOnly: true,
    maxAge: 7 * 24 * 60 * 60 * 1000 // 7 days
  });
  res.send('Cookie set');
});

// Read a cookie
app.get('/get', (req, res) => {
  res.json({ theme: req.cookies.theme || 'default' });
});

// Clear a cookie
app.get('/logout', (req, res) => {
  res.clearCookie('theme');
  res.send('Cookie cleared');
});

app.listen(3000);
```

## 7. Real World Use Case

An online store remembers a guest user's shopping cart using a cookie holding a cart ID. Even if the user closes the browser and returns days later, the cookie is sent back, the server looks up the cart by ID, and the items are still there — no login required. For logged-in users, an `httpOnly`, `secure`, `sameSite=strict` cookie stores the session ID safely.

## 8. Interview Questions

**Q1:** What does the `httpOnly` flag do?
**A:** It makes the cookie inaccessible to client-side JavaScript (`document.cookie`), which helps prevent it from being stolen via XSS attacks. It's essential for session/auth cookies.

**Q2:** What is the purpose of the `SameSite` attribute?
**A:** It controls whether cookies are sent on cross-site requests. `Strict` blocks all cross-site sending, `Lax` allows top-level navigations, and `None` (requires `Secure`) allows all — it's a key defense against CSRF.

**Q3:** How do cookies help with a stateless protocol like HTTP?
**A:** HTTP doesn't remember previous requests. Cookies let the server store a small identifier on the client that is automatically returned each request, so the server can associate requests with the same user/session.

**Q4:** What's the difference between a session cookie and a persistent cookie?
**A:** A session cookie has no `expires`/`maxAge` and is deleted when the browser closes. A persistent cookie has an expiry and survives browser restarts until it expires or is cleared.

**Q5:** Why use `cookie-parser` in Express?
**A:** Express doesn't parse the `Cookie` request header by default. `cookie-parser` middleware populates `req.cookies` (and `req.signedCookies`) so you can read them easily.

## 9. Common Mistakes

- Storing sensitive data (passwords, tokens) directly in non-`httpOnly` cookies.
- Forgetting `secure`/`sameSite`, leaving cookies vulnerable to interception/CSRF.
- Not using `cookie-parser`, so `req.cookies` is undefined.
- Exceeding the ~4 KB limit or overusing cookies (sent on every request → overhead).
- Setting cookies without `httpOnly` for auth, exposing them to XSS.

## 10. Advanced Notes

- **Signed cookies:** `cookie-parser('secret')` lets you detect tampering via `req.signedCookies`.
- **Encrypted cookies:** For storing data client-side, encrypt the value (e.g. `cookie-encryption`).
- **`__Host-` / `__Secure-` prefixes:** Enforce secure attributes at the browser level.
- **Partitioned cookies (CHIPS):** Newer privacy feature isolating third-party cookies per top-level site.
- **Domain scope:** A cookie set on `.example.com` is shared across subdomains — scope carefully.
- Cookies vs tokens-in-storage: cookies auto-send (CSRF risk) but resist XSS when `httpOnly`.
