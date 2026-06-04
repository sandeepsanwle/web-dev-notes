# Throttling

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
Throttling is a technique that ensures a function executes at most once within a specified time interval, regardless of how many times it is triggered. It enforces a steady maximum execution rate during continuous events.

## 2. Simple Explanation
Throttling is like a turnstile that lets one person through every few seconds no matter how many are pushing. The function runs on a fixed schedule, ignoring extra calls in between.

## 3. Why It Is Used
It caps how frequently a handler runs during high-frequency events (scroll, mousemove, resize), keeping the UI smooth and reducing CPU/network load while still updating regularly.

## 4. Key Points
- Runs at most **once per interval** during continuous calls.
- Unlike debounce, it fires **regularly** while activity continues.
- Typically implemented with a timestamp or a timer flag.
- Good for events needing periodic updates, not just an end result.
- Can support leading and/or trailing execution.

## 5. Syntax
```js
function throttle(fn, limit) {
  let lastCall = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      fn.apply(this, args);
    }
  };
}
```

## 6. Example
```js
function throttle(fn, limit) {
  let lastCall = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      fn.apply(this, args);
    }
  };
}

const onScroll = throttle(() => {
  console.log("scroll position:", window.scrollY);
}, 200);

window.addEventListener("scroll", onScroll); // runs at most every 200ms
```
Even if scroll fires hundreds of times, the handler runs at most once every 200ms.

## 7. Real World Use Case
Scroll position tracking, infinite scroll triggers, drag/resize handlers, rate-limiting button clicks, and sending periodic position updates in games or maps.

## 8. Interview Questions
**Q1:** What is throttling?
**A:** Restricting a function to run at most once per fixed interval during continuous triggering.

**Q2:** Throttle vs debounce — when to use which?
**A:** Throttle for regular updates during continuous events (scroll); debounce to run once after activity stops (search input).

**Q3:** How is throttle typically implemented?
**A:** With a stored timestamp comparing `Date.now()`, or a boolean flag reset by `setTimeout`.

**Q4:** Does throttling guarantee the last call runs?
**A:** Not in the simple timestamp version; a trailing-edge variant uses a timeout to ensure the final call executes.

**Q5:** Why throttle a scroll handler?
**A:** Scroll fires extremely often; throttling prevents performance issues from running heavy logic on every event.

## 9. Common Mistakes
- Using throttle when debounce is needed (or vice versa).
- Ignoring the trailing call, missing the final state.
- Setting the interval too high, making the UI feel laggy.
- Recreating the throttled function on each render.

## 10. Advanced Notes
- Combine leading + trailing for both immediate response and a final update.
- `requestAnimationFrame`-based throttling syncs updates with the browser's paint cycle (~60fps).
- Lodash `throttle` supports `leading`/`trailing` options and `maxWait`.
- For scroll/resize, rAF throttling often gives smoother results than time-based throttling.
