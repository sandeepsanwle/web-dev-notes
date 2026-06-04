# Change Detection

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

**Change Detection** is the mechanism by which Angular keeps the DOM in sync with your component data. It checks component state and updates the view whenever something may have changed.

## 2. Simple Explanation

Imagine Angular as a diligent assistant who, after anything interesting happens (a click, an HTTP response, a timer), walks through your components asking "did any value change? If so, update the screen." Change detection is that walk-through. You can make it faster by telling Angular to only re-check a component when its inputs change (`OnPush`) or by using Signals.

## 3. Why It Is Used

- **Keeps UI in sync** with the underlying data model automatically.
- **Performance control** — strategies let you skip unnecessary checks.
- **Predictability** — well-defined moments when the view updates.

## 4. Key Points

- Two strategies: **Default** (check whole subtree) and **OnPush** (check only when inputs change by reference, an event fires, or a read signal updates).
- Traditionally triggered by `zone.js`, which patches async APIs (events, timers, XHR).
- `OnPush` + immutable data (or Signals) dramatically reduces checks.
- Manual control via `ChangeDetectorRef`: `markForCheck()`, `detectChanges()`, `detach()`.
- **Zoneless** mode (signals-driven) is the modern direction.

## 5. Syntax

```ts
import { Component, ChangeDetectionStrategy, input } from '@angular/core';

@Component({
  selector: 'app-row',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush, // opt into OnPush
  template: `{{ data().label }}`,
})
export class RowComponent {
  data = input.required<{ label: string }>();
}
```

## 6. Example

```ts
import { Component, ChangeDetectionStrategy, ChangeDetectorRef, inject } from '@angular/core';

@Component({
  selector: 'app-clock',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `<p>{{ time }}</p>`,
})
export class ClockComponent {
  time = new Date().toLocaleTimeString();
  private cdr = inject(ChangeDetectorRef);

  constructor() {
    setInterval(() => {
      this.time = new Date().toLocaleTimeString();
      this.cdr.markForCheck(); // OnPush won't notice mutation without this
    }, 1000);
  }
}
```

## 7. Real World Use Case

A data grid renders thousands of rows. With Default change detection, every async event re-checks every row and the app stutters. Switching row components to `OnPush` (or signal inputs) means a row only re-renders when *its* input reference changes, cutting change-detection work by orders of magnitude and making scrolling smooth.

## 8. Interview Questions

**Q1:** What is the difference between Default and OnPush change detection?
**A:** Default checks a component and all its descendants on every change-detection cycle. OnPush only re-checks the component when an `@Input()` reference changes, an event originates in the component, an observable bound via `async` emits, or a signal it reads updates — so it skips far more work.

**Q2:** What role does `zone.js` play in change detection?
**A:** `zone.js` monkey-patches async browser APIs (events, `setTimeout`, XHR) so Angular knows when something potentially changed and can run change detection automatically after those tasks complete.

**Q3:** Why does OnPush sometimes "miss" updates?
**A:** Because OnPush only reacts to reference changes/events/signals. Mutating an object in place (same reference) won't trigger it; you must replace the reference, emit through an observable, use a signal, or call `markForCheck()`.

**Q4:** What is the difference between `markForCheck()` and `detectChanges()`?
**A:** `markForCheck()` marks the component and its ancestors to be checked in the *next* cycle. `detectChanges()` runs change detection on the component and its children *immediately and synchronously*.

**Q5:** What is zoneless change detection and how do Signals enable it?
**A:** Zoneless mode (`provideZonelessChangeDetection()`) removes `zone.js`; Angular relies on explicit notifications. Signals notify Angular precisely when a value used in a template changes, so the framework can schedule updates without globally patching async APIs.

## 9. Common Mistakes

- Mutating arrays/objects in place under OnPush and seeing **stale views**.
- Doing **heavy work in template expressions/getters**, which run on every check.
- Triggering change detection inside CD (causing `ExpressionChangedAfterItHasBeenChecked` errors).
- Overusing `detectChanges()` instead of `markForCheck()`.
- Forgetting that `async` pipe already calls `markForCheck()` for you.

## 10. Advanced Notes

- `ExpressionChangedAfterItHasBeenCheckedError` happens (dev mode) when a value changes during the verify pass; fix by setting values earlier or in proper lifecycle hooks.
- `ngZone.runOutsideAngular()` runs hot loops (animations, scroll) without triggering CD, then re-enter with `ngZone.run()`.
- `ChangeDetectorRef.detach()`/`reattach()` give manual control for ultra-high-performance scenarios.
- Signals + `OnPush` (or zoneless) is the recommended modern performance baseline.
- The `async` pipe, signals, and immutable data structures all play nicely with OnPush.
