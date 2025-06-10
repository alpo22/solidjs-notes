# Switch & Match

`Switch` and `Match` are control flow components that provide a way to conditionally render content based on multiple conditions, similar to a switch statement in JavaScript.

## Syntax

```javascript
<Switch fallback={defaultContent}>
  <Match when={condition1}>Content 1</Match>
  <Match when={condition2}>Content 2</Match>
  <Match when={condition3}>Content 3</Match>
</Switch>
```

## Props

### Switch
- `fallback` (optional) - Content to render when no Match conditions are true
- `children` - Match components

### Match
- `when` - The condition to evaluate (truthy/falsy)
- `children` - Content to render when condition is truthy

## Basic Example

```javascript
import { Switch, Match, createSignal } from 'solid-js';

function StatusDisplay() {
  const [status, setStatus] = createSignal('loading');

  return (
    <div>
      <button onClick={() => setStatus('loading')}>Loading</button>
      <button onClick={() => setStatus('success')}>Success</button>
      <button onClick={() => setStatus('error')}>Error</button>

      <Switch fallback={<p>Unknown status</p>}>
        <Match when={status() === 'loading'}>
          <p style={{ color: 'blue' }}>Loading...</p>
        </Match>
        <Match when={status() === 'success'}>
          <p style={{ color: 'green' }}>Success!</p>
        </Match>
        <Match when={status() === 'error'}>
          <p style={{ color: 'red' }}>Error occurred!</p>
        </Match>
      </Switch>
    </div>
  );
}
```

## User Role Example

```javascript
import { Switch, Match, createSignal } from 'solid-js';

function UserDashboard() {
  const [userRole, setUserRole] = createSignal('guest');

  return (
    <div>
      <select value={userRole()} onChange={(e) => setUserRole(e.target.value)}>
        <option value="admin">Admin</option>
        <option value="user">User</option>
        <option value="guest">Guest</option>
      </select>

      <Switch fallback={<p>Access denied</p>}>
        <Match when={userRole() === 'admin'}>
          <div>
            <h3>Admin Panel</h3>
            <p>Full system access</p>
          </div>
        </Match>
        
        <Match when={userRole() === 'user'}>
          <div>
            <h3>User Dashboard</h3>
            <p>Limited access</p>
          </div>
        </Match>
        
        <Match when={userRole() === 'guest'}>
          <div>
            <h3>Guest View</h3>
            <p>Read-only access</p>
          </div>
        </Match>
      </Switch>
    </div>
  );
}
```

## Grade Calculator

```javascript
import { Switch, Match, createSignal } from 'solid-js';

function GradeCalculator() {
  const [score, setScore] = createSignal(85);

  return (
    <div>
      <label>
        Score: {score()}
        <input 
          type="range" 
          min="0" 
          max="100" 
          value={score()} 
          onInput={(e) => setScore(Number(e.target.value))}
        />
      </label>

      <Switch fallback={<p>Invalid score</p>}>
        <Match when={score() >= 90}>
          <h2 style={{ color: 'green' }}>Grade: A 🎉</h2>
        </Match>
        <Match when={score() >= 80}>
          <h2 style={{ color: 'blue' }}>Grade: B 👍</h2>
        </Match>
        <Match when={score() >= 70}>
          <h2 style={{ color: 'orange' }}>Grade: C 👌</h2>
        </Match>
        <Match when={score() >= 60}>
          <h2 style={{ color: 'red' }}>Grade: D 😐</h2>
        </Match>
        <Match when={score() < 60}>
          <h2 style={{ color: 'darkred' }}>Grade: F ❌</h2>
        </Match>
      </Switch>
    </div>
  );
}
```

## API State Handling

```javascript
import { Switch, Match, createSignal } from 'solid-js';

function ApiExample() {
  const [apiState, setApiState] = createSignal('idle');

  const fetchData = () => {
    setApiState('loading');
    setTimeout(() => {
      setApiState(Math.random() > 0.5 ? 'success' : 'error');
    }, 2000);
  };

  return (
    <div>
      <button onClick={fetchData}>Fetch Data</button>
      <button onClick={() => setApiState('idle')}>Reset</button>

      <Switch fallback={<p>Ready to fetch</p>}>
        <Match when={apiState() === 'loading'}>
          <p>Loading...</p>
        </Match>
        <Match when={apiState() === 'success'}>
          <p style={{ color: 'green' }}>✅ Data loaded!</p>
        </Match>
        <Match when={apiState() === 'error'}>
          <p style={{ color: 'red' }}>❌ Failed to load</p>
        </Match>
      </Switch>
    </div>
  );
}
```

## Key Points

- Use Switch/Match for multiple conditional branches instead of multiple Show components
- Only the first matching Match component will render
- Switch evaluates Match components in order from top to bottom
- Use `fallback` on Switch for default cases
- More readable than nested ternary operators for complex conditions
- Perfect for state machines, user roles, themes, and multi-step processes
- More performant than multiple Show components for mutually exclusive conditions
