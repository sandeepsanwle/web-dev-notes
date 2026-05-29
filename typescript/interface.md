# Interface

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

An **interface** is a way to define the contract (shape) of an object — the names and types of its properties and methods — without providing an implementation.

## 2. Simple Explanation

An interface is like a blueprint or a checklist. It says "any object claiming to be a `User` must have an `id` (number) and a `name` (string)." It does not create anything itself; it just describes what something must look like.

## 3. Why It Is Used

- Enforce a consistent structure across objects, classes, and function arguments.
- Enable autocomplete and compile-time checks for object shapes.
- Support object-oriented patterns via `implements` on classes.
- Allow flexible, extensible models through `extends` and declaration merging.

## 4. Key Points

- Interfaces describe object shapes, function signatures, and class contracts.
- They support **optional** (`?`), **readonly**, and **index signatures**.
- Interfaces can `extends` one or more other interfaces.
- **Declaration merging:** declaring the same interface name twice merges them.
- Interfaces are erased at compile time (no runtime cost).

## 5. Syntax

```ts
interface User {
  readonly id: number;     // cannot be reassigned
  name: string;
  email?: string;          // optional
  greet(): string;         // method signature
}
```

## 6. Example

```ts
interface Product {
  id: number;
  title: string;
  price: number;
  inStock?: boolean;
}

const item: Product = {
  id: 1,
  title: "Keyboard",
  price: 49.99,
};

// Extending interfaces
interface DiscountedProduct extends Product {
  discountPercent: number;
}

// Implementing an interface in a class
interface Logger {
  log(message: string): void;
}

class ConsoleLogger implements Logger {
  log(message: string): void {
    console.log(message);
  }
}
```

## 7. Real World Use Case

Interfaces define the props contract of a React component, giving consumers autocomplete and catching missing props:

```ts
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

function Button({ label, onClick, disabled }: ButtonProps) {
  // ...render...
}
```

## 8. Interview Questions

**Q1:** What is the difference between an interface and a type alias?
**A:** Both describe shapes. Interfaces are open (support declaration merging and are optimized for object shapes and class contracts), while type aliases are closed but more flexible — they can represent unions, intersections, primitives, and tuples.

**Q2:** What is declaration merging?
**A:** If you declare two interfaces with the same name, TypeScript merges their members into one. This is useful for augmenting third-party types but is not possible with type aliases.

**Q3:** Can an interface extend a class?
**A:** Yes. An interface can extend a class, inheriting its members' types (including private/protected), but the resulting interface can only be implemented by that class or its subclasses.

**Q4:** What does `readonly` do in an interface?
**A:** It marks a property as immutable after initialization — the compiler blocks reassignment, though it does not enforce deep immutability at runtime.

**Q5:** Can interfaces describe functions?
**A:** Yes, using a call signature: `interface Fn { (x: number): number; }` describes a callable value.

## 9. Common Mistakes

- Expecting `readonly` to provide runtime immutability (it is compile-time only).
- Forgetting that declaration merging can unintentionally combine same-named interfaces.
- Using an interface for a union type — that requires a type alias.
- Adding methods/properties that the implementing class forgets to define (caught by `implements`).

## 10. Advanced Notes

- Interfaces support **index signatures**: `interface Dict { [key: string]: number; }`.
- Interfaces can describe **hybrid types** (callable objects that also have properties).
- Prefer interfaces for public API object shapes (better error messages, extensibility); use type aliases for unions and computed types.
- Extending multiple interfaces: `interface C extends A, B {}` combines all members.
