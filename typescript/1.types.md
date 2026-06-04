# Types

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A **type** in TypeScript describes the *shape* of a value — what kind of data it holds and what operations are allowed on it. TypeScript layers a static type system on top of JavaScript so errors can be caught at compile time instead of at runtime.

## 2. Simple Explanation

Think of a type as a label on a box that says what is allowed inside. If a box is labelled `number`, you cannot put the word `"hello"` in it. The compiler checks these labels for you before the code ever runs.

## 3. Why It Is Used

- Catch bugs early (before the code runs).
- Provide rich editor autocomplete and inline documentation.
- Make refactoring safe across large codebases.
- Serve as living documentation for function inputs and outputs.

## 4. Key Points

- **Primitive types:** `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`.
- **Special types:** `any` (opts out of checking), `unknown` (safe `any`), `never` (no value), `void` (no return value).
- **Object types:** arrays, objects, functions, tuples.
- Types are erased at compile time — they do **not** exist in the emitted JavaScript.
- Prefer `unknown` over `any` when a value's type is genuinely not known.

## 5. Syntax

```ts
let count: number = 10;
let username: string = "Ada";
let isActive: boolean = true;
let ids: number[] = [1, 2, 3];
let nothing: null = null;

function greet(name: string): string {
  return `Hello, ${name}`;
}
```

## 6. Example

```ts
// Type annotations on variables and parameters
let price: number = 99.5;
let title: string = "TypeScript Basics";

// Arrays
let scores: number[] = [90, 85, 78];

// Function with typed params and return type
function total(items: number[]): number {
  return items.reduce((sum, n) => sum + n, 0);
}

console.log(total(scores)); // 253

// 'unknown' forces a check before use
let input: unknown = "42";
if (typeof input === "string") {
  console.log(input.toUpperCase()); // safe
}
```

## 7. Real World Use Case

In an API client, typing the response prevents accessing fields that do not exist:

```ts
type User = { id: number; email: string };

async function getUser(id: number): Promise<User> {
  const res = await fetch(`/api/users/${id}`);
  return res.json() as Promise<User>;
}

// Editor now autocompletes .id and .email and blocks typos like .emial
```

## 8. Interview Questions

**Q1:** What is the difference between `any` and `unknown`?
**A:** `any` disables type checking entirely — you can do anything with it. `unknown` is type-safe: you must narrow it (e.g. with `typeof`) before performing operations, so it forces explicit checks.

**Q2:** What does the `never` type represent?
**A:** `never` represents values that never occur — e.g. the return type of a function that always throws or has an infinite loop, or the type of an exhaustively-narrowed variable.

**Q3:** Do TypeScript types exist at runtime?
**A:** No. Types are erased during compilation. The emitted JavaScript contains no type information, which is why you cannot use a type in a runtime `instanceof`-style check.

**Q4:** What is the difference between `void` and `undefined`?
**A:** `void` is used as the return type of functions that do not return a meaningful value. `undefined` is an actual value. A `void` function may technically return `undefined`, but `void` signals intent.

**Q5:** When should you use `any`?
**A:** Almost never in production code. It is acceptable as a temporary escape hatch during migration from JavaScript, but `unknown` or a precise type is preferred for safety.

## 9. Common Mistakes

- Using `any` everywhere, which silently disables all type safety.
- Forgetting that types are erased and trying to use them at runtime.
- Confusing `null` and `undefined` when `strictNullChecks` is on.
- Annotating types that TypeScript could already infer (unnecessary noise).

## 10. Advanced Notes

- Enable `strict` mode in `tsconfig.json` to turn on `strictNullChecks`, `noImplicitAny`, and more.
- `unknown` combined with type guards is the foundation of safe runtime validation (e.g. with Zod).
- The `never` type is essential for **exhaustiveness checking** in `switch` statements over union types — assigning a leftover case to a `never` variable triggers a compile error if a case is missed.
