# Promise

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value. It exists in one of three states: **pending**, **fulfilled**, or **rejected**.

## 2. Simple Explanation
A Promise is like an order receipt. You don't have your food yet (pending), but you hold a token that will eventually turn into your meal (fulfilled) or a "sorry, sold out" message (rejected).

## 3. Why It Is Used
Promises provide a cleaner alternative to nested callbacks for async work, support chaining, centralized error handling with `.catch()`, and composition utilities like `Promise.all`.

## 4. Key Points
- States: **pending → fulfilled** or **pending → rejected** (settled, then immutable).
- `.then()` handles success, `.catch()` handles errors, `.finally()` always runs.
- Promise callbacks run as **microtasks** (before macrotasks like `setTimeout`).
- `Promise.all` (all succeed), `Promise.race` (first settles), `Promise.allSettled` (wait for all), `Promise.any` (first fulfilled).
- Chaining returns a new promise; returning a value/promise inside `.then` passes it down.

## 5. Syntax
```js
const p = new Promise((resolve, reject) => {
  if (success) resolve(value);
  else reject(error);
});

p.then(onFulfilled).catch(onRejected).finally(cleanup);
```

## 6. Example
```js
function getUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      id ? resolve({ id, name: "Ada" }) : reject(new Error("No id"));
    }, 500);
  });
}

getUser(1)
  .then((user) => console.log(user.name)) // "Ada"
  .catch((err) => console.error(err.message));
```
The promise resolves after 500ms; `.then` receives the user, `.catch` would handle any rejection.

## 7. Real World Use Case
`fetch()` returns a promise for HTTP requests, database queries, reading files, loading images, and coordinating multiple parallel requests with `Promise.all`.

## 8. Interview Questions
**Q1:** What are the states of a Promise?
**A:** Pending, fulfilled, and rejected. Once fulfilled or rejected it is settled and cannot change.

**Q2:** Difference between `Promise.all` and `Promise.allSettled`?
**A:** `all` rejects as soon as any promise rejects; `allSettled` waits for all and reports each result (fulfilled or rejected).

**Q3:** Do promise callbacks run before or after `setTimeout`?
**A:** Before — promise `.then` callbacks are microtasks and run before macrotasks like `setTimeout`.

**Q4:** What does returning a value inside `.then()` do?
**A:** It becomes the resolved value of the next promise in the chain; returning a promise waits for it to settle.

**Q5:** How do you handle errors in a promise chain?
**A:** With a single `.catch()` at the end, which catches rejections from any prior step.

## 9. Common Mistakes
- Forgetting to `return` inside `.then`, breaking the chain.
- Not adding `.catch`, leading to unhandled rejections.
- Mixing callbacks and promises inconsistently.
- Using `Promise.all` when one failure shouldn't cancel the rest (use `allSettled`).

## 10. Advanced Notes
- A promise's executor function runs synchronously; only `.then` callbacks are async (microtasks).
- `Promise.resolve(value)` wraps a value; thenables are assimilated.
- Unhandled rejections can crash Node processes (configurable).
- Promises can't be cancelled natively — use `AbortController` with `fetch` for cancellation.

### State diagram
```
            ┌──────────┐  resolve()  ┌────────────┐
 new ─────▶ │ pending  │ ──────────▶ │ fulfilled  │
            └──────────┘             └────────────┘
                  │  reject()        ┌────────────┐
                  └─────────────────▶│  rejected  │
                                     └────────────┘
```
