# Routing

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

**Routing** is Angular's mechanism for navigating between views in a single-page application (SPA). The `Router` maps URL paths to components and swaps them in/out of a `<router-outlet>` without full page reloads.

## 2. Simple Explanation

Routing turns your app into a set of "pages" that all live in one downloaded app. When the URL changes (via a link or code), Angular looks up which component matches and shows it inside the outlet — like changing channels on a TV without turning it off and on.

## 3. Why It Is Used

- **SPA navigation** without full page reloads.
- **Deep linking** — URLs map to specific app states, shareable/bookmarkable.
- **Lazy loading** — load route code only when visited.
- **Guards & resolvers** — protect routes and pre-fetch data.

## 4. Key Points

- Define routes as an array of `Route` objects (`path`, `component`/`loadComponent`, `children`).
- Register with `provideRouter(routes)` (standalone) or `RouterModule.forRoot(routes)`.
- `<router-outlet>` is where matched components render.
- Navigate with `routerLink` (template) or `Router.navigate()` (code).
- Read params via `ActivatedRoute` (or the new `withComponentInputBinding()`).

## 5. Syntax

```ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'users/:id', component: UserDetailComponent },
  // lazy-loaded standalone component
  { path: 'admin', loadComponent: () => import('./admin.component').then(m => m.AdminComponent) },
  { path: '**', component: NotFoundComponent }, // wildcard
];
```

## 6. Example

```ts
// main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { provideRouter, withComponentInputBinding } from '@angular/router';
import { AppComponent } from './app.component';
import { routes } from './app.routes';

bootstrapApplication(AppComponent, {
  providers: [provideRouter(routes, withComponentInputBinding())],
});
```

```html
<!-- app.component.html -->
<nav>
  <a routerLink="/" routerLinkActive="active">Home</a>
  <a [routerLink]="['/users', 42]">User 42</a>
</nav>
<router-outlet />
```

```ts
// user-detail.component.ts — route param bound directly to an input
import { Component, input } from '@angular/core';

@Component({ selector: 'app-user-detail', standalone: true, template: `User: {{ id() }}` })
export class UserDetailComponent {
  id = input<string>(); // bound from :id via withComponentInputBinding()
}
```

## 7. Real World Use Case

A SaaS dashboard has routes like `/projects`, `/projects/:id`, and `/settings`. The settings and admin sections are lazy-loaded so the initial bundle is small. Route guards block unauthenticated access to `/projects`, and a resolver pre-fetches project data so the detail page renders fully on first paint.

## 8. Interview Questions

**Q1:** What is the difference between `routerLink` and `Router.navigate()`?
**A:** `routerLink` is a declarative directive used in templates for navigation on click. `Router.navigate()` (and `navigateByUrl()`) is the imperative API used in component code, e.g. after a form submit or a guard decision.

**Q2:** How does lazy loading work in the router?
**A:** Use `loadComponent` (for a standalone component) or `loadChildren` (for routes/modules) with a dynamic `import()`. Angular splits that code into a separate bundle loaded only when the route is activated, reducing initial load time.

**Q3:** How do you read route parameters?
**A:** Inject `ActivatedRoute` and read `snapshot.paramMap` (one-time) or subscribe to `paramMap`/`params` (reactive, for when params change without re-creating the component). With `withComponentInputBinding()`, params bind directly to component `@Input()`/`input()`.

**Q4:** What is the difference between path params, query params, and fragments?
**A:** Path params are part of the route definition (`/users/:id`); query params are key-value pairs after `?` (`?page=2`) and don't require route changes; fragments come after `#` and are often used for in-page anchors.

**Q5:** What is a resolver?
**A:** A resolver is a function/class that fetches data *before* a route activates, so the component receives ready data via the route. It avoids flashing empty views while data loads.

## 9. Common Mistakes

- **Forgetting `<router-outlet>`**, so routes match but nothing renders.
- Placing the **wildcard `**` route before specific routes** (order matters — first match wins).
- Using `snapshot` params when the component is reused across param changes (use the observable instead).
- Not handling the leading slash correctly in `navigate()` (relative vs absolute).
- Forgetting to register `provideRouter`/`RouterModule`.

## 10. Advanced Notes

- Router features: `withComponentInputBinding()`, `withInMemoryScrolling()`, `withViewTransitions()`, `withPreloading(PreloadAllModules)`.
- Child routes + nested `<router-outlet>` build master-detail layouts.
- `data` on a route passes static metadata (titles, roles); `title` sets the document title.
- Auxiliary (named) outlets allow multiple independent outlets simultaneously.
- `RouterLinkActive` with `exact` options highlights active links; `runGuardsAndResolvers` controls re-run behavior.
