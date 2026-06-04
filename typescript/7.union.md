# Union Types

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **union type** describes a value that can be one of several types. It is written with the pipe (`|`) symbol: `string | number` means "either a string or a number."

## 2. Simple Explanation

A union is like saying "this box can hold an apple OR an orange." The value is exactly one of the allowed types at any moment, and you must check which one before using type-specific features.

## 3. Why It Is Used

- Model values that can legitimately be more than one type.
- Represent finite sets of allowed values via literal unions.
- Build discriminated unions for safe, exhaustive state handling.
- Avoid `any` while staying flexible.

## 4. Key Points

- Written with `|`: `type A = X | Y | Z`.
- You can only access members common to **all** members until you narrow.
- **Narrowing** (via `typeof`, `in`, `instanceof`, equality) tells the compiler which type you have.
- **Literal unions** (`"a" | "b"`) model fixed option sets.
- **Discriminated unions** use a shared literal field to switch safely.

## 5. Syntax

```ts
type ID = string | number;
type Result = "success" | "error" | "loading";

function format(value: string | number): string {
  return typeof value === "string" ? value : value.toFixed(2);
}
```

## 6. Example

```ts
// Literal union
type Theme = "light" | "dark";
let theme: Theme = "dark"; // only "light" or "dark" allowed

// Narrowing with typeof
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // id is string here
  } else {
    console.log(id.toFixed(0));    // id is number here
  }
}

// Discriminated union
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number };

function area(s: Shape): number {
  switch (s.kind) {
    case "circle": return Math.PI * s.radius ** 2;
    case "square": return s.side ** 2;
  }
}
```

## 7. Real World Use Case

Modelling the lifecycle of an async request so the UI can render each state safely:

```ts
type Fetch<T> =
  | { status: "loading" }
  | { status: "error"; error: string }
  | { status: "done"; data: T };

function view(state: Fetch<string[]>) {
  if (state.status === "loading") return "Loading...";
  if (state.status === "error") return state.error;
  return state.data.join(", "); // status is "done", data is available
}
```

## 8. Interview Questions

**Q1:** What is a union type?
**A:** A type formed with `|` that allows a value to be one of several types, e.g. `string | number`.

**Q2:** What is type narrowing?
**A:** The process by which the compiler refines a union to a more specific type within a code branch, using checks like `typeof`, `instanceof`, `in`, or equality comparisons.

**Q3:** What is a discriminated (tagged) union?
**A:** A union of object types sharing a common literal property (the discriminant, e.g. `kind`). Switching on that property lets the compiler narrow to the exact variant and enables exhaustiveness checks.

**Q4:** What members can you access on a union without narrowing?
**A:** Only members that exist on **every** member of the union. Type-specific members require narrowing first.

**Q5:** How do you ensure all cases of a union are handled?
**A:** Use a `switch` on the discriminant and assign the leftover value to a `never`-typed variable in the `default` case; the compiler errors if a case is missing.

## 9. Common Mistakes

- Accessing a property that exists on only one union member without narrowing.
- Forgetting a case in a discriminated union (mitigated by `never` exhaustiveness checks).
- Overusing broad unions where a single precise type is clearer.
- Confusing union (`|`, "one of") with intersection (`&`, "all of").

## 10. Advanced Notes

- Discriminated unions are the idiomatic way to model state machines and Redux-style actions.
- Combine with the `never` type for compile-time exhaustiveness checking.
- Unions distribute over **conditional types**: `T extends U ? X : Y` applies to each member when `T` is a union.
- `NonNullable<T>` removes `null`/`undefined` from a union; `Extract`/`Exclude` filter union members.
