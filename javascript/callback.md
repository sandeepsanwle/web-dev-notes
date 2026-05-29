# Callback

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition
A callback is a function passed as an argument to another function, to be invoked ("called back") later — either synchronously or asynchronously — once some operation completes.

## 2. Simple Explanation
A callback is like leaving your phone number with a shop: "Call me when my order is ready." You don't wait at the counter; the shop calls you back when it's done.

## 3. Why It Is Used
Callbacks let code run after an event or async task finishes (timers, network requests, file reads). They make functions reusable and customizable by injecting behavior.

## 4. Key Points
- A callback can be synchronous (e.g., `Array.map`) or asynchronous (e.g., `setTimeout`).
- They are first-class functions — passed around like values.
- Nesting many async callbacks leads to **callback hell** (pyramid of doom).
- Error-first callbacks (`(err, data) => {}`) are a Node.js convention.
- Promises and async/await were introduced to flatten callback chains.

## 5. Syntax
```js
function doTask(callback) {
  // ...do work...
  callback(result);
}
doTask(function (result) {
  console.log(result);
});
```

## 6. Example
```js
function fetchData(callback) {
  setTimeout(() => {
    callback("data ready"); // called later
  }, 1000);
}

fetchData((result) => {
  console.log(result); // "data ready" after 1s
});

[1, 2, 3].forEach((n) => console.log(n)); // synchronous callback
```
`fetchData` runs the callback after a delay; `forEach` runs its callback immediately for each item.

## 7. Real World Use Case
Event listeners (`button.addEventListener('click', handler)`), array iteration methods, Node.js file/network APIs, timers, and any "do X when Y finishes" scenario.

## 8. Interview Questions
**Q1:** What is a callback function?
**A:** A function passed into another function to be executed later, often after an asynchronous operation completes.

**Q2:** What is callback hell?
**A:** Deeply nested callbacks that make code hard to read and maintain, often shaped like a pyramid.

**Q3:** Are all callbacks asynchronous?
**A:** No. `Array.map`/`forEach` callbacks run synchronously; `setTimeout`/fetch callbacks run asynchronously.

**Q4:** What is an error-first callback?
**A:** A Node.js convention where the first argument is an error (or `null`) and subsequent arguments hold the result.

**Q5:** How do you avoid callback hell?
**A:** Use Promises, async/await, named functions, or modularization to flatten nesting.

## 9. Common Mistakes
- Forgetting to call the callback or calling it multiple times.
- Not handling errors in async callbacks.
- Deep nesting that becomes unreadable.
- Assuming async callbacks run immediately/in order.

## 10. Advanced Notes
- Callbacks invoked asynchronously go through the event loop (macrotask/microtask queues).
- `this` inside a callback depends on how it's called — arrow functions preserve the enclosing `this`.
- Promisify callback-based APIs with `util.promisify` (Node) to use async/await.
- Inversion of control: passing a callback hands control to another function, which is why Promises offer more guarantees.
