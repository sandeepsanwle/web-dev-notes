# RxJS

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

**RxJS** (Reactive Extensions for JavaScript) is a library for composing asynchronous and event-based programs using **Observables**. Angular uses it heavily for HTTP, forms, router events, and reactive state.

## 2. Simple Explanation

RxJS lets you treat any stream of values over time — clicks, HTTP responses, timer ticks — like a pipeline. You declare what should happen to each value as it flows through (filter it, transform it, combine it), and RxJS runs the pipeline whenever new values arrive. It's like a conveyor belt with workstations (operators) that process each item.

## 3. Why It Is Used

- **Async made composable** — chain transformations on streams declaratively.
- **Powerful operators** — `map`, `filter`, `switchMap`, `debounceTime`, etc.
- **Cancellation** — unsubscribe to stop work (e.g. abort stale HTTP).
- **Coordination** — combine multiple async sources cleanly (`combineLatest`, `forkJoin`).

## 4. Key Points

- Core types: **Observable**, **Observer**, **Subscription**, **Subject**, **Operators**.
- Observables are **lazy** — nothing runs until you `subscribe()`.
- Operators are **pure functions** combined inside `pipe()`.
- **Higher-order mapping** operators (`switchMap`, `mergeMap`, `concatMap`, `exhaustMap`) flatten observables-of-observables.
- Always **unsubscribe** (or use `takeUntilDestroyed` / `async` pipe) to avoid leaks.

## 5. Syntax

```ts
import { of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

of(1, 2, 3, 4)
  .pipe(
    filter(n => n % 2 === 0),  // keep even
    map(n => n * 10),          // transform
  )
  .subscribe(value => console.log(value)); // 20, 40
```

## 6. Example

```ts
import { Component, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { FormControl } from '@angular/forms';
import { debounceTime, distinctUntilChanged, switchMap } from 'rxjs/operators';

@Component({
  selector: 'app-search',
  standalone: true,
  template: `<input [formControl]="query" placeholder="Search..." />`,
})
export class SearchComponent {
  private http = inject(HttpClient);
  query = new FormControl('');

  results$ = this.query.valueChanges.pipe(
    debounceTime(300),          // wait for typing pause
    distinctUntilChanged(),     // ignore duplicate terms
    switchMap(term =>           // cancel previous request, run new one
      this.http.get(`/api/search?q=${term}`)
    ),
  );
}
```

## 7. Real World Use Case

A type-ahead search box: as the user types, you don't want to fire a request on every keystroke or show results from outdated requests. `debounceTime` waits for a pause, `distinctUntilChanged` skips repeats, and `switchMap` cancels the in-flight request when a new term arrives — giving fast, correct, race-free search with a few lines of declarative code.

## 8. Interview Questions

**Q1:** What is the difference between `switchMap`, `mergeMap`, `concatMap`, and `exhaustMap`?
**A:** They flatten inner observables differently: `switchMap` cancels the previous inner observable when a new value arrives (great for search); `mergeMap` runs all concurrently; `concatMap` queues them in order; `exhaustMap` ignores new values while one is in flight (great for preventing double form submits).

**Q2:** What is the difference between an Observable and a Subject?
**A:** An Observable is unicast — each subscriber gets its own independent execution. A Subject is both an Observable and an Observer, and is multicast — it broadcasts the same values to all current subscribers, and you can push values into it with `.next()`.

**Q3:** What are hot and cold observables?
**A:** Cold observables create their data producer per subscription (e.g. `http.get`), so each subscriber gets a fresh run. Hot observables share a single producer (e.g. a Subject, DOM events); subscribers receive values emitted after they subscribe.

**Q4:** How do you prevent memory leaks from subscriptions?
**A:** Use the `async` pipe (auto-unsubscribes), `takeUntilDestroyed()`, `take(1)`/`first()` for one-shot streams, or store the `Subscription` and call `unsubscribe()` in `ngOnDestroy`.

**Q5:** What is the difference between `Subject`, `BehaviorSubject`, and `ReplaySubject`?
**A:** `Subject` has no initial value and only emits to current subscribers. `BehaviorSubject` requires an initial value and emits the latest value to new subscribers. `ReplaySubject` replays a configurable number of past values to new subscribers.

## 9. Common Mistakes

- **Nesting subscribes** instead of using `switchMap`/`mergeMap`, leading to callback hell and leaks.
- **Forgetting to unsubscribe**, causing memory leaks and duplicate handlers.
- Using `mergeMap` where `switchMap` is needed → stale/racy results.
- Subscribing inside a template or in a loop, creating multiple executions.
- Mutating emitted objects shared across subscribers.

## 10. Advanced Notes

- `shareReplay({ bufferSize: 1, refCount: true })` caches and shares the latest HTTP result among multiple subscribers.
- `combineLatest`, `forkJoin`, `withLatestFrom`, and `zip` coordinate multiple streams; pick based on completion/emission semantics.
- RxJS interop: `toSignal()` converts an Observable to a Signal, and `toObservable()` does the reverse — bridging RxJS and Angular Signals.
- Marble testing with `TestScheduler` verifies time-based operator behavior deterministically.
- Use `catchError` to recover gracefully and `retry`/`retryWhen` for resilient requests.
