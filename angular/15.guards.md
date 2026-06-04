# Guards

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **route guard** is a function (modern) or class that the Router runs to decide whether navigation to/from a route is allowed, or whether to load it. Guards return `true`/`false`, a `UrlTree` (redirect), or an async equivalent.

## 2. Simple Explanation

A guard is a bouncer at the door of a route. Before letting you in (or out), it checks the rules — "Are you logged in?", "Do you have permission?", "Did you save your work?" — and either lets you through, blocks you, or redirects you somewhere else.

## 3. Why It Is Used

- **Authentication/authorization** — block unauthenticated or unauthorized users.
- **Unsaved changes protection** — warn before leaving a dirty form.
- **Conditional loading** — prevent lazy modules from loading without permission.
- **Centralized access rules** — keep navigation policy in one place.

## 4. Key Points

- Guard types: `canActivate`, `canActivateChild`, `canDeactivate`, `canMatch`, `resolve`.
- Modern Angular uses **functional guards** (plain functions using `inject()`).
- Return `boolean`, `UrlTree`, `Promise`, or `Observable` of those.
- Returning a `UrlTree` redirects instead of just blocking.
- `canMatch` can prevent a route from matching at all (great for lazy routes).

## 5. Syntax

```ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const auth = inject(AuthService);
  const router = inject(Router);
  return auth.isLoggedIn() ? true : router.createUrlTree(['/login']);
};
```

## 6. Example

```ts
// can-deactivate.guard.ts
import { CanDeactivateFn } from '@angular/router';

export interface CanLeave { canLeave(): boolean; }

export const unsavedChangesGuard: CanDeactivateFn<CanLeave> = (component) =>
  component.canLeave() ? true : confirm('Discard unsaved changes?');
```

```ts
// app.routes.ts
import { Routes } from '@angular/router';
import { authGuard } from './auth.guard';
import { unsavedChangesGuard } from './can-deactivate.guard';

export const routes: Routes = [
  { path: 'dashboard', component: DashboardComponent, canActivate: [authGuard] },
  { path: 'edit', component: EditComponent, canDeactivate: [unsavedChangesGuard] },
  {
    path: 'admin',
    canMatch: [authGuard], // route won't even match/load if it fails
    loadComponent: () => import('./admin.component').then(m => m.AdminComponent),
  },
];
```

## 7. Real World Use Case

A blog editor uses `canDeactivate` to prompt "You have unsaved changes — leave anyway?" if the user navigates away mid-edit. An `authGuard` (`canActivate`) protects the `/account` route, redirecting guests to `/login` with a `returnUrl`, and `canMatch` ensures the admin bundle never even downloads for non-admins.

## 8. Interview Questions

**Q1:** What is the difference between `canActivate` and `canDeactivate`?
**A:** `canActivate` decides whether the user can *enter* a route (e.g. auth check). `canDeactivate` decides whether the user can *leave* a route (e.g. confirming unsaved changes), and it receives the component instance so it can inspect its state.

**Q2:** What is the difference between `canActivate` and `canMatch`?
**A:** `canActivate` runs after a route is matched and prevents activation. `canMatch` runs during route matching, so if it returns false the route doesn't match at all — the router tries the next route, and the lazy bundle isn't loaded.

**Q3:** How do functional guards differ from the old class-based guards?
**A:** Functional guards are plain functions that use `inject()` for dependencies — less boilerplate, easily composable, and the recommended approach. Class-based guards (`implements CanActivate`) are deprecated in favor of functions.

**Q4:** How can a guard redirect instead of just blocking?
**A:** Return a `UrlTree` (e.g. `router.createUrlTree(['/login'])`). The router cancels the current navigation and navigates to that URL instead of simply staying put.

**Q5:** Can guards be asynchronous?
**A:** Yes. A guard can return a `Promise` or `Observable` of `boolean | UrlTree`. The router waits for it to resolve/emit before completing navigation — useful for checking permissions via an API.

## 9. Common Mistakes

- Returning `false` and leaving the user **stuck with no feedback** instead of redirecting via a `UrlTree`.
- Putting heavy logic in guards that **block navigation** for too long.
- Using `canActivate` where `canMatch` would prevent loading a lazy bundle.
- Forgetting that `canDeactivate` receives the **component instance** (typing it correctly).
- Not handling the async case (returning a value before an API resolves).

## 10. Advanced Notes

- Multiple guards on one route run in order; **all must pass** (logical AND).
- Compose reusable guards by combining functions, e.g. a `roleGuard(role)` factory returning a `CanActivateFn`.
- `resolve` is technically a data guard — it blocks activation until data is fetched.
- Store a `returnUrl` query param when redirecting to login, then navigate back after auth.
- Guards run within an injection context, so `inject()` works directly inside them.
