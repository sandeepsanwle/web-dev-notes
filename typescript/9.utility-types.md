# Utility Types

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

**Utility types** are built-in generic types provided by TypeScript that transform existing types into new ones — making properties optional, readonly, picking a subset, and more — without rewriting the original type by hand.

## 2. Simple Explanation

Utility types are like ready-made tools in a toolbox. Need a version of a type where everything is optional? Use `Partial`. Need only a couple of its fields? Use `Pick`. You reshape types instead of redefining them.

## 3. Why It Is Used

- Avoid duplicating type definitions when you need variations.
- Express common transformations declaratively and consistently.
- Keep a single source of truth and derive other types from it.
- Improve maintainability — change the base type, and derived types update automatically.

## 4. Key Points

- They are generic types that take one or more type arguments.
- Most are built on mapped and conditional types under the hood.
- They compose: `Partial<Pick<T, "a" | "b">>`.
- Purely compile-time — no runtime cost.
- Knowing the common ones is a frequent interview topic.

## 5. Syntax

```ts
Partial<T>        // all properties optional
Required<T>       // all properties required
Readonly<T>       // all properties readonly
Pick<T, Keys>     // subset of properties
Omit<T, Keys>     // remove properties
Record<K, V>      // map of keys K to values V
```

### Common Utility Types Reference

| Utility | What it does | Example |
| --- | --- | --- |
| `Partial<T>` | Makes all properties optional | `Partial<User>` |
| `Required<T>` | Makes all properties required | `Required<User>` |
| `Readonly<T>` | Makes all properties readonly | `Readonly<User>` |
| `Pick<T, K>` | Keeps only keys `K` | `Pick<User, "id">` |
| `Omit<T, K>` | Removes keys `K` | `Omit<User, "password">` |
| `Record<K, V>` | Object with keys `K`, values `V` | `Record<string, number>` |
| `Exclude<T, U>` | Removes `U` from union `T` | `Exclude<"a"\|"b", "a">` |
| `Extract<T, U>` | Keeps only `U` from union `T` | `Extract<"a"\|"b", "a">` |
| `NonNullable<T>` | Removes `null`/`undefined` | `NonNullable<string\|null>` |
| `ReturnType<F>` | Return type of a function | `ReturnType<typeof fn>` |
| `Parameters<F>` | Tuple of a function's params | `Parameters<typeof fn>` |
| `Awaited<T>` | Unwraps a `Promise` | `Awaited<Promise<number>>` |

## 6. Example

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

// All optional – useful for update payloads
type UserUpdate = Partial<User>;

// Public-safe shape – drop sensitive field
type PublicUser = Omit<User, "password">;

// Only a couple fields
type UserPreview = Pick<User, "id" | "name">;

// A keyed dictionary
type RoleMap = Record<"admin" | "user", string[]>;
const roles: RoleMap = { admin: ["all"], user: ["read"] };

// Infer return/param types of functions
function createUser(name: string): User {
  return { id: 1, name, email: "", password: "" };
}
type Created = ReturnType<typeof createUser>;   // User
type Args = Parameters<typeof createUser>;      // [name: string]
```

## 7. Real World Use Case

Deriving a form/update DTO from a domain model so the two never drift apart:

```ts
interface Product {
  id: number;
  title: string;
  price: number;
  createdAt: Date;
}

// API accepts partial updates but never id/createdAt
type UpdateProductDto = Partial<Omit<Product, "id" | "createdAt">>;

function updateProduct(id: number, changes: UpdateProductDto) {
  // changes.title?, changes.price? — both optional and safe
}
```

## 8. Interview Questions

**Q1:** What is the difference between `Pick` and `Omit`?
**A:** `Pick<T, K>` keeps only the listed keys; `Omit<T, K>` keeps everything *except* the listed keys. They are complementary.

**Q2:** How does `Partial<T>` work internally?
**A:** It is a mapped type: `type Partial<T> = { [K in keyof T]?: T[K] }`. It iterates over each key and adds the optional modifier.

**Q3:** When would you use `Record<K, V>`?
**A:** To type an object used as a dictionary/map with known key types and a uniform value type, e.g. `Record<string, number>` for a counts map.

**Q4:** What do `Exclude` and `Extract` operate on?
**A:** They operate on union types. `Exclude<T, U>` removes members assignable to `U`; `Extract<T, U>` keeps only members assignable to `U`.

**Q5:** What does `ReturnType<typeof fn>` give you?
**A:** The type a function returns, inferred automatically — handy for keeping derived types in sync with implementation.

## 9. Common Mistakes

- Confusing `Pick` (keep) with `Omit` (remove).
- Using `Exclude`/`Extract` on object types — they work on unions, not object members.
- Forgetting `Partial` makes properties optional but not `null`-able.
- Reaching for handwritten variants when a composed utility type would stay in sync automatically.

## 10. Advanced Notes

- Utility types are implemented with **mapped types** + **conditional types** + `infer`; you can build your own (e.g. `DeepPartial<T>`).
- They compose freely: `Readonly<Partial<Pick<T, K>>>`.
- `Awaited<T>` recursively unwraps nested promises (added in TS 4.5).
- Custom utilities like deep variants, `Mutable<T>`, or `RequireAtLeastOne<T>` are common in larger codebases where the built-ins fall short.
