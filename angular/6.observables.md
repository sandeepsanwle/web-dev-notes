# Observables

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

An **Observable** is a lazy, cancellable stream that can emit **zero or more values over time** (plus an optional completion or error). It is the core primitive of RxJS and is used throughout Angular for async data.

## 2. Simple Explanation

An Observable is like a YouTube channel: it can publish many videos (values) over time. Nothing reaches you until you **subscribe**. Once subscribed, you receive each new value, and you can **unsubscribe** to stop receiving them. Unlike a Promise (which delivers exactly one result), an Observable can deliver many — or none.

## 3. Why It Is Used

- **Multiple values over time** — perfect for events, websockets, intervals.
- **Lazy & cancellable** — work starts on subscribe, stops on unsubscribe.
- **Composable** — transform with operators via `pipe()`.
- **Native to Angular** — HTTP, forms `valueChanges`, router events all return Observables.

## 4. Key Points

- An Observable emits via three callbacks: `next`, `error`, `complete`.
- **Lazy**: code inside the Observable runs only on `subscribe()`.
- `subscribe()` returns a **Subscription** used to `unsubscribe()`.
- Operators (`map`, `filter`, etc.) return new Observables without mutating the source.
- The `async` pipe subscribes and unsubscribes automatically in templates.

## 5. Syntax

```ts
import { Observable } from 'rxjs';

const numbers$ = new Observable<number>(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.complete();
});

const sub = numbers$.subscribe({
  next: v => console.log(v),
  error: err => console.error(err),
  complete: () => console.log('done'),
});

sub.unsubscribe();
```

## 6. Example

```ts
import { Component, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

interface User { id: number; name: string; }

@Component({
  selector: 'app-users',
  standalone: true,
  template: `
    <ul>
      <!-- async pipe subscribes & unsubscribes for you -->
      @for (user of users$ | async; track user.id) {
        <li>{{ user.name }}</li>
      }
    </ul>
  `,
})
export class UsersComponent {
  private http = inject(HttpClient);
  users$: Observable<User[]> = this.http.get<User[]>('/api/users');
}
```

## 7. Real World Use Case

A live notifications panel subscribes to a WebSocket-backed Observable. Each incoming message is an emission; the component renders them as they arrive. Using the `async` pipe means Angular cleans up the subscription automatically when the panel is destroyed, preventing leaks and duplicated listeners.

## 8. Interview Questions

**Q1:** What is the difference between an Observable and a Promise?
**A:** A Promise resolves once with a single value and is eager (runs immediately). An Observable is lazy, can emit many values over time, is cancellable via `unsubscribe()`, and supports rich operators for transformation.

**Q2:** What does it mean that Observables are "lazy"?
**A:** The producer function passed to the Observable doesn't execute until something subscribes. No subscription means no work happens — and each subscription (for cold observables) triggers a fresh execution.

**Q3:** What are the three notification types an Observable can send?
**A:** `next` (a new value, can happen many times), `error` (a terminal failure that stops the stream), and `complete` (a terminal signal that no more values will come). Only one of `error`/`complete` ever fires.

**Q4:** How does the `async` pipe help with Observables?
**A:** It subscribes to the Observable, returns the latest emitted value to the template, and automatically unsubscribes when the component is destroyed — eliminating manual subscription management and leaks.

**Q5:** Can you convert between Observables and Signals?
**A:** Yes. `toSignal(obs$)` turns an Observable into a Signal for template-friendly reactive reads, and `toObservable(sig)` turns a Signal into an Observable for use with RxJS operators.

## 9. Common Mistakes

- **Forgetting to subscribe** and wondering why nothing happens (Observables are lazy).
- **Not unsubscribing** from long-lived streams → memory leaks.
- Assuming an Observable behaves like a Promise (single value, eager).
- Subscribing manually when the `async` pipe would be cleaner.
- Performing side effects in `map` instead of using `tap`.

## 10. Advanced Notes

- Cold vs hot: HTTP observables are cold (re-run per subscriber); Subjects/DOM events are hot (shared).
- Use `shareReplay` to make a cold source behave hot and cache its last value.
- Creation operators: `of`, `from`, `interval`, `fromEvent`, `timer`, `EMPTY`, `throwError`.
- Error handling with `catchError`, `retry`; completion with `finalize`.
- Prefer Signals for synchronous UI state and Observables for genuinely async/event streams; bridge them with `toSignal`/`toObservable`.
