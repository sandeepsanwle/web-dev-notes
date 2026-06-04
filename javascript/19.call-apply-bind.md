# Call, Apply, Bind

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
`call`, `apply`, and `bind` are methods on `Function.prototype` used to explicitly set the value of `this` when invoking a function. `call` and `apply` invoke immediately (differing in how arguments are passed); `bind` returns a new function with `this` permanently bound.

## 2. Simple Explanation
These let you borrow a function and tell it "pretend you belong to this object." `call`/`apply` run it right away; `bind` hands you a ready-to-use copy locked to that object for later.

## 3. Why It Is Used
They control `this` for function borrowing, fix lost context in callbacks, enable method reuse across objects, and support partial application.

## 4. Key Points
- `call(thisArg, arg1, arg2, ...)` — args passed individually.
- `apply(thisArg, [argsArray])` — args passed as an array.
- `bind(thisArg, ...presetArgs)` — returns a new bound function (not called immediately).
- All three set `this` explicitly.
- `bind` can also do partial application by presetting arguments.

## 5. Syntax
```js
fn.call(thisArg, a, b);     // invoke now, listed args
fn.apply(thisArg, [a, b]);  // invoke now, array args
const bound = fn.bind(thisArg, a); // returns new function
```

## 6. Example
```js
function introduce(greeting, punct) {
  return `${greeting}, I'm ${this.name}${punct}`;
}

const person = { name: "Ada" };

console.log(introduce.call(person, "Hi", "!"));   // "Hi, I'm Ada!"
console.log(introduce.apply(person, ["Hey", "."])); // "Hey, I'm Ada."

const greetAda = introduce.bind(person, "Hello");
console.log(greetAda("?"));                        // "Hello, I'm Ada?"
```
`call` and `apply` run immediately with different argument styles; `bind` returns a reusable function locked to `person`.

## 7. Real World Use Case
Fixing `this` in event handlers and callbacks, borrowing array methods on array-like objects (`Array.prototype.slice.call(arguments)`), partial application, and method reuse across similar objects.

## 8. Interview Questions
**Q1:** Difference between `call` and `apply`?
**A:** Both invoke immediately and set `this`; `call` takes arguments individually, `apply` takes them as an array.

**Q2:** What does `bind` return?
**A:** A new function permanently bound to the given `this` (and optional preset arguments), to be called later.

**Q3:** Can you rebind a function created with `bind`?
**A:** No — once bound, `this` is fixed; further `bind`/`call`/`apply` cannot change it.

**Q4:** How do you use these for function borrowing?
**A:** Invoke another object's method with your object as `this`, e.g., `Array.prototype.slice.call(arrayLike)`.

**Q5:** How does `bind` enable partial application?
**A:** By presetting leading arguments, returning a function that only needs the remaining ones.

## 9. Common Mistakes
- Expecting `bind` to call the function immediately.
- Passing individual args to `apply` (it needs an array) or an array to `call`.
- Trying to rebind an already-bound function.
- Using arrow functions with these methods (arrows ignore `this` binding).

## 10. Advanced Notes
- With `new`, a bound function uses the new instance as `this`, ignoring the bound `this`.
- `apply` is handy for spreading arrays before spread syntax existed: `Math.max.apply(null, arr)` (now `Math.max(...arr)`).
- A polyfill for `bind` returns a closure that uses `apply` internally and handles `new`.
- Binding has a small performance/memory cost since it creates a new function object.
