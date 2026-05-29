# Interceptors

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

An **HTTP interceptor** is a function (or class) that sits in the middle of every `HttpClient` request/response, letting you inspect, modify, or handle them globally — e.g. attaching auth headers, logging, or catching errors.

## 2. Simple Explanation

An interceptor is like airport security for your HTTP traffic. Every outgoing request and incoming response passes through it, so you can stamp it (add headers), inspect it (log), reroute it (retry), or stop it — all in one central place instead of repeating code in every service.

## 3. Why It Is Used

- **Attach auth tokens** to every request automatically.
- **Global error handling** — catch 401s, show toasts, retry.
- **Logging / metrics** for all HTTP traffic.
- **Caching, loading spinners, request transformation** in one place.

## 4. Key Points

- Modern Angular uses **functional interceptors** (`HttpInterceptorFn`) registered with `withInterceptors([...])`.
- An interceptor calls `next(req)` to pass the (possibly cloned) request along the chain.
- Requests are **immutable** — use `req.clone()` to modify.
- Multiple interceptors form a **chain**, executed in registration order.
- Interceptors return an `Observable<HttpEvent>`.

## 5. Syntax

```ts
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from './auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).token();
  const authReq = token
    ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } })
    : req;
  return next(authReq);
};
```

## 6. Example

```ts
// error.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { Router } from '@angular/router';
import { catchError, throwError } from 'rxjs';

export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  const router = inject(Router);
  return next(req).pipe(
    catchError(err => {
      if (err.status === 401) router.navigate(['/login']);
      return throwError(() => err);
    }),
  );
};
```

```ts
// main.ts — register the chain (runs in this order)
import { bootstrapApplication } from '@angular/platform-browser';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { authInterceptor } from './auth.interceptor';
import { errorInterceptor } from './error.interceptor';
import { AppComponent } from './app.component';

bootstrapApplication(AppComponent, {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor, errorInterceptor])),
  ],
});
```

## 7. Real World Use Case

A secured app needs every API call to carry a JWT and to redirect to login on a `401`. An `authInterceptor` clones each request to add `Authorization`, while an `errorInterceptor` catches `401`s and redirects, `403`s and shows "Access denied", and network errors and retries once. No service ever touches headers or auth logic directly.

## 8. Interview Questions

**Q1:** Why must you clone a request to modify it?
**A:** `HttpRequest` objects are immutable to keep the interceptor chain predictable and side-effect-free. `req.clone({...})` returns a new request with your changes, leaving the original untouched.

**Q2:** In what order do multiple interceptors run?
**A:** For the request phase they run in the order they're registered; for the response phase they run in reverse order (like a nested middleware stack). Each calls `next()` to delegate to the following interceptor.

**Q3:** What is the difference between functional and class-based interceptors?
**A:** Functional interceptors (`HttpInterceptorFn`) are plain functions registered with `withInterceptors()`, using `inject()` for dependencies — the modern, less verbose approach. Class-based interceptors implement `HttpInterceptor` and are registered via the `HTTP_INTERCEPTORS` multi-provider.

**Q4:** Can an interceptor modify the response?
**A:** Yes. By piping the `next(req)` observable (e.g. with `map`), an interceptor can transform response bodies, handle errors with `catchError`, or react to specific `HttpResponse` events.

**Q5:** How would you implement a global loading spinner with interceptors?
**A:** Increment a counter in a `LoadingService` when a request starts, decrement it on completion using `finalize()`, and bind the spinner's visibility to whether the counter is greater than zero.

## 9. Common Mistakes

- **Mutating the request directly** instead of using `req.clone()`.
- **Forgetting to call `next()`**, which silently breaks the request chain.
- Adding auth headers to **third-party/external URLs** unintentionally (leaking tokens).
- Swallowing errors in `catchError` without rethrowing, hiding failures from callers.
- Registering interceptors but forgetting `provideHttpClient(withInterceptors(...))`.

## 10. Advanced Notes

- Use `withInterceptorsFromDi()` to keep using legacy class-based interceptors alongside functional ones.
- Implement retry with backoff using `retry({ count, delay })` inside an interceptor.
- Cache GET responses by returning a stored `HttpResponse` for matching requests.
- Use `HttpContext`/`HttpContextToken` to pass per-request flags (e.g. "skip auth") to interceptors.
- Be careful with token-refresh interceptors: use `switchMap` + a shared refresh observable to avoid stampedes of parallel refresh calls.
