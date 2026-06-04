# Components

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A **component** is the fundamental building block of an Angular UI. It is a TypeScript class decorated with `@Component`, paired with an HTML template and (optionally) styles, that controls a section of the screen called a *view*.

## 2. Simple Explanation

Think of a component as a reusable LEGO brick. Each brick has its own logic (the class), its own look (the template + styles), and a name (the selector). You snap bricks together to build a whole page. A button, a navbar, a user card — each can be its own component.

## 3. Why It Is Used

- **Reusability** — write a piece of UI once and reuse it everywhere.
- **Encapsulation** — styles and logic stay scoped to the component.
- **Maintainability** — small focused units are easier to test and reason about.
- **Composition** — complex screens are built by nesting simple components.

## 4. Key Points

- Declared with the `@Component` decorator.
- Modern Angular uses **standalone components** (`standalone: true`), removing the need for NgModules.
- A component has a `selector`, a `template`/`templateUrl`, and optional `styles`/`styleUrls`.
- Data flows **in** via `@Input()` and **out** via `@Output()` (or the newer `input()`/`output()` functions).
- Each component instance has its own lifecycle.

## 5. Syntax

```ts
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `
    <div class="card">
      <h3>{{ name() }}</h3>
      <button (click)="select.emit(name())">Select</button>
    </div>
  `,
  styles: [`.card { border: 1px solid #ddd; padding: 1rem; }`],
})
export class UserCardComponent {
  name = input.required<string>();      // input signal
  select = output<string>();            // output emitter
}
```

## 6. Example

```ts
import { Component, signal } from '@angular/core';
import { UserCardComponent } from './user-card.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [UserCardComponent],
  template: `
    <app-user-card
      [name]="currentUser()"
      (select)="onSelect($event)" />
    <p>Selected: {{ selected() }}</p>
  `,
})
export class AppComponent {
  currentUser = signal('Ada Lovelace');
  selected = signal('');

  onSelect(name: string) {
    this.selected.set(name);
  }
}
```

```html
<!-- rendered output -->
<div class="card">
  <h3>Ada Lovelace</h3>
  <button>Select</button>
</div>
<p>Selected: Ada Lovelace</p>
```

## 7. Real World Use Case

In an e-commerce app, a `ProductCardComponent` displays a product's image, title, and price. It takes the product as an `@Input()` and emits an `addToCart` event. The product list page renders dozens of these cards in a loop with `@for`, keeping the page code tiny and the card logic in one reusable place.

## 8. Interview Questions

**Q1:** What is the difference between a component and a directive?
**A:** A component is essentially a directive *with a template*. Components control a view (HTML + styles), while directives (attribute/structural) modify the behavior or appearance of existing elements without their own template.

**Q2:** What is a standalone component and why was it introduced?
**A:** A standalone component sets `standalone: true` and manages its own dependencies via its `imports` array, so it doesn't need to be declared in an NgModule. It was introduced to reduce boilerplate, simplify mental model, and improve tree-shaking/lazy loading.

**Q3:** How does data flow between a parent and child component?
**A:** Data flows **down** from parent to child via `@Input()` (or `input()`) bindings, and **up** from child to parent via `@Output()` (or `output()`) `EventEmitter`s. This one-way data flow makes change detection predictable.

**Q4:** What is view encapsulation?
**A:** It controls how component styles are scoped. The default `ViewEncapsulation.Emulated` adds unique attributes to scope CSS to the component. `None` makes styles global, and `ShadowDom` uses native Shadow DOM for true isolation.

**Q5:** What is the difference between `template` and `templateUrl`?
**A:** `template` defines the HTML inline as a string, while `templateUrl` points to an external `.html` file. Functionally identical; inline is handy for tiny templates, external files for larger ones.

## 9. Common Mistakes

- **Forgetting to import the component** in the consuming standalone component's `imports` array → "is not a known element" error.
- **Mutating `@Input()` objects directly** instead of emitting changes back to the parent, breaking one-way data flow.
- Putting **too much logic in the component** instead of delegating to a service.
- Using a **selector that clashes** with native HTML or another component.
- Forgetting `standalone: true` and then trying to use `imports`.

## 10. Advanced Notes

- Use `ChangeDetectionStrategy.OnPush` for performance — the component only re-renders when its inputs change by reference or a signal it reads updates.
- The new **signal inputs** (`input()`) are read in templates as `name()` and integrate with `computed()`/`effect()`.
- `host` metadata lets a component bind to its own host element (e.g. `host: { '[class.active]': 'isActive()' }`).
- Dynamic components can be created with `ViewContainerRef.createComponent()`.
- Deferred loading with `@defer` blocks can lazy-load heavy components only when needed.
