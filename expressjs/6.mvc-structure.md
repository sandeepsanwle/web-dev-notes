# MVC Structure

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

MVC (Model–View–Controller) is an architectural pattern that separates an application into three layers: **Models** (data and business rules), **Views** (presentation/output), and **Controllers** (request handling logic that connects the two). In an Express API, the "View" is often just JSON responses.

## 2. Simple Explanation

Think of a restaurant. The **Model** is the kitchen and pantry (data and how it's prepared). The **Controller** is the waiter who takes your order and brings food back. The **View** is the plated dish presented to you. Each has a clear job, so changing the menu doesn't require retraining the waiters.

## 3. Why It Is Used

- To keep code organized and easy to navigate as it grows.
- To separate concerns so each layer changes independently.
- To make code testable (test models/controllers in isolation).
- To enable team collaboration without stepping on each other.

## 4. Key Points

- **Routes** map URLs to controller functions.
- **Controllers** handle the request/response and orchestrate logic.
- **Models** define data structure and database interaction.
- **Services** (optional layer) hold reusable business logic.
- Keep controllers thin — push heavy logic into services/models.

### Typical Folder Structure

```
src/
├── routes/         # URL → controller mapping
├── controllers/    # request handlers
├── models/         # DB schemas / data access
├── services/       # business logic (optional)
├── middlewares/    # auth, validation, etc.
├── config/         # db config, env
└── app.js          # wires everything together
```

## 5. Syntax

```js
// route → controller → model flow
router.get('/:id', userController.getUser);

// controller calls the model
exports.getUser = async (req, res, next) => {
  const user = await User.findById(req.params.id);
  res.json(user);
};
```

## 6. Example

```js
// models/User.js
const users = [{ id: 1, name: 'Ada' }];
exports.findById = (id) => users.find(u => u.id === Number(id));

// controllers/userController.js
const User = require('../models/User');

exports.getUser = (req, res) => {
  const user = User.findById(req.params.id);
  if (!user) return res.status(404).json({ error: 'Not found' });
  res.json(user);
};

// routes/users.js
const router = require('express').Router();
const userController = require('../controllers/userController');
router.get('/:id', userController.getUser);
module.exports = router;

// app.js
const express = require('express');
const app = express();
app.use('/users', require('./routes/users'));
app.listen(3000);
```

## 7. Real World Use Case

A blogging platform organizes its API with MVC: `routes/posts.js` defines endpoints, `controllers/postController.js` validates the request and shapes responses, `models/Post.js` (a Mongoose schema) handles MongoDB queries, and `services/postService.js` contains reusable logic like slug generation and tag handling. When the team switches from MongoDB to PostgreSQL, only the model layer changes — controllers and routes stay untouched.

## 8. Interview Questions

**Q1:** What are the three components of MVC and their roles?
**A:** Model (data + business rules and DB access), View (presentation, often JSON in APIs), and Controller (handles requests, coordinates models, returns responses). The pattern separates concerns for maintainability.

**Q2:** In a REST API, what plays the role of the "View"?
**A:** The serialized response — usually JSON. There's no HTML template, so the controller formats data into a JSON payload that acts as the view.

**Q3:** Why keep controllers "thin"?
**A:** Thin controllers only handle HTTP concerns (parse request, call services, send response). Putting business logic in a service layer makes it reusable, testable, and keeps controllers readable.

**Q4:** What's the benefit of a service layer on top of MVC?
**A:** It isolates business logic from both the web layer (controllers) and data layer (models), so logic can be reused across controllers and unit-tested without HTTP or DB dependencies.

**Q5:** How does MVC improve testability?
**A:** Each layer has a single responsibility and clear interfaces, so you can unit-test models and services without spinning up the HTTP server, and mock the model when testing controllers.

## 9. Common Mistakes

- Putting business logic and DB queries directly in route files ("fat routes").
- Mixing concerns — controllers querying the DB directly with no model layer.
- Over-engineering small apps with too many layers.
- Inconsistent naming/folder conventions across the project.
- Circular dependencies between controllers, services, and models.

## 10. Advanced Notes

- **Dependency injection** makes layers swappable and easier to mock in tests.
- **Repository pattern** abstracts data access behind an interface, decoupling from a specific DB.
- **DTOs** (Data Transfer Objects) shape exactly what the API exposes, hiding internal model fields.
- For very large apps, consider **modular/feature-based** structure (group by feature, not by type).
- MVC pairs well with layered error handling and centralized validation middleware.
