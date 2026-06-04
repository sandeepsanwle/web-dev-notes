# Generics

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

**Generics** let you write reusable code that works over a variety of types while preserving type information. A generic introduces a *type parameter* (commonly `T`) that is filled in when the code is used.

## 2. Simple Explanation

A generic is like a function whose argument is a *type* instead of a value. You tell it "I'll decide the exact type later," and it adapts — keeping full type safety instead of falling back to `any`.

## 3. Why It Is Used

- Build reusable functions, classes, and types that work with any type.
- Preserve type relationships between inputs and outputs.
- Avoid duplicating code for each specific type.
- Avoid `any`, keeping autocomplete and compile-time checks.

## 4. Key Points

- A type parameter is declared in angle brackets: `function f<T>(x: T): T`.
- Type arguments are usually **inferred**, so you rarely pass them explicitly.
- **Constraints** restrict a type parameter: `<T extends object>`.
- Multiple parameters are allowed: `<K, V>`.
- Default type parameters: `<T = string>`.

## 5. Syntax

```ts
function identity<T>(value: T): T {
  return value;
}

interface Box<T> {
  value: T;
}

class Container<T> {
  constructor(public item: T) {}
}
```

## 6. Example

```ts
// Generic function – return type tracks the argument type
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const n = first([1, 2, 3]);      // n: number | undefined
const s = first(["a", "b"]);     // s: string | undefined

// Constraint: T must have a length property
function logLength<T extends { length: number }>(item: T): T {
  console.log(item.length);
  return item;
}
logLength("hello");   // ok
logLength([1, 2, 3]); // ok
// logLength(42);     // Error: number has no 'length'

// Multiple type parameters
function pair<K, V>(key: K, value: V): [K, V] {
  return [key, value];
}
```

## 7. Real World Use Case

A typed API wrapper that returns the correct shape for each endpoint:

```ts
async function getJson<T>(url: string): Promise<T> {
  const res = await fetch(url);
  return res.json() as Promise<T>;
}

interface User { id: number; name: string }

const user = await getJson<User>("/api/user/1");
// user is typed as User – autocomplete on user.name works
```

## 8. Interview Questions

**Q1:** What problem do generics solve?
**A:** They allow reusable, type-safe code across many types without losing type information or resorting to `any`.

**Q2:** What is a generic constraint?
**A:** A constraint (`<T extends X>`) limits the types a parameter can accept, guaranteeing certain properties/methods are available inside the generic.

**Q3:** What is the difference between `any` and a generic `T`?
**A:** `any` discards type information; `T` preserves it. With a generic, the input and output types stay linked, so the compiler still checks usage.

**Q4:** What does `keyof` combined with generics enable?
**A:** It enables type-safe property access: `function get<T, K extends keyof T>(obj: T, key: K): T[K]` returns exactly the value type for the requested key.

**Q5:** Can generics have default types?
**A:** Yes: `interface Box<T = string>` uses `string` when no type argument is supplied.

## 9. Common Mistakes

- Overusing generics where a simple concrete type would do (needless complexity).
- Forgetting constraints, then trying to access properties that may not exist on `T`.
- Specifying type arguments manually when inference already handles them.
- Confusing generic type parameters with regular value parameters.

## 10. Advanced Notes

- Generics underpin utility types like `Partial<T>`, `Record<K, V>`, and `Pick<T, K>`.
- Combine with **conditional types** for powerful transformations: `type Unwrap<T> = T extends Promise<infer U> ? U : T`.
- The `infer` keyword extracts a type from within another type during conditional matching.
- Generic constraints with `keyof` and indexed access (`T[K]`) are the backbone of type-safe object utilities.
