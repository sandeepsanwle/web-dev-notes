# Signals

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **Signal** is a reactive wrapper around a value that notifies Angular when that value changes. Signals are Angular's built-in fine-grained reactivity primitive (stable since v17), enabling efficient, predictable change detection.

## 2. Simple Explanation

A signal is a "smart variable." When you read it, Angular remembers who read it. When you change it, Angular automatically updates exactly the parts of the UI (and any derived values) that depend on it — nothing more. You read a signal by **calling it like a function**: `count()`.

## 3. Why It Is Used

- **Fine-grained updates** — only what depends on a signal re-renders.
- **Simpler than RxJS** for synchronous local/shared state.
- **Automatic dependency tracking** — `computed` values recalc only when needed.
- **Future of change detection** — enables zoneless Angular.

## 4. Key Points

- Create with `signal(initialValue)`; read with `value()`.
- Update with `.set(newValue)` or `.update(prev => next)`.
- `computed()` creates derived, memoized read-only signals.
- `effect()` runs side effects when read signals change.
- Read-only views via `signal.asReadonly()`; signal `input()`/`output()` for components.

## 5. Syntax

```ts
import { signal, computed, effect } from '@angular/core';

const count = signal(0);                       // writable signal
const double = computed(() => count() * 2);    // derived signal

effect(() => console.log('count is', count())); // runs on change

count.set(5);                 // double() === 10
count.update(n => n + 1);     // count() === 6
```

## 6. Example

```ts
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-counter',
  standalone: true,
  template: `
    <button (click)="dec()">-</button>
    <span>{{ count() }}</span>
    <button (click)="inc()">+</button>
    <p>Doubled: {{ doubled() }}</p>
  `,
})
export class CounterComponent {
  count = signal(0);
  doubled = computed(() => this.count() * 2);

  inc() { this.count.update(n => n + 1); }
  dec() { this.count.update(n => n - 1); }
}
```

## 7. Real World Use Case

A shopping cart service holds `items = signal<CartItem[]>([])`. Derived signals `total = computed(...)` and `count = computed(...)` recompute automatically whenever items change. Every component that displays the cart total updates instantly and efficiently — no manual subscriptions, no `OnPush` boilerplate, no risk of stale values.

## 8. Interview Questions

**Q1:** What is the difference between a signal and a `BehaviorSubject`?
**A:** Both hold a current value, but signals are synchronous, read by calling them, and have automatic dependency tracking with `computed`/`effect`. `BehaviorSubject` is RxJS-based, requires subscribe/unsubscribe, and is better for async streams and operator pipelines.

**Q2:** What is a `computed` signal and how is it optimized?
**A:** `computed()` creates a derived read-only signal whose value is calculated from other signals. It is **lazy and memoized** — it only recomputes when one of the signals it reads changes *and* something reads it.

**Q3:** What is an `effect` and when should you use it?
**A:** `effect()` runs a side-effecting function whenever any signal it reads changes — useful for logging, syncing to localStorage, or imperative DOM/3rd-party updates. It should not be used to set other signals (use `computed` instead).

**Q4:** How do signals improve change detection?
**A:** Signals let Angular track exactly which template expressions depend on which signals, so it can update only the affected views instead of checking the whole component tree. This enables zoneless change detection.

**Q5:** What are signal inputs and how do they differ from `@Input()`?
**A:** `input()` creates a signal-based input read as `value()` in the template and usable in `computed`/`effect`. Unlike the decorator `@Input()`, it's reactive by default, supports `input.required()`, and integrates with the signal graph.

## 9. Common Mistakes

- **Forgetting to call the signal**: writing `count` instead of `count()` in the template.
- **Setting signals inside an `effect`**, causing loops; use `computed` for derived state.
- Mutating an object/array in a signal in place instead of producing a new reference with `.update`.
- Overusing `effect` for things that should be `computed`.
- Mixing eager RxJS subscriptions where a simple signal would suffice.

## 10. Advanced Notes

- `toSignal()` / `toObservable()` bridge signals and RxJS observables.
- `untracked(() => ...)` reads a signal without registering it as a dependency.
- Signals power **zoneless** Angular (`provideZonelessChangeDetection()`), removing `zone.js`.
- `linkedSignal()` (newer API) creates writable state derived from a source signal.
- Use `equal` option in `signal(value, { equal })` to customize change detection comparisons for objects.
