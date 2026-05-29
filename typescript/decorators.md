# Decorators

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

A **decorator** is a special kind of declaration that can be attached to a class, method, accessor, property, or parameter to observe, modify, or replace its definition. Decorators are functions prefixed with `@` applied at design time.

## 2. Simple Explanation

A decorator is like a sticker you put on a class or method that adds extra behavior. Instead of changing the code inside, you wrap it from the outside — for logging, validation, dependency injection, and more.

## 3. Why It Is Used

- Add cross-cutting behavior (logging, caching, validation) without cluttering core logic.
- Power frameworks like Angular and NestJS for dependency injection and metadata.
- Register or annotate classes and members declaratively.
- Keep business logic clean by separating concerns.

## 4. Key Points

- Applied with `@expression` syntax above the target.
- There are several kinds: **class**, **method**, **accessor**, **property**, and **parameter** decorators.
- **Decorator factories** are functions that return a decorator, enabling configuration: `@Component({...})`.
- The legacy (experimental) decorators require `experimentalDecorators` in `tsconfig.json`; TC39/TS 5.0 introduced a standardized form with a different signature.
- Evaluation order: decorators evaluate top-to-bottom but apply bottom-to-top.

## 5. Syntax

```ts
// Class decorator
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@sealed
class Example {}

// Decorator factory (configurable)
function Log(prefix: string) {
  return function (target: any, key: string, descriptor: PropertyDescriptor) {
    // wrap the method
  };
}
```

## 6. Example

```ts
// (legacy decorators: enable "experimentalDecorators" in tsconfig)

// Method decorator that logs calls
function LogCall(
  target: any,
  propertyKey: string,
  descriptor: PropertyDescriptor
) {
  const original = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${propertyKey} with`, args);
    const result = original.apply(this, args);
    console.log(`Result:`, result);
    return result;
  };
  return descriptor;
}

class Calculator {
  @LogCall
  add(a: number, b: number): number {
    return a + b;
  }
}

new Calculator().add(2, 3);
// Logs: Calling add with [2, 3]  /  Result: 5
```

## 7. Real World Use Case

Angular and NestJS rely heavily on decorators for declarative configuration and dependency injection:

```ts
// NestJS-style controller
@Controller("users")
class UsersController {
  @Get(":id")
  findOne(@Param("id") id: string) {
    return { id };
  }
}
```

## 8. Interview Questions

**Q1:** What is a decorator in TypeScript?
**A:** A function applied with `@` syntax to a class or its members to observe, modify, or replace their definitions, enabling declarative cross-cutting behavior.

**Q2:** What is a decorator factory?
**A:** A function that returns a decorator, allowing the decorator to accept configuration arguments, e.g. `@Component({ selector: "app" })`.

**Q3:** What are the different types of decorators?
**A:** Class, method, accessor, property, and parameter decorators — each receiving different arguments describing the decorated target.

**Q4:** In what order are multiple decorators applied?
**A:** Decorator expressions are evaluated top-to-bottom, but the resulting decorator functions are applied bottom-to-top (closest to the declaration first).

**Q5:** What is the difference between legacy and standard (TC39) decorators?
**A:** Legacy decorators are experimental, require `experimentalDecorators`, and often use `reflect-metadata`. The standardized decorators (TS 5.0+) follow the TC39 proposal with a different function signature and context object, and do not need the experimental flag.

## 9. Common Mistakes

- Forgetting to enable `experimentalDecorators` (and `emitDecoratorMetadata` when using metadata) for legacy decorators.
- Mixing legacy and standard decorator signatures, which are incompatible.
- Mutating shared state in decorators, causing surprising side effects.
- Assuming method decorators run per call — they run once, at class definition time.

## 10. Advanced Notes

- **`reflect-metadata`** enables runtime type metadata (`emitDecoratorMetadata`), which DI frameworks use to resolve dependencies.
- Standard TS 5.0 decorators receive a `context` object describing the kind, name, and `addInitializer` hook.
- Parameter decorators cannot change behavior alone; they record metadata consumed elsewhere (e.g. by a DI container).
- Decorators are one of the rare TypeScript features that emit runtime code, so they affect bundle size and execution.
