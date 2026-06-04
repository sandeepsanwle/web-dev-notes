# Array Methods

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition
Array methods are built-in functions on `Array.prototype` for creating, transforming, searching, and iterating over arrays. They fall into mutating methods (change the original) and non-mutating methods (return a new value/array).

## 2. Simple Explanation
Arrays are lists, and array methods are ready-made tools to work with those lists — add, remove, find, loop, transform — without writing manual loops every time.

## 3. Why It Is Used
They make data manipulation concise, readable, and expressive. Chaining methods like `filter().map()` replaces verbose loops and reduces bugs.

## 4. Key Points
- **Mutating:** `push`, `pop`, `shift`, `unshift`, `splice`, `sort`, `reverse`.
- **Non-mutating:** `map`, `filter`, `reduce`, `slice`, `concat`, `flat`, `flatMap`.
- **Searching:** `find`, `findIndex`, `includes`, `indexOf`, `some`, `every`.
- `map`/`filter` return new arrays; `forEach` returns `undefined`.
- `sort` converts to strings by default — pass a comparator for numbers.

## 5. Syntax
```js
arr.map(fn);          // transform each element → new array
arr.filter(fn);       // keep elements where fn is truthy
arr.reduce(fn, init); // fold into single value
arr.find(fn);         // first matching element
arr.includes(value);  // boolean membership check
```

## 6. Example
```js
const nums = [1, 2, 3, 4, 5];

const doubled = nums.map((n) => n * 2);        // [2,4,6,8,10]
const evens = nums.filter((n) => n % 2 === 0); // [2,4]
const sum = nums.reduce((acc, n) => acc + n, 0); // 15
const found = nums.find((n) => n > 3);         // 4

console.log(doubled, evens, sum, found);
```
Each method is non-mutating here, leaving `nums` unchanged while producing new results.

## 7. Real World Use Case
Transforming API data into UI models (`map`), filtering search results, summing cart totals (`reduce`), checking permissions (`some`/`every`), and deduplicating or sorting lists.

## 8. Interview Questions
**Q1:** Difference between `map` and `forEach`?
**A:** `map` returns a new transformed array; `forEach` returns `undefined` and is used for side effects.

**Q2:** Which array methods mutate the original array?
**A:** `push`, `pop`, `shift`, `unshift`, `splice`, `sort`, and `reverse`.

**Q3:** Difference between `slice` and `splice`?
**A:** `slice` returns a shallow copy without changing the array; `splice` adds/removes elements in place and mutates it.

**Q4:** Difference between `find` and `filter`?
**A:** `find` returns the first matching element (or `undefined`); `filter` returns all matches as a new array.

**Q5:** How do you correctly sort numbers?
**A:** Provide a comparator: `arr.sort((a, b) => a - b)`, since default sort compares stringified values.

## 9. Common Mistakes
- Using `sort()` on numbers without a comparator (`[10, 2, 1]` → `[1, 10, 2]`).
- Expecting `forEach` to return a value or support `break`.
- Mutating an array while iterating over it.
- Forgetting `reduce` needs an initial value for safety on empty arrays.

## 10. Advanced Notes
- `reduce` can implement `map`, `filter`, grouping, and more.
- `flatMap` = `map` then `flat(1)`; useful for one-level flattening.
- Sparse arrays behave unexpectedly with some iteration methods.
- For large datasets, consider performance: chaining creates intermediate arrays; a single `reduce` or loop can be faster.
