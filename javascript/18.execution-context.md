# Execution Context

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
An execution context is the environment in which JavaScript code is evaluated and executed. It contains the variable environment, scope chain, and the value of `this`. Contexts are managed on the **call stack**.

## 2. Simple Explanation
Every time code runs, JavaScript sets up a little "workspace" holding its variables, its outer connections, and who `this` is. Each function call gets its own workspace, stacked on top of the previous one.

## 3. Why It Is Used
The execution context model explains how variables are resolved, how hoisting works, how `this` is set, and how the call stack tracks function calls — the foundation of JS runtime behavior.

## 4. Key Points
- Types: **Global** execution context (one per program) and **Function** execution contexts (one per call). (`eval` is a rare third.)
- Two phases: **creation** (hoisting, set up scope & `this`) and **execution** (run code).
- Each context has: Variable Environment, Lexical Environment (scope chain), and `this` binding.
- Managed via the **call stack** (LIFO).
- New function call → new context pushed; return → popped.

## 5. Syntax
```js
// Not a literal syntax; conceptual lifecycle:
// 1. Global Execution Context created
// 2. Calling a function pushes a new Function Execution Context
// 3. Returning pops it off the call stack
```

## 6. Example
```js
const g = "global";          // Global EC

function first() {
  const f = "first";
  second();                  // pushes second()'s EC
  console.log(f, g);
}

function second() {
  const s = "second";        // second()'s EC
  console.log(s, g);
}

first();
// Call stack growth: [global] -> [global, first] -> [global, first, second]
```
Each call creates a new context; the stack grows on calls and shrinks on returns.

## 7. Real World Use Case
Understanding stack traces and "Maximum call stack exceeded" errors, debugging recursion, reasoning about variable resolution and `this`, and grasping how closures capture their lexical environment.

## 8. Interview Questions
**Q1:** What is an execution context?
**A:** The environment where code runs, containing its variable environment, scope chain, and `this`, tracked on the call stack.

**Q2:** What are the phases of an execution context?
**A:** The creation phase (hoisting, scope and `this` setup) and the execution phase (line-by-line execution).

**Q3:** What is the call stack?
**A:** A LIFO structure that tracks execution contexts; calling a function pushes a context, returning pops it.

**Q4:** What gets set up during the creation phase?
**A:** The variable environment (hoisted declarations), the lexical environment/scope chain, and the `this` binding.

**Q5:** What causes a stack overflow?
**A:** Too many nested/recursive calls without a base case, exceeding the call stack's size limit.

## 9. Common Mistakes
- Confusing execution context with scope (related but distinct).
- Assuming `this` is fixed at definition rather than set per context.
- Ignoring the creation phase when reasoning about hoisting.
- Unbounded recursion causing stack overflow.

## 10. Advanced Notes
- The Lexical Environment record holds bindings; closures retain a reference to it.
- Async callbacks create new contexts when invoked later via the event loop.
- Tail-call optimization (spec'd in ES6) is rarely implemented, so deep recursion still overflows.
- The global context's `this` is `window`/`globalThis` (non-strict) or `undefined` semantics vary in modules.
