# Event Loop

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
The event loop is the mechanism that allows JavaScript — a single-threaded language — to perform non-blocking asynchronous operations by offloading tasks and processing their callbacks from queues when the call stack is empty.

## 2. Simple Explanation
JavaScript has one worker (single thread). Long tasks (timers, network) are handed off; when they finish, their callbacks wait in line. The event loop is the manager that, whenever the worker is free, picks the next task from the line and runs it.

## 3. Why It Is Used
It lets a single-threaded language stay responsive — handling I/O, timers, and user events without freezing the UI or blocking the program.

## 4. Key Points
- **Call stack:** where functions execute (one at a time).
- **Web/Node APIs:** handle async work outside the engine.
- **Macrotask queue:** `setTimeout`, `setInterval`, I/O, UI events.
- **Microtask queue:** Promise callbacks, `queueMicrotask`, `MutationObserver`.
- **Microtasks run before the next macrotask**, and the entire microtask queue is drained between macrotasks.

## 5. Syntax
```js
// Order of execution illustration:
console.log("1");                 // sync
setTimeout(() => console.log("2"), 0); // macrotask
Promise.resolve().then(() => console.log("3")); // microtask
console.log("4");                 // sync
// Output: 1, 4, 3, 2
```

## 6. Example
```js
console.log("start");

setTimeout(() => console.log("timeout"), 0);

Promise.resolve()
  .then(() => console.log("promise 1"))
  .then(() => console.log("promise 2"));

console.log("end");

// Output:
// start
// end
// promise 1
// promise 2
// timeout
```
Synchronous code runs first, then all microtasks (promises) drain, then the macrotask (`setTimeout`).

## 7. Real World Use Case
Understanding why UI updates batch a certain way, why a `setTimeout(fn, 0)` runs after promises, debugging async ordering bugs, and avoiding blocking the main thread with heavy synchronous loops.

## 8. Interview Questions
**Q1:** Is JavaScript single-threaded?
**A:** Yes, it has one call stack/main thread; concurrency comes from the event loop and host APIs offloading work.

**Q2:** What's the difference between microtasks and macrotasks?
**A:** Microtasks (promises) run after the current task and are fully drained before the next macrotask (e.g., `setTimeout`).

**Q3:** Will a `Promise.then` run before a `setTimeout(fn, 0)`?
**A:** Yes — microtasks have priority over macrotasks.

**Q4:** What happens if synchronous code blocks for a long time?
**A:** The call stack stays busy, the event loop can't process queued callbacks, and the page/app freezes.

**Q5:** When does the event loop pick up a callback?
**A:** Only when the call stack is empty; it then processes microtasks, then one macrotask, repeating.

## 9. Common Mistakes
- Assuming `setTimeout(fn, 0)` runs immediately.
- Believing JS is multi-threaded for normal code.
- Creating infinite microtask loops that starve macrotasks/rendering.
- Blocking the main thread with heavy computation (use Web Workers).

## 10. Advanced Notes
- Node.js has phases (timers, pending callbacks, poll, check, close) plus `process.nextTick` (runs before other microtasks) and `setImmediate` (check phase).
- Rendering in browsers happens between macrotasks; `requestAnimationFrame` aligns with paint.
- Starving the loop with continuous microtasks can block UI updates.
- Web Workers provide true parallelism off the main thread.

### Event loop diagram
```
   ┌─────────────┐
   │  Call Stack │◀── runs sync code
   └─────────────┘
         ▲  (empty?)
         │
   ┌─────┴───────────────┐
   │  Event Loop         │
   │  1) drain ALL       │──▶ [ Microtask Queue ]  (Promises)
   │     microtasks      │
   │  2) take ONE        │──▶ [ Macrotask Queue ]  (setTimeout, I/O)
   │     macrotask       │
   └─────────────────────┘
```
