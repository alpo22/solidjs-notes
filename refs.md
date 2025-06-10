# Refs

Refs in SolidJS provide direct access to DOM elements using simple variable assignment.

## Syntax

```javascript
let elementRef;
return <div ref={elementRef}>Content</div>;
```

## Basic Example

```javascript
import { onMount } from 'solid-js';

function AutoFocus() {
  let inputRef;

  onMount(() => {
    inputRef.focus();
  });

  return <input ref={inputRef} placeholder="Auto-focused" />;
}
```

## DOM Manipulation

```javascript
import { createSignal } from 'solid-js';

function StyleChanger() {
  let divRef;

  const changeColor = () => {
    divRef.style.backgroundColor = 'lightblue';
  };

  return (
    <div>
      <div ref={divRef}>Click to change my color</div>
      <button onClick={changeColor}>Change Color</button>
    </div>
  );
}
```

## Form Access

```javascript
function FormExample() {
  let nameRef, emailRef;

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Name:', nameRef.value);
    console.log('Email:', emailRef.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} placeholder="Name" />
      <input ref={emailRef} type="email" placeholder="Email" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## Canvas Example

```javascript
import { onMount } from 'solid-js';

function SimpleCanvas() {
  let canvasRef;

  onMount(() => {
    const ctx = canvasRef.getContext('2d');
    ctx.fillStyle = 'red';
    ctx.fillRect(10, 10, 50, 50);
  });

  return <canvas ref={canvasRef} width="200" height="100" />;
}
```

## Key Points

- Refs are simple variable assignments, not objects
- Available after element is mounted
- Use for DOM manipulation, focus, measurements
- Perfect for third-party library integration
- Prefer reactive patterns over refs when possible
