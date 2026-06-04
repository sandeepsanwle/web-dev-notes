# Services

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A **service** is a plain TypeScript class (usually decorated with `@Injectable`) that holds reusable business logic, shared state, or data-access code, kept separate from components so it can be injected wherever needed.

## 2. Simple Explanation

A service is a helper that does work that isn't about the UI — like fetching data, logging, or storing shared state. Components stay focused on *displaying* things, while services handle *doing* and *knowing* things. Angular creates the service once and hands the same instance to anyone who asks for it.

## 3. Why It Is Used

- **Separation of concerns** — keep logic out of components.
- **Reusability** — share the same logic across many components.
- **Single source of truth** — a singleton service can hold shared state.
- **Testability** — logic in a service is easy to unit test in isolation.

## 4. Key Points

- Decorated with `@Injectable({ providedIn: 'root' })` for an app-wide singleton.
- Injected into components/other services via the constructor or the `inject()` function.
- `providedIn: 'root'` makes the service **tree-shakable** — removed from the bundle if unused.
- A service can depend on other services through DI.
- Services are the recommended place for HTTP calls and shared state.

## 5. Syntax

```ts
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class LoggerService {
  log(message: string): void {
    console.log(`[App] ${message}`);
  }
}
```

## 6. Example

```ts
import { Injectable, signal, inject } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class CartService {
  private items = signal<string[]>([]);
  readonly count = this.items.asReadonly();

  add(product: string) {
    this.items.update(list => [...list, product]);
  }
}
```

```ts
import { Component, inject } from '@angular/core';
import { CartService } from './cart.service';

@Component({
  selector: 'app-product',
  standalone: true,
  template: `
    <button (click)="cart.add('Book')">Add Book</button>
    <p>Items in cart: {{ cart.count().length }}</p>
  `,
})
export class ProductComponent {
  cart = inject(CartService); // modern inject() syntax
}
```

## 7. Real World Use Case

An `AuthService` centralizes everything about authentication: the current user signal, `login()`, `logout()`, and token storage. Components, route guards, and HTTP interceptors all inject the *same* `AuthService` instance, so the user's logged-in state is consistent everywhere and there is one place to change auth logic.

## 8. Interview Questions

**Q1:** What makes a service a singleton in Angular?
**A:** Registering it with `providedIn: 'root'` (or in a single root-level provider) means Angular's root injector creates exactly one instance and shares it across the whole app.

**Q2:** What is the difference between `inject()` and constructor injection?
**A:** Both retrieve dependencies from the injector. Constructor injection lists params in the constructor; `inject()` is a function callable in injection contexts (field initializers, factories) and is more flexible — e.g. usable in functional guards/interceptors and composable helpers.

**Q3:** Where should you put HTTP calls — in components or services?
**A:** In services. This keeps components thin, makes the data logic reusable and testable, and centralizes concerns like caching and error handling.

**Q4:** Is the `@Injectable` decorator always required?
**A:** It's required if the service itself has dependencies to inject (so Angular can read the metadata), and it's needed for `providedIn`. As best practice, always add it to services for consistency.

**Q5:** How can two components share state via a service?
**A:** Provide the service at root (singleton) and have both components inject it. State held in the service (e.g. a `signal` or `BehaviorSubject`) is then shared and reactive across both.

## 9. Common Mistakes

- **Providing a service in multiple places**, accidentally creating several instances and losing shared state.
- Putting **business logic in components** instead of services.
- Storing state in a component-scoped provider when an app-wide singleton was intended.
- Forgetting `@Injectable` on a service that has its own dependencies.
- Creating circular dependencies between services.

## 10. Advanced Notes

- Scope a service to a component by listing it in that component's `providers` — each instance of the component gets its own service instance.
- `providedIn: 'platform'` and `'any'` give finer control over injector hierarchy (rarely needed).
- Use `InjectionToken` to provide non-class values (config objects, strings) through DI.
- Services can expose reactive state via Signals or RxJS `BehaviorSubject`; Signals are now the recommended default for simple shared state.
- For multi-instance scenarios (e.g. per-route services), provide the service at the route or component level rather than root.
