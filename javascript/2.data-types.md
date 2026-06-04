# Data Types

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition
A data type defines the kind of value a variable can hold and the operations that can be performed on it. JavaScript is dynamically typed and has two categories: **primitives** (immutable, stored by value) and **objects** (reference types, stored by reference).

## 2. Simple Explanation
Data types are the different "shapes" of information your program works with — numbers, text, true/false, lists, and so on. JavaScript figures out the type automatically based on the value you assign.

## 3. Why It Is Used
Knowing types helps you store data correctly, predict how values behave in comparisons and math, avoid bugs from unexpected coercion, and choose the right methods (e.g., string vs array methods).

## 4. Key Points
- **7 primitives:** `string`, `number`, `boolean`, `undefined`, `null`, `symbol`, `bigint`.
- Everything else is an **object** (including arrays, functions, dates).
- `typeof null` returns `"object"` — a long-standing language quirk.
- Primitives are **immutable** and compared **by value**; objects are compared **by reference**.
- `undefined` = not assigned; `null` = intentional empty value.

## 5. Syntax
```js
let str = "hello";        // string
let num = 42;             // number
let big = 9007199254740993n; // bigint
let bool = true;          // boolean
let nothing = null;       // null
let notSet;               // undefined
let id = Symbol("id");    // symbol
let obj = { a: 1 };       // object
```

## 6. Example
```js
console.log(typeof "hi");      // "string"
console.log(typeof 10);        // "number"
console.log(typeof null);      // "object"  (quirk!)
console.log(typeof undefined); // "undefined"
console.log(typeof [1, 2]);    // "object"
console.log(Array.isArray([1])); // true (proper array check)
```
`typeof` is the standard way to check primitive types, but use `Array.isArray()` for arrays.

## 7. Real World Use Case
Validating form inputs (is it a string/number?), serializing data to JSON, handling API responses where fields may be `null`, and using `bigint` for large IDs or financial calculations beyond safe integer range.

## 8. Interview Questions
**Q1:** What are the primitive types in JavaScript?
**A:** `string`, `number`, `boolean`, `undefined`, `null`, `symbol`, and `bigint`.

**Q2:** What is the difference between `null` and `undefined`?
**A:** `undefined` means a variable was declared but not assigned; `null` is an explicit "no value" assigned by the developer.

**Q3:** Why does `typeof null` return `"object"`?
**A:** It's a historical bug from the first JS implementation that was kept for backward compatibility.

**Q4:** How are primitives and objects compared?
**A:** Primitives are compared by value; objects are compared by reference (identity), not by content.

**Q5:** How do you reliably check if something is an array?
**A:** Use `Array.isArray(value)`, since `typeof` returns `"object"` for arrays.

## 9. Common Mistakes
- Relying on `typeof` to detect arrays or `null`.
- Confusing `null` and `undefined` in checks.
- Floating-point surprises like `0.1 + 0.2 !== 0.3`.
- Mutating a shared object and being surprised it changed elsewhere (reference semantics).

## 10. Advanced Notes
- Numbers are 64-bit IEEE-754 doubles; `Number.MAX_SAFE_INTEGER` is `2^53 - 1`. Use `bigint` beyond that.
- `Symbol` creates unique keys, useful for non-colliding object properties and well-known symbols (`Symbol.iterator`).
- Type coercion rules: `==` coerces types, `===` does not — always prefer `===`.
- Wrapper objects (`new String()`) exist but should be avoided; primitive auto-boxing lets you call methods on primitives.
