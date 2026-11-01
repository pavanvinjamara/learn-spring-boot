# React Interview Q&A – 3 Years Experience Level
### Java Full Stack Developer – Kaiser Permanente GCC, Pune

These answers are written the way a developer with about 3 years of hands-on experience would speak in an interview: clear definition first, then a practical example from project work. Replace the project examples with your own real ones before the interview.

---

## Section 1: React Fundamentals

### Q1. What is React, and why did your team use it?

**Answer:**
React is a JavaScript library for building user interfaces using reusable components. In my projects we used it because it lets us break the UI into small, independent components like a header, a data table, or a search form, and reuse them across screens. Its Virtual DOM makes updates efficient, and the ecosystem is strong. We have React Router for navigation, Axios for API calls, and testing tools like Jest and React Testing Library. Since our backend was Spring Boot microservices exposing REST APIs, React worked very well as a separate frontend that just consumes those APIs.

---

### Q2. What is the Virtual DOM, and how does reconciliation work?

**Answer:**
The Virtual DOM is a lightweight JavaScript copy of the real DOM. When state or props change, React creates a new Virtual DOM tree and compares it with the previous one. This comparison is called reconciliation, or diffing. React then finds the minimum set of changes and updates only those parts of the real DOM.

This matters because direct DOM manipulation is expensive. For example, in a table of 500 rows, if one row's status changes, React updates only that row instead of re-rendering the whole table in the browser.

---

### Q3. Why do we need the `key` prop in lists? Can we use the index as a key?

**Answer:**
Keys help React identify which items were added, removed, or changed during reconciliation. Without stable keys, React may re-render the wrong items or lose component state.

Using the index as a key is fine only for static lists that never change order. In one of my projects, we had a list of editable rows, and using the index caused a bug: when we deleted a row, the input values shifted to the wrong rows. We fixed it by using the unique `id` from the database as the key. Since our data came from Spring Boot APIs, every record already had a primary key, so that was the natural choice.

---

### Q4. What is the difference between props and state?

**Answer:**
Props are inputs passed from a parent to a child component. They're read-only, so the child should not modify them. State is data owned and managed inside the component, and when state changes, the component re-renders.

For example, in a patient list page, the parent component fetches the patient list and holds it in state. It passes each patient object as a prop to a `PatientCard` component. The card just displays it. If the card needs to show an "expanded" view, that toggle is the card's own local state.

---

### Q5. What is the difference between controlled and uncontrolled components?

**Answer:**
In a controlled component, the form input's value is stored in React state and updated via `onChange`. React is the single source of truth. In an uncontrolled component, the DOM keeps the value, and we read it with a `ref` when needed.

```jsx
// Controlled
const [email, setEmail] = useState("");
<input value={email} onChange={(e) => setEmail(e.target.value)} />

// Uncontrolled
const emailRef = useRef();
<input ref={emailRef} />
```

I mostly use controlled components because they make validation, conditional enabling of buttons, and resetting forms easy. For large forms, I've used React Hook Form, which internally uses uncontrolled inputs for better performance.

---

### Q6. What are functional components vs class components? Which do you use?

**Answer:**
Class components extend `React.Component` and use lifecycle methods like `componentDidMount` and `this.state`. Functional components are plain JavaScript functions, and since React 16.8 they can use Hooks for state and side effects.

In my 3 years, I've worked almost entirely with functional components and Hooks. They are shorter, easier to test, and logic can be reused through custom hooks. I have seen some class components in legacy code, and I understand the lifecycle mapping: `componentDidMount` maps to `useEffect` with an empty array, and `componentWillUnmount` maps to the cleanup function in `useEffect`.

---

### Q7. What are React Fragments?

**Answer:**
Fragments let us group multiple elements without adding an extra `div` to the DOM. We write them as `<>...</>` or `<React.Fragment>`. I use them commonly when returning table cells or list items, because an extra `div` inside a `<tr>` would break the HTML structure. The longer form is needed when you want to pass a `key`, for example when mapping over a list.

---

## Section 2: Hooks

### Q8. Explain `useState`. When do you use the functional update form?

**Answer:**
`useState` adds state to a functional component. It returns the current value and a setter function.

When the new state depends on the previous state, I use the functional form:

```jsx
setCount(prev => prev + 1);
```

This is important because state updates are asynchronous and batched. If I call `setCount(count + 1)` three times in the same handler, the result is only +1, because all three calls read the same old value. With the functional form, each update gets the latest value. I also use this pattern when updating arrays, like `setItems(prev => [...prev, newItem])`.

---

### Q9. Explain `useEffect` and its dependency array.

**Answer:**
`useEffect` is used for side effects such as API calls, subscriptions, timers, or manually changing the document title.

- With no dependency array, it runs after every render.
- With an empty array `[]`, it runs only once after the first render. I use this for initial data loading.
- With dependencies like `[userId]`, it runs whenever `userId` changes.
- The function returned from the effect is the cleanup. It runs before the next effect and when the component unmounts.

For example, on a details page, I fetch data inside `useEffect` with `[id]` as the dependency, so when the user navigates from one record to another, the data reloads automatically.

---

### Q10. How do you prevent memory leaks or race conditions in API calls?

**Answer:**
A common issue is when a component unmounts before the API response arrives, or when the user changes a filter quickly and an older response arrives after a newer one. I handle this with `AbortController` in the cleanup:

```jsx
useEffect(() => {
  const controller = new AbortController();
  axios.get(`/api/patients/${id}`, { signal: controller.signal })
    .then(res => setPatient(res.data))
    .catch(err => {
      if (!axios.isCancel(err)) setError(err.message);
    });
  return () => controller.abort();
}, [id]);
```

I faced this exact race condition on a search screen. Results were sometimes showing for the previous search term. Adding abort on cleanup fixed it.

---

### Q11. What is the difference between `useMemo`, `useCallback`, and `React.memo`?

**Answer:**
- `useMemo` caches the result of an expensive calculation and recalculates only when dependencies change. For example, filtering and sorting a large list.
- `useCallback` caches a function reference so it isn't recreated on every render.
- `React.memo` is a higher-order component that skips re-rendering a child if its props haven't changed.

They usually work together. If I wrap a child in `React.memo` but pass it an inline function, the child still re-renders because the function is a new reference every time. So I wrap that function in `useCallback`.

That said, I don't use them everywhere. Memoization has its own cost. I use them when I see an actual performance issue in the React DevTools Profiler, like a large table re-rendering on every keystroke.

---

### Q12. What is `useRef` used for?

**Answer:**
`useRef` has two main uses. First, to access DOM elements directly, such as focusing an input on page load or scrolling to an element. Second, to store a mutable value that persists across renders without causing a re-render, such as a timer ID, a previous value, or a flag.

```jsx
const inputRef = useRef(null);
useEffect(() => { inputRef.current.focus(); }, []);
```

The key difference from state is that changing `ref.current` does not trigger a re-render.

---

### Q13. What are the Rules of Hooks?

**Answer:**
There are two rules. Call hooks only at the top level, meaning not inside loops, conditions, or nested functions. And call hooks only from React function components or custom hooks.

React relies on the order of hook calls to know which state belongs to which hook. If a hook is inside an `if` condition, the order can change between renders, and the state gets mixed up. We have the ESLint plugin `eslint-plugin-react-hooks` in our project, which catches these issues and also warns about missing dependencies in `useEffect`.

---

### Q14. Have you written custom hooks? Give an example.

**Answer:**
Yes. A custom hook is a function starting with `use` that reuses stateful logic across components. In my project, many pages were fetching data with the same loading and error handling, so I created a `useFetch` hook:

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);
    axios.get(url, { signal: controller.signal })
      .then(res => setData(res.data))
      .catch(err => { if (!axios.isCancel(err)) setError(err.message); })
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
```

This removed a lot of duplicate code. I've also written `useDebounce` for search inputs and `useAuth` to read the logged-in user from context.

---

### Q15. When would you use `useReducer` instead of `useState`?

**Answer:**
I use `useReducer` when state is complex, with multiple related values, or when the next state depends on the type of action. For example, a multi-step form or a shopping cart where we have add, remove, update quantity, and clear actions. Having all the update logic in one reducer function makes it easier to read and test. For simple values like a toggle or an input value, `useState` is enough.

---

### Q16. What is the Context API, and what is its drawback?

**Answer:**
Context lets us share data across the component tree without passing props manually through every level. We create a context, wrap the tree in a Provider, and read the value with `useContext`.

I've used it for the logged-in user, auth token, theme, and language settings. The drawback is that whenever the Provider's value changes, every component consuming that context re-renders. So I avoid putting frequently changing data in context, and I split contexts by concern, for example a separate AuthContext and ThemeContext, instead of one big global context. I also wrap the value object in `useMemo` so it doesn't create a new reference on every render.

---

## Section 3: API Integration

### Q17. Fetch vs Axios: which do you prefer?

**Answer:**
I prefer Axios in projects. It automatically parses JSON, throws errors for 4xx and 5xx status codes, supports timeouts, and most importantly provides interceptors. With Fetch, I have to call `res.json()` manually and check `res.ok`, because Fetch only rejects on network failure, not on a 404 or 500.

That said, Fetch is built into the browser, so for a small app or a quick task it's perfectly fine.

---

### Q18. How do you send the JWT token with every API request?

**Answer:**
I create a central Axios instance and add a request interceptor:

```js
const api = axios.create({ baseURL: process.env.REACT_APP_API_URL });

api.interceptors.request.use(config => {
  const token = sessionStorage.getItem("token");
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

On the Spring Boot side, a JWT filter in Spring Security validates this token. I also add a response interceptor: if any API returns 401, it clears the session and redirects to the login page, so I don't have to handle it in every component.

---

### Q19. How do you handle loading, error, and empty states?

**Answer:**
I keep separate state for each case and render conditionally:

```jsx
if (loading) return <Spinner />;
if (error) return <ErrorMessage message={error} />;
if (!data?.length) return <p>No records found.</p>;
return <PatientTable rows={data} />;
```

From a user experience point of view, showing a proper empty state and a meaningful error message is important. On the backend, we used `@RestControllerAdvice` to return consistent error JSON, so the frontend could display the message from the response directly.

---

### Q20. How do you call multiple APIs at the same time?

**Answer:**
If the APIs are independent, I call them in parallel with `Promise.all`, which is faster than calling them one after another:

```js
const [usersRes, rolesRes] = await Promise.all([
  api.get("/users"),
  api.get("/roles"),
]);
```

If one failure shouldn't break the whole page, I use `Promise.allSettled`, which returns the result of each promise separately. For example, on a dashboard, if one widget's API fails, the other widgets should still load.

---

### Q21. Have you faced CORS issues? How did you fix them?

**Answer:**
Yes, it's very common during local development, when React runs on port 3000 and Spring Boot runs on 8080. The browser blocks the response because the origins are different.

CORS is fixed on the server side. In Spring Boot, I configured it globally with a `CorsConfigurationSource` bean, or at controller level with `@CrossOrigin`, allowing only the specific frontend origin. With Spring Security, CORS also has to be enabled in the security filter chain. For local development, another option is the `proxy` setting in React's `package.json` or the Vite dev server proxy. In production, both are usually served behind an API gateway or the same domain, so CORS isn't an issue.

---

## Section 4: State Management

### Q22. What is prop drilling, and how do you avoid it?

**Answer:**
Prop drilling is passing data through multiple layers of components that don't need it, just to reach a deeply nested child. It makes code hard to maintain.

I avoid it using Context API for global data like the user or theme, component composition (passing components as children), or Redux for larger shared state.

---

### Q23. Context API vs Redux: when do you use which?

**Answer:**
I use Context for simple global data that doesn't change often, like the authenticated user, theme, or language.

I use Redux, specifically Redux Toolkit, when the application has a lot of shared state that changes frequently and is used across many screens, or when I need features like Redux DevTools for debugging, middleware, and predictable state updates. In one project, we used Redux Toolkit with `createSlice` and `createAsyncThunk` for API calls, which reduced a lot of boilerplate compared to old Redux.

---

### Q24. Explain the Redux flow.

**Answer:**
The UI dispatches an action, which is a plain object with a `type` and optional `payload`. The reducer, which is a pure function, takes the current state and the action and returns new state. The store holds this state, and components subscribed with `useSelector` re-render when the data they use changes.

With Redux Toolkit:

```js
const cartSlice = createSlice({
  name: "cart",
  initialState: [],
  reducers: {
    addItem: (state, action) => { state.push(action.payload); },
    removeItem: (state, action) => state.filter(i => i.id !== action.payload),
  },
});
```

Redux Toolkit uses Immer internally, so we can write code that looks like mutation, and it's converted to immutable updates.

---

## Section 5: Performance Optimization

### Q25. How do you identify and fix performance issues in React?

**Answer:**
First I measure. I use the React DevTools Profiler to see which components re-render and how long they take, and Chrome's Lighthouse or Performance tab for page load.

Common fixes I've applied:
- `React.memo`, `useMemo`, and `useCallback` to avoid unnecessary re-renders
- Lazy loading routes with `React.lazy` and `Suspense` to reduce bundle size
- Pagination or virtualization for large lists
- Debouncing search inputs to reduce API calls
- Keeping state as local as possible, so a change doesn't re-render the whole page

In one project, a page with a large table was lagging on every keystroke in the filter box. The profiler showed the whole table re-rendering. I memoized the filtered data with `useMemo`, wrapped the row component in `React.memo`, and debounced the filter input. The lag went away.

---

### Q26. What is code splitting and lazy loading?

**Answer:**
By default, the bundler puts all code into one big JavaScript file, so the user downloads everything on the first load. Code splitting breaks it into smaller chunks loaded on demand.

```jsx
const Reports = React.lazy(() => import("./pages/Reports"));

<Suspense fallback={<Spinner />}>
  <Routes>
    <Route path="/reports" element={<Reports />} />
  </Routes>
</Suspense>
```

I usually apply it at route level, so heavy pages like reports or admin screens load only when the user visits them. This noticeably improved our initial load time.

---

### Q27. How do you display a list of 10,000 records?

**Answer:**
Rendering 10,000 DOM rows at once makes the page slow. I'd use one of two approaches. The preferred one is server-side pagination: the Spring Boot API accepts `page` and `size` parameters using Spring Data's `Pageable`, and the UI requests one page at a time. This also reduces network payload and database load. If the business needs an infinite scroll or all data on the client, I use virtualization with a library like `react-window`, which renders only the rows visible on screen.

---

### Q28. What is debouncing? Where have you used it?

**Answer:**
Debouncing delays a function call until the user stops triggering it for a set time. I used it in a search box. Without debouncing, typing "patient" would send seven API calls. With a 400 ms debounce, it sends only one call after the user stops typing.

```jsx
useEffect(() => {
  const timer = setTimeout(() => {
    if (query) fetchResults(query);
  }, 400);
  return () => clearTimeout(timer);
}, [query]);
```

Throttling is similar but different. It limits a function to run at most once per interval, which is useful for scroll or resize events.

---

## Section 6: Routing, Forms, and Security

### Q29. How do you implement protected routes?

**Answer:**
In React Router v6, I create a wrapper component that checks authentication:

```jsx
function ProtectedRoute({ children, roles }) {
  const { user } = useAuth();
  if (!user) return <Navigate to="/login" replace />;
  if (roles && !roles.includes(user.role)) return <Navigate to="/unauthorized" />;
  return children;
}
```

I also handle role-based access here, for example only admins can see the admin page. But I always mention that frontend checks are only for user experience. Real security must be enforced on the backend with Spring Security, because anyone can bypass frontend code.

---

### Q30. How do you handle form validation?

**Answer:**
For small forms, I validate manually in the submit handler and keep errors in state. For larger forms, I use React Hook Form with a Yup schema, which handles validation, error messages, and touched fields cleanly with good performance.

I always validate on both sides. Frontend validation gives instant feedback to the user, and backend validation with `@Valid` and annotations like `@NotBlank` and `@Email` is the actual safety net. When the backend returns field-level errors, I map them back to the corresponding form fields.

---

### Q31. Where do you store the JWT token: localStorage or cookies?

**Answer:**
It's a trade-off. localStorage is simple, but any JavaScript on the page can read it, so an XSS vulnerability could steal the token. An httpOnly cookie can't be read by JavaScript, so it's safer against XSS, but then we need CSRF protection, such as SameSite cookie settings or a CSRF token.

For a healthcare application dealing with sensitive data, I'd prefer httpOnly secure cookies with short-lived access tokens and a refresh token mechanism. In my previous project, we used sessionStorage with short token expiry, which at least clears the token when the tab is closed.

---

### Q32. How does React protect against XSS?

**Answer:**
React automatically escapes any value rendered inside JSX, so if a user enters `<script>` tags, they're shown as plain text and not executed. The risk comes with `dangerouslySetInnerHTML`, which bypasses this escaping. If we must render HTML from an API, for example rich text, we should sanitize it first with a library like DOMPurify. We should also avoid putting user input directly into `href` attributes, because of `javascript:` URLs.

---

## Section 7: Testing

### Q33. How do you test React components?

**Answer:**
I use Jest as the test runner and React Testing Library for rendering and interacting with components. The idea in React Testing Library is to test how the user sees the component, not internal implementation. So I find elements by role, label, or text, simulate user actions, and assert on what appears on screen.

```jsx
test("shows error when email is empty", async () => {
  render(<LoginForm />);
  await userEvent.click(screen.getByRole("button", { name: /login/i }));
  expect(screen.getByText(/email is required/i)).toBeInTheDocument();
});
```

---

### Q34. How do you mock API calls in tests?

**Answer:**
The simple approach is `jest.mock("axios")` and setting a resolved value for `axios.get`. A cleaner approach that I prefer is MSW (Mock Service Worker), which intercepts network requests at the network level. That way, the component code runs exactly as in production, including Axios interceptors, and I just define what the fake API returns.

---

## Section 8: JavaScript (ES6+) Questions Asked in React Rounds

### Q35. What is the difference between `let`, `const`, and `var`?

**Answer:**
`var` is function-scoped and hoisted with `undefined`, which leads to bugs. `let` and `const` are block-scoped. `const` can't be reassigned, but if it holds an object or array, the contents can still be modified. In React code, I use `const` by default and `let` only when reassignment is needed. I never use `var`.

---

### Q36. Explain Promises and async/await.

**Answer:**
A Promise represents a value that will be available in the future. It's either pending, fulfilled, or rejected. We handle results with `.then()` and errors with `.catch()`.

`async/await` is cleaner syntax over Promises. It makes asynchronous code read like synchronous code, and we use `try/catch` for errors:

```js
const loadUser = async (id) => {
  try {
    const res = await api.get(`/users/${id}`);
    setUser(res.data);
  } catch (err) {
    setError(err.response?.data?.message || "Something went wrong");
  } finally {
    setLoading(false);
  }
};
```

One thing to note: the `useEffect` callback itself can't be `async`, because it must return either nothing or a cleanup function. So I define an async function inside the effect and call it.

---

### Q37. What are closures? Where do you see them in React?

**Answer:**
A closure is when a function remembers variables from the scope where it was created, even after that scope has finished. In React, every render creates new functions that close over that render's props and state.

This causes the "stale closure" problem. For example, in a `setInterval` inside `useEffect` with an empty dependency array, the callback always sees the initial state value. I fix it using the functional update form `setCount(c => c + 1)`, or by adding the correct dependencies, or by using a `ref` to hold the latest value.

---

### Q38. What is the spread operator, and why is immutability important in React?

**Answer:**
The spread operator `...` copies elements of an array or properties of an object into a new one. In React, state must be treated as immutable. React compares the old and new state by reference, so if I mutate the existing array with `push`, the reference doesn't change and React may not re-render.

```js
// Wrong
items.push(newItem); setItems(items);

// Correct
setItems(prev => [...prev, newItem]);
setUser(prev => ({ ...prev, name: "Pavan" }));
```

---

## Section 9: Scenario-Based and Coding Questions

### Q39. Predict the output.

```jsx
const [count, setCount] = useState(0);
const handleClick = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
  console.log(count);
};
```

**Answer:**
The console prints `0`, and after the re-render, `count` becomes `1`, not `3`. State updates are asynchronous and batched, and all three calls read the same `count` value from the current render's closure. To get `3`, I'd use `setCount(c => c + 1)` three times.

---

### Q40. Write a timer that increments every second and stops when the component unmounts.

**Answer:**

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const id = setInterval(() => setSeconds(s => s + 1), 1000);
    return () => clearInterval(id);
  }, []);

  return <h2>{seconds}s</h2>;
}
```

The key points are the cleanup function, which prevents a memory leak after unmount, and the functional update, which avoids the stale closure problem.

---

### Q41. Build a todo list with add, toggle, and delete.

**Answer:**

```jsx
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [text, setText] = useState("");

  const addTodo = () => {
    if (!text.trim()) return;
    setTodos(prev => [...prev, { id: Date.now(), text, done: false }]);
    setText("");
  };

  const toggleTodo = id =>
    setTodos(prev => prev.map(t => (t.id === id ? { ...t, done: !t.done } : t)));

  const deleteTodo = id => setTodos(prev => prev.filter(t => t.id !== id));

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} placeholder="Add task" />
      <button onClick={addTodo}>Add</button>
      <ul>
        {todos.map(t => (
          <li key={t.id}>
            <span
              onClick={() => toggleTodo(t.id)}
              style={{ textDecoration: t.done ? "line-through" : "none", cursor: "pointer" }}
            >
              {t.text}
            </span>
            <button onClick={() => deleteTodo(t.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

While writing it, I'd explain the points the interviewer is checking: immutable updates with spread, `map` and `filter`, and a unique `id` as the key instead of the index.

---

### Q42. Fetch users from an API and display them with pagination.

**Answer:**

```jsx
function UserList() {
  const [page, setPage] = useState(0);
  const { data, loading, error } = useFetch(`/api/users?page=${page}&size=10`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <>
      <table>
        <tbody>
          {data.content.map(u => (
            <tr key={u.id}><td>{u.name}</td><td>{u.email}</td></tr>
          ))}
        </tbody>
      </table>
      <button disabled={data.first} onClick={() => setPage(p => p - 1)}>Prev</button>
      <span> Page {page + 1} of {data.totalPages} </span>
      <button disabled={data.last} onClick={() => setPage(p => p + 1)}>Next</button>
    </>
  );
}
```

The response format here (`content`, `totalPages`, `first`, `last`) matches Spring Data's `Page` object, so on the backend the controller just accepts a `Pageable` and returns `repository.findAll(pageable)`.

---

### Q43. Explain the end-to-end flow of a form submission from React to Spring Boot.

**Answer:**
1. The user fills a controlled form. React Hook Form with Yup validates on the client and shows errors instantly.
2. On submit, I call `api.post("/patients", formData)` through the Axios instance, which attaches the JWT via the interceptor.
3. The request goes through the API gateway to the patient microservice.
4. Spring Security's JWT filter validates the token.
5. The controller receives `@Valid @RequestBody PatientDTO`. Bean Validation checks the fields.
6. If validation fails, `@RestControllerAdvice` returns a 400 response with a map of field names to error messages.
7. The service layer applies business logic, and the repository saves it using JPA within a transaction.
8. The API returns 201 Created with the saved object.
9. On the React side, on success I show a toast and navigate or refresh the list. On a 400, I map the field errors back to the form inputs. On a 500, I show a general error message.

This question shows full-stack understanding, so I explain both sides with confidence.

---

## Section 10: Experience-Based Questions (Common in the KP US Round)

### Q44. Tell me about a challenging bug you fixed in React.

**Sample answer (adapt to your real experience):**
In one project, users reported that search results sometimes showed data for an older search term. I reproduced it by typing quickly on a slow network. The issue was a race condition: multiple API calls were in flight, and a slower older response was overwriting the newer one. I fixed it in two ways. I added a debounce of 400 ms to reduce the number of calls, and I used `AbortController` in the `useEffect` cleanup to cancel the previous request whenever the search term changed. After that, results were always correct, and API calls to the backend dropped significantly.

---

### Q45. How did you structure your React project?

**Sample answer:**
We used a feature-based folder structure. Each feature, like patients, appointments, or auth, had its own folder with components, hooks, API calls, and tests. Shared things lived in a common folder: reusable UI components like Button, Modal, and Table; custom hooks like `useFetch` and `useDebounce`; and an `api` folder with the configured Axios instance and interceptors. Environment-specific values like API URLs were in `.env` files. This kept each feature self-contained and made it easier for multiple developers to work in parallel without conflicts.

---

### Q46. How do you ensure code quality in your React code?

**Sample answer:**
We used ESLint with the React Hooks plugin and Prettier for consistent formatting, enforced through pre-commit hooks. Every change went through a pull request with at least one reviewer. In reviews, I look for unnecessary re-renders, missing `useEffect` dependencies, missing error handling, hardcoded values, and missing tests. We wrote unit tests with Jest and React Testing Library, and the CI pipeline ran lint and tests on every PR, so nothing broken got merged.

---

### Q47. How do you collaborate with backend and QA teams?

**Sample answer:**
Before starting frontend work, I agree on the API contract with the backend team: the request and response structure, status codes, and error format. We used Swagger or OpenAPI documentation for this. While the API was being built, I used mock data or MSW so I wasn't blocked. Since I'm a full stack developer, I often worked on both sides of a feature myself. With QA, I shared the acceptance criteria and edge cases I'd tested, and I fixed bugs raised during the sprint. We followed Agile with two-week sprints, daily stand-ups, and sprint demos.

---

## Final Interview Tips

- **Structure every answer:** definition → why it matters → example from your project.
- **Replace sample stories** in Section 10 with your own real experiences. Interviewers ask follow-up questions, so the details must be yours.
- **Connect frontend to backend** whenever possible. This is a full stack role, so showing how React integrates with Spring Boot is a strong advantage.
- **For coding rounds,** think out loud, start with a working solution, then improve it (handle loading, errors, edge cases).
- **Shift is 2 PM – 11 PM** to overlap with the US team, so communication skills matter in the KP US round. Practice explaining answers clearly and concisely.
