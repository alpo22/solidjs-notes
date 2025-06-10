# createEffect

`createEffect` is used to create side effects that run when their dependencies change. It's similar to `useEffect` in React but with automatic dependency tracking.

## Syntax

```javascript
createEffect(fn, initialValue, options);
```

## Parameters

- `fn` - The effect function to run
- `initialValue` (optional) - Initial value for the effect
- `options` (optional) - Configuration object

## Basic Example

```javascript
import { createSignal, createEffect } from 'solid-js';

function Logger() {
  const [count, setCount] = createSignal(0);

  createEffect(() => {
    console.log('Count changed to:', count());
  });

  return (
    <div>
      <p>Count: {count()}</p>
      <button onClick={() => setCount(count() + 1)}>
        Increment
      </button>
    </div>
  );
}
```

## Cleanup Example

```javascript
import { createSignal, createEffect, onCleanup } from 'solid-js';

function Timer() {
  const [seconds, setSeconds] = createSignal(0);

  createEffect(() => {
    const interval = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);

    onCleanup(() => clearInterval(interval));
  });

  return <div>Seconds: {seconds()}</div>;
}
```

## Effect with Previous Value

```javascript
import { createSignal, createEffect } from 'solid-js';

function ValueTracker() {
  const [value, setValue] = createSignal(0);

  createEffect((prev) => {
    console.log(`Value changed from ${prev} to ${value()}`);
    return value(); // Return current value to be used as prev in next run
  });

  return (
    <div>
      <input 
        type="number" 
        value={value()} 
        onInput={(e) => setValue(Number(e.target.value))}
      />
    </div>
  );
}
```

## Key Points

- Effects automatically track their dependencies
- Use `onCleanup` for cleanup logic (timers, subscriptions, etc.)
- Effects run after the DOM has been updated
- The effect function can return a value that becomes the previous value in the next run
- Effects are disposed when their containing component is unmounted
