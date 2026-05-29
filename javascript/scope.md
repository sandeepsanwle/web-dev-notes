# Scope

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
Scope is the current context of execution that determines the accessibility (visibility) of variables and functions. JavaScript has global scope, function scope, block scope, and module scope, organized into a **scope chain**.

## 2. Simple Explanation
Scope is like nested rooms. A variable declared in an inner room can see things in outer rooms, but the outer rooms can't see inside the inner ones. You look outward, never inward.

## 3. Why It Is Used
Scope prevents naming collisions, protects variables from unintended access, enables encapsulation, and underlies powerful patterns like closures and modules.

## 4. Key Points
- **Global scope:** accessible everywhere.
- **Function scope:** `var` and function locals live here.
- **Block scope:** `let`/`const` are confined to `{ }` blocks.
- **Scope chain:** inner scopes can access outer variables (lexical lookup outward).
- **Lexical scoping:** scope is determined by where code is written, not how it's called.

## 5. Syntax
```js
let global = "G";          // global scope
function outer() {
  let outerVar = "O";      // function scope
  if (true) {
    let blockVar = "B";    // block scope
  }
}
```

## 6. Example
```js
const x = "global";

function outer() {
  const y = "outer";
  function inner() {
    const z = "inner";
    console.log(x, y, z); // global outer inner — looks outward
  }
  inner();
}
outer();
// console.log(z); // ReferenceError — not visible outside inner
```
`inner` can read variables from `outer` and global scope via the scope chain, but not vice versa.

## 7. Real World Use Case
Keeping helper variables private inside a function, avoiding polluting the global namespace, building module patterns, and creating private state with closures (e.g., a counter that can't be tampered with from outside).

## 8. Interview Questions
**Q1:** What is the scope chain?
**A:** The ordered set of nested scopes the engine searches to resolve a variable, moving from the current scope outward to the global scope.

**Q2:** What is lexical scoping?
**A:** Scope determined by the physical location of code in the source, established at author time — not by the runtime call site.

**Q3:** Difference between function scope and block scope?
**A:** `var` is function-scoped (visible throughout the function); `let`/`const` are block-scoped (visible only within the nearest `{ }`).

**Q4:** Can an outer scope access inner scope variables?
**A:** No. Visibility flows inward-to-outward only; inner variables are private to their scope.

**Q5:** What happens when a variable isn't found in any scope?
**A:** A `ReferenceError` is thrown (or, in non-strict mode, an implicit global is created on assignment).

## 9. Common Mistakes
- Accidentally creating globals by assigning without declaration (non-strict mode).
- Expecting `var` to be block-scoped.
- Shadowing outer variables unintentionally.
- Assuming scope depends on where a function is called rather than defined.

## 10. Advanced Notes
- **Closures** capture their lexical scope, keeping variables alive after the outer function returns.
- Each module has its own scope; top-level `let`/`const` aren't global.
- `eval` and `with` can dynamically alter scope and are discouraged.
- Block-scoped bindings per loop iteration (`let`) fix classic closure-in-loop bugs.

### Scope chain diagram
```
[ Global Scope ]  x
   └── [ outer() Scope ]  y
          └── [ inner() Scope ]  z   → looks up: z → y → x
```
