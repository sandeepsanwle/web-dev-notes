# Forms

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Angular **forms** capture and validate user input. Angular offers two approaches: **template-driven forms** (logic in the HTML, good for simple cases) and **reactive forms** (logic in TypeScript, good for complex/dynamic cases).

## 2. Simple Explanation

A form is how your app collects information — a login box, a sign-up page, a checkout. Angular gives you tools to bind inputs to data, validate them ("email is required"), and know when fields are touched, dirty, or invalid. You can write the logic mostly in the template (template-driven) or mostly in code (reactive).

## 3. Why It Is Used

- **Capture user input** reliably and bound to your model.
- **Validation** with instant feedback and error messages.
- **Track form state** — valid, dirty, touched, pristine.
- **Two strategies** to fit simple vs complex needs.

## 4. Key Points

- Template-driven uses `[(ngModel)]` and the `FormsModule`.
- Reactive uses `FormControl`/`FormGroup`/`FormBuilder` and `ReactiveFormsModule`.
- Both track control state: `valid`, `invalid`, `dirty`, `pristine`, `touched`, `untouched`.
- Validators can be built-in (`Validators.required`) or custom.
- Reactive forms are more testable, scalable, and support dynamic controls.

## 5. Syntax

```html
<!-- Template-driven -->
<form #f="ngForm" (ngSubmit)="submit(f.value)">
  <input name="email" ngModel required email />
  <button [disabled]="f.invalid">Save</button>
</form>
```

```ts
// Reactive (overview — see reactive-forms.md for detail)
form = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email]),
});
```

## 6. Example

```ts
// Template-driven login form
import { Component } from '@angular/core';
import { FormsModule, NgForm } from '@angular/forms';

@Component({
  selector: 'app-login',
  standalone: true,
  imports: [FormsModule],
  template: `
    <form #f="ngForm" (ngSubmit)="submit(f)">
      <input name="email" ngModel required email #email="ngModel" />
      @if (email.invalid && email.touched) {
        <small>Valid email required</small>
      }
      <input name="password" type="password" ngModel required minlength="6" />
      <button [disabled]="f.invalid">Log in</button>
    </form>
  `,
})
export class LoginComponent {
  submit(f: NgForm) {
    if (f.valid) console.log(f.value); // { email, password }
  }
}
```

## 7. Real World Use Case

A quick newsletter sign-up with a single email field is a perfect fit for a **template-driven** form — minimal code, validation right in the markup. A multi-step checkout with dynamic line items, cross-field validation, and conditional sections is better served by **reactive** forms, where the structure lives in TypeScript and can be built/changed programmatically.

## 8. Interview Questions

**Q1:** What are the two types of forms in Angular and when do you use each?

**A:** Template-driven (logic in the template via `ngModel`, `FormsModule`) for simple, small forms; reactive (logic in the component via `FormControl`/`FormGroup`, `ReactiveFormsModule`) for complex, dynamic, and heavily-tested forms.

**Q2:** What does the table below summarize about the two approaches?

**A:**

| Aspect | Template-driven | Reactive |
|---|---|---|
| Source of truth | Template | Component class |
| Module | `FormsModule` | `ReactiveFormsModule` |
| Data flow | Async | Synchronous |
| Validation | Directives in template | Validator functions in code |
| Scalability | Simple forms | Complex/dynamic forms |
| Testability | Harder | Easier |

**Q3:** What is the difference between `dirty`/`pristine` and `touched`/`untouched`?
**A:** `dirty`/`pristine` track whether the *value* has changed. `touched`/`untouched` track whether the user has *focused and blurred* the control. They're often combined to decide when to show errors.

**Q4:** How do you show a validation error only after interaction?
**A:** Check both the validity and the interaction state, e.g. `control.invalid && (control.touched || control.dirty)`, so errors don't appear before the user has engaged with the field.

**Q5:** Can you mix template-driven and reactive forms?
**A:** In the same form, no — pick one approach per form. You can use both styles in the same application (different forms), but mixing `ngModel` with `formControl` on one control is discouraged.

## 9. Common Mistakes

- Forgetting to import `FormsModule` (template-driven) or `ReactiveFormsModule` (reactive).
- Omitting the `name` attribute on `ngModel` inputs inside a form.
- Showing validation errors immediately instead of after `touched`/`dirty`.
- Mixing the two approaches on a single control.
- Not handling the submit's `valid` check before using the data.

## 10. Advanced Notes

- Reactive forms support **typed forms** (strict typing of values) since Angular 14.
- Cross-field validation is done with a validator on the `FormGroup` level.
- `updateOn: 'blur' | 'submit'` controls when validation/value updates fire.
- For very large/dynamic forms, reactive + `FormBuilder` + `FormArray` scales best.
- Signal-based forms are an emerging direction; for now reactive forms remain the robust default for complex scenarios.
