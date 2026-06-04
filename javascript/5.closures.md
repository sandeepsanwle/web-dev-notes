# Closures

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
A closure is a function bundled together with references to its surrounding lexical environment. It gives an inner function access to an outer function's variables even after the outer function has finished executing.

## 2. Simple Explanation
A closure is like a backpack a function carries. When a function is created inside another, it packs the variables it needs into its backpack and keeps them, even after the outer function is gone.

## 3. Why It Is Used
Closures enable data privacy/encapsulation, stateful functions (counters, memoization), function factories, callbacks that remember context, and the module pattern.

## 4. Key Points
- Closures are created **every time a function is created**.
- They capture variables **by reference**, not by copy.
- They keep outer variables alive (prevent garbage collection) as long as the closure exists.
- Common in callbacks, event handlers, and async code.
- Each call to an outer function creates a fresh, independent closure.

## 5. Syntax
```js
function outer() {
  let captured = 0;
  return function inner() {  // inner closes over `captured`
    captured++;
    return captured;
  };
}
```

## 6. Example
```js
function makeCounter() {
  let count = 0;             // private state
  return () => ++count;      // closure over count
}

const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2

const another = makeCounter();
console.log(another()); // 1 — independent closure
```
`count` is private; only the returned function can change it, and each counter has its own copy.

## 7. Real World Use Case
Private variables in modules, debouncing/throttling (remembering timers), React hooks (`useState` relies on closures), memoization caches, and event handlers that need to remember setup data.

## 8. Interview Questions
**Q1:** What is a closure?
**A:** A function plus a reference to its lexical environment, allowing it to access outer-scope variables after the outer function has returned.

**Q2:** Do closures copy or reference outer variables?
**A:** They hold a live reference, so they see the latest value of captured variables.

**Q3:** Give a practical use of closures.
**A:** Creating private state, like a counter or a memoization cache, that can't be accessed directly from outside.

**Q4:** Why does a `var` loop with a `setTimeout` log the same final value?
**A:** All callbacks close over the same single `var` binding; by the time they run, the loop has finished. Using `let` creates a new binding per iteration.

**Q5:** Can closures cause memory leaks?
**A:** Yes — if a closure holds references to large objects longer than needed, they can't be garbage collected.

## 9. Common Mistakes
- The classic `var` + loop + `setTimeout` bug (use `let` or an IIFE).
- Unintentionally retaining large objects, causing memory leaks.
- Assuming captured values are snapshots instead of live references.
- Overusing closures where a simple parameter would do.

## 10. Advanced Notes
- The engine only keeps the variables a closure actually references (closure optimization), though implementations vary.
- Module pattern and IIFEs use closures to emulate private members.
- Closures + currying enable partial application.
- In hot loops, excessive closure creation can pressure the GC — reuse functions when possible.

### Loop closure fix
```js
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i)); // 0 1 2
for (var j = 0; j < 3; j++) setTimeout(() => console.log(j)); // 3 3 3
```
