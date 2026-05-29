# Advanced Types

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

**Advanced types** are TypeScript's type-level programming features — mapped types, conditional types, template literal types, `keyof`/indexed access, and the `infer` keyword — that let you compute new types from existing ones.

## 2. Simple Explanation

Advanced types are like writing programs that run on *types* instead of values. You can transform, filter, and generate types automatically, the same way functions transform data — all at compile time.

## 3. Why It Is Used

- Build flexible, reusable, and precise type definitions.
- Eliminate duplication by deriving types from a single source.
- Power libraries and utility types with strong, automatic guarantees.
- Encode domain rules into the type system so mistakes fail to compile.

## 4. Key Points

- **`keyof`** produces a union of an object's keys.
- **Indexed access** (`T[K]`) reads the type of a property.
- **Mapped types** iterate over keys to transform each property.
- **Conditional types** (`T extends U ? X : Y`) choose a type based on a condition.
- **`infer`** extracts a type from within another during conditional matching.
- **Template literal types** build string types from unions.

## 5. Syntax

```ts
type Keys = keyof { a: number; b: string };       // "a" | "b"
type Value = { a: number }["a"];                  // number
type Optional<T> = { [K in keyof T]?: T[K] };     // mapped
type IsString<T> = T extends string ? true : false; // conditional
type ElementOf<T> = T extends (infer U)[] ? U : never; // infer
type Event = `on${Capitalize<"click">}`;          // "onClick"
```

## 6. Example

```ts
// keyof + indexed access for type-safe getter
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
const user = { id: 1, name: "Ada" };
const name = getProp(user, "name"); // string

// Mapped type: make every property nullable
type Nullable<T> = { [K in keyof T]: T[K] | null };

// Conditional + infer: unwrap a Promise
type Unwrap<T> = T extends Promise<infer U> ? U : T;
type A = Unwrap<Promise<number>>; // number
type B = Unwrap<string>;          // string

// Template literal type
type Method = "get" | "post";
type Route = `/${Method}/users`;  // "/get/users" | "/post/users"

// Key remapping (TS 4.1+)
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};
type UserGetters = Getters<{ id: number }>; // { getId: () => number }
```

## 7. Real World Use Case

Generating an event-handler prop type from a set of event names, keeping handlers in sync with events automatically:

```ts
type Events = "click" | "focus" | "blur";

type Handlers = {
  [E in Events as `on${Capitalize<E>}`]?: (event: Event) => void;
};
// Handlers = { onClick?: ...; onFocus?: ...; onBlur?: ... }

const props: Handlers = {
  onClick: () => console.log("clicked"),
};
```

## 8. Interview Questions

**Q1:** What does the `infer` keyword do?
**A:** Inside a conditional type, `infer` introduces a type variable that captures part of the matched type — e.g. `T extends Promise<infer U> ? U : T` extracts the resolved type of a promise.

**Q2:** What is a mapped type?
**A:** A type that iterates over the keys of another type to produce a new type, applying transformations like adding `?`, `readonly`, or remapping keys — e.g. `{ [K in keyof T]: T[K] }`.

**Q3:** What is a conditional type and how does distribution work?
**A:** A conditional type `T extends U ? X : Y` selects a branch based on assignability. When `T` is a union and is a naked type parameter, the condition distributes over each member individually.

**Q4:** What are template literal types?
**A:** Types that construct string literal types using template syntax and unions, e.g. `` `on${Capitalize<E>}` ``, enabling precise string keys and routes.

**Q5:** How do `keyof` and indexed access types work together?
**A:** `keyof T` gives the union of keys; `T[K]` gives the type at key `K`. Combined with a constraint `K extends keyof T`, they enable fully type-safe property access.

## 9. Common Mistakes

- Forgetting that conditional types distribute over unions, producing unexpected results (wrap in `[T]` to prevent distribution).
- Writing overly complex type logic that is hard to read and slow to compile.
- Misusing `infer` outside of a conditional type's `extends` clause.
- Ignoring performance — deeply recursive types can slow down the compiler significantly.

## 10. Advanced Notes

- Recursive conditional types can implement type-level algorithms (e.g. `DeepPartial`, JSON parsers, path types).
- Distribution can be disabled by wrapping the checked type in a tuple: `[T] extends [U] ? X : Y`.
- Key remapping with `as` (TS 4.1+) enables renaming and filtering keys in mapped types.
- These features make the type system **Turing-complete**; use them judiciously to balance power against readability and compile speed.
