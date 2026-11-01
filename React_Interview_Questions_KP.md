# React Interview Questions – Java Full Stack Developer (Kaiser Permanente GCC, Pune)

Tailored to the JD: React.js, functional components, props, state, Hooks, ES6+, Promises, async/await, Fetch/Axios, state management, performance optimization, HTML5/CSS3, responsive UI.

---

## 1. React Fundamentals

**1. What is the Virtual DOM and how does reconciliation work?**
React keeps an in-memory tree, diffs it against the previous one on each render, and applies only the minimal changes to the real DOM.

**2. Why do lists need a `key` prop? Why is using the array index risky?**
Keys help React match items between renders. Index keys break when items are reordered, inserted, or deleted, which causes wrong state and extra re-renders.

**3. What's the difference between props and state?**
Props are read-only inputs passed from a parent. State is owned by the component, and changing it triggers a re-render.

**4. Controlled vs uncontrolled components?**
A controlled input's value comes from React state. An uncontrolled input keeps its own value in the DOM, which you read with a ref.

```jsx
// Controlled
const [name, setName] = useState("");
<input value={name} onChange={e => setName(e.target.value)} />

// Uncontrolled
const inputRef = useRef();
<input ref={inputRef} />   // read with inputRef.current.value
```

**5. What is JSX, and what does it compile to?**
It's syntax sugar for `React.createElement` (or the `jsx()` runtime) calls.

**6. What are React Fragments and why use them?**
They group children without adding an extra DOM node: `<>...</>`.

---

## 2. Hooks (heavily emphasized in the JD)

**7. Explain `useState`. Why use the functional update form?**
`setCount(c => c + 1)` avoids stale values when updates are batched or several updates happen in a row.

**8. Explain `useEffect` and its dependency array.**
- No array: runs after every render
- `[]`: runs once on mount
- `[deps]`: runs when those values change
- Returned function: cleanup (runs before the next effect and on unmount)

**9. How do you avoid memory leaks or race conditions when fetching in `useEffect`?**
Return a cleanup function that uses `AbortController` or an `ignore` flag.

```jsx
useEffect(() => {
  const controller = new AbortController();
  fetch(url, { signal: controller.signal })
    .then(res => res.json())
    .then(setData)
    .catch(err => { if (err.name !== "AbortError") setError(err); });
  return () => controller.abort();
}, [url]);
```

**10. `useMemo` vs `useCallback` vs `React.memo`?**
- `useMemo` caches a computed value
- `useCallback` caches a function reference
- `React.memo` skips re-rendering a component when its props haven't changed

**11. What is `useRef` used for besides DOM access?**
It holds mutable values that persist across renders without triggering a re-render, such as timer IDs or previous values.

**12. What are the Rules of Hooks, and why do they exist?**
Only call hooks at the top level and only from React functions or custom hooks, because React tracks hooks by call order.

**13. Write a custom hook `useFetch(url)` returning `{ data, loading, error }`.**

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let ignore = false;
    setLoading(true);
    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(json => { if (!ignore) setData(json); })
      .catch(err => { if (!ignore) setError(err); })
      .finally(() => { if (!ignore) setLoading(false); });
    return () => { ignore = true; };
  }, [url]);

  return { data, loading, error };
}
```

**14. When would you use `useReducer` over `useState`?**
For complex state with multiple sub-values, or when the next state depends on the action type.

**15. What is `useContext`, and what's its performance downside?**
It reads a value from the nearest Provider. Every consumer re-renders when the context value changes.

---

## 3. API Integration (Fetch/Axios, async/await)

**16. Fetch vs Axios?**
Axios parses JSON automatically, rejects on HTTP error statuses, and supports interceptors and timeouts. Fetch is native but only rejects on network failure, so you must check `response.ok` yourself.

**17. How would you attach a JWT to every request?**

```js
axios.interceptors.request.use(config => {
  const token = getToken();
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

**18. How do you handle loading, error, and empty states for an API call?**
Track them as separate state values and render conditionally.

**19. How do you call multiple APIs in parallel?**
Use `Promise.all`, or `Promise.allSettled` if partial failures are acceptable.

```js
const [users, orders] = await Promise.all([
  axios.get("/api/users"),
  axios.get("/api/orders"),
]);
```

**20. How would you handle a 401 globally?**
Use a response interceptor that refreshes the token or redirects to login.

**21. What causes CORS errors, and whose side fixes them?**
The browser blocks cross-origin responses. The fix belongs on the server, for example `@CrossOrigin` or a `CorsConfigurationSource` bean in Spring Boot.

---

## 4. State Management

**22. What is prop drilling, and how do you avoid it?**
Passing props through many layers that don't use them. Avoid it with Context, component composition, or a state library.

**23. Context API vs Redux (Redux Toolkit)?**
Context works well for low-frequency global data like theme or auth. Redux suits large, frequently changing shared state and offers devtools and middleware.

**24. Explain the Redux flow.**
Action → reducer → store → UI. In Redux Toolkit, `createSlice` and `createAsyncThunk` handle the boilerplate.

**25. Where should server data live versus UI state?**
Many teams now keep server data in React Query (TanStack Query) and UI-only state locally.

---

## 5. Performance Optimization

**26. What causes unnecessary re-renders, and how do you find them?**
New object or function references in props, and context changes. Use the React DevTools Profiler to spot them.

**27. What is code splitting?**
`React.lazy` with `Suspense` loads parts of the app on demand. Route-based splitting is the usual starting point.

```jsx
const Reports = React.lazy(() => import("./Reports"));
<Suspense fallback={<Spinner />}><Reports /></Suspense>
```

**28. How do you render a list of 10,000 rows efficiently?**
Use virtualization (react-window) or pagination.

**29. What is debouncing, and where would you use it?**
It delays a call until input pauses, useful for search boxes that hit an API.

---

## 6. Routing, Forms, and Security

**30. How do protected routes work in React Router v6?**

```jsx
function ProtectedRoute({ children }) {
  const { user } = useAuth();
  return user ? children : <Navigate to="/login" replace />;
}
```

**31. How do you handle form validation?**
By hand, or with React Hook Form plus Yup or Zod.

**32. Where should you store a JWT: localStorage or httpOnly cookie?**
localStorage is exposed to XSS. httpOnly cookies are safer but need CSRF protection. Interviewers mainly want to hear the tradeoff.

**33. How does React protect against XSS, and when doesn't it?**
JSX escapes values automatically. `dangerouslySetInnerHTML` bypasses that protection.

---

## 7. Testing

**34. How do you test a React component?**
Jest with React Testing Library. Test behavior the way a user sees it, not implementation details.

**35. How do you mock an API call in a test?**
`jest.mock('axios')` or MSW (Mock Service Worker).

---

## 8. Responsive UI (HTML5/CSS3)

**36. Flexbox vs Grid: when to use each?**
Flexbox for one-dimensional layouts, Grid for two-dimensional ones.

**37. How do you make a component responsive?**
Media queries, relative units (`rem`, `%`, `vw`), and a mobile-first approach.

---

## 9. Scenario and Coding Questions (likely in KP Pune and US rounds)

**38. Todo list with add, delete, and toggle.**

```jsx
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [text, setText] = useState("");

  const add = () => {
    if (!text.trim()) return;
    setTodos(t => [...t, { id: Date.now(), text, done: false }]);
    setText("");
  };
  const toggle = id => setTodos(t => t.map(x => x.id === id ? { ...x, done: !x.done } : x));
  const remove = id => setTodos(t => t.filter(x => x.id !== id));

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={add}>Add</button>
      <ul>
        {todos.map(t => (
          <li key={t.id}>
            <span onClick={() => toggle(t.id)}
                  style={{ textDecoration: t.done ? "line-through" : "none" }}>
              {t.text}
            </span>
            <button onClick={() => remove(t.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

**39. Search box with debounce that fetches results from an API.**

```jsx
function Search() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query) { setResults([]); return; }
    const id = setTimeout(async () => {
      const res = await axios.get(`/api/search?q=${encodeURIComponent(query)}`);
      setResults(res.data);
    }, 400);
    return () => clearTimeout(id);
  }, [query]);

  return (
    <>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <ul>{results.map(r => <li key={r.id}>{r.name}</li>)}</ul>
    </>
  );
}
```

**40. Fetch users and show them in a paginated table with loading and error states.**
Combine `useFetch` with `page` state, pass `?page=&size=` to the backend (Spring Data `Pageable`), and render Prev/Next buttons.

**41. Counter that increments every second and stops correctly on unmount.**

```jsx
function Timer() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => setCount(c => c + 1), 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>{count}</h1>;
}
```

**42. Predict the output.**

```jsx
const [count, setCount] = useState(0);
const handleClick = () => {
  setCount(count + 1);
  console.log(count);
};
```
It logs the old value (`0`), because state updates are asynchronous and batched. Calling `setCount(count + 1)` three times still gives `1`; `setCount(c => c + 1)` three times gives `3`.

**43. Full-stack design: connect a React form to a Spring Boot REST endpoint.**
- Frontend: controlled form, client-side validation (React Hook Form + Yup), Axios POST, show field errors from the response
- Backend: `@PostMapping` + `@Valid @RequestBody DTO`, Bean Validation annotations (`@NotBlank`, `@Email`), `@RestControllerAdvice` returning a consistent error JSON (e.g. 400 with field → message map)
- Map backend field errors back to the form fields in React

---

## Interview Tips

- The US round is usually more conversational. Expect "tell me about a performance issue you fixed in React" or "how did you structure state in your last project."
- Prepare one or two real stories from your work with concrete numbers (load time reduced, re-renders cut, bugs fixed).
- Be ready to explain how your React app talks to your Spring Boot microservices end to end: auth, API gateway, error handling.
