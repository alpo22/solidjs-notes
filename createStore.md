# createStore

`createStore` creates a reactive store for managing complex nested state. It provides fine-grained reactivity and immutable updates through a proxy-based API.

## Syntax

```javascript
const [store, setStore] = createStore(initialValue);
```

## Parameters

- `initialValue` - The initial state object or array

## Returns

Returns a tuple with:
- `store` - Reactive proxy for reading state
- `setStore` - Function for updating state

## Basic Example

```javascript
import { createStore } from 'solid-js/store';

function UserProfile() {
  const [user, setUser] = createStore({
    name: 'John Doe',
    email: 'john@example.com',
    preferences: {
      theme: 'dark',
      notifications: true
    }
  });

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
      <p>Theme: {user.preferences.theme}</p>
      
      <button onClick={() => setUser('name', 'Jane Doe')}>
        Change Name
      </button>
      <button onClick={() => setUser('preferences', 'theme', 'light')}>
        Toggle Theme
      </button>
    </div>
  );
}
```

## Array Store

```javascript
import { createStore } from 'solid-js/store';
import { For } from 'solid-js';

function TodoList() {
  const [todos, setTodos] = createStore([
    { id: 1, text: 'Learn SolidJS', completed: false },
    { id: 2, text: 'Build an app', completed: false }
  ]);

  const addTodo = (text) => {
    setTodos(todos.length, {
      id: Date.now(),
      text,
      completed: false
    });
  };

  const toggleTodo = (index) => {
    setTodos(index, 'completed', !todos[index].completed);
  };

  return (
    <div>
      <For each={todos}>
        {(todo, index) => (
          <div>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(index())}
            />
            <span style={{ 
              textDecoration: todo.completed ? 'line-through' : 'none' 
            }}>
              {todo.text}
            </span>
          </div>
        )}
      </For>
      <button onClick={() => addTodo('New task')}>
        Add Todo
      </button>
    </div>
  );
}
```

## Function Updates

```javascript
import { createStore } from 'solid-js/store';

function Counter() {
  const [state, setState] = createStore({
    count: 0,
    history: []
  });

  const increment = () => {
    setState('count', c => c + 1);
    setState('history', h => [...h, state.count + 1]);
  };

  const reset = () => {
    setState({
      count: 0,
      history: []
    });
  };

  return (
    <div>
      <p>Count: {state.count}</p>
      <p>History: {state.history.join(', ')}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

## Nested Updates

```javascript
import { createStore } from 'solid-js/store';

function SettingsPanel() {
  const [settings, setSettings] = createStore({
    user: {
      profile: {
        name: 'John',
        avatar: 'default.jpg'
      },
      preferences: {
        theme: 'dark',
        language: 'en'
      }
    },
    app: {
      notifications: true,
      autoSave: false
    }
  });

  return (
    <div>
      <h2>{settings.user.profile.name}</h2>
      <p>Theme: {settings.user.preferences.theme}</p>
      
      <button onClick={() => 
        setSettings('user', 'profile', 'name', 'Jane')
      }>
        Change Name
      </button>
      
      <button onClick={() => 
        setSettings('user', 'preferences', 'theme', 
          settings.user.preferences.theme === 'dark' ? 'light' : 'dark'
        )
      }>
        Toggle Theme
      </button>
      
      <button onClick={() => 
        setSettings('app', 'notifications', !settings.app.notifications)
      }>
        Toggle Notifications: {settings.app.notifications ? 'On' : 'Off'}
      </button>
    </div>
  );
}
```

## Key Points

- Use stores for complex nested state instead of multiple signals
- Provides fine-grained reactivity - only changed parts re-render
- Updates are immutable - original state is never mutated
- Path-based updates: `setStore('path', 'to', 'property', newValue)`
- Function updates: `setStore('count', c => c + 1)`
- Perfect for forms, settings, and complex application state
- More efficient than signals for large objects
