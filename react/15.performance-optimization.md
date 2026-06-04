# Performance Optimization

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

Performance optimization in React is the practice of reducing unnecessary work — re-renders, expensive computations, and large bundles — so the app stays fast, responsive, and efficient.

## 2. Simple Explanation

React is fast by default, but apps can slow down when components re-render too often, recompute heavy things, or ship too much JavaScript. Optimization means finding those bottlenecks (with profiling) and fixing them with the right tools: memoization, code-splitting, and smarter rendering.

## 3. Why It Is Used

- Keep the UI **responsive** (no jank during typing, scrolling, animations).
- Reduce **unnecessary re-renders** of components.
- Shrink **bundle size** for faster load times.
- Improve **perceived performance** and user experience.

## 4. Key Points

- **Measure first** with the React DevTools Profiler — don't optimize blindly.
- **`React.memo`** skips re-rendering when props are shallow-equal.
- **`useMemo`** caches expensive values; **`useCallback`** caches function references.
- **Code-splitting** with `React.lazy` + `<Suspense>` defers non-critical code.
- **List virtualization** (react-window/react-virtualized) renders only visible rows.
- Stable **keys**, avoiding inline objects/functions, and lifting state appropriately all reduce re-renders.

## 5. Syntax

```jsx
import { memo, useMemo, useCallback, lazy, Suspense } from "react";

const Heavy = memo(function Heavy({ data }) { /* ... */ });

const sorted = useMemo(() => [...data].sort(), [data]);
const onClick = useCallback(() => doThing(id), [id]);

const Chart = lazy(() => import("./Chart"));
// <Suspense fallback={<Spinner/>}><Chart/></Suspense>
```

## 6. Example

```jsx
import { memo, useState, useCallback } from "react";

const ExpensiveList = memo(function ExpensiveList({ items, onSelect }) {
  console.log("List rendered");
  return (
    <ul>
      {items.map((i) => (
        <li key={i.id} onClick={() => onSelect(i.id)}>{i.name}</li>
      ))}
    </ul>
  );
});

function Page({ items }) {
  const [count, setCount] = useState(0);

  // Stable callback → ExpensiveList won't re-render when only `count` changes
  const handleSelect = useCallback((id) => console.log("picked", id), []);

  return (
    <>
      <button onClick={() => setCount((c) => c + 1)}>Count {count}</button>
      <ExpensiveList items={items} onSelect={handleSelect} />
    </>
  );
}
```

## 7. Real World Use Case

A social feed renders thousands of posts. Without optimization, scrolling causes massive re-renders and layout work. By virtualizing the list (rendering only visible posts), memoizing each post component, and lazy-loading images and the comment editor, the feed scrolls at 60fps even on low-end devices.

## 8. Interview Questions

**Q1:** What causes unnecessary re-renders in React, and how do you prevent them?
**A:** A component re-renders when its parent re-renders or its state/props change. Unnecessary re-renders come from new prop references (inline objects/functions), non-memoized children, or context value changes. Prevent them with `React.memo`, `useMemo`/`useCallback` for stable references, and splitting context.

**Q2:** How do `React.memo`, `useMemo`, and `useCallback` differ?
**A:** `React.memo` memoizes a **component** (skips re-render on shallow-equal props). `useMemo` memoizes a **value** (skips recomputation). `useCallback` memoizes a **function reference**. They're often used together so memoized children actually benefit.

**Q3:** What is list virtualization and when do you need it?
**A:** Virtualization (windowing) renders only the rows currently visible in the viewport plus a small buffer, instead of the entire list. It's essential for very long lists/tables (thousands of items) to keep DOM size and render time small. Libraries: react-window, react-virtualized, TanStack Virtual.

**Q4:** How does code-splitting improve performance?
**A:** It breaks the bundle into smaller chunks loaded on demand (via `React.lazy`/dynamic `import()`), so users download only what they need initially. This reduces initial load time and time-to-interactive. `<Suspense>` shows a fallback while chunks load.

**Q5:** How do you find performance bottlenecks in a React app?
**A:** Use the React DevTools Profiler to see which components render, how often, and why; use the browser Performance tab for CPU/flame charts; and Lighthouse/Web Vitals for load metrics. Always measure before and after a change to confirm impact.

## 9. Common Mistakes

- Optimizing prematurely without profiling — adding complexity for no gain.
- Passing new inline objects/functions to memoized children, defeating `React.memo`.
- Putting frequently-changing values in Context, re-rendering all consumers.
- Overusing `useMemo`/`useCallback` where the cost exceeds the benefit.
- Rendering huge lists without virtualization.

## 10. Advanced Notes

- **React 18 concurrent features**: `useTransition`/`startTransition` mark non-urgent updates so urgent ones (typing) stay responsive; `useDeferredValue` defers expensive derived values.
- The **React Compiler** (React 19+) can auto-memoize, reducing manual memoization.
- Optimize **Web Vitals** (LCP, INP, CLS); SSR/streaming (Next.js) improves initial load.
- Debounce/throttle expensive event handlers (search, resize, scroll).
- Avoid large synchronous work on the main thread; consider web workers for heavy computation.
