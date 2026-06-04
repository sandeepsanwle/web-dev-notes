# this Keyword

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
`this` is a special identifier that refers to the execution context of a function — the object on which (or in which) the function is invoked. Its value is determined by **how** a function is called, not where it is defined (except for arrow functions).

## 2. Simple Explanation
`this` answers "who is calling me right now?" Depending on how a function runs, `this` points to different owners — like the word "I" means a different person depending on who's speaking.

## 3. Why It Is Used
`this` lets methods access the object they belong to, enables reusable functions that operate on different contexts, and is central to OOP, event handlers, and class methods.

## 4. Key Points
- **Method call:** `this` = the object before the dot.
- **Plain function call:** `this` = `undefined` (strict) or global object (non-strict).
- **Arrow functions:** no own `this` — they inherit from the enclosing lexical scope.
- **`new`:** `this` = the newly created instance.
- **Explicit binding:** `call`, `apply`, `bind` set `this` manually.

## 5. Syntax
```js
obj.method();          // this === obj
func();                // this === undefined / global
new Func();            // this === new instance
func.call(ctx);        // this === ctx
const arrow = () => this; // this from outer scope
```

## 6. Example
```js
const counter = {
  count: 0,
  increment() {
    this.count++;        // this === counter
    return this.count;
  },
};
console.log(counter.increment()); // 1

const fn = counter.increment;
// console.log(fn()); // error/NaN — `this` is now undefined/global

const obj = {
  value: 42,
  getArrow() {
    return (() => this.value)(); // arrow inherits this from getArrow
  },
};
console.log(obj.getArrow()); // 42
```
Calling via the object sets `this` correctly; detaching the method loses context; arrows keep the enclosing `this`.

## 7. Real World Use Case
Class methods accessing instance state, React class components (`this.setState`), event handlers needing the element/component, and reusable utilities bound to specific objects.

## 8. Interview Questions
**Q1:** How is the value of `this` determined?
**A:** By the call site — how the function is invoked (method, plain, `new`, or explicitly bound), except arrow functions which inherit `this` lexically.

**Q2:** What is `this` inside an arrow function?
**A:** The `this` of the enclosing lexical scope; arrow functions don't have their own `this`.

**Q3:** What is `this` in a regular function called standalone?
**A:** `undefined` in strict mode, or the global object (`window`/`globalThis`) in non-strict mode.

**Q4:** How do you fix a lost `this` when passing a method as a callback?
**A:** Bind it (`method.bind(obj)`), wrap it in an arrow function, or use a class field arrow method.

**Q5:** What is `this` inside a constructor called with `new`?
**A:** The newly created object instance.

## 9. Common Mistakes
- Passing a method as a callback and losing `this`.
- Using an arrow function as an object method and expecting `this` to be the object.
- Assuming `this` depends on where the function is defined.
- Forgetting `new`, causing `this` to be global/undefined.

## 10. Advanced Notes
- Binding precedence: `new` > explicit (`bind`/`call`/`apply`) > implicit (method) > default.
- `bind` returns a new permanently-bound function; rebinding has no effect.
- In DOM event handlers (non-arrow), `this` is the element that fired the event.
- Class fields with arrow functions auto-bind `this`, common for React handlers.
