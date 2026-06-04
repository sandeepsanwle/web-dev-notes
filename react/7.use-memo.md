# useMemo

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

`useMemo` is a React Hook that **memoizes** (caches) the result of an expensive calculation, recomputing it only when its dependencies change. It returns a cached value.

## 2. Simple Explanation

If a calculation is slow and its inputs haven't changed, why redo it on every render? `useMemo` remembers the last result and reuses it until the inputs change — saving work and keeping the UI snappy.

## 3. Why It Is Used

- Avoid **expensive recalculations** on every render (sorting, filtering, heavy math).
- Keep **referential equality** of objects/arrays so memoized children don't re-render.
- Stabilize values passed to dependency arrays of other Hooks.

## 4. Key Points

- Signature: `const value = useMemo(() => computeValue(), [deps])`.
- Returns a **value** (compare to `useCallback`, which returns a function).
- Only recomputes when a dependency changes; otherwise returns the cached value.
- It's an **optimization**, not a guarantee — React may discard the cache. Don't rely on it for correctness.
- Don't overuse it; for cheap calculations the overhead isn't worth it.

## 5. Syntax

```jsx
import { useMemo } from "react";

const sortedItems = useMemo(() => {
  return [...items].sort((a, b) => a.price - b.price);
}, [items]); // recompute only when `items` changes
```

## 6. Example

```jsx
import { useState, useMemo } from "react";

function ProductList({ products }) {
  const [query, setQuery] = useState("");

  // Expensive filter recomputed only when products or query change
  const filtered = useMemo(() => {
    return products.filter((p) =>
      p.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [products, query]);

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ul>
        {filtered.map((p) => (
          <li key={p.id}>{p.name}</li>
        ))}
      </ul>
    </>
  );
}
```

## 7. Real World Use Case

A data dashboard renders a table of thousands of rows with sorting and filtering. Wrapping the sort/filter pipeline in `useMemo` (keyed on the data, sort column, and filters) prevents re-sorting on every unrelated render — e.g. when a tooltip opens — keeping interactions smooth.

## 8. Interview Questions

**Q1:** What does `useMemo` do and what does it return?
**A:** It memoizes the result of a function, returning the cached *value* and only recomputing when a listed dependency changes. It's used to skip expensive recalculations between renders.

**Q2:** What is the difference between `useMemo` and `useCallback`?
**A:** `useMemo(fn, deps)` returns the memoized *return value* of `fn`; `useCallback(fn, deps)` returns the memoized *function* itself. `useCallback(fn, deps)` is equivalent to `useMemo(() => fn, deps)`.

**Q3:** Does `useMemo` guarantee the value won't be recomputed?
**A:** No. It's a performance hint, not a semantic guarantee. React may throw away the cache (e.g. to free memory) and recompute. Your code must remain correct even if it recomputes, so never put required side effects in it.

**Q4:** When should you NOT use `useMemo`?
**A:** For cheap computations, where memoization overhead (storing deps, comparing) outweighs the savings. Premature `useMemo` everywhere adds complexity and can even hurt performance.

**Q5:** How does `useMemo` help prevent child re-renders?
**A:** Passing a freshly created object/array to a `React.memo` child every render breaks shallow equality and forces re-render. Memoizing that object with `useMemo` keeps the same reference, so the memoized child skips re-rendering.

## 9. Common Mistakes

- Overusing it for trivial calculations, adding noise and overhead.
- Wrong/missing dependencies, returning stale cached values.
- Putting side effects inside the memo function (it must be pure).
- Expecting it to always cache — relying on it for correctness.
- Memoizing a value but still recreating the deps every render, defeating the purpose.

## 10. Advanced Notes

- `useMemo` and `useCallback` are most impactful when combined with `React.memo` and stable references throughout a subtree.
- The **React Compiler** (React 19+) can auto-memoize, reducing the need for manual `useMemo`/`useCallback`.
- Measure first with the **React DevTools Profiler**; don't guess where memoization helps.
- For deriving values from props/state, prefer plain calculation during render unless profiling shows a real cost.
- The dependency array uses `Object.is` comparison — same caveats as `useEffect` deps.
