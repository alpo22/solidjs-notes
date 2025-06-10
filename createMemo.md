# createMemo

`createMemo` creates a memoized derived value that only recalculates when its dependencies change. It's useful for expensive computations that depend on reactive values.

## Syntax

```javascript
const memoizedValue = createMemo(fn, initialValue, options);
```

## Parameters

- `fn` - Function that returns the computed value
- `initialValue` (optional) - Initial value before first computation
- `options` (optional) - Configuration object with properties like `equals`

## Returns

Returns a getter function that returns the memoized value.

## Basic Example

```javascript
import { createSignal, createMemo } from 'solid-js';

function ExpensiveCalculation() {
  const [numbers, setNumbers] = createSignal([1, 2, 3, 4, 5]);

  const sum = createMemo(() => {
    console.log('Calculating sum...'); // Only logs when numbers change
    return numbers().reduce((acc, num) => acc + num, 0);
  });

  return (
    <div>
      <p>Numbers: {numbers().join(', ')}</p>
      <p>Sum: {sum()}</p>
      <button onClick={() => setNumbers([...numbers(), numbers().length + 1])}>
        Add Number
      </button>
    </div>
  );
}
```

## Filtering Example

```javascript
import { createSignal, createMemo } from 'solid-js';

function FilteredList() {
  const [items, setItems] = createSignal(['apple', 'banana', 'cherry', 'date']);
  const [filter, setFilter] = createSignal('');

  const filteredItems = createMemo(() => {
    const filterValue = filter().toLowerCase();
    return items().filter(item => 
      item.toLowerCase().includes(filterValue)
    );
  });

  return (
    <div>
      <input 
        type="text"
        placeholder="Filter items..."
        value={filter()}
        onInput={(e) => setFilter(e.target.value)}
      />
      <ul>
        {filteredItems().map(item => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Chained Memos

```javascript
import { createSignal, createMemo } from 'solid-js';

function ChainedCalculations() {
  const [radius, setRadius] = createSignal(5);

  const area = createMemo(() => Math.PI * radius() ** 2);
  const circumference = createMemo(() => 2 * Math.PI * radius());
  const volume = createMemo(() => (4/3) * Math.PI * radius() ** 3);

  return (
    <div>
      <label>
        Radius: 
        <input 
          type="number" 
          value={radius()} 
          onInput={(e) => setRadius(Number(e.target.value))}
        />
      </label>
      <p>Area: {area().toFixed(2)}</p>
      <p>Circumference: {circumference().toFixed(2)}</p>
      <p>Volume: {volume().toFixed(2)}</p>
    </div>
  );
}
```

## Key Points

- Memos only recalculate when their dependencies change
- Use memos for expensive computations or complex derivations
- Memos are cached until their dependencies update
- Like signals, always call memos as functions: `memo()`
- Memos can depend on other memos, creating computation chains
- Use memos instead of effects when you need a return value
