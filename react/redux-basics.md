# Redux Basics

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

Redux is a predictable **state management library** for JavaScript apps. It stores the entire application state in a single, immutable **store** and updates it through pure functions called **reducers**, triggered by **actions**.

## 2. Simple Explanation

Imagine one central box (the store) holding all your app's data. Components don't change it directly. Instead they **dispatch** an action ("ADD_TODO") describing what happened. A **reducer** reads the action and returns the next state. Components subscribed to the store re-render with the new data.

## 3. Why It Is Used

- Manage **complex, shared global state** predictably.
- Provide a **single source of truth** for the whole app.
- Make state changes **traceable** (time-travel debugging, devtools).
- Decouple state logic from UI components.
- Handle **async** flows (API calls) with middleware/thunks.

## 4. Key Points

- **Three principles:** single source of truth, state is read-only (change via actions), changes via pure reducers.
- **Store** holds state; **actions** describe events; **reducers** compute new state.
- **Dispatch** sends an action; **selectors** read slices of state.
- Reducers must be **pure** and must not mutate state.
- **Redux Toolkit (RTK)** is the official, recommended way — far less boilerplate.
- Use `react-redux` (`Provider`, `useSelector`, `useDispatch`) to connect React.

## 5. Syntax

```jsx
// Modern Redux with Redux Toolkit
import { createSlice, configureStore } from "@reduxjs/toolkit";
import { Provider, useSelector, useDispatch } from "react-redux";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; }, // Immer makes this safe
    addBy: (state, action) => { state.value += action.payload; },
  },
});

export const { increment, addBy } = counterSlice.actions;
const store = configureStore({ reducer: { counter: counterSlice.reducer } });
```

## 6. Example

```jsx
function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(increment())}>+1</button>
      <button onClick={() => dispatch(addBy(5))}>+5</button>
    </div>
  );
}

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

## 7. Real World Use Case

A large e-commerce app keeps the shopping cart, authenticated user, and product filters in a Redux store. Cart items added from a product page instantly appear in the header badge and checkout page, all reading from the same single source of truth — no tangled prop passing across distant pages.

## 8. Interview Questions

**Q1:** What are the three core principles of Redux?
**A:** (1) Single source of truth — all state lives in one store. (2) State is read-only — the only way to change it is by dispatching an action. (3) Changes are made with pure reducers — functions that take state + action and return new state without side effects.

**Q2:** What is the role of a reducer, and why must it be pure?
**A:** A reducer computes the next state from the current state and an action. It must be pure (no mutations, no side effects, deterministic) so Redux stays predictable, supports time-travel debugging, and reducers can be tested and composed reliably.

**Q3:** How do you handle asynchronous logic in Redux?
**A:** With middleware. `redux-thunk` lets action creators return functions that dispatch later (e.g. after a fetch). Redux Toolkit's `createAsyncThunk` standardizes this with pending/fulfilled/rejected actions. `redux-saga` is an alternative for complex flows.

**Q4:** What is Redux Toolkit and why is it recommended?
**A:** RTK is the official, opinionated toolset that reduces boilerplate. `createSlice` auto-generates actions/reducers, uses Immer for "mutating" syntax with immutable results, and `configureStore` sets up the store with good defaults and devtools. It's the standard modern way to write Redux.

**Q5:** When should you use Redux versus Context or local state?
**A:** Use local state for component-specific data and Context for low-frequency shared values (theme, auth). Reach for Redux when you have complex, frequently updated global state shared across many parts of the app, need middleware/devtools, or want strict, testable update logic.

## 9. Common Mistakes

- Mutating state in plain reducers (allowed *only* inside RTK's Immer-powered `createSlice`).
- Putting everything in Redux, including local UI state that doesn't need to be global.
- Storing non-serializable values (functions, class instances) in the store.
- Doing side effects inside reducers instead of in middleware/thunks.
- Writing manual action types/creators instead of using Redux Toolkit.

## 10. Advanced Notes

- **RTK Query** adds powerful data fetching/caching on top of RTK, often replacing manual thunks for server state.
- Use **memoized selectors** (`reselect` / `createSelector`) to derive data efficiently and avoid unnecessary re-renders.
- `useSelector` re-renders when its selected slice changes (by reference) — keep selectors narrow.
- The store is a single object tree; structure it by feature ("slices") for scalability.
- Alternatives like **Zustand**, **Jotai**, and **Recoil** offer simpler APIs for many use cases; Redux still shines for large, structured apps.
