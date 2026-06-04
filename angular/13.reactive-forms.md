# Reactive Forms

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

**Reactive forms** are a model-driven approach where the form structure, values, and validation are defined explicitly in the component class using `FormControl`, `FormGroup`, `FormArray`, and `FormBuilder` from `ReactiveFormsModule`.

## 2. Simple Explanation

With reactive forms, the form lives in your TypeScript code as objects you fully control. You build a tree of controls, attach validators, and bind them to the template. Because the model is in code, you can read, change, add, and validate fields programmatically — ideal for complex, dynamic forms.

## 3. Why It Is Used

- **Full control & predictability** — the model is explicit and synchronous.
- **Dynamic forms** — add/remove controls at runtime with `FormArray`.
- **Powerful validation** — sync, async, and cross-field validators.
- **Highly testable** — no DOM needed to test form logic.

## 4. Key Points

- Building blocks: `FormControl` (single field), `FormGroup` (object of controls), `FormArray` (list of controls).
- `FormBuilder` (`inject(FormBuilder)` / `fb.group(...)`) reduces boilerplate.
- Bind with `[formGroup]`, `formControlName`, `formArrayName`.
- React to changes via `valueChanges` and `statusChanges` Observables.
- Supports **typed forms** for compile-time safety.

## 5. Syntax

```ts
import { FormControl, FormGroup, Validators } from '@angular/forms';

const form = new FormGroup({
  name: new FormControl('', { nonNullable: true, validators: [Validators.required] }),
  email: new FormControl('', [Validators.required, Validators.email]),
});

form.value;            // { name: '', email: '' }
form.get('email')?.invalid;
form.patchValue({ name: 'Ada' });
```

## 6. Example

```ts
import { Component, inject } from '@angular/core';
import { FormBuilder, FormArray, Validators, ReactiveFormsModule } from '@angular/forms';

@Component({
  selector: 'app-order',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="submit()">
      <input formControlName="customer" placeholder="Customer" />

      <div formArrayName="items">
        @for (item of items.controls; track $index) {
          <input [formControlName]="$index" placeholder="Item" />
        }
      </div>

      <button type="button" (click)="addItem()">Add item</button>
      <button [disabled]="form.invalid">Submit</button>
    </form>
  `,
})
export class OrderComponent {
  private fb = inject(FormBuilder);

  form = this.fb.group({
    customer: ['', Validators.required],
    items: this.fb.array([this.fb.control('', Validators.required)]),
  });

  get items() { return this.form.get('items') as FormArray; }
  addItem() { this.items.push(this.fb.control('', Validators.required)); }
  submit() { if (this.form.valid) console.log(this.form.getRawValue()); }
}
```

## 7. Real World Use Case

An invoice builder lets users add an arbitrary number of line items. A `FormArray` holds one `FormGroup` per line (description, qty, price), the user clicks "Add line" to push new groups, a cross-field validator ensures totals are positive, and `valueChanges` recomputes the grand total live. This dynamic, validated structure is exactly what reactive forms excel at.

## 8. Interview Questions

**Q1:** What is the difference between `FormGroup`, `FormControl`, and `FormArray`?
**A:** `FormControl` represents a single field's value and validation state. `FormGroup` is a fixed collection of controls keyed by name (like an object). `FormArray` is an ordered, dynamic list of controls (like an array), ideal when the number of fields varies at runtime.

**Q2:** What is the difference between `setValue` and `patchValue`?
**A:** `setValue` requires a value for **every** control and throws if any are missing — strict and safe. `patchValue` updates only the provided controls and ignores the rest — flexible for partial updates.

**Q3:** What's the difference between `value` and `getRawValue()`?
**A:** `value` excludes disabled controls, while `getRawValue()` returns all controls including disabled ones. Use `getRawValue()` when you need disabled fields' values on submit.

**Q4:** How do synchronous and asynchronous validators differ?
**A:** Sync validators run immediately and return errors or null. Async validators return a `Promise`/`Observable` (e.g. checking username availability via API); while pending, the control's status is `PENDING`.

**Q5:** How do you react to value changes in a reactive form?
**A:** Subscribe to `form.valueChanges` (or a control's) — an Observable emitting on every change. Combine with operators like `debounceTime`/`distinctUntilChanged`, or use `toSignal()` to expose it as a signal.

## 9. Common Mistakes

- Forgetting to import `ReactiveFormsModule`.
- Using `setValue` with a partial object (throws) — use `patchValue` instead.
- Not casting `FormArray`/`FormGroup` from `get()` (it returns `AbstractControl`).
- Subscribing to `valueChanges` without unsubscribing → memory leak.
- Mutating control values directly instead of using `setValue`/`patchValue`.

## 10. Advanced Notes

- **Typed reactive forms** (v14+) infer value types from the control structure; use `nonNullable` to avoid `null` in values.
- Custom validators are functions returning `ValidationErrors | null`; group-level validators enable cross-field checks (e.g. password match).
- `updateOn: 'blur' | 'submit'` reduces validation churn on heavy forms.
- `markAllAsTouched()` reveals all errors on submit attempt.
- Bridge to signals with `toSignal(form.valueChanges)` for reactive derived UI; combine with `OnPush` for performance.
