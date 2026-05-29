# Lifecycle Hooks

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

**Lifecycle hooks** are methods Angular calls at specific moments in a component's or directive's life — creation, change detection, and destruction — letting you run code at the right time (e.g. `ngOnInit`, `ngOnDestroy`).

## 2. Simple Explanation

Every component has a life: it's born, it grows and reacts to changes, and eventually it's removed. Lifecycle hooks are the "events" Angular fires along that journey so you can hook in — "set things up when I'm created", "clean up when I'm destroyed". You implement the ones you need.

## 3. Why It Is Used

- **Initialization** — fetch data once the component is ready (`ngOnInit`).
- **React to input changes** (`ngOnChanges`).
- **Cleanup** — unsubscribe, clear timers (`ngOnDestroy`).
- **DOM access** after rendering (`ngAfterViewInit`).

## 4. Key Points

- Common order: `ngOnChanges` → `ngOnInit` → `ngDoCheck` → `ngAfterContentInit` → `ngAfterContentChecked` → `ngAfterViewInit` → `ngAfterViewChecked` → `ngOnDestroy`.
- `ngOnInit` runs once, after the first `ngOnChanges`.
- `ngOnChanges` receives a `SimpleChanges` object and runs on every input change.
- `@ViewChild`/`@ContentChild` results are available in `ngAfterViewInit`/`ngAfterContentInit`.
- Implement the matching interface (e.g. `OnInit`) for type safety.

## 5. Syntax

```ts
import { Component, OnInit, OnDestroy, OnChanges, SimpleChanges, input } from '@angular/core';

@Component({ selector: 'app-x', standalone: true, template: `` })
export class XComponent implements OnInit, OnChanges, OnDestroy {
  id = input<number>();

  ngOnChanges(changes: SimpleChanges) { /* input changed */ }
  ngOnInit() { /* one-time init */ }
  ngOnDestroy() { /* cleanup */ }
}
```

## 6. Example

```ts
import { Component, OnInit, OnDestroy, inject } from '@angular/core';
import { interval, Subscription } from 'rxjs';

@Component({
  selector: 'app-ticker',
  standalone: true,
  template: `<p>Ticks: {{ ticks }}</p>`,
})
export class TickerComponent implements OnInit, OnDestroy {
  ticks = 0;
  private sub?: Subscription;

  ngOnInit() {
    this.sub = interval(1000).subscribe(() => this.ticks++);
  }

  ngOnDestroy() {
    this.sub?.unsubscribe(); // prevent memory leak
  }
}
```

## 7. Real World Use Case

A dashboard widget uses `ngOnInit` to load its data from a service once it's created, `ngOnChanges` to refetch when its `filter` input changes, and `ngOnDestroy` to unsubscribe from a polling stream so the request stops when the user navigates away — preventing memory leaks and stale background work.

## 8. Interview Questions

**Q1:** What is the difference between the constructor and `ngOnInit`?
**A:** The constructor is a TypeScript feature for dependency injection and basic field setup; it runs before Angular sets inputs. `ngOnInit` runs once after the first `ngOnChanges`, when inputs are available — making it the right place for initialization logic like data fetching.

**Q2:** When does `ngOnChanges` fire, and what does it receive?
**A:** It fires before `ngOnInit` and again whenever a data-bound `@Input()` changes. It receives a `SimpleChanges` map with `previousValue`, `currentValue`, and `firstChange` for each changed input.

**Q3:** Why is `ngOnDestroy` important?
**A:** It runs just before Angular destroys the component, giving you a place to clean up — unsubscribe from observables, clear timers/intervals, remove event listeners — to avoid memory leaks.

**Q4:** What is the difference between `ngAfterViewInit` and `ngAfterContentInit`?
**A:** `ngAfterContentInit` fires after projected content (`<ng-content>`) is initialized; `@ContentChild` is then available. `ngAfterViewInit` fires after the component's own view (and child views) is initialized; `@ViewChild` is then available.

**Q5:** What's the danger of `ngDoCheck` and the `AfterChecked` hooks?
**A:** They run on **every** change-detection cycle, so heavy logic there can severely hurt performance. They should be used sparingly and kept extremely lightweight.

## 9. Common Mistakes

- Doing **data fetching in the constructor** instead of `ngOnInit`.
- **Forgetting `ngOnDestroy`** cleanup → memory leaks.
- Accessing `@ViewChild` in `ngOnInit` (it isn't ready until `ngAfterViewInit`).
- Putting expensive logic in `ngDoCheck`/`ngAfterViewChecked`.
- Mutating a view-bound value in `ngAfterViewInit` → `ExpressionChangedAfterItHasBeenChecked` error.

## 10. Advanced Notes

- `takeUntilDestroyed()` (from `@angular/core/rxjs-interop`) auto-unsubscribes when the component is destroyed — cleaner than manual `ngOnDestroy`.
- `DestroyRef` lets you register cleanup callbacks imperatively without implementing `OnDestroy`.
- With signals, `effect()` cleans itself up automatically on destroy and reduces the need for some hooks.
- `afterNextRender`/`afterRender` (v17+) run code after the browser renders — useful for DOM measurement and SSR-safe DOM work.
- Hook order is deterministic; understanding it is key to debugging timing issues with `@ViewChild` and inputs.
