# createSignal

`createSignal` is the most fundamental reactive primitive in SolidJS. It creates a reactive value that can be read and updated, automatically triggering updates to any dependent computations or effects.

## Syntax

```javascript
const [getter, setter] = createSignal(initialValue, options);
```

## Parameters

- `initialValue` - The initial value of the signal
- `options` (optional) - Configuration object with properties like `equals`

## Returns

Returns a tuple with:
- `getter()` - Function to read the current value
- `setter(newValue)` - Function to update the value

## Basic Example

```javascript
import { createSignal } from 'solid-js';

function Counter() {
  const [count, setCount] = createSignal(0);

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

## Advanced Example

```javascript
import { createSignal } from 'solid-js';

function UserProfile() {
  const [user, setUser] = createSignal({ name: 'John', age: 30 });

  const updateName = (newName) => {
    setUser(prev => ({ ...prev, name: newName }));
  };

  return (
    <div>
      <h1>{user().name}</h1>
      <p>Age: {user().age}</p>
      <button onClick={() => updateName('Jane')}>
        Change Name
      </button>
    </div>
  );
}
```

## Key Points

- Signals are the foundation of SolidJS reactivity
- Always call the getter as a function: `count()` not `count`
- Setter can accept a value or a function that receives the previous value
- Changes automatically propagate to dependent computations
