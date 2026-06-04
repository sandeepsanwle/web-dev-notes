# Hoisting

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
Hoisting is JavaScript's default behavior of moving declarations to the top of their containing scope during the compilation phase, before code executes. Function declarations and `var` declarations are hoisted; `let`/`const` are hoisted but kept uninitialized in the Temporal Dead Zone.

## 2. Simple Explanation
Before running your code, JavaScript scans it and "registers" all the variable and function names first. So a name can be known to exist before the line where you wrote it — though its value may not be ready yet.

## 3. Why It Is Used
Hoisting isn't something you enable — it's how the engine sets up scopes. Understanding it explains otherwise confusing behavior, like why a `var` is `undefined` instead of throwing, or why you can call a function declared later in the file.

## 4. Key Points
- **Function declarations** are fully hoisted (you can call them before they appear).
- **`var`** is hoisted and initialized to `undefined`.
- **`let`/`const`** are hoisted but **not initialized** → TDZ → `ReferenceError` if accessed early.
- **Function expressions / arrow functions** assigned to variables follow the variable's hoisting rules (not callable before assignment).
- Hoisting happens per scope (function/block), not globally only.

## 5. Syntax
```js
// Conceptually, the engine treats this:
greet();
function greet() { console.log("hi"); }

// ...as if `greet` was registered at the top of the scope first.
```

## 6. Example
```js
console.log(a); // undefined (var hoisted, not yet assigned)
var a = 5;

sayHi();        // "Hi!" (function declaration fully hoisted)
function sayHi() { console.log("Hi!"); }

console.log(b); // ReferenceError (TDZ)
let b = 10;
```
`var a` exists but is `undefined`; `sayHi` works; accessing `b` before its `let` throws.

## 7. Real World Use Case
Lets you organize helper function declarations at the bottom of a module while calling them at the top. More practically, knowing hoisting helps debug "undefined" and TDZ errors and write safer code by declaring variables before use.

## 8. Interview Questions
**Q1:** What is hoisting?
**A:** The engine's process of registering declarations at the top of their scope before execution, so names exist before their written position.

**Q2:** Are `let` and `const` hoisted?
**A:** Yes, but they remain in the Temporal Dead Zone and throw a `ReferenceError` if accessed before declaration.

**Q3:** Difference between hoisting a function declaration vs a function expression?
**A:** Declarations are fully hoisted and callable early; expressions follow variable hoisting and aren't callable until assigned.

**Q4:** What value does a hoisted `var` have before assignment?
**A:** `undefined`.

**Q5:** Does hoisting move code physically?
**A:** No — declarations are registered in memory during compilation; the source code itself is not rearranged.

## 9. Common Mistakes
- Assuming `let`/`const` aren't hoisted at all (they are, just in the TDZ).
- Expecting a function expression to be callable before its line.
- Relying on hoisting for readability instead of declaring before use.
- Confusing initialization with declaration.

## 10. Advanced Notes
- In compilation, the engine performs a creation phase (set up scope, hoist) then an execution phase.
- Function declarations are hoisted **above** `var` declarations of the same name within a scope.
- Block-scoped function declarations behave inconsistently across environments in non-strict mode — prefer expressions in blocks.
- The TDZ is a runtime concept enforced from scope entry up to the declaration statement.
