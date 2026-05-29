# Lifecycle

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

The component lifecycle is the series of phases a React component goes through: **mounting** (created and inserted into the DOM), **updating** (re-rendered due to prop/state changes), and **unmounting** (removed from the DOM).

## 2. Simple Explanation

A component is "born," "lives" (updating as data changes), and eventually "dies." React lets you run code at each of these moments — for setup when it appears, reactions when it changes, and cleanup when it leaves. In function components, the `useEffect` Hook covers all three.

## 3. Why It Is Used

- Run **setup** logic on mount (fetch data, subscribe, start timers).
- Respond to **updates** when props/state change.
- Perform **cleanup** on unmount (unsubscribe, clear timers) to avoid leaks.
- Control side effects at the right time relative to rendering.

## 4. Key Points

- Three phases: **Mounting → Updating → Unmounting**.
- **Class** lifecycle methods: `constructor`, `render`, `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`, plus `getDerivedStateFromProps`, `shouldComponentUpdate`, `getSnapshotBeforeUpdate`.
- **Function** components use `useEffect` (and `useLayoutEffect`) to cover the same needs.
- Cleanup functions returned from effects map to `componentWillUnmount` and pre-update cleanup.
- Rendering should stay pure; side effects belong in effects/lifecycle methods.

## 5. Syntax

```jsx
// Class lifecycle
class Timer extends React.Component {
  componentDidMount() { /* after first render */ }
  componentDidUpdate(prevProps) { /* after updates */ }
  componentWillUnmount() { /* before removal */ }
  render() { return <div />; }
}

// Function equivalent with useEffect
useEffect(() => {
  // mount + update
  return () => {
    // unmount / cleanup
  };
}, [deps]);
```

## 6. Example

```jsx
import { useState, useEffect } from "react";

function Clock() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    // componentDidMount: start the interval
    const id = setInterval(() => setTime(new Date()), 1000);

    // componentWillUnmount: stop the interval
    return () => clearInterval(id);
  }, []); // empty deps → run once on mount

  return <p>{time.toLocaleTimeString()}</p>;
}
```

## 7. Real World Use Case

A live stock-ticker component subscribes to a price feed on mount, updates the displayed price as new data streams in, and unsubscribes on unmount so it doesn't keep receiving (and leaking) data after the user navigates away.

## 8. Interview Questions

**Q1:** What are the main phases of a React component's lifecycle?
**A:** Mounting (component is created and inserted into the DOM), Updating (re-rendered when props or state change), and Unmounting (removed from the DOM). Some references add an Error phase via error boundaries.

**Q2:** How do lifecycle methods map to `useEffect`?
**A:** `useEffect(fn, [])` ≈ `componentDidMount`; `useEffect(fn, [deps])` ≈ `componentDidUpdate` for those deps; the returned cleanup ≈ `componentWillUnmount`. A single effect can express mount + update + cleanup together.

**Q3:** What is `shouldComponentUpdate` and its functional equivalent?
**A:** `shouldComponentUpdate(nextProps, nextState)` lets a class skip re-rendering by returning `false`. The functional equivalents are `React.memo` (skip re-render on shallow-equal props) and `useMemo`/`useCallback` for stable references.

**Q4:** Why is `componentWillUnmount` (or effect cleanup) important?
**A:** It releases resources — clearing timers, removing event listeners, closing sockets, cancelling requests — preventing memory leaks and errors from updating an unmounted component.

**Q5:** What's the difference between `componentDidMount` and `useLayoutEffect`/`useEffect` timing?
**A:** `componentDidMount` and `useLayoutEffect` run synchronously after DOM mutations, before the browser paints. `useEffect` runs asynchronously after paint. Use layout effects only when you must measure or mutate the DOM before the user sees it.

## 9. Common Mistakes

- Forgetting cleanup, causing leaks (timers, listeners, subscriptions).
- Missing dependencies in `useEffect`, leading to stale data or skipped updates.
- Doing side effects during render instead of in effects/lifecycle methods.
- Causing infinite update loops by setting state unconditionally in `componentDidUpdate`/effects.
- Relying on deprecated `componentWillMount`/`componentWillReceiveProps` (unsafe legacy methods).

## 10. Advanced Notes

- **Error boundaries** add an error phase via `static getDerivedStateFromError` and `componentDidCatch` (class-only feature).
- In **React 18 Strict Mode (dev)**, components mount, unmount, and remount once to catch missing cleanup — effects run twice intentionally.
- `getSnapshotBeforeUpdate` captures DOM info (like scroll position) right before mutations are applied; pair with `componentDidUpdate`.
- Function components don't have lifecycle "methods" — they re-run entirely each render, with Hooks managing persistence and effects.
- Concurrent rendering means render phase work may be paused/restarted, so never put side effects in render.
