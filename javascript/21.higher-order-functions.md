# Higher Order Functions

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
A higher-order function (HOF) is a function that takes one or more functions as arguments, returns a function, or both. They are possible because functions in JavaScript are first-class values.

## 2. Simple Explanation
A higher-order function is a function that works with other functions — either accepting them as inputs or producing them as outputs, like a machine that can take or build tools.

## 3. Why It Is Used
HOFs enable abstraction, code reuse, and composition. They power array methods, callbacks, decorators, and functional patterns, letting you write concise, declarative code.

## 4. Key Points
- Functions are **first-class** — assignable, passable, returnable.
- Examples: `map`, `filter`, `reduce`, `setTimeout`, `addEventListener`.
- A HOF either **takes** a function, **returns** a function, or both.
- Enable callbacks, currying, composition, and decorators.
- Promote DRY, declarative code.

## 5. Syntax
```js
// Takes a function:
function withLogging(fn) {
  return function (...args) {  // returns a function too
    console.log("calling with", args);
    return fn(...args);
  };
}
```

## 6. Example
```js
const numbers = [1, 2, 3, 4];

// map/filter are HOFs (they take a function)
const result = numbers
  .filter((n) => n % 2 === 0)
  .map((n) => n * 10);
console.log(result); // [20, 40]

// A HOF that returns a function
function multiplier(factor) {
  return (n) => n * factor;
}
const triple = multiplier(3);
console.log(triple(5)); // 15
```
`filter`/`map` accept functions; `multiplier` returns a customized function.

## 7. Real World Use Case
Array transformations, event handling, middleware (Express, Redux), function decorators (memoization, logging, debounce/throttle), and composing reusable behavior.

## 8. Interview Questions
**Q1:** What is a higher-order function?
**A:** A function that takes other functions as arguments and/or returns a function.

**Q2:** Why are HOFs possible in JavaScript?
**A:** Because functions are first-class citizens — they can be stored, passed, and returned like any value.

**Q3:** Give examples of built-in HOFs.
**A:** `map`, `filter`, `reduce`, `forEach`, `sort`, `setTimeout`, and `addEventListener`.

**Q4:** How do HOFs help with code reuse?
**A:** They abstract common patterns (iteration, wrapping behavior) so logic is written once and customized via passed-in functions.

**Q5:** What's an example of a HOF that returns a function?
**A:** A function factory like `multiplier(factor)` returning `n => n * factor`, or a `debounce` wrapper.

## 9. Common Mistakes
- Confusing the callback with the higher-order function itself.
- Forgetting to return the inner function.
- Overusing HOFs where a simple loop is clearer.
- Losing `this` when passing methods as callbacks.

## 10. Advanced Notes
- HOFs underpin `compose`/`pipe`, currying, and decorators.
- Middleware patterns (Redux, Express) are chains of HOFs.
- Returning closures from HOFs captures state for memoization and caching.
- Excessive function allocation in hot paths can pressure the GC; reuse where needed.
