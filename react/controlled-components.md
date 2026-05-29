# Controlled Components

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A controlled component is a form input whose value is **driven by React state**. The state is the single source of truth, and the input displays whatever the state says, updating state on every change via an `onChange` handler.

## 2. Simple Explanation

In plain HTML, an `<input>` keeps its own value internally. In a controlled component, React holds the value in state and tells the input what to show. Every keystroke updates state, and the new state flows back into the input — React is fully "in control."

## 3. Why It Is Used

- Have a **single source of truth** for form data in React state.
- Enable **instant validation**, formatting, and conditional UI as the user types.
- Easily **read, transform, or reset** input values programmatically.
- Keep form data in sync with the rest of the app.

## 4. Key Points

- Driven by two things: a `value` (or `checked`) prop bound to state, and an `onChange` handler that updates that state.
- Opposite of an **uncontrolled component**, which stores its own value in the DOM (read via a `ref`).
- Works for `<input>`, `<textarea>`, and `<select>`.
- For checkboxes/radios, bind `checked` instead of `value`.
- Setting `value` without `onChange` makes a **read-only** input (React warns).

## 5. Syntax

```jsx
const [name, setName] = useState("");

<input
  value={name}                                   // state drives the input
  onChange={(e) => setName(e.target.value)}      // input updates state
/>
```

## 6. Example

```jsx
import { useState } from "react";

function LoginForm() {
  const [form, setForm] = useState({ email: "", remember: false });

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setForm((prev) => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(form); // single source of truth
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={form.email} onChange={handleChange} />
      <label>
        <input
          name="remember"
          type="checkbox"
          checked={form.remember}
          onChange={handleChange}
        />
        Remember me
      </label>
      <button type="submit">Log in</button>
    </form>
  );
}
```

## 7. Real World Use Case

A sign-up form validates as the user types: the email field is controlled, so React can instantly check the format and show "Invalid email" the moment it's wrong, disable the submit button until the form is valid, and auto-format a phone number — all because every keystroke updates and reflects React state.

## 8. Interview Questions

**Q1:** What is a controlled component?
**A:** A form input whose value is controlled by React state. It receives its current value via the `value` (or `checked`) prop and updates state through an `onChange` handler, making React state the single source of truth.

**Q2:** What is the difference between controlled and uncontrolled components?
**A:** Controlled inputs store their value in React state (`value` + `onChange`). Uncontrolled inputs keep their value in the DOM and you read it imperatively with a `ref` (often using `defaultValue`). Controlled gives more control/validation; uncontrolled is simpler for basic forms.

**Q3:** Why might you get a "controlled to uncontrolled" warning?
**A:** It happens when an input's `value` switches between a defined value and `undefined`/`null` between renders (e.g. starting state as `undefined`). Always initialize controlled values to a defined string (like `""`) to keep them controlled throughout.

**Q4:** How do you handle checkboxes and radio buttons as controlled components?
**A:** Bind the `checked` prop to a boolean in state (instead of `value`) and update it in `onChange` using `e.target.checked`. For radio groups, compare the bound state value to each radio's `value`.

**Q5:** When would you prefer an uncontrolled component?
**A:** For simple forms where you only need the value at submit time, for file inputs (which must be uncontrolled), or for performance-sensitive large forms where you want to avoid re-rendering on every keystroke. Libraries like React Hook Form lean on uncontrolled inputs for speed.

## 9. Common Mistakes

- Initializing `value` as `undefined`/`null`, causing controlled→uncontrolled warnings.
- Setting `value` without an `onChange`, making the input read-only by accident.
- Using `value` for checkboxes instead of `checked`.
- Mutating state directly instead of using the setter.
- Re-rendering huge forms on every keystroke without optimization.

## 10. Advanced Notes

- **React Hook Form** and **Formik** manage form state efficiently; React Hook Form favors uncontrolled inputs + refs to minimize re-renders.
- Debounce expensive `onChange` work (e.g. async validation, search) to avoid lag.
- File inputs (`<input type="file">`) are always uncontrolled — read files via the `ref` or event.
- For very large forms, splitting fields into memoized components or using uncontrolled inputs prevents per-keystroke re-render storms.
- React 19 adds form Actions and `useActionState`/`useFormStatus` for streamlined form submission and pending states.
