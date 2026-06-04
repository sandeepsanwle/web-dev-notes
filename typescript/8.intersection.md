# Intersection Types

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

An **intersection type** combines multiple types into one. A value of an intersection type must satisfy **all** the combined types simultaneously. It is written with the ampersand (`&`).

## 2. Simple Explanation

If a union (`|`) means "either A or B", an intersection (`&`) means "A *and* B at the same time." The result has every property from all the combined types.

## 3. Why It Is Used

- Merge several object shapes into one combined shape.
- Compose reusable mixins and capability bundles.
- Add extra fields to an existing type without inheritance.
- Model objects that play multiple roles at once.

## 4. Key Points

- Written with `&`: `type C = A & B`.
- The result requires **all** properties of every part.
- Combining conflicting primitive types yields `never` (impossible).
- Order does not matter: `A & B` equals `B & A`.
- Commonly used with type aliases to extend object shapes.

## 5. Syntax

```ts
type A = { a: number };
type B = { b: string };
type AB = A & B; // { a: number; b: string }

const value: AB = { a: 1, b: "hi" };
```

## 6. Example

```ts
type Timestamps = {
  createdAt: Date;
  updatedAt: Date;
};

type User = {
  id: number;
  name: string;
};

// Combine both shapes
type UserRecord = User & Timestamps;

const record: UserRecord = {
  id: 1,
  name: "Ada",
  createdAt: new Date(),
  updatedAt: new Date(),
};

// Mixin-style composition
type Serializable = { serialize(): string };
type Loggable = { log(): void };
type Entity = Serializable & Loggable;
```

## 7. Real World Use Case

Composing props in a component library — combining base HTML attributes with custom props:

```ts
type BaseProps = { className?: string; id?: string };
type ButtonOwnProps = { variant: "primary" | "secondary"; onClick: () => void };

type ButtonProps = BaseProps & ButtonOwnProps;

function Button(props: ButtonProps) {
  // props has className, id, variant, and onClick
}
```

## 8. Interview Questions

**Q1:** What is the difference between a union and an intersection type?
**A:** A union (`A | B`) means the value is *one of* the types. An intersection (`A & B`) means the value is *all of* the types at once and must include every member of each.

**Q2:** What happens when you intersect incompatible primitives like `string & number`?
**A:** The result is `never`, because no value can be both a string and a number simultaneously.

**Q3:** How do intersection types support mixins?
**A:** By combining multiple capability types (e.g. `Serializable & Loggable`), you describe an object that has all those capabilities, mirroring the mixin pattern.

**Q4:** Does property order matter in intersections?
**A:** No. `A & B` is equivalent to `B & A`; intersection is commutative.

**Q5:** What happens if two intersected object types have a same-named property with different types?
**A:** The property's type becomes the intersection of the two types. If those are incompatible (e.g. `string` & `number`), that property becomes `never`.

## 9. Common Mistakes

- Confusing `&` (all of) with `|` (one of).
- Accidentally producing `never` by intersecting conflicting types.
- Creating deeply nested intersections that hurt readability and error messages.
- Expecting an intersection to "pick" properties when it actually requires all of them.

## 10. Advanced Notes

- Intersections combine with generics for flexible composition: `function merge<A, B>(a: A, b: B): A & B`.
- Use them to extend third-party types non-invasively without modifying source.
- Conflicting nested properties resolve member-by-member to intersections, sometimes yielding `never` deep inside.
- Prefer interfaces with `extends` for simple object inheritance; use intersections for ad-hoc, composed, or generic combinations.
