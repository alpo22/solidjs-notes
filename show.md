# Show

`Show` is a control flow component that conditionally renders content based on a boolean condition. It's more efficient than using ternary operators for conditional rendering in SolidJS.

## Syntax

```javascript
<Show when={condition} fallback={fallbackContent}>
  {contentToShow}
</Show>
```

## Props

- `when` - The condition to evaluate (truthy/falsy)
- `fallback` (optional) - Content to render when condition is falsy
- `children` - Content to render when condition is truthy

## Basic Example

```javascript
import { Show, createSignal } from 'solid-js';

function ConditionalContent() {
  const [isVisible, setIsVisible] = createSignal(false);

  return (
    <div>
      <button onClick={() => setIsVisible(!isVisible())}>
        Toggle Content
      </button>
      
      <Show when={isVisible()}>
        <p>This content is now visible!</p>
      </Show>
    </div>
  );
}
```

## With Fallback

```javascript
import { Show, createSignal } from 'solid-js';

function UserStatus() {
  const [user, setUser] = createSignal(null);

  return (
    <div>
      <button onClick={() => setUser({ name: 'John', email: 'john@example.com' })}>
        Login
      </button>
      <button onClick={() => setUser(null)}>
        Logout
      </button>

      <Show 
        when={user()} 
        fallback={<p>Please log in to continue</p>}
      >
        <div>
          <h2>Welcome back!</h2>
          <p>Name: {user().name}</p>
          <p>Email: {user().email}</p>
        </div>
      </Show>
    </div>
  );
}
```

## Loading States

```javascript
import { Show, createSignal, onMount } from 'solid-js';

function DataLoader() {
  const [data, setData] = createSignal(null);
  const [loading, setLoading] = createSignal(true);
  const [error, setError] = createSignal(null);

  onMount(async () => {
    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 2000));
      setData({ message: 'Data loaded successfully!' });
    } catch (err) {
      setError('Failed to load data');
    } finally {
      setLoading(false);
    }
  });

  return (
    <div>
      <Show when={loading()}>
        <p>Loading...</p>
      </Show>

      <Show when={error()}>
        <p style={{ color: 'red' }}>Error: {error()}</p>
      </Show>

      <Show when={data() && !loading()}>
        <div>
          <h2>Success!</h2>
          <p>{data().message}</p>
        </div>
      </Show>
    </div>
  );
}
```

## Nested Show Components

```javascript
import { Show, createSignal } from 'solid-js';

function NestedConditions() {
  const [user, setUser] = createSignal(null);
  const [isAdmin, setIsAdmin] = createSignal(false);

  const loginAsUser = () => setUser({ name: 'John', role: 'user' });
  const loginAsAdmin = () => {
    setUser({ name: 'Admin', role: 'admin' });
    setIsAdmin(true);
  };
  const logout = () => {
    setUser(null);
    setIsAdmin(false);
  };

  return (
    <div>
      <button onClick={loginAsUser}>Login as User</button>
      <button onClick={loginAsAdmin}>Login as Admin</button>
      <button onClick={logout}>Logout</button>

      <Show when={user()} fallback={<p>Not logged in</p>}>
        <div>
          <h2>Welcome, {user().name}!</h2>
          
          <Show when={isAdmin()} fallback={<p>User dashboard</p>}>
            <div>
              <h3>Admin Panel</h3>
              <p>You have admin privileges</p>
            </div>
          </Show>
        </div>
      </Show>
    </div>
  );
}
```

## Show with Function Children

```javascript
import { Show, createSignal } from 'solid-js';

function FunctionChildren() {
  const [user, setUser] = createSignal(null);

  const fetchUser = async () => {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1000));
    setUser({ 
      name: 'Jane Doe', 
      email: 'jane@example.com',
      avatar: 'https://via.placeholder.com/50'
    });
  };

  return (
    <div>
      <button onClick={fetchUser}>Load User</button>
      <button onClick={() => setUser(null)}>Clear</button>

      <Show when={user()}>
        {(userData) => (
          <div style={{ display: 'flex', alignItems: 'center', gap: '10px' }}>
            <img src={userData.avatar} alt="Avatar" />
            <div>
              <h3>{userData.name}</h3>
              <p>{userData.email}</p>
            </div>
          </div>
        )}
      </Show>
    </div>
  );
}
```

## Form Validation Example

```javascript
import { Show, createSignal, createMemo } from 'solid-js';

function FormValidation() {
  const [email, setEmail] = createSignal('');
  const [password, setPassword] = createSignal('');

  const isEmailValid = createMemo(() => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email());
  });

  const isPasswordValid = createMemo(() => password().length >= 8);

  const canSubmit = createMemo(() => isEmailValid() && isPasswordValid());

  return (
    <form>
      <div>
        <input 
          type="email" 
          placeholder="Email"
          value={email()}
          onInput={(e) => setEmail(e.target.value)}
        />
        <Show when={email() && !isEmailValid()}>
          <p style={{ color: 'red' }}>Please enter a valid email</p>
        </Show>
      </div>

      <div>
        <input 
          type="password" 
          placeholder="Password"
          value={password()}
          onInput={(e) => setPassword(e.target.value)}
        />
        <Show when={password() && !isPasswordValid()}>
          <p style={{ color: 'red' }}>Password must be at least 8 characters</p>
        </Show>
      </div>

      <Show 
        when={canSubmit()} 
        fallback={<button disabled>Please fix errors above</button>}
      >
        <button type="submit">Submit</button>
      </Show>
    </form>
  );
}
```

## Key Points

- More efficient than ternary operators for conditional rendering
- The `when` prop can be any truthy/falsy value
- Use `fallback` prop for else conditions
- Children can be JSX elements or functions
- When using function children, the function receives the truthy value as an argument
- Show components can be nested for complex conditional logic
- Perfect for loading states, authentication checks, and form validation
