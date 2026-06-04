# Directives

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **directive** is a class that adds behavior to existing DOM elements. Angular has three kinds: **components** (directives with a template), **structural directives** (change DOM layout, e.g. `*ngIf`), and **attribute directives** (change appearance/behavior, e.g. `ngClass`).

## 2. Simple Explanation

If a component is a brand-new UI piece, a directive is a "sticker" you put on an existing element to give it extra powers — make it highlight on hover, show/hide based on a condition, or repeat for each item in a list. You attach it as an attribute and it enhances the element.

## 3. Why It Is Used

- **Reusable DOM behavior** without creating new components.
- **Structural control** — conditionally render or repeat elements.
- **Cross-cutting UI logic** — tooltips, autofocus, permissions, drag.
- **Keep templates declarative** while encapsulating DOM manipulation.

## 4. Key Points

- Created with `@Directive` and a selector (often an attribute like `[appHighlight]`).
- **Attribute directives** modify the host element's look/behavior.
- **Structural directives** (`*ngIf`, `*ngFor`) add/remove DOM via a `TemplateRef`/`ViewContainerRef`; modern templates favor `@if`/`@for` control flow.
- Use `@HostBinding`/`@HostListener` (or `host` metadata) to bind to the host.
- Standalone directives are imported into a component's `imports`.

## 5. Syntax

```ts
import { Directive, ElementRef, HostListener, inject, input } from '@angular/core';

@Directive({ selector: '[appHighlight]', standalone: true })
export class HighlightDirective {
  color = input('yellow', { alias: 'appHighlight' });
  private el = inject(ElementRef<HTMLElement>);

  @HostListener('mouseenter') onEnter() { this.set(this.color()); }
  @HostListener('mouseleave') onLeave() { this.set(''); }

  private set(c: string) { this.el.nativeElement.style.backgroundColor = c; }
}
```

## 6. Example

```ts
// usage component
import { Component } from '@angular/core';
import { HighlightDirective } from './highlight.directive';

@Component({
  selector: 'app-demo',
  standalone: true,
  imports: [HighlightDirective],
  template: `
    <p appHighlight="lightblue">Hover to highlight me</p>
    <p [appHighlight]="dynamicColor">Hover for dynamic color</p>
  `,
})
export class DemoComponent {
  dynamicColor = 'pink';
}
```

```html
<!-- built-in structural directives vs modern control flow -->
<div *ngIf="loggedIn">Welcome</div>     <!-- classic -->
@if (loggedIn) { <div>Welcome</div> }    <!-- modern (v17+) -->
```

## 7. Real World Use Case

A `hasPermission` (e.g. `*appHasRole="'admin'"`) structural directive removes UI elements the user isn't allowed to see, centralizing authorization in the template. An `autofocus` attribute directive focuses an input on render, and a `tooltip` directive shows help text on hover — all reusable across the whole app without new components.

## 8. Interview Questions

**Q1:** What are the three types of directives in Angular?
**A:** Components (directives with a template), structural directives (change DOM structure, e.g. `*ngIf`, `*ngFor`), and attribute directives (change appearance/behavior of an element, e.g. `ngClass`, `ngStyle`).

**Q2:** What is the difference between a structural and an attribute directive?
**A:** Structural directives add or remove elements from the DOM (they use the `*` shorthand and a template). Attribute directives modify the look or behavior of an existing element without changing the DOM layout.

**Q3:** What do `@HostBinding` and `@HostListener` do?
**A:** `@HostBinding` binds a property/attribute/class/style of the host element to a directive property. `@HostListener` subscribes to events on the host element and runs a method when they fire.

**Q4:** Why can you only have one structural directive per element?
**A:** The `*` shorthand desugars into an `<ng-template>` wrapping the element; two structural directives would compete to control that single template. Use `<ng-container>` to nest them, or modern `@if`/`@for`.

**Q5:** How do modern control-flow blocks relate to structural directives?
**A:** `@if`, `@for`, and `@switch` (v17+) are built-in template syntax that replace `*ngIf`/`*ngFor`/`*ngSwitch`. They're faster, need no imports, and offer features like `@empty` and required `track`.

## 9. Common Mistakes

- Putting **two structural directives on one element** (not allowed).
- Manipulating the DOM directly with `nativeElement` instead of `Renderer2` (breaks SSR/security).
- Forgetting to import the directive into a standalone component's `imports`.
- Forgetting `track` in `@for` / `trackBy` in `*ngFor`, hurting performance.
- Overusing directives where a component or pipe is a better fit.

## 10. Advanced Notes

- Use `Renderer2` for DOM changes to stay compatible with server-side rendering and security policies.
- Custom structural directives use `TemplateRef` + `ViewContainerRef`; `createEmbeddedView()` with a context exposes template variables.
- Directive composition API (`hostDirectives`) lets a component/directive apply other directives automatically.
- Selectors can target attributes, classes, or combinations (e.g. `button[appConfirm]`).
- `exportAs` lets a template reference the directive instance via a template variable.
