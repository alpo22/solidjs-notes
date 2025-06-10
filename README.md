# Cheat sheet

Here’s a the commonly used commands and syntax in **React** vs **SolidJS**:

| **Concept**               | **React**                                | **SolidJS**                                             |
| ------------------------- | ---------------------------------------- | ------------------------------------------------------- |
| **State**                 | `const [count, setCount] = useState(0)`  | `const [count, setCount] = createSignal(0)`             |
| **Update state**          | `setCount(count + 1)`                    | `setCount(count() + 1)` or `setCount(c => c+1)`         |
| **Access state**          | `count`                                  | `count()`                                               |
| **Effect**                | `useEffect(() => { ... }, [deps])`       | `createEffect(() => { ... })`                           |
|   onMount                 | `useEffect(() => { ... }, [])`           | `onMount()`                                             |
|   onCleanup               | `useEffect(() => { ... return () => {...} }, [])`  | `onCleanup()`                                 |
| **Memoized value**        | `useMemo(() => {...}, [deps])`           | `createMemo(() => {...})`                               |
| **Refs**                  | `const ref = useRef(null)`               | `let ref;` (direct DOM refs via `ref={el => ref = el}`) |
| **Conditional rendering** | `{condition ? <A /> : <B />}`            | `<Show when={condition} fallback={<B />}><A /></Show>`  |
|                           | `if () return <A />`                     |     <Switch><Match when={...}>...</Match><Match when={...}>... |
| **List rendering**        | `{items.map(item => <div>{item}</div>)}` | `<For each={items}>{item => <div>{item}</div>}</For>`   |
| **Context API**           | `createContext/useContext`               | `createContext/useContext`                              |
| **Fragment**              | `<> ... </>`                             | `<> ... </>`                                            |
| **Routing**               | `react-router-dom`                       | `solid-app-router` or `@solidjs/router`                 |
| **Dev tools**             | React DevTools                           | SolidJS DevTools                                        |

