# onMount

`onMount` is a lifecycle function that runs after the component has been mounted to the DOM. It's equivalent to `useEffect` with an empty dependency array in React.

## Syntax

```javascript
onMount(fn);
```

## Parameters

- `fn` - Function to execute after the component mounts

## Basic Example

```javascript
import { onMount, createSignal } from 'solid-js';

function MountedComponent() {
  const [message, setMessage] = createSignal('Loading...');

  onMount(() => {
    console.log('Component mounted!');
    setMessage('Component is now mounted!');
  });

  return <div>{message()}</div>;
}
```

## API Call Example

```javascript
import { onMount, createSignal } from 'solid-js';

function UserProfile() {
  const [user, setUser] = createSignal(null);
  const [loading, setLoading] = createSignal(true);

  onMount(async () => {
    try {
      const response = await fetch('/api/user');
      const userData = await response.json();
      setUser(userData);
    } catch (error) {
      console.error('Failed to fetch user:', error);
    } finally {
      setLoading(false);
    }
  });

  return (
    <div>
      {loading() ? (
        <p>Loading user...</p>
      ) : user() ? (
        <div>
          <h1>{user().name}</h1>
          <p>Email: {user().email}</p>
        </div>
      ) : (
        <p>Failed to load user</p>
      )}
    </div>
  );
}
```

## DOM Focus Example

```javascript
import { onMount } from 'solid-js';

function AutoFocusInput() {
  let inputRef;

  onMount(() => {
    if (inputRef) {
      inputRef.focus();
    }
  });

  return (
    <div>
      <h2>Auto-focused Input</h2>
      <input 
        ref={inputRef}
        type="text" 
        placeholder="This input will be focused on mount"
      />
    </div>
  );
}
```

## Event Listener Setup

```javascript
import { onMount, onCleanup, createSignal } from 'solid-js';

function WindowSizeTracker() {
  const [windowSize, setWindowSize] = createSignal({
    width: 0,
    height: 0
  });

  onMount(() => {
    const updateSize = () => {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    // Set initial size
    updateSize();

    // Add event listener
    window.addEventListener('resize', updateSize);

    // Cleanup on unmount
    onCleanup(() => {
      window.removeEventListener('resize', updateSize);
    });
  });

  return (
    <div>
      <h2>Window Size</h2>
      <p>Width: {windowSize().width}px</p>
      <p>Height: {windowSize().height}px</p>
    </div>
  );
}
```

## Timer Setup Example

```javascript
import { onMount, onCleanup, createSignal } from 'solid-js';

function Timer() {
  const [seconds, setSeconds] = createSignal(0);

  onMount(() => {
    const interval = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);

    onCleanup(() => {
      clearInterval(interval);
    });
  });

  return (
    <div>
      <h2>Timer</h2>
      <p>Seconds elapsed: {seconds()}</p>
    </div>
  );
}
```

## Multiple onMount Calls

```javascript
import { onMount, createSignal } from 'solid-js';

function MultipleOnMounts() {
  const [status, setStatus] = createSignal([]);

  onMount(() => {
    setStatus(prev => [...prev, 'First onMount executed']);
  });

  onMount(() => {
    setStatus(prev => [...prev, 'Second onMount executed']);
  });

  onMount(() => {
    setStatus(prev => [...prev, 'Third onMount executed']);
  });

  return (
    <div>
      <h2>Mount Status</h2>
      <ul>
        {status().map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Key Points

- Runs only once after the component is mounted to the DOM
- Perfect for API calls, DOM manipulation, and setting up subscriptions
- Can be called multiple times in the same component
- Use with `onCleanup` for cleanup logic (event listeners, timers, etc.)
- Async functions are supported
- Executes after the initial render is complete
