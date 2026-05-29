# Routing

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

Routing is how a single-page application (SPA) maps URLs to different views/components and navigates between them **without full page reloads**. In React, this is commonly handled by **React Router**.

## 2. Simple Explanation

In a traditional site, each URL loads a new HTML page from the server. In a React SPA, JavaScript swaps components in and out based on the URL while the page stays loaded. Routing keeps the URL bar in sync with what the user sees, so back/forward buttons and bookmarks still work.

## 3. Why It Is Used

- Show **different views** for different URLs (`/`, `/about`, `/users/5`).
- Enable **client-side navigation** without reloading the whole page.
- Support **bookmarkable, shareable URLs** and browser history.
- Handle **dynamic params**, nested layouts, and protected routes.

## 4. Key Points

- React Router is the de-facto library (`react-router-dom` for web).
- `<BrowserRouter>` uses the HTML5 History API; `<HashRouter>` uses URL hashes.
- Define routes with `<Routes>` + `<Route path element>`.
- Navigate declaratively with `<Link>`/`<NavLink>`, imperatively with `useNavigate`.
- Read URL params with `useParams`, query strings with `useSearchParams`.
- Supports **nested routes** and shared **layouts** via `<Outlet>`.

## 5. Syntax

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link,
  useParams,
  useNavigate,
} from "react-router-dom";

<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/users/:id" element={<User />} />
    <Route path="*" element={<NotFound />} />
  </Routes>
</BrowserRouter>
```

## 6. Example

```jsx
import { Link, Routes, Route, useParams, useNavigate } from "react-router-dom";

function User() {
  const { id } = useParams();          // read :id from URL
  const navigate = useNavigate();      // programmatic navigation
  return (
    <div>
      <h1>User {id}</h1>
      <button onClick={() => navigate("/")}>Go home</button>
    </div>
  );
}

function App() {
  return (
    <>
      <nav>
        <Link to="/">Home</Link> | <Link to="/users/42">User 42</Link>
      </nav>
      <Routes>
        <Route path="/" element={<h1>Home</h1>} />
        <Route path="/users/:id" element={<User />} />
      </Routes>
    </>
  );
}
```

## 7. Real World Use Case

A dashboard app uses nested routing: `/dashboard` renders a sidebar layout with an `<Outlet>`, and child routes like `/dashboard/analytics` and `/dashboard/settings` render inside it. A protected-route wrapper redirects unauthenticated users to `/login`, preserving the URL they tried to visit.

## 8. Interview Questions

**Q1:** How does client-side routing differ from traditional server-side routing?
**A:** Server-side routing requests a new HTML page from the server per URL (full reload). Client-side routing intercepts navigation, updates the URL via the History API, and swaps components in the existing page without a reload — faster transitions and preserved app state.

**Q2:** What is the difference between `BrowserRouter` and `HashRouter`?
**A:** `BrowserRouter` uses clean URLs via the HTML5 History API (`/about`) but needs server config to serve `index.html` for all routes. `HashRouter` puts the route after a `#` (`/#/about`), requiring no server config but producing uglier URLs and weaker SEO.

**Q3:** How do you create and read dynamic route parameters?
**A:** Define a param in the path with a colon (`/users/:id`) and read it in the component with the `useParams()` Hook (`const { id } = useParams()`). Query strings use `useSearchParams()`.

**Q4:** How do you implement a protected/private route?
**A:** Wrap the route's element in a component that checks auth state; if unauthenticated, render a `<Navigate to="/login" />` redirect, otherwise render the children (or `<Outlet>`). You can also store the attempted URL to redirect back after login.

**Q5:** What are nested routes and the `<Outlet>` component?
**A:** Nested routes let child routes render inside a parent route's layout. The parent renders `<Outlet />` as a placeholder where the matched child route's element appears, enabling shared layouts (sidebars, headers) without duplication.

## 9. Common Mistakes

- Using `<a href>` for internal links (causes a full reload) instead of `<Link>`.
- Forgetting server config for `BrowserRouter`, causing 404s on refresh of deep routes.
- Not adding a catch-all `path="*"` route for 404 pages.
- Putting side effects in render instead of reacting to route changes properly.
- Mixing up `useParams` (path params) with `useSearchParams` (query params).

## 10. Advanced Notes

- React Router v6.4+ introduced **data routers** (`createBrowserRouter`) with `loader`/`action` for data fetching and mutations tied to routes.
- **Code-splitting** routes with `React.lazy` + `<Suspense>` reduces initial bundle size.
- **Lazy loaders** and `defer`/`Await` enable streaming and skeletons while data loads.
- Frameworks like **Next.js** and **Remix** provide file-based routing with built-in SSR/SSG.
- Use `<NavLink>` for active-link styling and `useLocation` to react to the current path.
