# Memory Leak

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
A memory leak occurs when a program retains references to memory that is no longer needed, preventing the garbage collector from reclaiming it. Over time this causes growing memory usage and degraded performance or crashes.

## 2. Simple Explanation
It's like keeping every receipt you'll never need in your wallet. The wallet (memory) keeps filling up because you never throw away what you no longer use, until there's no room left.

## 3. Why It Is Used
You don't "use" leaks — you avoid them. Understanding leaks is essential to writing apps that stay fast and stable over long sessions, especially SPAs and long-running Node services.

## 4. Key Points
- JS uses **automatic garbage collection** based on reachability.
- Leaks happen when unneeded references stay reachable.
- Common causes: lingering timers, detached DOM nodes, forgotten event listeners, accidental globals, growing caches/closures.
- Use DevTools Memory/Heap snapshots to detect them.
- `WeakMap`/`WeakSet` allow keys to be garbage collected.

## 5. Syntax
```js
// Common leak: listener never removed
function setup() {
  const handler = () => doStuff();
  window.addEventListener("resize", handler);
  // Fix: window.removeEventListener("resize", handler) when done
}
```

## 6. Example
```js
let cache = [];

function leaky() {
  const bigData = new Array(1_000_000).fill("*");
  cache.push(bigData); // keeps growing, never released → leak
}

// Better: bound the cache or use WeakMap so unused entries can be collected
const weakCache = new WeakMap();
function safe(keyObj, value) {
  weakCache.set(keyObj, value); // entry collectible when keyObj is gone
}
```
The array pushed into `cache` is never removed, so memory grows; a bounded or weak structure avoids this.

## 7. Real World Use Case
Diagnosing why a long-lived dashboard slows down, fixing memory growth in Node servers, cleaning up subscriptions/listeners in SPA components (React `useEffect` cleanup), and managing caches.

## 8. Interview Questions
**Q1:** What causes memory leaks in JavaScript?
**A:** Unintended references that keep objects reachable — uncleared timers, detached DOM nodes, unremoved listeners, accidental globals, and unbounded caches/closures.

**Q2:** How does JavaScript garbage collection decide what to free?
**A:** By reachability — objects unreachable from roots (global, stack) are eligible for collection (mark-and-sweep).

**Q3:** How do `WeakMap`/`WeakSet` help?
**A:** They hold weak references to keys, so entries can be garbage collected when no other references exist, preventing leaks.

**Q4:** How do you detect a memory leak?
**A:** Use browser DevTools heap snapshots/allocation timelines to find retained, growing objects, or monitor process memory in Node.

**Q5:** Why can closures cause leaks?
**A:** A closure keeps its referenced variables alive; if it lives long and captures large objects, that memory can't be freed.

## 9. Common Mistakes
- Forgetting to remove event listeners and intervals.
- Keeping references to detached DOM nodes.
- Accidentally creating globals (missing declaration).
- Unbounded caches/arrays that only grow.

## 10. Advanced Notes
- V8 uses generational mark-and-sweep with young/old spaces.
- Detached DOM trees are a frequent SPA leak source; null out references.
- `FinalizationRegistry` can run cleanup when objects are collected (use sparingly).
- In React, return cleanup functions from `useEffect`; in RxJS, unsubscribe observables.
