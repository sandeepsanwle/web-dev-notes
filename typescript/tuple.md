# Tuple

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **tuple** is a fixed-length array where the type of each element at each position is known. Unlike a regular array, the order and types of elements are significant and enforced.

## 2. Simple Explanation

A tuple is an array with assigned seats. Position 0 must be a `string`, position 1 must be a `number`, and so on. The compiler knows exactly what lives at each index.

## 3. Why It Is Used

- Represent a fixed group of values with different types (e.g. a key/value pair).
- Return multiple values from a function with precise types.
- Model coordinates, RGB colors, or React-style `[state, setState]` returns.
- Enforce a known length and element order.

## 4. Key Points

- Declared with bracketed types: `[string, number]`.
- Element order and count are enforced by the compiler.
- Supports **optional** (`[string, number?]`) and **rest** (`[string, ...number[]]`) elements.
- **Labeled tuples** improve readability: `[name: string, age: number]`.
- Use `as const` to create readonly tuples with literal types.

## 5. Syntax

```ts
let pair: [string, number] = ["age", 30];
let point: [number, number] = [10, 20];
type Entry = [key: string, value: number]; // labeled
```

## 6. Example

```ts
// Basic tuple
let user: [string, number] = ["Ada", 36];
let name = user[0]; // string
let age = user[1];  // number

// Function returning a tuple
function useCounter(): [number, () => void] {
  let count = 0;
  const increment = () => { count++; };
  return [count, increment];
}
const [value, inc] = useCounter();

// Rest elements
type Path = [root: string, ...segments: string[]];
const p: Path = ["/", "users", "1", "profile"];

// Readonly tuple
const rgb = [255, 0, 128] as const; // readonly [255, 0, 128]
```

## 7. Real World Use Case

The classic React `useState` hook returns a tuple so consumers can name both the value and its setter:

```ts
function useState<T>(initial: T): [T, (next: T) => void] {
  let state = initial;
  const setState = (next: T) => { state = next; };
  return [state, setState];
}

const [count, setCount] = useState(0); // count: number, setCount: (n: number) => void
```

## 8. Interview Questions

**Q1:** How does a tuple differ from a regular array?
**A:** An array (`number[]`) has a single element type and arbitrary length. A tuple (`[string, number]`) has a fixed length with a specific type at each position, enforced by the compiler.

**Q2:** What are labeled tuple elements?
**A:** Labels like `[name: string, age: number]` add names for documentation and better tooling. They are purely for readability and do not change runtime behavior.

**Q3:** Can tuples have optional or rest elements?
**A:** Yes. `[string, number?]` makes the second element optional, and `[string, ...number[]]` allows a variable number of trailing elements.

**Q4:** What does `as const` do to a tuple?
**A:** It makes the tuple `readonly` and narrows each element to its literal type, useful for fixed configuration values and preserving exact types.

**Q5:** Why are tuples useful for function returns?
**A:** They let a function return multiple values of different types in a fixed order, which the caller can destructure with full type safety.

## 9. Common Mistakes

- Treating a tuple like an array and pushing extra elements (allowed by `push` but breaks the intended fixed length).
- Forgetting that array methods like `push`/`pop` can bypass tuple length guarantees.
- Mixing up element order, which the compiler enforces strictly.
- Not using `as const` when literal, readonly values are needed.

## 10. Advanced Notes

- Tuples power **variadic tuple types** for typing functions like `concat` and `curry` precisely.
- Rest elements in tuples enable typing of spread arguments: `function f(...args: [string, number]) {}`.
- `readonly [A, B]` prevents mutation methods at compile time.
- Tuples integrate with mapped and conditional types to transform parameter lists, the basis of advanced utility types.
