# Enum

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

An **enum** (enumeration) is a named set of related constant values. It gives friendly names to a fixed collection of values, such as directions, roles, or status codes.

## 2. Simple Explanation

An enum is like a labelled menu of choices. Instead of remembering that `0` means "North" and `1` means "East", you write `Direction.North` and `Direction.East` — readable and self-documenting.

## 3. Why It Is Used

- Replace "magic numbers" and loose strings with named constants.
- Group related constants under a single namespace.
- Improve readability and reduce typos (autocomplete-backed).
- Centralize a fixed set of allowed values.

## 4. Key Points

- **Numeric enums** auto-increment from 0 by default.
- **String enums** require an explicit value for each member.
- Numeric enums support **reverse mapping** (value → name); string enums do not.
- `const enum` is fully inlined at compile time (no runtime object).
- Many teams prefer **union of string literals** over enums for simplicity.

## 5. Syntax

```ts
enum Direction {
  North, // 0
  East,  // 1
  South, // 2
  West,  // 3
}

enum Role {
  Admin = "ADMIN",
  User = "USER",
}
```

## 6. Example

```ts
enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE",
  Pending = "PENDING",
}

function describe(s: Status): string {
  switch (s) {
    case Status.Active:
      return "User is active";
    case Status.Inactive:
      return "User is inactive";
    case Status.Pending:
      return "Awaiting approval";
  }
}

console.log(describe(Status.Active)); // "User is active"

// Numeric enum reverse mapping
enum Level { Low, Medium, High }
console.log(Level[2]); // "High"
```

## 7. Real World Use Case

Modelling HTTP status handling or user roles where a fixed set of values is checked across the app:

```ts
enum HttpStatus {
  OK = 200,
  NotFound = 404,
  ServerError = 500,
}

function handle(code: HttpStatus) {
  if (code === HttpStatus.NotFound) return "Resource missing";
  return "Handled";
}
```

## 8. Interview Questions

**Q1:** What is the difference between a numeric and a string enum?
**A:** Numeric enums auto-increment and support reverse mapping (value → name). String enums require explicit values, are more readable in logs/debugging, but have no reverse mapping.

**Q2:** What is a `const enum` and why use it?
**A:** A `const enum` is inlined at compile time — its members are replaced by their literal values and no runtime object is generated, reducing bundle size. The trade-off is reduced flexibility and some tooling limitations.

**Q3:** What is reverse mapping in enums?
**A:** For numeric enums, TypeScript generates both name→value and value→name mappings, so `Level[2]` returns `"High"`. String enums do not get reverse mappings.

**Q4:** Why might you prefer a union of string literals over an enum?
**A:** Union literals (`type Status = "active" | "inactive"`) have zero runtime footprint, are simpler, and work seamlessly with structural typing, whereas enums generate runtime code and are nominally typed.

**Q5:** Are enums type-safe?
**A:** String enums are nominal and quite safe. Plain numeric enums are looser — any number is assignable to a numeric enum type in some contexts, which can be a pitfall.

## 9. Common Mistakes

- Expecting reverse mapping on string enums (only numeric enums have it).
- Assigning arbitrary numbers to numeric enum-typed variables unintentionally.
- Using `const enum` in projects with isolated modules / certain bundlers where it is unsupported.
- Forgetting that regular enums emit runtime JavaScript (unlike type-only constructs).

## 10. Advanced Notes

- `const enum` removes the runtime object but is incompatible with `isolatedModules` and some build setups.
- Prefer string-literal unions plus an `as const` object for tree-shakeable, runtime-light alternatives.
- Enums are one of the few TypeScript features that emit runtime code, breaking the "types are erased" rule.
- Use exhaustive `switch` with a `never` default to ensure all enum members are handled.
