# HTTP Client

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

The **`HttpClient`** is Angular's built-in service (from `@angular/common/http`) for making HTTP requests to backend APIs. It returns **Observables** of typed responses and integrates with interceptors, testing, and RxJS operators.

## 2. Simple Explanation

`HttpClient` is the messenger your app uses to talk to a server: "GET me these users", "POST this new order". You call a method, get back an Observable, and subscribe (or use the `async` pipe) to receive the data when it arrives. It handles JSON parsing and errors for you.

## 3. Why It Is Used

- **Talk to REST/GraphQL APIs** with a clean, typed API.
- **Observable-based** — composable with RxJS operators.
- **Interceptors** for cross-cutting concerns (auth, logging).
- **Strong typing** of responses and **testability** via `HttpTestingController`.

## 4. Key Points

- Enable with `provideHttpClient()` (standalone) in app providers.
- Inject `HttpClient` and call `get`, `post`, `put`, `patch`, `delete`.
- Methods are **lazy Observables** — nothing happens until you subscribe.
- Pass `params`, `headers`, and `responseType` via the options object.
- Type responses with generics: `http.get<User[]>(url)`.

## 5. Syntax

```ts
import { inject } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';

const http = inject(HttpClient);

// GET with query params and typing
http.get<User[]>('/api/users', {
  params: new HttpParams().set('active', 'true'),
});

// POST with a body
http.post<User>('/api/users', { name: 'Ada' });
```

## 6. Example

```ts
// user.service.ts
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

export interface User { id: number; name: string; }

@Injectable({ providedIn: 'root' })
export class UserService {
  private http = inject(HttpClient);
  private url = '/api/users';

  getAll(): Observable<User[]> { return this.http.get<User[]>(this.url); }
  create(user: Partial<User>): Observable<User> {
    return this.http.post<User>(this.url, user);
  }
  remove(id: number): Observable<void> {
    return this.http.delete<void>(`${this.url}/${id}`);
  }
}
```

```ts
// component using the async pipe
import { Component, inject } from '@angular/core';
import { AsyncPipe } from '@angular/common';
import { UserService } from './user.service';

@Component({
  selector: 'app-user-list',
  standalone: true,
  imports: [AsyncPipe],
  template: `
    @for (u of users$ | async; track u.id) { <li>{{ u.name }}</li> }
  `,
})
export class UserListComponent {
  users$ = inject(UserService).getAll();
}
```

## 7. Real World Use Case

A user-management screen loads a list with `getAll()` (rendered via the `async` pipe), creates new users with `create()`, and deletes with `remove()`. An interceptor attaches the auth token and a global error handler catches failures, so the service stays focused purely on the API contract while components stay declarative.

## 8. Interview Questions

**Q1:** Why does `HttpClient` return an Observable instead of a Promise?
**A:** Observables are lazy and cancellable, support retries and rich operators (`switchMap`, `retry`, `debounceTime`), and integrate with the `async` pipe. Cancellation, in particular, lets Angular abort stale requests (e.g. in type-ahead search).

**Q2:** What happens if you don't subscribe to an `HttpClient` call?
**A:** Nothing — the request is never sent. `HttpClient` observables are cold/lazy; the HTTP call only fires when something subscribes (directly or via the `async` pipe).

**Q3:** How do you send query parameters and headers?
**A:** Pass an options object: `{ params: new HttpParams().set('k','v'), headers: new HttpHeaders().set('X-Token','abc') }`. You can also pass plain object literals for params/headers.

**Q4:** How do you handle errors from an HTTP request?
**A:** Use the `catchError` operator in the pipe to inspect the `HttpErrorResponse`, optionally return a fallback value or rethrow with `throwError`. Global handling is best done in an interceptor.

**Q5:** How do you test code that uses `HttpClient`?
**A:** Use `provideHttpClientTesting()` and `HttpTestingController` to intercept requests, assert their URL/method/body, and flush mock responses — no real network calls.

## 9. Common Mistakes

- **Not subscribing** (or using `async`) so the request never fires.
- **Subscribing in a loop or template repeatedly**, triggering duplicate requests.
- Forgetting to **type responses**, losing compile-time safety.
- Putting HTTP calls in components instead of services.
- Not handling errors, letting them surface as unhandled rejections.

## 10. Advanced Notes

- `provideHttpClient(withFetch())` uses the Fetch API backend (better for SSR).
- Use `shareReplay(1)` to cache and share a response among multiple subscribers.
- Track upload/download progress with `{ reportProgress: true, observe: 'events' }`.
- `observe: 'response'` returns the full `HttpResponse` (status, headers), not just the body.
- Combine with `toSignal()` to expose HTTP results as signals for templates.
