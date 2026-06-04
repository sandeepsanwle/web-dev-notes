# Shallow vs Deep Copy

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
A **shallow copy** duplicates an object's top-level properties, but nested objects/arrays are still shared by reference. A **deep copy** recursively duplicates all levels, producing a fully independent clone with no shared references.

## 2. Simple Explanation
A shallow copy is like photocopying a folder's cover page but still pointing to the same documents inside. A deep copy photocopies the folder and every document inside, so changes to one don't affect the other.

## 3. Why It Is Used
Copying prevents unintended mutations of shared data. Choosing shallow vs deep matters for immutability in state management (React/Redux), avoiding side effects, and safely modifying nested structures.

## 4. Key Points
- **Shallow:** spread (`{...obj}`), `Object.assign`, `Array.slice`, `Array.from` — nested refs shared.
- **Deep:** `structuredClone`, recursion, or libraries (`lodash.cloneDeep`).
- `JSON.parse(JSON.stringify(obj))` deep-copies but **loses** functions, `undefined`, `Date` (→string), `Map`/`Set`, and breaks on circular refs.
- `structuredClone` handles many types and circular refs (no functions).
- Mutating a nested object in a shallow copy also changes the original.

## 5. Syntax
```js
// Shallow
const shallow = { ...obj };
const shallowArr = [...arr];

// Deep
const deep = structuredClone(obj);
const deepJson = JSON.parse(JSON.stringify(obj)); // with caveats
```

## 6. Example
```js
const original = { name: "Ada", address: { city: "London" } };

const shallow = { ...original };
shallow.address.city = "Paris";
console.log(original.address.city); // "Paris" — nested object shared!

const deep = structuredClone(original);
deep.address.city = "Berlin";
console.log(original.address.city); // "Paris" — original untouched
```
The shallow copy shares the nested `address`, so it mutates the original; the deep copy is fully independent.

## 7. Real World Use Case
Immutable state updates in React/Redux, snapshotting data before edits, cloning configuration objects, and safely passing data without exposing internal references.

## 8. Interview Questions
**Q1:** What's the difference between shallow and deep copy?
**A:** Shallow copies only top-level properties (nested objects shared by reference); deep copies recursively clone all levels for full independence.

**Q2:** How do you make a shallow copy?
**A:** Spread syntax (`{...obj}`/`[...arr]`), `Object.assign`, `Array.slice`, or `Array.from`.

**Q3:** What are the downsides of `JSON.parse(JSON.stringify())`?
**A:** It drops functions and `undefined`, converts `Date` to strings, can't handle `Map`/`Set`, and throws on circular references.

**Q4:** What is `structuredClone`?
**A:** A built-in that deep-clones many data types including circular references, though it can't clone functions.

**Q5:** Why does mutating a shallow copy sometimes affect the original?
**A:** Because nested objects are shared by reference between the copy and the original.

## 9. Common Mistakes
- Assuming spread/`Object.assign` deep-copies nested data.
- Using `JSON` clone on data with functions, dates, or circular refs.
- Mutating nested state in React and breaking change detection.
- Forgetting arrays of objects are also only shallow-copied by spread.

## 10. Advanced Notes
- Deep copy cost grows with object size — avoid cloning huge structures unnecessarily.
- `structuredClone` uses the structured clone algorithm (same as `postMessage`/IndexedDB).
- For partial immutability, prefer targeted spreads at each updated level (React pattern).
- Circular references require `structuredClone` or a custom recursive clone with a visited set.

### Shallow vs Deep diagram
```
Shallow copy:
  copy ──► { name } (own)
  copy.address ──┐
  original.address ──┴──► { city }   (SHARED nested ref)

Deep copy:
  copy.address ──► { city }   (independent)
  original.address ──► { city }
```
