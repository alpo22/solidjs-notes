# For

`For` is a control flow component for efficiently rendering lists in SolidJS. It's optimized for rendering arrays and provides better performance than using `.map()` for dynamic lists.

## Syntax

```javascript
<For each={array} fallback={fallbackContent}>
  {(item, index) => JSX}
</For>
```

## Props

- `each` - The array to iterate over
- `fallback` (optional) - Content to render when array is empty
- `children` - Function that receives `(item, index)` and returns JSX

## Basic Example

```javascript
import { For, createSignal } from 'solid-js';

function BasicList() {
  const [items, setItems] = createSignal(['Apple', 'Banana', 'Cherry']);

  return (
    <div>
      <h2>Fruits</h2>
      <ul>
        <For each={items()}>
          {(item) => <li>{item}</li>}
        </For>
      </ul>
    </div>
  );
}
```

## With Index

```javascript
import { For, createSignal } from 'solid-js';

function IndexedList() {
  const [tasks, setTasks] = createSignal([
    'Learn SolidJS',
    'Build an app',
    'Deploy to production'
  ]);

  return (
    <div>
      <h2>Tasks</h2>
      <ol>
        <For each={tasks()}>
          {(task, index) => (
            <li>
              <strong>#{index() + 1}:</strong> {task}
            </li>
          )}
        </For>
      </ol>
    </div>
  );
}
```

## With Fallback

```javascript
import { For, createSignal } from 'solid-js';

function ListWithFallback() {
  const [items, setItems] = createSignal([]);

  const addItem = () => {
    const newItem = `Item ${items().length + 1}`;
    setItems([...items(), newItem]);
  };

  const clearItems = () => setItems([]);

  return (
    <div>
      <button onClick={addItem}>Add Item</button>
      <button onClick={clearItems}>Clear All</button>
      
      <For each={items()} fallback={<p>No items to display</p>}>
        {(item) => <div style={{ padding: '5px', border: '1px solid #ccc', margin: '5px' }}>
          {item}
        </div>}
      </For>
    </div>
  );
}
```

## Object Arrays

```javascript
import { For, createSignal } from 'solid-js';

function UserList() {
  const [users, setUsers] = createSignal([
    { id: 1, name: 'John Doe', email: 'john@example.com', age: 30 },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com', age: 25 },
    { id: 3, name: 'Bob Johnson', email: 'bob@example.com', age: 35 }
  ]);

  const removeUser = (id) => {
    setUsers(users().filter(user => user.id !== id));
  };

  return (
    <div>
      <h2>Users</h2>
      <For each={users()} fallback={<p>No users found</p>}>
        {(user) => (
          <div style={{ 
            border: '1px solid #ddd', 
            padding: '10px', 
            margin: '10px 0',
            borderRadius: '5px'
          }}>
            <h3>{user.name}</h3>
            <p>Email: {user.email}</p>
            <p>Age: {user.age}</p>
            <button onClick={() => removeUser(user.id)}>
              Remove
            </button>
          </div>
        )}
      </For>
    </div>
  );
}
```

## Dynamic List Management

```javascript
import { For, createSignal } from 'solid-js';

function TodoList() {
  const [todos, setTodos] = createSignal([]);
  const [inputValue, setInputValue] = createSignal('');

  const addTodo = () => {
    if (inputValue().trim()) {
      const newTodo = {
        id: Date.now(),
        text: inputValue().trim(),
        completed: false
      };
      setTodos([...todos(), newTodo]);
      setInputValue('');
    }
  };

  const toggleTodo = (id) => {
    setTodos(todos().map(todo => 
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const deleteTodo = (id) => {
    setTodos(todos().filter(todo => todo.id !== id));
  };

  return (
    <div>
      <h2>Todo List</h2>
      <div>
        <input 
          type="text"
          value={inputValue()}
          onInput={(e) => setInputValue(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && addTodo()}
          placeholder="Add a new todo..."
        />
        <button onClick={addTodo}>Add</button>
      </div>

      <For each={todos()} fallback={<p>No todos yet. Add one above!</p>}>
        {(todo) => (
          <div style={{ 
            display: 'flex', 
            alignItems: 'center', 
            gap: '10px',
            padding: '10px',
            backgroundColor: todo.completed ? '#f0f0f0' : 'white',
            border: '1px solid #ddd',
            margin: '5px 0'
          }}>
            <input 
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ 
              textDecoration: todo.completed ? 'line-through' : 'none',
              flex: 1
            }}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>
              Delete
            </button>
          </div>
        )}
      </For>
    </div>
  );
}
```

## Nested For Loops

```javascript
import { For, createSignal } from 'solid-js';

function NestedLists() {
  const [categories, setCategories] = createSignal([
    {
      name: 'Fruits',
      items: ['Apple', 'Banana', 'Orange']
    },
    {
      name: 'Vegetables',
      items: ['Carrot', 'Broccoli', 'Spinach']
    },
    {
      name: 'Grains',
      items: ['Rice', 'Wheat', 'Oats']
    }
  ]);

  return (
    <div>
      <h2>Food Categories</h2>
      <For each={categories()}>
        {(category) => (
          <div style={{ marginBottom: '20px' }}>
            <h3>{category.name}</h3>
            <ul>
              <For each={category.items}>
                {(item) => <li>{item}</li>}
              </For>
            </ul>
          </div>
        )}
      </For>
    </div>
  );
}
```

## Filtered Lists

```javascript
import { For, createSignal, createMemo } from 'solid-js';

function FilteredList() {
  const [items, setItems] = createSignal([
    'Apple', 'Banana', 'Cherry', 'Date', 'Elderberry', 'Fig', 'Grape'
  ]);
  const [filter, setFilter] = createSignal('');

  const filteredItems = createMemo(() => {
    const filterValue = filter().toLowerCase();
    return items().filter(item => 
      item.toLowerCase().includes(filterValue)
    );
  });

  return (
    <div>
      <h2>Filtered Fruit List</h2>
      <input 
        type="text"
        placeholder="Filter fruits..."
        value={filter()}
        onInput={(e) => setFilter(e.target.value)}
      />
      
      <For each={filteredItems()} fallback={<p>No fruits match your filter</p>}>
        {(item) => (
          <div style={{ 
            padding: '5px 10px', 
            margin: '5px 0', 
            backgroundColor: '#f5f5f5',
            borderRadius: '3px'
          }}>
            {item}
          </div>
        )}
      </For>
    </div>
  );
}
```

## Key Points

- More efficient than `.map()` for dynamic lists
- Automatically handles list updates and reconciliation
- The `each` prop should be a reactive array (signal)
- Children function receives `(item, index)` where index is also a signal
- Use `fallback` for empty state handling
- Perfect for todo lists, user lists, and any dynamic content
- Handles additions, removals, and reordering efficiently
- Can be nested for complex data structures
