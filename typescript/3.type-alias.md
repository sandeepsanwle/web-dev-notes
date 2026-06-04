# Type Alias

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A **type alias** gives a name to any type — primitive, object, union, intersection, tuple, or function — using the `type` keyword. It does not create a new type; it creates a reusable name for an existing one.

## 2. Simple Explanation

A type alias is like a nickname for a type. Instead of writing the same long type over and over, you write it once, give it a name, and reuse that name everywhere.

## 3. Why It Is Used

- Reuse complex types without repetition (DRY).
- Name union and intersection types, which interfaces cannot express directly.
- Improve readability of function signatures and generics.
- Model primitives, tuples, and function types with a clear name.

## 4. Key Points

- Created with the `type` keyword: `type Name = ...`.
- Can alias **anything**: primitives, unions, intersections, tuples, functions, objects.
- Cannot be reopened/merged (no declaration merging) — unlike interfaces.
- Supports generics: `type Box<T> = { value: T }`.
- Erased at compile time.

## 5. Syntax

```ts
type ID = string | number;
type Point = { x: number; y: number };
type Callback = (data: string) => void;
type Pair<T> = [T, T];
```

## 6. Example

```ts
// Union alias
type Status = "active" | "inactive" | "banned";

// Object alias
type User = {
  id: number;
  name: string;
  status: Status;
};

const u: User = { id: 1, name: "Ada", status: "active" };

// Function type alias
type Reducer<T> = (acc: T, item: T) => T;
const add: Reducer<number> = (a, b) => a + b;

// Intersection alias
type Timestamps = { createdAt: Date; updatedAt: Date };
type Post = { title: string } & Timestamps;
```

## 7. Real World Use Case

Aliasing a discriminated union for predictable state handling in a UI:

```ts
type RequestState =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: string[] }
  | { status: "error"; message: string };

function render(state: RequestState) {
  if (state.status === "success") return state.data.join(", ");
  if (state.status === "error") return state.message;
  return "...";
}
```

## 8. Interview Questions

**Q1:** When would you choose a type alias over an interface?
**A:** Use a type alias for unions, intersections, tuples, primitives, mapped/conditional types, or any computed type. Use an interface for extensible object/class shapes that may benefit from declaration merging.

**Q2:** Can type aliases be extended?
**A:** Not with `extends`, but you can compose them using intersections: `type B = A & { extra: string }`.

**Q3:** Do type aliases support generics?
**A:** Yes. `type Box<T> = { value: T }` creates a parameterized, reusable type.

**Q4:** Can two type aliases with the same name merge like interfaces?
**A:** No. Type aliases do not support declaration merging; a duplicate name causes an error.

**Q5:** Is there a runtime difference between a type alias and an interface?
**A:** No. Both are purely compile-time constructs and are erased in the emitted JavaScript.

## 9. Common Mistakes

- Trying to merge two aliases of the same name (not allowed).
- Using an interface where a union is needed (only aliases express unions cleanly).
- Over-nesting aliases until error messages become unreadable.
- Assuming aliases create distinct runtime types — they are just names.

## 10. Advanced Notes

- Type aliases power **mapped types** (`type Partial<T> = { [K in keyof T]?: T[K] }`) and **conditional types** (`type NonNull<T> = T extends null ? never : T`).
- Recursive aliases are supported: `type Json = string | number | boolean | null | Json[] | { [k: string]: Json }`.
- Aliasing literal unions enables exhaustive switch checks with `never`.
- Prefer aliases for function signatures and complex generics; the compiler often shows the alias name in errors, improving readability.
