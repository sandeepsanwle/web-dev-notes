# Variables

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition
A variable is a named reference (an identifier) to a memory location used to store a value that can be read and, in most cases, reassigned during program execution. In JavaScript, variables are declared with `var`, `let`, or `const`.

## 2. Simple Explanation
Think of a variable as a labeled box. You write a label on the box (the name) and put something inside it (the value). Later you can look inside the box by using its label, and sometimes swap out what is inside.

## 3. Why It Is Used
Variables let programs store, label, and reuse data instead of hard-coding values everywhere. They make code readable, maintainable, and dynamic — the same code can work with different data over time.

## 4. Key Points
- `var` is **function-scoped**, hoisted, and initialized as `undefined`.
- `let` and `const` are **block-scoped** and live in the **Temporal Dead Zone (TDZ)** until declared.
- `const` must be initialized at declaration and cannot be **reassigned** (but objects/arrays it points to can still be mutated).
- Prefer `const` by default, use `let` when reassignment is needed, avoid `var` in modern code.
- Variable names are case-sensitive and cannot start with a number.

## 5. Syntax
```js
var oldStyle = 1;     // function-scoped (legacy)
let mutable = 2;      // block-scoped, reassignable
const fixed = 3;      // block-scoped, not reassignable
```

## 6. Example
```js
const name = "Ada";   // constant reference
let count = 0;        // can change
count = count + 1;    // 1

const user = { age: 30 };
user.age = 31;        // OK — mutating the object, not reassigning
console.log(name, count, user.age); // Ada 1 31
```
`name` cannot be reassigned, `count` can, and `user` is constant but its properties can still change.

## 7. Real World Use Case
Storing configuration (`const API_URL`), tracking UI state (`let isOpen`), holding fetched data, loop counters, and accumulating results — essentially every piece of dynamic data in an application.

## 8. Interview Questions
**Q1:** What is the difference between `var`, `let`, and `const`?
**A:** `var` is function-scoped and hoisted as `undefined`; `let` and `const` are block-scoped with a TDZ. `const` cannot be reassigned, `let` can.

**Q2:** Does `const` make a value immutable?
**A:** No. It only prevents reassignment of the binding. Objects and arrays referenced by a `const` can still be mutated.

**Q3:** What is the Temporal Dead Zone?
**A:** The period between entering a scope and the actual `let`/`const` declaration, during which accessing the variable throws a `ReferenceError`.

**Q4:** What happens if you access a `var` before its declaration?
**A:** You get `undefined` because `var` declarations are hoisted and initialized to `undefined`.

**Q5:** Why is `const` preferred by default?
**A:** It signals intent (the binding won't change), reduces accidental reassignment bugs, and makes code easier to reason about.

## 9. Common Mistakes
- Thinking `const` makes objects fully immutable.
- Using `var` and being surprised by leakage out of blocks.
- Accessing `let`/`const` before declaration and hitting the TDZ.
- Re-declaring the same `let` in the same scope (throws a `SyntaxError`).

## 10. Advanced Notes
- Use `Object.freeze()` for shallow immutability of objects.
- In loops, `let` creates a new binding per iteration — crucial for closures inside loops, where `var` shares a single binding.
- Global `var` declarations become properties of the global object (`window`/`globalThis`); `let`/`const` at top level do not.
