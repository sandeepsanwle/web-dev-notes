# Type Assertion

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **type assertion** tells the compiler to treat a value as a specific type, overriding its inferred type. It does not change the value at runtime — it only changes how the compiler views it.

## 2. Simple Explanation

A type assertion is you telling TypeScript, "Trust me, I know what this is." It is a promise to the compiler, not a conversion. If you are wrong, you can get runtime errors the compiler would otherwise have caught.

## 3. Why It Is Used

- Narrow a broad type (like `unknown` or `HTMLElement`) when you have more knowledge than the compiler.
- Work with DOM APIs that return general types.
- Bridge gaps when migrating JavaScript or handling external data.
- Tell the compiler the precise type after a runtime check it cannot follow.

## 4. Key Points

- Two syntaxes: `value as Type` (preferred) and `<Type>value` (not allowed in `.tsx`).
- It is **compile-time only** — no runtime conversion or validation happens.
- Assertions can be unsafe — incorrect assertions cause runtime bugs.
- `as const` is a special literal assertion (readonly + narrow).
- `as unknown as Type` is a "double assertion" escape hatch — use sparingly.

## 5. Syntax

```ts
let value: unknown = "hello";
let str = value as string;       // preferred syntax
let str2 = <string>value;        // angle-bracket (not in .tsx)
```

## 6. Example

```ts
// DOM example – getElementById returns HTMLElement | null
const input = document.getElementById("email") as HTMLInputElement;
console.log(input.value); // .value exists on HTMLInputElement

// Asserting parsed JSON
interface Config { debug: boolean }
const data = JSON.parse('{"debug":true}') as Config;
console.log(data.debug);

// as const
const directions = ["up", "down"] as const;
// type: readonly ["up", "down"]

// Non-null assertion (!): "this is definitely not null"
function getLength(text?: string) {
  return text!.length; // asserts text is not undefined
}
```

## 7. Real World Use Case

Working with the DOM or third-party data where the type is known to the developer but not to the compiler:

```ts
// We know this canvas exists in the page
const canvas = document.querySelector("#chart") as HTMLCanvasElement;
const ctx = canvas.getContext("2d");

// Narrowing an event target
function onClick(e: Event) {
  const target = e.target as HTMLButtonElement;
  console.log(target.disabled);
}
```

## 8. Interview Questions

**Q1:** What is a type assertion and does it change the value at runtime?
**A:** It tells the compiler to treat a value as a given type. It is purely compile-time — the runtime value is untouched and no conversion or validation occurs.

**Q2:** What is the difference between type assertion and type casting?
**A:** Casting (in languages like Java/C#) performs a real runtime conversion. A TypeScript assertion only changes the compiler's view; there is no runtime effect.

**Q3:** What is the non-null assertion operator (`!`)?
**A:** It asserts that a value is not `null` or `undefined`, removing those from the type. It is unsafe if the value can actually be nullish at runtime.

**Q4:** Why are type assertions considered risky?
**A:** They bypass the compiler's safety checks. If the asserted type is wrong, the error surfaces at runtime instead of compile time.

**Q5:** What is a double assertion and when is it used?
**A:** `value as unknown as Target` forces a conversion between unrelated types by going through `unknown`. It is a last-resort escape hatch and signals the type model may need rethinking.

## 9. Common Mistakes

- Using assertions to silence errors instead of validating data, leading to runtime crashes.
- Overusing `!` (non-null assertion) where a real null check is safer.
- Asserting incompatible types and relying on double assertions as a habit.
- Confusing assertions with runtime validation — they validate nothing.

## 10. Advanced Notes

- Prefer **type guards** and runtime validation (e.g. Zod) over assertions for untrusted external data.
- **`as const`** assertions produce readonly literal types, ideal for config and discriminated-union tags.
- **Assertion functions** (`function assert(x): asserts x is T`) combine a runtime check with a compile-time narrowing.
- The `satisfies` operator is often a safer alternative: it checks compatibility without widening or unsafely overriding the inferred type.
