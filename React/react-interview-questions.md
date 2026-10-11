# React Interview Questions & Answers

A comprehensive guide covering React fundamentals through advanced concepts, hooks, performance, ecosystem, and React 18+ features.

---

## Table of Contents

1. [React Fundamentals](#1-react-fundamentals)
2. [JSX](#2-jsx)
3. [Components, Props & State](#3-components-props--state)
4. [Component Lifecycle](#4-component-lifecycle)
5. [Hooks](#5-hooks)
6. [Virtual DOM & Reconciliation](#6-virtual-dom--reconciliation)
7. [Events & Forms](#7-events--forms)
8. [Context API](#8-context-api)
9. [Refs & DOM Access](#9-refs--dom-access)
10. [Performance Optimization](#10-performance-optimization)
11. [Code Splitting & Suspense](#11-code-splitting--suspense)
12. [Error Handling](#12-error-handling)
13. [Higher-Order Components & Render Props](#13-higher-order-components--render-props)
14. [React Router](#14-react-router)
15. [State Management (Redux & Alternatives)](#15-state-management-redux--alternatives)
16. [Testing](#16-testing)
17. [Server-Side Rendering & Next.js](#17-server-side-rendering--nextjs)
18. [React 18+ Features](#18-react-18-features)
19. [TypeScript with React](#19-typescript-with-react)
20. [Miscellaneous & Best Practices](#20-miscellaneous--best-practices)

---

## 1. React Fundamentals

### Q1. What is React?
React is a JavaScript library (not a full framework) for building user interfaces, primarily for single-page applications. It lets developers build encapsulated components that manage their own state and compose them to create complex UIs. It's maintained by Meta and a community of developers.

### Q2. What are the key features of React?
- **Component-based architecture** – UI is split into reusable, independent pieces.
- **Virtual DOM** – an in-memory representation of the UI used to minimize direct DOM manipulation.
- **Declarative UI** – you describe *what* the UI should look like for a given state, and React handles the *how*.
- **Unidirectional data flow** – data flows from parent to child via props.
- **JSX** – a syntax extension that allows writing HTML-like code in JavaScript.
- **Rich ecosystem** – hooks, context, concurrent rendering, and a large third-party library ecosystem.

### Q3. What is the difference between a library and a framework, and why is React a library?
A framework dictates the architecture of your application and calls your code (inversion of control), while a library is a collection of functions you call as needed. React only handles the view layer — routing, state management, and HTTP requests require separate libraries — so you assemble your own "stack," which is why it's classified as a library.

### Q4. What is the difference between React and ReactDOM?
`react` contains the core logic of the library (components, hooks, elements, reconciliation logic) and is platform-agnostic. `react-dom` is the renderer that binds React to the browser DOM (`ReactDOM.createRoot`, `render`, `createPortal`). Other renderers exist for other platforms, e.g., `react-native` for mobile.

### Q5. What is an Element vs a Component?
A **React element** is a plain, immutable JavaScript object describing what should appear on screen (`{ type: 'div', props: {...} }`), created via `React.createElement` or JSX. A **component** is a function or class that returns elements. Elements are cheap to create; components are the reusable blueprints that produce elements.

---

## 2. JSX

### Q6. What is JSX?
JSX (JavaScript XML) is a syntax extension that lets you write HTML-like markup inside JavaScript. It compiles (via Babel) into `React.createElement()` calls. It is not required to use React, but it makes component structure easier to read and write.

```jsx
const element = <h1 className="greeting">Hello, world!</h1>;
// Compiles roughly to:
const element = React.createElement('h1', { className: 'greeting' }, 'Hello, world!');
```

### Q7. Why can't browsers read JSX directly?
Browsers only understand JavaScript. JSX must be transpiled by a tool like Babel or the TypeScript compiler into standard `React.createElement()` (or the newer automatic JSX runtime `_jsx()`) calls before it can run.

### Q8. What are the rules of JSX?
- Must return a **single root element** (or use a Fragment `<>...</>`).
- All tags must be **closed** (`<img />`, `<br />`).
- Use `className` instead of `class`, `htmlFor` instead of `for` (since they're reserved JS words).
- JavaScript expressions go inside curly braces `{}`.
- Attributes use camelCase (`onClick`, `tabIndex`).

### Q9. What are Fragments and why use them?
`<React.Fragment>` or the shorthand `<>...</>` lets a component return multiple children without adding an extra wrapper DOM node (like a redundant `<div>`), keeping the DOM tree clean and avoiding CSS/layout issues (e.g., in tables or flex/grid layouts).

### Q10. How do you render a list in JSX, and why do list items need a `key` prop?
```jsx
{items.map(item => <li key={item.id}>{item.name}</li>)}
```
The `key` gives each element a stable identity across renders, letting React's diffing algorithm efficiently determine which items changed, were added, or removed, instead of re-rendering the whole list. Keys should be stable, unique IDs — not array indices — when list order can change, since index-based keys can cause incorrect state association between re-ordered items.

---

## 3. Components, Props & State

### Q11. What is the difference between functional and class components?
Class components extend `React.Component`, use `this.state` and `this.setState()`, and implement lifecycle methods (`componentDidMount`, etc.). Functional components are plain JavaScript functions that use **Hooks** (`useState`, `useEffect`, etc.) to manage state and side effects. Since React 16.8, functional components with hooks can do everything class components can, and they're now the standard/preferred approach due to simpler syntax, easier logic reuse, and no `this` binding issues.

### Q12. What are props?
Props ("properties") are read-only inputs passed from a parent component to a child component, similar to function arguments. They enable component reusability and configuration but **cannot be modified by the child** — data flows one way (top-down).

### Q13. What is state, and how does it differ from props?
State is data that is local and private to a component, managed within that component (via `useState`/`useReducer` or `this.state`), and can change over time in response to user actions or network responses. Unlike props, state is mutable (via its setter) and triggers a re-render when updated.

| | Props | State |
|---|---|---|
| Mutability | Immutable (read-only) | Mutable via setter |
| Owner | Passed from parent | Owned by the component itself |
| Purpose | Configure a component | Track internal data/UI changes |

### Q14. Why should you never mutate state directly?
React relies on detecting a *new* reference (via `Object.is` comparison) to know that state changed and a re-render is needed. Directly mutating an object or array (`state.push(x)`) keeps the same reference, so React may not detect the change and won't re-render. Always create new objects/arrays: `setState(prev => [...prev, x])`.

### Q15. What is "lifting state up"?
When two or more sibling components need to share the same changing data, the state is moved ("lifted") to their closest common ancestor. The ancestor then passes the state down as props and passes callback functions down to let children update it, keeping data flow consistent and predictable.

### Q16. What is prop drilling, and how can it be avoided?
Prop drilling is passing props through several layers of intermediate components that don't need the data themselves, just to get it to a deeply nested child. It can be avoided using the **Context API**, state management libraries (Redux, Zustand, Jotai), or component composition (passing components as `children`/props instead of data).

### Q17. What are controlled vs uncontrolled components? (also covered under Forms)
See [Q29](#q29-what-is-the-difference-between-a-controlled-and-uncontrolled-component).

### Q18. What is `children` prop?
`props.children` is a special prop that contains whatever is nested between a component's opening and closing tags, allowing components to act as generic wrappers/containers.

```jsx
function Card({ children }) { return <div className="card">{children}</div>; }
<Card><p>Some content</p></Card>
```

### Q19. Can a component return multiple elements?
Yes, by wrapping them in a Fragment (`<>...</>`) or returning an array of elements each with a unique `key`.

### Q20. What is composition, and why does React favor composition over inheritance?
Composition means building complex components by combining simpler ones (via props and `children`) rather than through class inheritance hierarchies. React favors it because UI hierarchies map naturally to component trees, and composition is more flexible — inheritance chains become rigid and hard to reason about as apps grow.

---

## 4. Component Lifecycle

### Q21. What are the three phases of a class component's lifecycle?
1. **Mounting** – component is created and inserted into the DOM: `constructor` → `render` → `componentDidMount`.
2. **Updating** – triggered by new props/state: `render` → `componentDidUpdate`.
3. **Unmounting** – component removed from the DOM: `componentWillUnmount`.

### Q22. What does `componentDidMount` do, and what's its hook equivalent?
It runs once, immediately after the component is first rendered to the DOM — ideal for data fetching, subscriptions, or DOM measurements. Equivalent: `useEffect(() => { ... }, [])` (empty dependency array).

### Q23. What does `componentDidUpdate` do, and what's its hook equivalent?
It runs after every re-render caused by state/prop changes (not the initial render), useful for reacting to specific prop/state changes. Equivalent: `useEffect(() => { ... }, [dep1, dep2])`.

### Q24. What does `componentWillUnmount` do, and what's its hook equivalent?
It runs right before a component is removed from the DOM — used to clean up timers, subscriptions, or event listeners to prevent memory leaks. Equivalent: the **cleanup function** returned from `useEffect`:
```jsx
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id); // cleanup
}, []);
```

### Q25. What is `shouldComponentUpdate`, and its function-component equivalent?
A lifecycle method that lets a class component bail out of re-rendering by returning `false`, used for performance optimization. Functional equivalents: `React.memo()` (shallow prop comparison) and `useMemo`/`useCallback` to avoid unnecessary recalculations.

### Q26. What are `getDerivedStateFromProps` and `getSnapshotBeforeUpdate`?
- `getDerivedStateFromProps(props, state)`: a static method invoked before every render (mount and update) allowing state to be updated based on prop changes. Rarely needed; often replaceable by computing values directly during render.
- `getSnapshotBeforeUpdate(prevProps, prevState)`: called right before DOM changes are applied, letting you capture info (like scroll position) before it's potentially changed, passed as a third argument to `componentDidUpdate`.

### Q27. What are Error Boundaries in relation to the lifecycle?
Special class components implementing `static getDerivedStateFromError()` and/or `componentDidCatch()` to catch JavaScript errors in their child component tree, log them, and display a fallback UI instead of crashing the whole app. See [Section 12](#12-error-handling).

---

## 5. Hooks

### Q28. What are Hooks, and what rules govern their use?
Hooks are functions that let functional components "hook into" state and lifecycle features previously only available in class components. **Rules of Hooks:**
1. Only call hooks at the **top level** — never inside loops, conditions, or nested functions (React relies on consistent call order across renders to associate state correctly).
2. Only call hooks from **React function components** or **custom hooks** — not regular JS functions.

### Q29. Explain `useState`.
Returns a stateful value and a function to update it, preserving the value between re-renders.
```jsx
const [count, setCount] = useState(0);
setCount(c => c + 1); // functional update — safest for updates based on previous state
```
Calling the setter triggers a re-render with the new value (and React batches multiple setState calls within an event handler).

### Q30. Explain `useEffect` and its dependency array.
Runs a side effect after render. The dependency array controls when it re-runs:
- No array → runs after **every** render.
- `[]` → runs **once**, after initial mount.
- `[a, b]` → runs whenever `a` or `b` changes.
- Returning a function from the effect defines **cleanup** logic, run before the next effect execution and on unmount.

### Q31. What is the difference between `useEffect` and `useLayoutEffect`?
`useEffect` runs **asynchronously**, after the browser has painted the screen — it doesn't block visual updates. `useLayoutEffect` runs **synchronously**, immediately after DOM mutations but *before* the browser paints — used when you need to measure/mutate the DOM (e.g., avoid layout flicker) before the user sees it. `useLayoutEffect` should be used sparingly since it can hurt performance by blocking paint.

### Q32. Explain `useContext`.
Lets a component subscribe to a React Context without wrapping it in a `<Context.Consumer>`. Returns the current context value from the nearest matching `<Provider>`. Re-renders the component whenever that context value changes.
```jsx
const theme = useContext(ThemeContext);
```

### Q33. Explain `useReducer`. When would you use it over `useState`?
An alternative to `useState` for managing complex state logic, inspired by Redux's reducer pattern.
```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```
Preferred when: state has multiple sub-values that update together, the next state depends heavily on the previous one, or update logic is complex enough to benefit from being centralized in a pure reducer function rather than scattered across handlers.

### Q34. Explain `useMemo`.
Memoizes the **result of an expensive computation**, recalculating only when a dependency changes. Helps avoid unnecessary recalculation on every render.
```jsx
const sortedList = useMemo(() => expensiveSort(list), [list]);
```

### Q35. Explain `useCallback`.
Memoizes a **function reference** itself (not its result), so a new function isn't created on every render. Primarily useful when passing callbacks to memoized child components (`React.memo`) or as effect dependencies, to prevent unnecessary re-renders/re-runs.
```jsx
const handleClick = useCallback(() => doSomething(id), [id]);
```

### Q36. `useMemo` vs `useCallback` — what's the real difference?
`useCallback(fn, deps)` is essentially `useMemo(() => fn, deps)`. `useMemo` returns a memoized **value**; `useCallback` returns a memoized **function**.

### Q37. Explain `useRef`.
Returns a mutable ref object (`{ current: value }`) that persists across renders **without** causing a re-render when changed. Common uses: accessing DOM nodes directly, and storing mutable values (like timer IDs or previous values) that shouldn't trigger re-renders.

### Q38. What is a custom hook, and why use one?
A custom hook is a JavaScript function whose name starts with `use` that calls other hooks internally, allowing stateful logic to be extracted and reused across multiple components without duplicating code or resorting to HOCs/render props.
```jsx
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);
  useEffect(() => {
    const handler = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handler);
    return () => window.removeEventListener('resize', handler);
  }, []);
  return width;
}
```

### Q39. What are `useImperativeHandle` and `forwardRef` used for?
`forwardRef` lets a parent pass a `ref` down to a child function component so the parent can access an underlying DOM node or imperative method. `useImperativeHandle` customizes the instance value exposed via that ref, letting a child component expose only a specific, controlled API (e.g., a `focus()` method) instead of the raw DOM node — used sparingly, for imperative escape hatches like managing focus or triggering animations.

### Q40. What are `useTransition`, `useDeferredValue`, and `useId`? (React 18)
See [Section 18](#18-react-18-features).

### Q41. What are common `useEffect` pitfalls?
- **Infinite loops**: updating state inside an effect that depends on that same state without proper conditions.
- **Missing dependencies**: omitting a variable used inside the effect from the dependency array causes stale closures (the ESLint `react-hooks/exhaustive-deps` rule helps catch this).
- **Forgetting cleanup**: leads to memory leaks (dangling subscriptions, timers) or "setState on unmounted component" warnings.
- **Overusing `useEffect`** for logic that could just be computed during render (a common React 18+ guidance point).

---

## 6. Virtual DOM & Reconciliation

### Q42. What is the Virtual DOM?
A lightweight, in-memory JavaScript representation of the real DOM. When state changes, React creates a new Virtual DOM tree, compares ("diffs") it against the previous one, and computes the minimal set of real DOM mutations needed, then applies them in a single batch. This avoids costly, repeated direct DOM manipulation.

### Q43. What is reconciliation?
The algorithm React uses to diff two Virtual DOM trees and determine the minimal number of operations needed to update the real DOM to match the new tree. It relies on heuristics (like elements of different types produce different trees, and `key` props identify stable list items) to keep diffing close to O(n) instead of the theoretical O(n³).

### Q44. What is React Fiber?
Fiber is the reconciliation engine introduced in React 16, a complete rewrite of React's core algorithm. It breaks rendering work into small units ("fibers") that can be paused, aborted, reused, or prioritized, enabling features like incremental rendering, concurrent rendering, and better responsiveness (avoiding blocking the main thread on large updates).

### Q45. What are "keys" and why does using array index as a key cause bugs?
Keys tell React which array item each element corresponds to across renders. Using the array index as a key breaks down when items are inserted, removed, or reordered — React may associate the wrong state/DOM node with the wrong item since the "identity" (index) doesn't move with the actual data, causing UI bugs like incorrect input values or animations on the wrong element.

### Q46. Why is direct DOM manipulation discouraged in React?
It bypasses React's Virtual DOM diffing, meaning React's internal representation of the UI becomes out of sync with the actual DOM, which can cause bugs on subsequent React-driven updates. Direct manipulation should only be done through refs, and only for cases React can't handle declaratively (e.g., focus management, integrating a non-React library).

---

## 7. Events & Forms

### Q47. What is the SyntheticEvent system?
React wraps native browser events in a cross-browser wrapper called `SyntheticEvent`, providing a consistent API across all browsers. Since React 17+, events are attached at the root DOM container (not `document`), enabling multiple React versions to coexist on a page and supporting better event delegation.

### Q48. How does event handling differ in React vs vanilla JS?
- Event names are camelCase: `onClick` instead of `onclick`.
- You pass a function reference, not a string: `<button onClick={handleClick}>`.
- You cannot `return false` to prevent default behavior — call `event.preventDefault()` explicitly.

### Q49. What is the difference between a controlled and uncontrolled component?
A **controlled component** has its form value driven entirely by React state — the input's `value` is set from state, and every change updates that state via `onChange`. An **uncontrolled component** keeps its own internal DOM state, and React accesses the current value only when needed via a `ref`.

```jsx
// Controlled
<input value={name} onChange={e => setName(e.target.value)} />

// Uncontrolled
<input defaultValue="Bob" ref={inputRef} />
// later: inputRef.current.value
```
Controlled components give React full control (easy validation, conditional disabling, formatting), while uncontrolled components can be simpler for basic forms or integrating non-React code, trading off real-time reactivity.

### Q50. How do you handle form submission in React?
```jsx
function Form() {
  const [value, setValue] = useState('');
  const handleSubmit = (e) => {
    e.preventDefault();
    // submit logic
  };
  return (
    <form onSubmit={handleSubmit}>
      <input value={value} onChange={e => setValue(e.target.value)} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Q51. How do you optimize event handlers that are passed to many child list items?
Use `useCallback` to memoize the handler (especially combined with `React.memo` on children), or pass the item's ID and use one shared handler with delegation logic, rather than creating a new closure for every list item on every render.

---

## 8. Context API

### Q52. What problem does the Context API solve?
It provides a way to share values (theme, authenticated user, locale, etc.) between components without manually passing props down through every level of the tree ("prop drilling").

### Q53. How do you create and use Context?
```jsx
const ThemeContext = React.createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const theme = useContext(ThemeContext); // 'dark'
  return <div>{theme}</div>;
}
```

### Q54. What are the downsides of Context, and when should you avoid it?
Every component consuming a context re-renders whenever the context value changes, even if it only cares about part of that value — this can cause unnecessary re-renders in large trees. Context is best for low-frequency updates (theme, auth, locale) rather than high-frequency state (like form input on every keystroke) or complex global state logic, where a dedicated state library is more efficient (since it can support selective/partial subscriptions).

### Q55. Context vs Redux — when do you choose one over the other?
Context is built into React and great for simple, infrequently-changing global data. Redux (or similar libraries) provides a centralized store, middleware for side effects, time-travel debugging, and fine-grained subscriptions (components only re-render when the specific slice of state they use changes) — better suited for large apps with complex, frequently updating shared state.

---

## 9. Refs & DOM Access

### Q56. What is `createRef` vs `useRef`?
`useRef` is the hook for function components — the ref object persists across the component's lifetime without being recreated on each render. `React.createRef()` is used in class components — a new ref is created on every render if called inside `render()`, so it's normally created once, in the constructor.

### Q57. How do you access a DOM node directly?
```jsx
function TextInput() {
  const inputRef = useRef(null);
  useEffect(() => { inputRef.current.focus(); }, []);
  return <input ref={inputRef} />;
}
```

### Q58. What are Portals?
`ReactDOM.createPortal(child, domNode)` renders children into a DOM node that exists outside the parent component's DOM hierarchy, while preserving the normal React event bubbling/context behavior as if it were nested normally. Commonly used for modals, tooltips, and dropdowns that need to escape parent `overflow: hidden` or `z-index` constraints.

---

## 10. Performance Optimization

### Q59. What is `React.memo`, and how does it differ from `useMemo`?
`React.memo(Component)` is a higher-order component that memoizes an entire component, skipping re-render if its props are shallowly equal to the previous render's props. `useMemo` memoizes a value *inside* a component. They solve different problems: `memo` prevents an unnecessary re-render of a component; `useMemo` prevents unnecessary recomputation of a value.

### Q60. What causes unnecessary re-renders, and how do you prevent them?
Common causes: parent re-renders causing all children to re-render by default (even with unchanged props), creating new object/array/function literals inline as props (breaking shallow-equality checks), and unoptimized context usage. Mitigations: `React.memo`, `useMemo`/`useCallback` to stabilize references, splitting components/context to isolate frequently-changing state, and using the key prop correctly.

### Q61. What is windowing/list virtualization, and why is it useful?
Rendering only the visible portion of a large list (plus a small buffer) instead of the entire dataset, using libraries like `react-window` or `react-virtualized`. This drastically reduces the number of DOM nodes rendered at once, improving performance for large lists/tables.

### Q62. How can you identify performance bottlenecks in a React app?
- **React DevTools Profiler** — records renders and shows why each component rendered and how long it took.
- **Chrome DevTools Performance tab** — for broader JS/paint/layout analysis.
- The `<React.Profiler>` component API for programmatic measurement.
- `why-did-you-render` library for flagging avoidable re-renders during development.

### Q63. What is memoization, in general, and its trade-off?
Memoization caches the result of a function call so repeated calls with the same inputs skip recomputation. The trade-off is memory usage and the cost of the comparison itself — over-memoizing trivial computations can add overhead greater than the savings, so it should be applied where profiling shows an actual bottleneck, not indiscriminately.

---

## 11. Code Splitting & Suspense

### Q64. What is code splitting, and how do you do it in React?
Splitting the app's bundle into smaller chunks loaded on demand (e.g., per route) instead of one large bundle, reducing initial load time. Achieved via dynamic `import()` combined with `React.lazy()`:
```jsx
const ProfilePage = React.lazy(() => import('./ProfilePage'));

<Suspense fallback={<Spinner />}>
  <ProfilePage />
</Suspense>
```

### Q65. What is `Suspense`?
A component that lets you declaratively show a fallback UI (like a spinner) while its children are "not yet ready" — originally for lazy-loaded components, and expanded in React 18 to support data fetching (with compatible libraries/frameworks like React Query, Relay, or Next.js) and streaming server rendering.

### Q66. What is `React.lazy`'s main limitation?
It only supports **default exports** and only works for **component** imports (not arbitrary modules), and it doesn't work for server-side rendering out of the box without additional handling (frameworks like Next.js handle this differently).

---

## 12. Error Handling

### Q67. What are Error Boundaries?
Class components that implement `static getDerivedStateFromError(error)` (to render a fallback UI) and/or `componentDidCatch(error, info)` (to log the error), catching JavaScript errors anywhere in their child component tree during rendering, in lifecycle methods, and in constructors — preventing the entire app from crashing (a white screen).

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  componentDidCatch(error, info) { logErrorToService(error, info); }
  render() {
    return this.state.hasError ? <FallbackUI /> : this.props.children;
  }
}
```

### Q68. What do Error Boundaries NOT catch?
- Errors inside event handlers (use regular `try/catch` there).
- Asynchronous code (e.g., `setTimeout`, promises).
- Errors during server-side rendering.
- Errors thrown in the error boundary itself.

### Q69. Are there hook-based error boundaries?
No built-in hook exists (as of React 18/19) to implement error-boundary behavior — it must still be a class component, though libraries like `react-error-boundary` provide a convenient wrapper/hook-friendly API around this.

---

## 13. Higher-Order Components & Render Props

### Q70. What is a Higher-Order Component (HOC)?
A function that takes a component and returns a new, enhanced component — a pattern for reusing component logic (e.g., adding authentication checks, injecting data).
```jsx
function withLogging(WrappedComponent) {
  return function EnhancedComponent(props) {
    useEffect(() => { console.log('mounted'); }, []);
    return <WrappedComponent {...props} />;
  };
}
```

### Q71. What is the render props pattern?
A technique where a component accepts a function as a prop (often called `render` or passed as `children`) that returns JSX, letting the component share stateful logic while leaving the actual rendering up to the consumer.
```jsx
<DataFetcher render={data => <List items={data} />} />
```

### Q72. How have Hooks affected the use of HOCs and render props?
Custom hooks now cover most logic-reuse use cases that HOCs and render props were designed for, with a simpler syntax and without the added component nesting ("wrapper hell") or naming collisions those patterns could introduce. HOCs/render props are still occasionally seen in older codebases and some libraries (e.g., `connect()` from Redux).

---

## 14. React Router

### Q73. What is React Router, and why is it needed?
A standard third-party library for handling client-side routing/navigation in single-page React applications, synchronizing the UI with the URL without full page reloads.

### Q74. What are the core components/hooks in React Router (v6+)?
- `<BrowserRouter>` — wraps the app, uses the HTML5 History API.
- `<Routes>` / `<Route path="..." element={<Component />} />` — define route-to-component mapping.
- `<Link>` / `<NavLink>` — declarative, SPA-friendly navigation (vs. a full-reload `<a>` tag).
- `useNavigate()` — programmatic navigation.
- `useParams()` — access dynamic URL segments (e.g., `/users/:id`).
- `useSearchParams()` — access/update query string parameters.
- `<Outlet>` — renders matched child routes for nested routing layouts.

### Q75. How do you implement protected/private routes?
Wrap the route element in a component that checks authentication state and either renders the child or redirects using `<Navigate to="/login" />`.
```jsx
function ProtectedRoute({ children }) {
  const { user } = useAuth();
  return user ? children : <Navigate to="/login" />;
}
```

---

## 15. State Management (Redux & Alternatives)

### Q76. What are the core principles of Redux?
1. **Single source of truth** — the whole app state lives in one store.
2. **State is read-only** — the only way to change it is by dispatching an **action** (a plain object describing what happened).
3. **Changes are made with pure functions** — **reducers** take the current state and an action and return a new state, without mutating the original.

### Q77. What is Redux Toolkit (RTK), and why is it now recommended over "classic" Redux?
RTK is the official, opinionated toolset for Redux that simplifies store setup (`configureStore`), reduces boilerplate (`createSlice` auto-generates action creators/types), includes Immer internally (letting you write "mutating" logic that's actually converted to immutable updates safely), and integrates data-fetching via `createAsyncThunk`/RTK Query. It's the recommended standard because classic Redux required extensive manual boilerplate (action types, action creators, combineReducers, manual immutability).

### Q78. What is middleware in Redux, and what is `redux-thunk` used for?
Middleware intercepts actions between being dispatched and reaching the reducer, useful for logging, crash reporting, or async logic. `redux-thunk` allows action creators to return a function (instead of a plain object) that can perform async work (like an API call) and dispatch further actions once it resolves — necessary because reducers must remain pure and synchronous.

### Q79. What are common alternatives to Redux, and how do they compare?
- **Context + useReducer** — built-in, fine for small-to-medium apps, but lacks selective subscriptions/middleware.
- **Zustand** — minimal, hook-based API, less boilerplate, no provider requirement.
- **Jotai/Recoil** — atomic state model, fine-grained reactivity, good for avoiding unnecessary re-renders.
- **MobX** — observable-based, more "magic"/implicit reactivity compared to Redux's explicit unidirectional flow.
- **React Query / TanStack Query / SWR** — specialized for **server state** (caching, refetching, syncing async data) rather than general client state; often paired with a separate client-state solution.

### Q80. What's the distinction between "client state" and "server state," and why does it matter?
Client state is UI-only data owned by the app (form inputs, toggles, modal visibility). Server state is data that actually lives on a server and is just cached on the client (user records, API results) — it can go stale, needs refetching/invalidation, and involves async loading/error states. Treating server state like client state (e.g., manually caching API responses in Redux) tends to reinvent caching, deduplication, and invalidation logic that libraries like React Query already solve.

---

## 16. Testing

### Q81. What tools are commonly used to test React components?
- **Jest** (or Vitest) — test runner/assertion library.
- **React Testing Library (RTL)** — renders components and queries the DOM the way a user would (by role, text, label) rather than testing implementation details.
- **Cypress / Playwright** — end-to-end testing across a real browser.

### Q82. What is the guiding philosophy of React Testing Library?
"The more your tests resemble the way your software is used, the more confidence they can give you." Tests should interact with the rendered output (buttons, text, labels) rather than a component's internal state or private methods, making tests resilient to refactors that don't change user-facing behavior.

### Q83. Example: testing a simple counter component.
```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('increments count on button click', () => {
  render(<Counter />);
  const button = screen.getByRole('button', { name: /increment/i });
  fireEvent.click(button);
  expect(screen.getByText('Count: 1')).toBeInTheDocument();
});
```

### Q84. How do you test asynchronous behavior (e.g., data fetching)?
Use `findBy*` queries (which return a promise and retry until found) or `waitFor()` to wait for UI updates after an async operation resolves, and mock network requests with tools like `msw` (Mock Service Worker) rather than hitting real APIs in tests.

---

## 17. Server-Side Rendering & Next.js

### Q85. What is Server-Side Rendering (SSR), and why use it?
Rendering React components to HTML on the server for the initial request (via `ReactDOMServer.renderToString`/`renderToPipeableStream`), then "hydrating" that HTML on the client to attach event listeners and make it interactive. Benefits: faster perceived load (content visible before JS loads), and better SEO since crawlers see fully-rendered content.

### Q86. What is hydration?
The process where React attaches event listeners and internal state to server-rendered HTML on the client, rather than re-rendering everything from scratch — it reconciles the existing DOM with what React would have rendered, assuming they match.

### Q87. What is a "hydration mismatch," and what causes it?
An error/warning that occurs when the server-rendered HTML doesn't match what the client renders on first render — commonly caused by using browser-only APIs (`window`, `Date.now()`, `Math.random()`) during render, or environment-dependent content, without guarding it to run only after mount.

### Q88. SSR vs. Static Site Generation (SSG) vs. Client-Side Rendering (CSR)?
- **CSR**: browser downloads a near-empty HTML shell and JS bundle, then renders everything client-side (traditional SPA).
- **SSR**: HTML generated per-request on the server — good for dynamic, frequently-changing, personalized content.
- **SSG**: HTML generated at **build time** and served as static files (e.g., via a CDN) — fastest, but content is fixed until the next build/rebuild (or on-demand/incremental regeneration in some frameworks).

### Q89. What role does Next.js (or similar frameworks) play?
Next.js is a React framework that provides file-based routing, built-in SSR/SSG/ISR (Incremental Static Regeneration), API routes, image optimization, and other production concerns (bundling, code splitting) out of the box, since React itself only handles the view layer.

---

## 18. React 18+ Features

### Q90. What is Concurrent Rendering in React 18?
A set of new internal capabilities that let React prepare multiple versions of the UI at once and interrupt, pause, resume, or abandon a render in progress — for example, to prioritize an urgent update (like text input) over a less urgent one (like re-rendering a large list), improving perceived responsiveness. It's opt-in via new APIs, not a breaking behavior change by itself.

### Q91. What is `createRoot`, and how does it differ from the old `ReactDOM.render`?
`ReactDOM.createRoot(container).render(<App />)` is the React 18 API that enables concurrent features. The legacy `ReactDOM.render()` API still works in React 18 but opts the app out of concurrent rendering, running in the older, synchronous-only mode.

### Q92. What is automatic batching in React 18?
Prior to React 18, state updates were only batched (grouped into a single re-render) inside React event handlers. React 18 extends batching to updates that happen inside promises, `setTimeout`, native event handlers, and other non-React contexts, reducing the number of re-renders and improving performance by default.

### Q93. What is `useTransition`?
A hook that lets you mark certain state updates as **non-urgent ("transitions")**, so React can keep the UI responsive (e.g., keep an input field responsive) while a more expensive update (like filtering a large list) happens in the background.
```jsx
const [isPending, startTransition] = useTransition();
startTransition(() => setFilteredList(computeFilteredList(query)));
```

### Q94. What is `useDeferredValue`?
Lets you defer re-rendering a non-urgent part of the UI until more urgent updates finish, by giving you a "lagging" version of a value that updates after the more urgent render completes. Useful when you don't control the state update itself (e.g., a value from props) but want to defer its downstream rendering effect.
```jsx
const deferredQuery = useDeferredValue(query);
```

### Q95. What is `useId`?
Generates a unique, stable ID string useful for accessibility attributes (linking a `<label>` to an `<input>` via `htmlFor`/`id`), designed specifically to work correctly with server-side rendering, avoiding ID mismatches between server and client.

### Q96. What is Strict Mode, and why does it double-invoke certain functions?
`<React.StrictMode>` is a development-only tool that helps surface potential problems: it double-invokes component render functions, effects, and certain lifecycle methods in development to help detect side effects that aren't properly cleaned up or aren't idempotent (since concurrent rendering may need to render a component more than once). It doesn't affect production builds.

### Q97. What is the (experimental/evolving) React Compiler?
A build-time tool (introduced as part of ongoing React evolution beyond 18) that automatically analyzes component code and inserts memoization (similar to manually applying `useMemo`/`useCallback`/`React.memo`) so developers don't need to hand-optimize re-renders as often. It's part of a broader shift toward reducing the manual performance-tuning burden.

---

## 19. TypeScript with React

### Q98. How do you type a functional component's props?
```tsx
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({ label, onClick, disabled }) => (
  <button onClick={onClick} disabled={disabled}>{label}</button>
);
```
(Note: many style guides now prefer typing props directly as a function argument rather than using `React.FC`, since `React.FC` implicitly adds a `children` prop and has other minor drawbacks.)

### Q99. How do you type `useState` and `useRef`?
```tsx
const [count, setCount] = useState<number>(0);
const inputRef = useRef<HTMLInputElement>(null);
```

### Q100. What's the difference between `interface` and `type` for props, practically speaking?
Both work for defining prop shapes. `interface` supports declaration merging (useful for extending third-party types) and is generally preferred by convention for object/props shapes; `type` is more flexible for unions, intersections, and mapped types. Either is acceptable; consistency within a codebase matters more than which one is chosen.

---

## 20. Miscellaneous & Best Practices

### Q101. What is PropTypes, and is it still relevant?
`prop-types` is a runtime type-checking library for validating props passed to components, primarily used in plain JavaScript codebases without TypeScript. It's largely been superseded by TypeScript for compile-time safety, though it's still seen in legacy JS-only codebases.

### Q102. What is the difference between `React.PureComponent` and `React.Component`?
`PureComponent` automatically implements `shouldComponentUpdate` with a shallow comparison of props and state, skipping re-renders when nothing shallowly changed — the class-component equivalent of wrapping a functional component in `React.memo`.

### Q103. Why shouldn't you use array index as a `key`, and when is it actually acceptable?
As covered in Q45, index keys can cause bugs when list order changes. It's acceptable when the list is static, never reordered/filtered/inserted into, and items have no state tied to identity.

### Q104. What is "prop types drilling" vs Context vs composition — how do you choose?
For 1–2 levels of passing, plain props are simplest. For deeply nested, infrequently changing data, Context is appropriate. For structural reuse (e.g., wrapping arbitrary content), composition via `children` avoids the problem entirely by restructuring the component tree instead of passing data through it.

### Q105. What is the difference between `null`, `undefined`, and `false` when returned from a component's render?
All three render nothing to the DOM in JSX — this is commonly used for conditional rendering: `{condition && <Component />}` renders nothing if `condition` is falsy. Returning `0`, however, *will* render the literal `0` to the screen — a common bug source people should watch for (`{count && <Badge/>}` renders `0` when count is 0; prefer `{count > 0 && <Badge/>}` or `{Boolean(count) && ...}`).

### Q106. What is the significance of the `dangerouslySetInnerHTML` prop?
It's React's equivalent of the DOM's `innerHTML`, used to inject raw HTML strings directly into the DOM. Named deliberately to be alarming — it bypasses React's automatic escaping and can expose the app to **XSS (cross-site scripting)** attacks if the HTML source isn't trusted/sanitized.
```jsx
<div dangerouslySetInnerHTML={{ __html: sanitizedHtml }} />
```

### Q107. What is the difference between imperative and declarative programming, in the context of React?
Imperative code specifies *how* to do something step by step (e.g., manually querying and mutating DOM nodes). Declarative code specifies *what* the result should look like for a given state, letting the underlying system (React) figure out the how. React encourages a declarative mental model: describe the UI as a function of state, and let React handle updates.

### Q108. What are some common React anti-patterns to avoid?
- Mutating state directly instead of creating new references.
- Using index as key in dynamic lists.
- Overusing `useEffect` for logic that could be derived during render.
- Deeply nested prop drilling instead of composition/context.
- Defining components inside other components (causes remounting and state loss on every parent render, since a "new" component type is created each time).
- Fetching data without handling loading/error states or race conditions (e.g., not canceling stale requests).
- Ignoring accessibility (missing semantic HTML, ARIA attributes, keyboard navigation).

### Q109. How do you prevent memory leaks in React?
- Clean up subscriptions, timers, and event listeners in `useEffect`'s cleanup function.
- Cancel in-flight network requests (via `AbortController`) when a component unmounts before the request resolves.
- Avoid calling `setState` after unmount (React 18 no longer warns about this the same way, but the underlying race condition should still be handled properly, typically via cleanup/cancellation).

### Q110. What is the difference between `React.StrictMode` and production error boundaries — should Strict Mode be used in production?
No — Strict Mode is purely a development aid; it has no effect in production builds and adds no runtime overhead there. Error boundaries, by contrast, are a production-relevant pattern for gracefully handling runtime errors that could otherwise crash the app.

---

*This guide covers the core React interview surface area. For role-specific deep dives (e.g., animation libraries, GraphQL integration with Apollo/Relay, micro-frontends, or specific meta-framework internals like Next.js App Router/React Server Components), further topic-specific preparation is recommended.*
