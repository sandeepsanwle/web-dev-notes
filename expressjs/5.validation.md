# Validation

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Validation is the process of checking that incoming request data (body, params, query, headers) is present, correctly typed, and within allowed constraints **before** it reaches business logic — rejecting bad input early with a clear error.

## 2. Simple Explanation

Validation is like a bouncer checking a guest list at a club. Before anyone gets in, the bouncer makes sure their name is on the list and their ID is valid. Similarly, validation ensures the data sent to your API is complete and correct before your code tries to use it.

## 3. Why It Is Used

- To protect against malformed or malicious input.
- To return helpful error messages to clients.
- To prevent corrupt data from reaching the database.
- To reduce bugs caused by unexpected data shapes.

## 4. Key Points

- Validate **all** untrusted input: body, query, params, headers, files.
- Prefer schema-based libraries: `zod`, `joi`, or `express-validator`.
- Validation is often implemented as middleware.
- **Sanitize** (clean/normalize) in addition to validating.
- Never trust client-side validation alone — always validate on the server.

## 5. Syntax

```js
// express-validator
const { body, validationResult } = require('express-validator');

const rules = [
  body('email').isEmail(),
  body('age').isInt({ min: 18 })
];

function validate(req, res, next) {
  const errors = validationResult(req);
  if (!errors.isEmpty()) return res.status(400).json({ errors: errors.array() });
  next();
}
```

## 6. Example

Using **express-validator**:

```js
const express = require('express');
const { body, validationResult } = require('express-validator');
const app = express();
app.use(express.json());

app.post(
  '/register',
  [
    body('email').isEmail().withMessage('Valid email required'),
    body('password').isLength({ min: 8 }).withMessage('Min 8 chars'),
    body('age').optional().isInt({ min: 18 }).withMessage('Must be 18+')
  ],
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    res.status(201).json({ message: 'User created' });
  }
);

app.listen(3000);
```

Using **Zod** (schema-based):

```js
const { z } = require('zod');

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  age: z.number().int().min(18).optional()
});

app.post('/register', (req, res) => {
  const result = schema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json({ errors: result.error.flatten() });
  }
  res.status(201).json({ data: result.data });
});
```

## 7. Real World Use Case

A signup form on a SaaS platform sends email, password, and plan to `POST /signup`. The validation middleware confirms the email is well-formed and unique, the password meets strength rules, and the plan is one of `["free", "pro", "enterprise"]`. If anything fails, the API returns a `400` with field-level messages so the frontend can highlight exactly which inputs are wrong — preventing invalid accounts from ever being created.

## 8. Interview Questions

**Q1:** Why validate on the server if the frontend already validates?
**A:** Client-side validation improves UX but can be bypassed (DevTools, direct API calls, scripts). The server is the only trusted boundary, so it must always validate to ensure data integrity and security.

**Q2:** What's the difference between validation and sanitization?
**A:** Validation checks whether data meets rules (e.g. is it a valid email?). Sanitization cleans/normalizes data (e.g. trimming whitespace, escaping HTML, lowercasing emails). They're often used together.

**Q3:** How do you typically structure validation in Express?
**A:** As middleware that runs before the route handler — it checks the request, collects errors, and either responds with `400` or calls `next()` to proceed.

**Q4:** What HTTP status code should invalid input return?
**A:** `400 Bad Request` for malformed/invalid data, or `422 Unprocessable Entity` when the syntax is fine but semantics fail. `400` is the most common choice.

**Q5:** Name some popular validation libraries and a key difference.
**A:** `express-validator` (middleware-based, chainable), `joi` (standalone schema objects), and `zod` (TypeScript-first with type inference). Zod gives you static types from schemas; express-validator integrates tightly with Express middleware.

## 9. Common Mistakes

- Relying only on frontend validation.
- Validating the body but ignoring `req.params` and `req.query`.
- Returning vague errors like "Invalid input" instead of field-level messages.
- Not sanitizing input, allowing stored XSS or inconsistent data.
- Forgetting `express.json()`, so `req.body` is `undefined`.

## 10. Advanced Notes

- **Reusable schemas:** Define schemas once and share across routes/services; with Zod you also get TypeScript types via `z.infer`.
- **Centralized error formatting:** A single middleware can convert validator errors into a consistent API error shape.
- **Coercion:** Query/params are strings — coerce to numbers/booleans (`z.coerce.number()`).
- **Conditional validation:** Validate fields based on others (e.g. require `cardNumber` only if `paymentMethod === 'card'`).
- **Performance:** Compile schemas once at module load, not per request.
