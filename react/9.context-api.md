# Context API

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

The Context API is React's built-in mechanism for sharing data across the component tree **without passing props manually at every level**. It provides a way to make values "globally" available to a subtree.

## 2. Simple Explanation

Normally data flows parent → child via props. If a deeply nested component needs data from the top, you'd have to pass it through every layer (prop drilling). Context creates a shortcut: a Provider broadcasts a value, and any descendant can read it directly.

## 3. Why It Is Used

- Avoid **prop drilling** through many intermediate components.
- Share **global-ish** data: theme, current user, language/locale, auth state.
- Provide values that many components need but few components own.

## 4. Key Points

- Create with `createContext(defaultValue)`.
- Wrap a subtree in `<Context.Provider value={...}>`.
- Read with the **`useContext`** Hook.
- All consumers **re-render** when the Provider's `value` changes.
- Context is for **low-frequency** updates; it's not a full state manager.
- The `defaultValue` is used only when there's no matching Provider above.

## 5. Syntax

```jsx
import { createContext, useContext } from "react";

const ThemeContext = createContext("light"); // default

// Provide
<ThemeContext.Provider value="dark">
  <App />
</ThemeContext.Provider>

// Consume
const theme = useContext(ThemeContext);
```

## 6. Example

```jsx
import { createContext, useContext, useState } from "react";

const AuthContext = createContext(null);

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const login = (name) => setUser({ name });
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

function Navbar() {
  const { user, logout } = useContext(AuthContext);
  return user ? (
    <button onClick={logout}>Hi {user.name}, log out</button>
  ) : (
    <span>Please log in</span>
  );
}

function App() {
  return (
    <AuthProvider>
      <Navbar />
    </AuthProvider>
  );
}
```

## 7. Real World Use Case

A theme switcher (light/dark mode) uses Context: a `ThemeProvider` holds the current theme and a toggle function. Buttons, cards, and headers anywhere in the app read the theme via `useContext` and restyle instantly when the user flips the switch — no props threaded through the tree.

## 8. Interview Questions

**Q1:** What problem does the Context API solve?
**A:** It eliminates prop drilling by letting deeply nested components access shared data directly, without intermediate components forwarding props they don't use.

**Q2:** What is the performance concern with Context?
**A:** When a Provider's `value` changes, **all** consuming components re-render, even if they only use part of the value. Frequent updates or large monolithic context values can cause widespread re-renders. Split contexts and memoize the value to mitigate.

**Q3:** How do you avoid unnecessary re-renders from a context value object?
**A:** Memoize the value with `useMemo` so a new object isn't created on every render, and split context into separate providers (e.g. state vs. dispatch) so unrelated consumers don't re-render.

**Q4:** Is Context a replacement for Redux?
**A:** Not fully. Context is a transport mechanism for sharing values; it has no built-in reducers, middleware, devtools, or selective subscription. For complex, high-frequency global state, Redux (or Zustand/Jotai) is often better. Context + `useReducer` can suffice for simpler apps.

**Q5:** What does the `defaultValue` in `createContext` do?
**A:** It's the value consumers receive when there is no matching Provider above them in the tree. It's mainly useful for testing and for catching missing-provider mistakes.

## 9. Common Mistakes

- Passing a new object as `value` every render (breaks memoization, re-renders all consumers).
- Using one giant context for everything instead of splitting by concern.
- Using Context for high-frequency updates (e.g. mouse position) — causes performance issues.
- Forgetting the Provider, so consumers silently get the default value.
- Treating Context as a state manager rather than a sharing mechanism.

## 10. Advanced Notes

- Pattern: combine **`useReducer` + Context** for a lightweight Redux-like store.
- Split **state** and **dispatch** into separate contexts so components that only dispatch don't re-render on state changes.
- For selective subscriptions (re-render only on the slice you use), libraries like `use-context-selector`, Zustand, or Jotai help.
- Context values can be functions, objects, or primitives — wrap object values in `useMemo`.
- React 19 lets you render `<Context>` directly as a provider (instead of `<Context.Provider>`).
