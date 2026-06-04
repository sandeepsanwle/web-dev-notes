# Type Inference

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

**Type inference** is TypeScript's ability to automatically determine the type of a value without an explicit annotation, based on how the value is initialized and used.

## 2. Simple Explanation

You do not always have to tell TypeScript the type — it figures it out. If you write `let x = 5`, TypeScript already knows `x` is a `number`. The compiler reads your code and guesses the right type.

## 3. Why It Is Used

- Reduce boilerplate — fewer explicit annotations to write and maintain.
- Keep code clean and readable while staying fully type-safe.
- Let return types and variable types track the implementation automatically.
- Power features like contextual typing in callbacks.

## 4. Key Points

- Variables get the type of their initializer.
- Function **return types** are inferred from the `return` statements.
- **Contextual typing** infers callback parameter types from context.
- `let` infers a widened type; `const` infers a narrower literal type.
- Use `as const` to infer the most specific (literal, readonly) types.

## 5. Syntax

```ts
let count = 10;            // inferred: number
const name = "Ada";        // inferred: "Ada" (literal)
let items = [1, 2, 3];     // inferred: number[]

function double(n: number) {
  return n * 2;            // return type inferred: number
}
```

## 6. Example

```ts
// Variable inference
let message = "hello";     // string
// message = 42;           // Error: not assignable to string

// Return type inference
function add(a: number, b: number) {
  return a + b;            // inferred return: number
}

// Contextual typing – 'n' is inferred as number
[1, 2, 3].map((n) => n.toFixed(2));

// let vs const
let mutable = "active";    // type: string
const fixed = "active";    // type: "active" (literal)

// as const for precise inference
const config = { mode: "dark", size: 12 } as const;
// config.mode: "dark", config is readonly
```

## 7. Real World Use Case

Letting return-type inference keep derived types in sync, so a refactor of the function automatically updates everything downstream:

```ts
function createUser(name: string, age: number) {
  return { id: Date.now(), name, age, active: true };
}

type User = ReturnType<typeof createUser>;
// User automatically = { id: number; name: string; age: number; active: boolean }
```

## 8. Interview Questions

**Q1:** What is type inference?
**A:** The compiler's automatic determination of a value's type based on its initializer, usage, and context, without an explicit annotation.

**Q2:** What is the difference in inference between `let` and `const`?
**A:** `let x = "a"` widens to `string`, while `const x = "a"` infers the literal type `"a"` because a `const` cannot be reassigned.

**Q3:** What is contextual typing?
**A:** When TypeScript infers types from the surrounding context — e.g. the parameter types of a callback passed to `.map()` are inferred from the array's element type.

**Q4:** What does `as const` do for inference?
**A:** It tells the compiler to infer the narrowest possible types (literals) and make the result deeply `readonly`, preventing widening.

**Q5:** When should you add explicit annotations despite inference?
**A:** For public API boundaries (function parameters, exported function return types) to lock the contract, and when inference would produce a too-wide or unexpected type.

## 9. Common Mistakes

- Relying on inference for public function signatures, allowing accidental contract changes.
- Expecting `let` to infer literal types (it widens; use `const` or `as const`).
- Annotating everything redundantly, adding noise inference already handles.
- Forgetting that `any` propagates through inference, silently disabling checks.

## 10. Advanced Notes

- **Best common type:** for arrays of mixed values, TypeScript infers a union of the element types.
- **Contextual typing** flows from the expected type into expressions (e.g. event handler parameters).
- Generic functions infer type arguments from their call arguments — explicit type arguments are rarely needed.
- The `satisfies` operator validates a value against a type while preserving the more specific inferred type.
