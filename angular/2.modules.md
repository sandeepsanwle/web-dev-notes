# Modules

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

An Angular **module** (`NgModule`) is a class decorated with `@NgModule` that groups related components, directives, pipes, and services into a cohesive block of functionality and tells Angular how to compile and run them.

## 2. Simple Explanation

A module is like a folder or a "box" that organizes related pieces of your app. You put your components, the things they depend on, and the things they share into the box, then plug the box into your application. Modern Angular increasingly replaces modules with **standalone components**, but understanding modules is still essential for legacy and many existing codebases.

## 3. Why It Is Used

- **Organization** — group related features (e.g. a `UserModule`).
- **Reusability** — share a `SharedModule` of common UI across the app.
- **Lazy loading** — load feature modules only when their route is visited.
- **Dependency scoping** — control which providers and declarations are available where.

## 4. Key Points

- Key `@NgModule` metadata: `declarations`, `imports`, `exports`, `providers`, `bootstrap`.
- A declarable (component/directive/pipe) belongs to **exactly one** module's `declarations`.
- `imports` brings in other modules; `exports` makes declarables available to importers.
- The **root module** (`AppModule`) is traditionally bootstrapped.
- Standalone components can be **mixed** with modules — you import standalone items into a module's `imports`.

## 5. Syntax

```ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { UserCardComponent } from './user-card.component';

@NgModule({
  declarations: [/* non-standalone components/pipes/directives */],
  imports: [CommonModule, UserCardComponent], // standalone components go in imports
  exports: [UserCardComponent],
  providers: [],
})
export class UserModule {}
```

## 6. Example

```ts
// shared.module.ts — a reusable bundle of common UI
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ButtonComponent } from './button.component';
import { TruncatePipe } from './truncate.pipe';

@NgModule({
  declarations: [ButtonComponent, TruncatePipe],
  imports: [CommonModule],
  exports: [ButtonComponent, TruncatePipe], // re-export for consumers
})
export class SharedModule {}
```

```ts
// feature.module.ts — consumes the shared module
import { NgModule } from '@angular/core';
import { SharedModule } from './shared.module';

@NgModule({
  imports: [SharedModule], // now ButtonComponent & TruncatePipe are available
})
export class FeatureModule {}
```

## 7. Real World Use Case

A large enterprise dashboard splits the app into feature modules: `BillingModule`, `ReportsModule`, `AdminModule`. Each is lazy-loaded via the router so the initial bundle stays small — users who never open the admin area never download its code. A `CoreModule` holds app-wide singletons and a `SharedModule` holds reusable components.

## 8. Interview Questions

**Q1:** What is the difference between `declarations` and `imports`?
**A:** `declarations` lists the components, directives, and pipes that *belong to* this module. `imports` lists *other modules* (or standalone components) whose exported declarables this module needs to use.

**Q2:** Why can't a component be declared in two modules?
**A:** Each declarable must belong to exactly one module to give Angular a single, unambiguous compilation context. To share it, export it from one module and import that module elsewhere.

**Q3:** What is the difference between a root module and a feature module?
**A:** The root module (`AppModule`) bootstraps the application and is loaded eagerly. Feature modules organize domain functionality and can be lazy-loaded on demand via the router.

**Q4:** How does lazy loading work with modules?
**A:** In route config you use `loadChildren: () => import('./feature.module').then(m => m.FeatureModule)`. Angular creates a separate bundle that downloads only when the route is activated.

**Q5:** Are NgModules still required in modern Angular?
**A:** No. Since v15+ standalone components, directives, and pipes can be used without NgModules, and apps can be fully bootstrapped standalone with `bootstrapApplication()`. Modules remain supported for backward compatibility.

## 9. Common Mistakes

- **Declaring the same component in multiple modules** → "Type X is part of the declarations of 2 modules" error.
- **Forgetting to export** a component from a shared module, so importers can't use it.
- Importing `BrowserModule` in feature modules instead of `CommonModule`.
- Putting providers meant to be singletons in a lazy module, accidentally creating multiple instances.
- Bloating `AppModule` instead of splitting into feature modules.

## 10. Advanced Notes

- `forRoot()` / `forChild()` is a convention (used by `RouterModule`) so a library can provide singletons once at root and only declarations to children.
- `providedIn: 'root'` on services is preferred over module `providers` for tree-shakable singletons.
- A `CoreModule` imported only once in `AppModule` is a classic pattern for app-wide singletons; guard it with a constructor check.
- Migrating to standalone: Angular provides a schematic `ng generate @angular/core:standalone` to automate the conversion.
- `EnvironmentInjector` and route-level `providers` now offer module-free dependency scoping.
