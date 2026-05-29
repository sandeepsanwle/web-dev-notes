# useCallback

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

`useCallback` is a React Hook that **memoizes a function definition**, returning the same function reference between renders as long as its dependencies don't change.

## 2. Simple Explanation

Every render creates brand-new function objects. Usually that's fine, but if you pass a function to a memoized child or use it as a dependency, a new reference each time defeats the optimization. `useCallback` keeps the *same* function until its inputs change.

## 3. Why It Is Used

- Keep a **stable function reference** for `React.memo` children so they don't re-render needlessly.
- Provide stable functions for `useEffect`/`useMemo` **dependency arrays**.
- Avoid recreating callbacks passed to many children or expensive subtrees.

## 4. Key Points

- Signature: `const fn = useCallback(callback, [deps])`.
- Returns the **function** itself (compare to `useMemo`, which returns a value).
- `useCallback(fn, deps)` ≡ `useMemo(() => fn, deps)`.
- Only useful when the stable reference actually matters (memoized child / dependency).
- Like `useMemo`, it's an optimization, not a correctness guarantee.

## 5. Syntax

```jsx
import { useCallback } from "react";

const handleClick = useCallback(() => {
  doSomething(id);
}, [id]); // new function only when `id` changes
```

## 6. Example

```jsx
import { useState, useCallback, memo } from "react";

const Child = memo(function Child({ onAdd }) {
  console.log("Child rendered");
  return <button onClick={onAdd}>Add</button>;
});

function Parent() {
  const [count, setCount] = useState(0);
  const [other, setOther] = useState(0);

  // Stable reference: Child won't re-render when only `other` changes
  const handleAdd = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  return (
    <>
      <p>Count: {count}</p>
      <button onClick={() => setOther((o) => o + 1)}>Other: {other}</button>
      <Child onAdd={handleAdd} />
    </>
  );
}
```

## 7. Real World Use Case

In a large form with many memoized field components, each field receives an `onChange` callback. Wrapping these handlers in `useCallback` keeps their references stable so unrelated state changes (like a validation message updating) don't re-render every field, improving performance on big forms.

## 8. Interview Questions

**Q1:** What does `useCallback` do?
**A:** It returns a memoized version of a callback that keeps the same reference between renders until one of its dependencies changes. This is useful for passing stable functions to memoized children or to other Hooks' dependency arrays.

**Q2:** How is `useCallback` related to `useMemo`?
**A:** `useCallback(fn, deps)` is exactly `useMemo(() => fn, deps)`. `useMemo` memoizes a computed value; `useCallback` memoizes the function itself.

**Q3:** Does `useCallback` improve performance by itself?
**A:** Not usually. It only helps when the stable reference is consumed by something that cares about identity — a `React.memo` child or a dependency array. Wrapping every function in `useCallback` without that adds overhead and complexity.

**Q4:** Why does passing an inline arrow function to a `React.memo` child break memoization?
**A:** A new arrow function is created on each render, so the child's prop reference changes, failing the shallow equality check and forcing a re-render. `useCallback` gives a stable reference to prevent that.

**Q5:** What happens if you provide an empty dependency array but the callback uses changing state?
**A:** The callback "captures" the initial values (stale closure) and won't see updated state. Either include the values in deps or use the functional updater form (`setX(prev => ...)`) to access the latest state safely.

## 9. Common Mistakes

- Wrapping every function in `useCallback` even when no memoized child/dependency benefits.
- Empty deps causing stale closures over props/state.
- Forgetting that `useCallback` only helps if the child is also wrapped in `React.memo`.
- Listing unstable deps, so the callback changes every render anyway.
- Confusing it with `useMemo` (value vs function).

## 10. Advanced Notes

- Use the functional updater (`setState(prev => ...)`) to keep deps empty while staying correct.
- Combine `useCallback` + `React.memo` + `useMemo` consistently across a subtree; partial use often yields no benefit.
- The **React Compiler** can auto-memoize callbacks, reducing manual `useCallback` usage in the future.
- For event handlers that always need the latest values without changing identity, the "latest ref" pattern (storing the function in a `useRef`) is an alternative.
- Always profile with React DevTools before optimizing — measure, don't guess.
