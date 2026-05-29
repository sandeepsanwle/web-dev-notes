# Pipes

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A **pipe** transforms a value directly in the template for display, using the `|` syntax. Angular ships built-in pipes (e.g. `date`, `currency`, `async`) and lets you create custom ones with `@Pipe`.

## 2. Simple Explanation

A pipe is a little formatter you apply right in your HTML. You take a raw value, "pipe" it through a transformer, and get a nicely formatted result — like turning `1234.5` into `$1,234.50` or a raw date into `May 29, 2026`. It changes how data *looks*, not the data itself.

## 3. Why It Is Used

- **Format data for display** (dates, currency, percentages) cleanly.
- **Keep components clean** — no formatting logic in the class.
- **Reusable & declarative** — apply the same transform anywhere.
- **Composable** — chain pipes together.

## 4. Key Points

- Applied in templates with `value | pipeName:arg1:arg2`.
- Built-ins include `date`, `currency`, `number`, `percent`, `json`, `slice`, `uppercase`, `async`.
- Custom pipes implement `PipeTransform` with a `transform()` method.
- Pipes are **pure by default** (re-run only when the input reference changes); set `pure: false` for impure.
- Standalone pipes are imported into a component's `imports`.

## 5. Syntax

```html
<p>{{ amount | currency:'USD' }}</p>
<p>{{ today | date:'mediumDate' }}</p>
<p>{{ name | uppercase }}</p>
<p>{{ items | slice:0:3 }}</p>  <!-- first 3 items -->
```

## 6. Example

```ts
// truncate.pipe.ts — a custom standalone pipe
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'truncate', standalone: true })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit = 20, trail = '…'): string {
    return value.length > limit ? value.slice(0, limit) + trail : value;
  }
}
```

```ts
import { Component } from '@angular/core';
import { TruncatePipe } from './truncate.pipe';

@Component({
  selector: 'app-post',
  standalone: true,
  imports: [TruncatePipe],
  template: `<p>{{ body | truncate:10 }}</p>`, // "Hello worl…"
})
export class PostComponent {
  body = 'Hello world, this is a long post body.';
}
```

## 7. Real World Use Case

A product table displays prices with `| currency:'EUR'`, posting dates with `| date:'short'`, and long descriptions with a custom `| truncate:50`. The component holds raw values from the API; all presentation lives in the template via pipes, so formatting is consistent and easy to change in one place.

## 8. Interview Questions

**Q1:** What is the difference between a pure and an impure pipe?
**A:** A pure pipe (default) only re-executes when its input value changes by reference, making it efficient. An impure pipe (`pure: false`) runs on every change-detection cycle — flexible for mutable data but potentially costly.

**Q2:** Why is the `async` pipe special?
**A:** It subscribes to an Observable/Promise, returns the latest emitted value, and automatically unsubscribes when the component is destroyed — handling subscription lifecycle and triggering change detection for you.

**Q3:** How do you pass parameters to a pipe?
**A:** Append them after the pipe name separated by colons: `value | date:'short':'UTC'`. Each colon-separated value becomes an argument to the pipe's `transform()` method.

**Q4:** Can you chain pipes, and how is order handled?
**A:** Yes: `value | slice:0:10 | uppercase`. They apply left to right — the output of one pipe becomes the input of the next.

**Q5:** When should you use a pipe vs a method in the component?
**A:** Use a pure pipe for display transformations — it's memoized and won't re-run unnecessarily. A method called in the template runs on every change-detection cycle, hurting performance.

## 9. Common Mistakes

- Using an **impure pipe carelessly**, causing performance problems.
- Expecting a **pure pipe to react to in-place mutations** (it needs a new reference).
- Putting heavy computation in a pipe used in a large `@for` loop.
- Forgetting to add the pipe to a standalone component's `imports`.
- Using the `json` pipe in production output (it's a debugging aid).

## 10. Advanced Notes

- Pipes can inject dependencies (e.g. `LOCALE_ID`, services) through their constructor.
- The built-in i18n pipes (`date`, `currency`, `number`) respect the configured locale; register locale data with `registerLocaleData`.
- Prefer pipes over getters/methods in templates for cacheable transforms.
- An impure pipe can implement caching internally to mitigate cost.
- With signals, many derived display values can move to `computed()` instead of pipes when logic is component-specific.
