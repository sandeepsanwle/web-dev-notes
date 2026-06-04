# Hooks

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Hooks are special functions (prefixed with `use`) that let function components "hook into" React features like state, lifecycle, and context — without writing a class.

## 2. Simple Explanation

Before Hooks, only class components could have state and lifecycle methods. Hooks gave that power to simple function components. `useState` adds memory, `useEffect` runs side effects, `useContext` reads shared data — each Hook plugs a capability into your function.

## 3. Why It Is Used

- Add **state** and **side effects** to function components.
- **Reuse stateful logic** between components via custom Hooks (without HOCs/render props).
- Write **cleaner, shorter** components than class equivalents.
- Group related logic together instead of scattering it across lifecycle methods.

## 4. Key Points

- **Rules of Hooks:** call Hooks only at the **top level** (not in loops, conditions, or nested functions) and only from **React functions** (components or custom Hooks).
- Hook names must start with **`use`**.
- Hooks rely on a **stable call order** between renders — that's why conditional calls are forbidden.
- Built-in Hooks: `useState`, `useEffect`, `useContext`, `useReducer`, `useMemo`, `useCallback`, `useRef`, `useLayoutEffect`, `useId`, etc.
- **Custom Hooks** are functions that call other Hooks to share logic.

## 5. Syntax

```jsx
import { useState, useEffect, useRef } from "react";

function Example() {
  const [value, setValue] = useState(0);   // state
  const inputRef = useRef(null);           // mutable ref

  useEffect(() => {
    document.title = `Value: ${value}`;    // side effect
  }, [value]);                             // dependency array

  return <input ref={inputRef} value={value} onChange={(e) => setValue(+e.target.value)} />;
}
```

## 6. Example

```jsx
import { useState, useEffect } from "react";

// Custom Hook: reusable logic for tracking window width
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const onResize = () => setWidth(window.innerWidth);
    window.addEventListener("resize", onResize);
    return () => window.removeEventListener("resize", onResize); // cleanup
  }, []);

  return width;
}

function Responsive() {
  const width = useWindowWidth();
  return <p>{width < 768 ? "Mobile view" : "Desktop view"}</p>;
}
```

## 7. Real World Use Case

A custom `useFetch(url)` Hook encapsulates loading, error, and data state plus the fetch logic. Any component can call `const { data, loading, error } = useFetch("/api/users")` and get consistent data-fetching behavior, eliminating duplicated code across the app.

## 8. Interview Questions

**Q1:** What are the Rules of Hooks and why do they exist?
**A:** Call Hooks only at the top level (never inside loops, conditions, or nested functions) and only from React function components or custom Hooks. React tracks Hook state by call order, so a consistent order across renders is required for it to associate state correctly.

**Q2:** Why were Hooks introduced?
**A:** To let function components use state and lifecycle features, and to make stateful logic reusable without HOCs or render props (which caused "wrapper hell"). They also let you organize code by concern rather than by lifecycle method.

**Q3:** What is a custom Hook?
**A:** A custom Hook is a JavaScript function whose name starts with `use` and that calls other Hooks. It extracts and shares reusable stateful logic between components, e.g. `useFetch`, `useForm`, `useLocalStorage`.

**Q4:** Can you call a Hook conditionally? Why or why not?
**A:** No. Hooks must be called in the same order every render. A conditional call would shift the order and corrupt how React maps Hooks to their stored state, causing bugs. Put the condition *inside* the Hook instead.

**Q5:** What's the difference between `useEffect` and `useLayoutEffect`?
**A:** `useEffect` runs asynchronously after the browser paints; `useLayoutEffect` runs synchronously after DOM mutations but before paint. Use `useLayoutEffect` only when you must read/measure layout and mutate the DOM before the user sees it, since it can block painting.

## 9. Common Mistakes

- Calling Hooks conditionally or inside loops.
- Calling Hooks from regular (non-React) functions.
- Missing or incorrect dependency arrays in `useEffect`/`useMemo`/`useCallback`.
- Naming a custom Hook without the `use` prefix (breaks the linter's checks).
- Overusing `useState` for derived values that could just be computed.

## 10. Advanced Notes

- The **`eslint-plugin-react-hooks`** package enforces the Rules of Hooks and dependency correctness — keep it enabled.
- Hooks store data in a linked list on the component's fiber, keyed by call index — this is why order matters.
- `useRef` holds a mutable value that persists across renders **without** triggering re-renders.
- React 18 added `useId`, `useTransition`, `useDeferredValue`, and `useSyncExternalStore` for concurrent rendering and external stores.
- React 19 adds `use()` for unwrapping promises/context and improved form Hooks like `useActionState`.
