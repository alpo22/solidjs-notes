# createContext

`createContext` provides a way to pass data through the component tree without having to pass props down manually at every level. It's useful for sharing state between components that are far apart in the component tree.

## Syntax

```javascript
const Context = createContext(defaultValue);
```

## Parameters

- `defaultValue` - The default value used when a component doesn't have a matching Provider above it

## Returns

Returns a context object with `Provider` component.

## Basic Example

```javascript
import { createContext, useContext, createSignal } from 'solid-js';

// Create context
const ThemeContext = createContext('light');

function App() {
  const [theme, setTheme] = createSignal('dark');

  return (
    <ThemeContext.Provider value={theme()}>
      <Header />
      <Main />
      <button onClick={() => setTheme(theme() === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </ThemeContext.Provider>
  );
}

function Header() {
  const theme = useContext(ThemeContext);
  
  return (
    <header style={{ 
      background: theme === 'dark' ? '#333' : '#fff',
      color: theme === 'dark' ? '#fff' : '#333'
    }}>
      <h1>Header (Theme: {theme})</h1>
    </header>
  );
}

function Main() {
  const theme = useContext(ThemeContext);
  
  return (
    <main style={{ 
      background: theme === 'dark' ? '#222' : '#f5f5f5',
      color: theme === 'dark' ? '#fff' : '#333'
    }}>
      <p>Main content (Theme: {theme})</p>
    </main>
  );
}
```

## Reactive Context Example

```javascript
import { createContext, useContext, createSignal } from 'solid-js';

// Create context with reactive value
const CounterContext = createContext();

function CounterProvider(props) {
  const [count, setCount] = createSignal(0);
  
  const counterValue = {
    count,
    increment: () => setCount(c => c + 1),
    decrement: () => setCount(c => c - 1),
    reset: () => setCount(0)
  };

  return (
    <CounterContext.Provider value={counterValue}>
      {props.children}
    </CounterContext.Provider>
  );
}

function Counter() {
  const { count, increment, decrement, reset } = useContext(CounterContext);
  
  return (
    <div>
      <h2>Count: {count()}</h2>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

function CounterDisplay() {
  const { count } = useContext(CounterContext);
  
  return <p>Current count is: {count()}</p>;
}

function App() {
  return (
    <CounterProvider>
      <Counter />
      <CounterDisplay />
    </CounterProvider>
  );
}
```

## User Authentication Context

```javascript
import { createContext, useContext, createSignal, onMount } from 'solid-js';

const AuthContext = createContext();

function AuthProvider(props) {
  const [user, setUser] = createSignal(null);
  const [loading, setLoading] = createSignal(true);

  const login = async (email, password) => {
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, password })
      });
      const userData = await response.json();
      setUser(userData);
      return true;
    } catch (error) {
      console.error('Login failed:', error);
      return false;
    }
  };

  const logout = () => {
    setUser(null);
    // Clear tokens, etc.
  };

  onMount(async () => {
    // Check for existing session
    try {
      const response = await fetch('/api/me');
      if (response.ok) {
        const userData = await response.json();
        setUser(userData);
      }
    } catch (error) {
      console.error('Session check failed:', error);
    } finally {
      setLoading(false);
    }
  });

  const authValue = {
    user,
    login,
    logout,
    loading,
    isAuthenticated: () => !!user()
  };

  return (
    <AuthContext.Provider value={authValue}>
      {props.children}
    </AuthContext.Provider>
  );
}

function LoginForm() {
  const { login, loading } = useContext(AuthContext);
  const [email, setEmail] = createSignal('');
  const [password, setPassword] = createSignal('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    await login(email(), password());
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        type="email" 
        value={email()} 
        onInput={(e) => setEmail(e.target.value)}
        placeholder="Email"
        disabled={loading()}
      />
      <input 
        type="password" 
        value={password()} 
        onInput={(e) => setPassword(e.target.value)}
        placeholder="Password"
        disabled={loading()}
      />
      <button type="submit" disabled={loading()}>
        {loading() ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}

function UserProfile() {
  const { user, logout } = useContext(AuthContext);
  
  return (
    <div>
      <h2>Welcome, {user().name}!</h2>
      <p>Email: {user().email}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

## Multiple Contexts

```javascript
import { createContext, useContext, createSignal } from 'solid-js';

const ThemeContext = createContext('light');
const LanguageContext = createContext('en');

function App() {
  const [theme, setTheme] = createSignal('dark');
  const [language, setLanguage] = createSignal('en');

  return (
    <ThemeContext.Provider value={theme()}>
      <LanguageContext.Provider value={language()}>
        <Settings onThemeChange={setTheme} onLanguageChange={setLanguage} />
        <Content />
      </LanguageContext.Provider>
    </ThemeContext.Provider>
  );
}

function Settings(props) {
  const theme = useContext(ThemeContext);
  const language = useContext(LanguageContext);

  return (
    <div>
      <h2>Settings</h2>
      <p>Current theme: {theme}</p>
      <p>Current language: {language}</p>
      <button onClick={() => props.onThemeChange(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
      <button onClick={() => props.onLanguageChange(language === 'en' ? 'es' : 'en')}>
        Toggle Language
      </button>
    </div>
  );
}

function Content() {
  const theme = useContext(ThemeContext);
  const language = useContext(LanguageContext);

  const messages = {
    en: 'Hello World',
    es: 'Hola Mundo'
  };

  return (
    <div style={{ 
      background: theme === 'dark' ? '#333' : '#fff',
      color: theme === 'dark' ? '#fff' : '#333',
      padding: '20px'
    }}>
      <h1>{messages[language]}</h1>
    </div>
  );
}
```

## Key Points

- Use context to avoid prop drilling for deeply nested components
- Context values can be reactive (signals, memos, etc.)
- Always provide a default value for better error handling
- Context providers can be nested for multiple contexts
- Use `useContext` to consume context values in child components
- Context is perfect for themes, authentication, language settings, etc.
