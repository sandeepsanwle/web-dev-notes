# Dependency Injection

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

**Dependency Injection (DI)** is a design pattern, built into Angular's core, where a class receives its dependencies from an external **injector** instead of creating them itself. Angular's injector resolves, creates, and caches these dependencies.

## 2. Simple Explanation

Instead of a component building its own tools, it just *asks* for them and Angular hands them over ready to use. It's like ordering coffee at a café — you don't grow the beans and brew it yourself, you ask the barista (the injector) and receive a finished cup. This makes code looser, swappable, and easy to test.

## 3. Why It Is Used

- **Loose coupling** — classes depend on abstractions, not concrete creation logic.
- **Testability** — swap real dependencies for mocks in tests.
- **Reusability & configuration** — provide different implementations per environment.
- **Lifecycle management** — Angular handles creating and caching instances.

## 4. Key Points

- The **injector** maintains a container of provider → instance.
- **Providers** tell the injector *how* to create a dependency (`useClass`, `useValue`, `useFactory`, `useExisting`).
- Injectors form a **hierarchy** (root → route → component); resolution bubbles up.
- Inject via constructor params or the `inject()` function.
- `InjectionToken` allows injecting non-class values.

## 5. Syntax

```ts
import { Injectable, InjectionToken, inject } from '@angular/core';

// Token for a non-class value
export const API_URL = new InjectionToken<string>('API_URL');

@Injectable({ providedIn: 'root' })
export class ApiService {
  private baseUrl = inject(API_URL); // inject a value token
}

// Providing values (e.g. in bootstrapApplication / route providers)
const providers = [
  { provide: API_URL, useValue: 'https://api.example.com' },
];
```

## 6. Example

```ts
import { Injectable, inject } from '@angular/core';

// An abstraction
export abstract class Logger {
  abstract log(msg: string): void;
}

@Injectable()
export class ConsoleLogger extends Logger {
  log(msg: string) { console.log('CONSOLE:', msg); }
}

@Injectable()
export class NoopLogger extends Logger {
  log(_msg: string) { /* silent */ }
}
```

```ts
// Provide a concrete implementation for the abstraction
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { Logger, ConsoleLogger } from './logger';

bootstrapApplication(AppComponent, {
  providers: [{ provide: Logger, useClass: ConsoleLogger }],
});

// Anywhere: inject(Logger) returns a ConsoleLogger — swap to NoopLogger in tests
```

## 7. Real World Use Case

A reporting widget depends on a `DataSource`. In production you provide an `HttpDataSource`; in Storybook or unit tests you provide a `MockDataSource` — all without touching the widget code. Likewise, an `API_URL` token lets the same build point at staging or production simply by changing the provided value.

## 8. Interview Questions

**Q1:** What are the different provider strategies in Angular DI?
**A:** `useClass` (instantiate a class), `useValue` (provide a ready value), `useFactory` (call a factory function, optionally with `deps`), and `useExisting` (alias to another token). Each maps a token to a way of producing the dependency.

**Q2:** Explain the Angular injector hierarchy.
**A:** Injectors are layered: the root/environment injector, then route-level injectors, then element/component injectors. When a dependency is requested, Angular searches from the current injector upward until it finds a provider, otherwise it throws a `NullInjectorError`.

**Q3:** What is an `InjectionToken` and when do you need it?
**A:** It's a unique token used to provide/inject values that aren't classes (strings, config objects, functions) or to disambiguate when interfaces (which don't exist at runtime) can't be used as tokens.

**Q4:** What do `@Optional`, `@Self`, `@SkipSelf`, and `@Host` do?
**A:** They are resolution modifiers: `@Optional` returns null instead of throwing if not found; `@Self` restricts the search to the current injector; `@SkipSelf` skips the current injector and looks upward; `@Host` stops the search at the host element.

**Q5:** What's the difference between `providedIn: 'root'` and providing in a component?
**A:** `providedIn: 'root'` creates one app-wide singleton (tree-shakable). Providing in a component's `providers` creates a new instance scoped to each instance of that component and its children.

## 9. Common Mistakes

- Using a **TypeScript interface as a DI token** — interfaces don't exist at runtime; use `InjectionToken` or an abstract class.
- Accidentally creating **multiple instances** by re-providing a "singleton" in feature/lazy scopes.
- Calling `inject()` **outside an injection context**, causing a runtime error.
- Overusing component-level providers and being surprised by non-shared state.
- Circular dependencies between providers.

## 10. Advanced Notes

- `inject()` enables functional patterns: functional route guards, interceptors, and reusable "inject helpers" that encapsulate logic.
- `useFactory` with `deps` lets you build a dependency from other injected values.
- `runInInjectionContext()` lets you call `inject()` from places that aren't normally injection contexts.
- Multi-providers (`multi: true`) collect several values under one token (e.g. `HTTP_INTERCEPTORS`).
- The environment injector (introduced with standalone APIs) replaces much of what NgModule providers did, enabling module-free configuration.
