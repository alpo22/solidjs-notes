# createRenderEffect

`createRenderEffect` is similar to `createEffect` but runs synchronously during the render phase, before the DOM is committed. It's useful for DOM measurements and synchronous side effects.

## Syntax

```javascript
createRenderEffect(fn, initialValue, options);
```

## Parameters

- `fn` - The effect function to run during render
- `initialValue` (optional) - Initial value for the effect
- `options` (optional) - Configuration object

## Basic Example

```javascript
import { createSignal, createRenderEffect } from 'solid-js';

function DOMMeasurement() {
  const [text, setText] = createSignal('Hello World');
  let divRef;

  createRenderEffect(() => {
    if (divRef) {
      console.log('Element width:', divRef.offsetWidth);
      console.log('Element height:', divRef.offsetHeight);
    }
  });

  return (
    <div>
      <div ref={divRef} style={{ padding: '10px', border: '1px solid black' }}>
        {text()}
      </div>
      <input 
        value={text()} 
        onInput={(e) => setText(e.target.value)}
        placeholder="Change text to see measurements"
      />
    </div>
  );
}
```

## Synchronous DOM Updates

```javascript
import { createSignal, createRenderEffect } from 'solid-js';

function SynchronousUpdate() {
  const [count, setCount] = createSignal(0);
  let elementRef;

  createRenderEffect(() => {
    if (elementRef) {
      // This runs synchronously during render
      elementRef.style.backgroundColor = count() % 2 === 0 ? 'lightblue' : 'lightcoral';
    }
  });

  return (
    <div>
      <div ref={elementRef} style={{ padding: '20px', transition: 'background-color 0.3s' }}>
        Count: {count()}
      </div>
      <button onClick={() => setCount(count() + 1)}>
        Increment
      </button>
    </div>
  );
}
```

## Comparison with createEffect

```javascript
import { createSignal, createEffect, createRenderEffect } from 'solid-js';

function EffectComparison() {
  const [value, setValue] = createSignal(0);

  createRenderEffect(() => {
    console.log('Render Effect - runs during render:', value());
  });

  createEffect(() => {
    console.log('Effect - runs after DOM commit:', value());
  });

  return (
    <div>
      <p>Value: {value()}</p>
      <button onClick={() => setValue(value() + 1)}>
        Update (check console)
      </button>
      <p>
        <small>
          Open console to see the order of execution.
          Render effects run before regular effects.
        </small>
      </p>
    </div>
  );
}
```

## Canvas Drawing Example

```javascript
import { createSignal, createRenderEffect } from 'solid-js';

function CanvasDrawing() {
  const [radius, setRadius] = createSignal(50);
  let canvasRef;

  createRenderEffect(() => {
    if (canvasRef) {
      const ctx = canvasRef.getContext('2d');
      const canvas = canvasRef;
      
      // Clear canvas
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      
      // Draw circle
      ctx.beginPath();
      ctx.arc(canvas.width / 2, canvas.height / 2, radius(), 0, 2 * Math.PI);
      ctx.fillStyle = 'blue';
      ctx.fill();
    }
  });

  return (
    <div>
      <canvas 
        ref={canvasRef} 
        width="200" 
        height="200" 
        style={{ border: '1px solid black' }}
      />
      <div>
        <label>
          Radius: {radius()}
          <input 
            type="range" 
            min="10" 
            max="90" 
            value={radius()} 
            onInput={(e) => setRadius(Number(e.target.value))}
          />
        </label>
      </div>
    </div>
  );
}
```

## Key Points

- Runs synchronously during the render phase
- Executes before `createEffect`
- Useful for DOM measurements and synchronous DOM manipulations
- Should be used sparingly as it can impact performance
- Perfect for canvas drawing, DOM measurements, or synchronous style updates
- Avoid heavy computations in render effects as they block rendering
