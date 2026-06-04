# Debouncing

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
Debouncing is a technique that delays the execution of a function until a specified period of inactivity has passed since the last time it was invoked. Rapid repeated calls reset the timer, so the function runs only once after the bursts stop.

## 2. Simple Explanation
Imagine an elevator that waits a few seconds after the last person presses a button before closing. Every new press resets the wait. Debouncing does the same — it acts only after things go quiet.

## 3. Why It Is Used
It limits how often expensive operations run during rapid events (typing, resizing, scrolling), improving performance and avoiding unnecessary work like redundant API calls.

## 4. Key Points
- Runs the function **once** after activity stops for `delay` ms.
- Each new call **resets** the timer.
- Uses a closure to remember the timer ID.
- Different from throttling, which runs at a steady rate during activity.
- Optionally supports a "leading" (immediate) call variant.

## 5. Syntax
```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

## 6. Example
```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

const search = debounce((q) => {
  console.log("Searching:", q);
}, 300);

search("a");
search("ap");
search("app"); // only this runs, ~300ms after the last call
```
Only the final call executes once typing pauses for 300ms, avoiding a request per keystroke.

## 7. Real World Use Case
Search-as-you-type API calls, autosave drafts, validating input after the user stops typing, and handling window `resize` events efficiently.

## 8. Interview Questions
**Q1:** What is debouncing?
**A:** Delaying a function until a pause in activity; repeated calls reset the timer so it runs only after calls stop.

**Q2:** How does debouncing differ from throttling?
**A:** Debouncing waits for inactivity and runs once at the end; throttling runs at most once per interval during continuous activity.

**Q3:** What JavaScript feature makes debounce work?
**A:** Closures, which retain the timer ID between calls, plus `setTimeout`/`clearTimeout`.

**Q4:** When would you debounce vs throttle a scroll handler?
**A:** Throttle for continuous updates during scrolling; debounce to act only after scrolling stops (e.g., lazy-load on stop).

**Q5:** What is leading vs trailing debounce?
**A:** Trailing runs after the delay (default); leading runs immediately on the first call, then ignores until quiet.

## 9. Common Mistakes
- Forgetting to `clearTimeout`, so the function fires multiple times.
- Losing `this`/arguments by not using `apply(this, args)`.
- Recreating the debounced function on every render (React) — memoize it.
- Confusing debounce with throttle for the wrong use case.

## 10. Advanced Notes
- Libraries like Lodash offer `leading`, `trailing`, and `maxWait` options.
- In React, wrap debounced callbacks in `useMemo`/`useCallback` or use `useRef` for the timer.
- Cancel pending invocations on component unmount to avoid stale updates.
- Combine with `AbortController` to cancel in-flight requests from earlier inputs.
