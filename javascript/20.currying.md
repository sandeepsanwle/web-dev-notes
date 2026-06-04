# Currying

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
Currying is the technique of transforming a function that takes multiple arguments into a sequence of functions that each take a single argument. `f(a, b, c)` becomes `f(a)(b)(c)`.

## 2. Simple Explanation
Instead of asking for all ingredients at once, a curried function asks for them one at a time, remembering each until it has everything it needs to produce the result.

## 3. Why It Is Used
Currying enables partial application (pre-filling arguments), creates reusable specialized functions, improves composability, and makes code more declarative in functional programming.

## 4. Key Points
- Converts `f(a, b, c)` into `f(a)(b)(c)`.
- Relies on **closures** to remember earlier arguments.
- Enables **partial application** — fixing some arguments to make new functions.
- Each step returns a function until all arguments are collected.
- Differs from partial application but they're closely related.

## 5. Syntax
```js
const curry = (a) => (b) => (c) => a + b + c;
curry(1)(2)(3); // 6
```

## 6. Example
```js
function multiply(a) {
  return function (b) {
    return function (c) {
      return a * b * c;
    };
  };
}

console.log(multiply(2)(3)(4)); // 24

const double = multiply(2);   // partial application
const doubleTriple = double(3);
console.log(doubleTriple(5));  // 30

// Generic curry helper
const curry = (fn) =>
  function curried(...args) {
    return args.length >= fn.length
      ? fn(...args)
      : (...next) => curried(...args, ...next);
  };
```
Each call captures arguments via closures; partial application creates specialized reusable functions.

## 7. Real World Use Case
Configurable utility/logging functions, event handler factories, building reusable validators, React/Redux selectors, and functional pipelines with libraries like Ramda/Lodash FP.

## 8. Interview Questions
**Q1:** What is currying?
**A:** Transforming a multi-argument function into a chain of single-argument functions, each returning the next until all args are supplied.

**Q2:** What JavaScript feature enables currying?
**A:** Closures, which let each returned function remember previously passed arguments.

**Q3:** Difference between currying and partial application?
**A:** Currying always breaks a function into unary steps; partial application fixes some arguments and returns a function taking the rest (not necessarily one at a time).

**Q4:** Why is currying useful?
**A:** It enables reusable, specialized functions and cleaner function composition.

**Q5:** How do you write a generic curry function?
**A:** Collect arguments until their count reaches the original function's arity (`fn.length`), then invoke it.

## 9. Common Mistakes
- Confusing currying with partial application.
- Forgetting that `fn.length` ignores default/rest parameters.
- Over-currying simple functions, hurting readability.
- Losing `this` context when currying methods.

## 10. Advanced Notes
- Lodash `_.curry` supports placeholders for skipping arguments.
- Currying pairs naturally with `compose`/`pipe` for point-free style.
- Each curried step creates a closure, which has minor memory overhead.
- Variadic functions are awkward to curry because arity is unknown.
