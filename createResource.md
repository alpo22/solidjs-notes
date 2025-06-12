# createResource

`createResource` is SolidJS's primitive for handling asynchronous data fetching with reactive state management. It provides a declarative way to manage loading states, errors, and data updates.

## Basic Usage

```js
import { createResource } from "solid-js";

const [data, { mutate, refetch }] = createResource(fetcher);
```

## Simple Example

```js
import { createResource } from "solid-js";

// Basic resource without dependencies
const [users] = createResource(async () => {
  const response = await fetch("/api/users");
  return response.json();
});

function UserList() {
  return (
    <div>
      {users.loading && <p>Loading users...</p>}
      {users.error && <p>Error: {users.error.message}</p>}
      {users() && (
        <ul>
          {users().map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## With Dependencies

```js
import { createSignal, createResource } from "solid-js";

const [userId, setUserId] = createSignal(1);

// Resource that depends on userId signal
const [user] = createResource(userId, async id => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
});

function UserProfile() {
  return (
    <div>
      <button onClick={() => setUserId(userId() + 1)}>Next User</button>

      {user.loading && <p>Loading user...</p>}
      {user() && <h1>{user().name}</h1>}
    </div>
  );
}
```

## Resource Properties

```js
const [data, actions] = createResource(source, fetcher);

// Data properties
data(); // The resolved data (undefined while loading)
data.loading; // Boolean indicating if request is in progress
data.error; // Error object if request failed
data.state; // 'unresolved' | 'pending' | 'ready' | 'refreshing' | 'errored'
data.latest; // Latest data, even during refetch

// Action methods
actions.mutate; // Manually set the data
actions.refetch; // Trigger a new fetch
```

## Advanced Example with Error Handling

```js
import { createSignal, createResource, Show, For } from "solid-js";

const [searchTerm, setSearchTerm] = createSignal("");

const [searchResults, { refetch }] = createResource(searchTerm, async term => {
  if (!term.trim()) return [];

  const response = await fetch(`/api/search?q=${encodeURIComponent(term)}`);

  if (!response.ok) {
    throw new Error(`Search failed: ${response.statusText}`);
  }

  return response.json();
});

function SearchComponent() {
  return (
    <div>
      <input type="text" placeholder="Search..." value={searchTerm()} onInput={e => setSearchTerm(e.target.value)} />

      <button onClick={() => refetch()}>Refresh</button>

      <Show when={searchResults.loading}>
        <div class="spinner">Searching...</div>
      </Show>

      <Show when={searchResults.error}>
        <div class="error">
          Error: {searchResults.error.message}
          <button onClick={() => refetch()}>Retry</button>
        </div>
      </Show>

      <Show when={searchResults()}>
        <For each={searchResults()}>
          {result => (
            <div class="result">
              <h3>{result.title}</h3>
              <p>{result.description}</p>
            </div>
          )}
        </For>
      </Show>
    </div>
  );
}
```

## Mutating Data

```js
const [todos, { mutate }] = createResource(fetchTodos);

// Optimistic update
function addTodo(newTodo) {
  // Update UI immediately
  mutate(prev => [...(prev || []), newTodo]);

  // Send to server
  fetch("/api/todos", {
    method: "POST",
    body: JSON.stringify(newTodo),
  }).catch(() => {
    // Revert on error
    mutate(prev => prev.filter(todo => todo.id !== newTodo.id));
  });
}
```

## Multiple Resources

```js
const [user] = createResource(() => fetch("/api/user").then(r => r.json()));
const [posts] = createResource(() => fetch("/api/posts").then(r => r.json()));

function Dashboard() {
  return (
    <div>
      <Show when={user()}>
        <h1>Welcome, {user().name}!</h1>
      </Show>

      <Show when={posts()}>
        <PostList posts={posts()} />
      </Show>

      {(user.loading || posts.loading) && <LoadingSpinner />}
    </div>
  );
}
```

## Best Practices

- Use `createResource` for any asynchronous data fetching
- Leverage the `loading` and `error` states for better UX
- Use `mutate` for optimistic updates
- Handle errors gracefully with retry mechanisms
- Consider using `data.latest` when you need the previous data during refetch
- Combine with `Suspense` for coordinated loading states
