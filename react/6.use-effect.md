# useEffect

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

`useEffect` is a React Hook that lets you run **side effects** in function components — code that interacts with the world outside React, such as data fetching, subscriptions, timers, or manual DOM updates.

## 2. Simple Explanation

Rendering should be pure (just compute JSX). But sometimes you need to do something *after* rendering: fetch data, set a timer, or listen to events. `useEffect` is where that "extra" work lives, and it runs after React paints the screen.

## 3. Why It Is Used

- **Fetch data** from an API after a component mounts.
- **Subscribe/unsubscribe** to events, sockets, or stores.
- **Synchronize** React state with non-React systems (DOM, browser APIs).
- Run **timers** and intervals.
- Clean up resources to prevent memory leaks.

## 4. Key Points

- Signature: `useEffect(setupFn, dependencies?)`.
- The **dependency array** controls when the effect re-runs:
  - `[]` → runs **once** after mount.
  - `[a, b]` → runs after mount and whenever `a` or `b` change.
  - *omitted* → runs after **every** render.
- Return a **cleanup function** to undo the effect (runs before re-run and on unmount).
- Effects run **after** the browser paints (asynchronously).
- Don't lie about dependencies — include everything the effect uses.

## 5. Syntax

```jsx
useEffect(() => {
  // setup: side effect runs here
  const id = setInterval(() => console.log("tick"), 1000);

  // cleanup: runs before next effect & on unmount
  return () => clearInterval(id);
}, [/* dependencies */]);
```

## 6. Example

```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let ignore = false; // guard against race conditions

    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => {
        if (!ignore) setUser(data);
      });

    return () => {
      ignore = true; // cleanup: ignore stale response
    };
  }, [userId]); // re-fetch when userId changes

  if (!user) return <p>Loading…</p>;
  return <h1>{user.name}</h1>;
}
```

## 7. Real World Use Case

A chat app uses `useEffect` to open a WebSocket connection when the chat room opens and to close it (cleanup) when the user leaves or switches rooms. The dependency `[roomId]` ensures it reconnects to the right room whenever the user navigates.

## 8. Interview Questions

**Q1:** What is the purpose of the dependency array in `useEffect`?
**A:** It tells React when to re-run the effect. With `[]` it runs once after mount; with `[a]` it re-runs when `a` changes; with no array it runs after every render. Listing the correct dependencies keeps the effect in sync with current props/state.

**Q2:** What is the cleanup function and when does it run?
**A:** The function returned from the effect is the cleanup. React runs it before re-running the effect (to clean up the previous one) and when the component unmounts. It's used to clear timers, unsubscribe, or abort requests, preventing memory leaks.

**Q3:** Why might an effect cause an infinite loop?
**A:** If the effect updates state that is (directly or indirectly) in its dependency array, each run triggers a re-render, which re-runs the effect, and so on. Fix it by correcting dependencies or using functional updates / stable references.

**Q4:** When does `useEffect` run relative to rendering and painting?
**A:** It runs asynchronously *after* React commits changes to the DOM and the browser paints. This avoids blocking visual updates. Use `useLayoutEffect` if you need to run before paint.

**Q5:** How do you fetch data safely to avoid race conditions in `useEffect`?
**A:** Use an `ignore`/`AbortController` flag in the cleanup so that if dependencies change before a request resolves, the stale response is ignored or aborted, preventing it from overwriting newer data.

## 9. Common Mistakes

- Missing dependencies, causing stale values ("closure" bugs).
- Including unstable objects/functions in deps, causing the effect to run every render.
- Updating state inside an effect without proper deps → infinite loop.
- Forgetting the cleanup function (leaks: timers, listeners, sockets).
- Using `useEffect` for logic that should just be computed during render (derived state).

## 10. Advanced Notes

- In **React 18 Strict Mode (dev)**, effects run **twice** on mount to surface missing cleanup — this is intentional and dev-only.
- Many effects are unnecessary; the React docs ("You Might Not Need an Effect") recommend computing derived values during render and handling events in handlers instead.
- Use **`AbortController`** to cancel fetches in cleanup for robust data fetching.
- Prefer data-fetching libraries (React Query, SWR) over hand-rolled `useEffect` fetches for caching and dedup.
- Separate **unrelated** concerns into multiple effects rather than one big effect.
