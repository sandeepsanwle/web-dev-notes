# Objects

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition
An object is a collection of key–value pairs (properties), where keys are strings or symbols and values can be any type, including functions (methods). Objects are reference types and the foundational data structure in JavaScript.

## 2. Simple Explanation
An object is like a labeled drawer set. Each label (key) maps to something stored inside (value). You can add, read, update, or remove labeled items at any time.

## 3. Why It Is Used
Objects model real-world entities (a user, a product), group related data and behavior, and serve as the basis for JSON, configuration, and most data passed around in apps.

## 4. Key Points
- Access via dot (`obj.key`) or bracket (`obj["key"]`) notation.
- Objects are copied/compared **by reference**.
- `Object.keys/values/entries` enumerate properties.
- `spread (...)` and `Object.assign` create shallow copies.
- Optional chaining `?.` safely accesses nested properties.

## 5. Syntax
```js
const obj = {
  key: "value",
  method() { return this.key; },
};
obj.newKey = 1;       // add
delete obj.key;       // remove
const { newKey } = obj; // destructure
```

## 6. Example
```js
const user = {
  name: "Ada",
  age: 30,
  greet() {
    return `Hi, I'm ${this.name}`;
  },
};

console.log(user.name);        // "Ada"
console.log(user["age"]);      // 30
console.log(user.greet());     // "Hi, I'm Ada"
console.log(Object.keys(user)); // ["name", "age", "greet"]
```
Properties are accessed by key, `greet` uses `this`, and `Object.keys` lists the property names.

## 7. Real World Use Case
Representing API responses, app state, configuration objects, dictionaries/maps of data, and as arguments to functions (options objects).

## 8. Interview Questions
**Q1:** How are objects compared in JavaScript?
**A:** By reference — two objects are equal only if they point to the same memory location, not if they have identical contents.

**Q2:** How do you copy an object?
**A:** Shallow copy with spread (`{...obj}`) or `Object.assign`; deep copy with `structuredClone` or a recursive/library method.

**Q3:** Difference between dot and bracket notation?
**A:** Dot needs a valid identifier known at write time; bracket allows dynamic/computed keys and keys with special characters.

**Q4:** How do you iterate over an object's properties?
**A:** `Object.keys`, `Object.values`, `Object.entries`, or `for...in` (which also includes inherited enumerable keys).

**Q5:** What is optional chaining?
**A:** `?.` safely accesses nested properties, returning `undefined` instead of throwing if an intermediate value is null/undefined.

## 9. Common Mistakes
- Assuming object assignment copies the object (it copies the reference).
- Using `for...in` without `hasOwnProperty`, picking up inherited keys.
- Mutating shared objects causing side effects elsewhere.
- Confusing shallow vs deep copy when nesting exists.

## 10. Advanced Notes
- Property descriptors control `writable`, `enumerable`, `configurable` via `Object.defineProperty`.
- `Object.freeze` (shallow) and `Object.seal` restrict mutation.
- Computed property names: `{ [dynamicKey]: value }`.
- Prototypes link objects; `Object.create(proto)` sets the prototype directly. Use `Map` for frequent additions/deletions and non-string keys.
