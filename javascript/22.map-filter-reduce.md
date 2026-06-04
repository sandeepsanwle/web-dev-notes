# Map, Filter, Reduce

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
`map`, `filter`, and `reduce` are non-mutating higher-order array methods: `map` transforms each element into a new array, `filter` selects elements matching a condition into a new array, and `reduce` folds all elements into a single accumulated value.

## 2. Simple Explanation
- `map`: change every item (e.g., double each number).
- `filter`: keep only the items you want (e.g., even numbers).
- `reduce`: combine all items into one result (e.g., a total).

## 3. Why It Is Used
They replace manual loops with clear, declarative, chainable operations — making data transformation pipelines concise, readable, and less error-prone.

## 4. Key Points
- All three are **non-mutating** (return new values).
- `map` always returns an array of the **same length**.
- `filter` returns a subset (same or fewer elements).
- `reduce` returns **any** type (number, object, array, etc.) and takes an initial value.
- They can be **chained**: `filter().map().reduce()`.

## 5. Syntax
```js
arr.map((item, i, arr) => newItem);
arr.filter((item, i, arr) => booleanCondition);
arr.reduce((accumulator, item, i, arr) => newAcc, initialValue);
```

## 6. Example
```js
const products = [
  { name: "Pen", price: 5, inStock: true },
  { name: "Book", price: 20, inStock: false },
  { name: "Bag", price: 50, inStock: true },
];

const totalInStock = products
  .filter((p) => p.inStock)         // keep in-stock
  .map((p) => p.price)              // extract prices
  .reduce((sum, price) => sum + price, 0); // total

console.log(totalInStock); // 55
```
The pipeline filters in-stock items, maps to prices, and reduces to a single total.

## 7. Real World Use Case
Computing cart totals, transforming API responses for UI, aggregating analytics, grouping/counting data, building lookup objects, and any data-processing pipeline.

## 8. Interview Questions
**Q1:** What's the difference between `map` and `filter`?
**A:** `map` transforms every element (same length output); `filter` keeps only elements passing a test (possibly fewer).

**Q2:** What can `reduce` return?
**A:** Anything — a number, string, object, or array — based on the accumulator logic.

**Q3:** Why provide an initial value to `reduce`?
**A:** It defines the accumulator's starting type/value and prevents errors on empty arrays.

**Q4:** Do these methods mutate the original array?
**A:** No, all three return new values and leave the source array unchanged.

**Q5:** How would you implement `map` using `reduce`?
**A:** `arr.reduce((acc, x) => [...acc, fn(x)], [])` — accumulate transformed items into a new array.

## 9. Common Mistakes
- Forgetting `reduce`'s initial value, causing bugs on empty arrays.
- Using `map` for side effects (use `forEach` instead).
- Not returning a value from the `map`/`reduce` callback.
- Mutating the accumulator vs returning a new one inconsistently.

## 10. Advanced Notes
- `reduce` is the most general — `map`, `filter`, grouping, and flattening can all be expressed with it.
- Chaining creates intermediate arrays; for very large data, a single `reduce` or loop is more memory-efficient.
- `reduceRight` processes right-to-left.
- Combine with `flatMap` for one-level flattening transforms.
