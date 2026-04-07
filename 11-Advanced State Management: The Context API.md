## What is the Context API?

### The Problem: Prop Drilling

Passing state down through multiple levels of a component tree can quickly become cumbersome and inconvenient.

### The Solution: Context API

The React Context API allows you to pass state from a parent component into a deeply nested child component without manually passing props down the component tree.

### Three Main Components of Context API

| Component | Description |
|-----------|-------------|
| Provider | Special React component that gives all child components access to a value |
| Consumer | React component that reads the value from a provider |
| Context object | Object that represents the context and is used to create provider and consumer components |

### How It Works

1. Create a context object using `createContext()`
2. Create provider component that wraps components needing access
3. Pass context value to provider as prop
4. Consume context in components using `useContext()` hook

### Basic Example

```javascript
// Create a context object
const ThemeContext = React.createContext({
  theme: "light",
});

// Create a provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Create a consumer component
function ThemeConsumer() {
  const { theme } = React.useContext(ThemeContext);
  return <div style={{ color: theme }}>This is my content.</div>;
}

// Render the app
function App() {
  return (
    <ThemeProvider>
      <ThemeConsumer />
    </ThemeProvider>
  );
}
```

### Benefits of Context API

| Benefit | Description |
|---------|-------------|
| Reduces prop drilling | Makes code more organized |
| Easier state sharing | Share state across multiple components |
| Centralized management | Creates centralized state management |

### Best Practices

| Practice | Description |
|----------|-------------|
| Use for shared state | Only for state needed across multiple components |
| Avoid for local state | Don't use for state within single component |
| Separate file | Create separate file for context objects |
| Descriptive names | Use clear names for context objects and values |
| Document | Document context objects and values |

---

## Creating and Providing a Context

### Steps to Implement Context

| Step | Action |
|------|--------|
| 1 | Create context using createContext() |
| 2 | Create provider component wrapping needed components |
| 3 | Pass context value to provider as prop |
| 4 | Consume context using useContext() hook |

### Example: Post Context

```javascript
// Create a new context
const PostContext = React.createContext({
  posts: [],
  onClearPosts: () => {},
  onAddPost: () => {},
  searchQuery: "",
});

// Create a provider component
function PostProvider({ children }) {
  const [posts, setPosts] = useState([]);
  const [searchQuery, setSearchQuery] = useState("");

  const handleClearPosts = () => {
    setPosts([]);
  };

  const handleAddPost = () => {
    // Add a new post to the posts array
  };

  return (
    <PostContext.Provider value={{ posts, onClearPosts: handleClearPosts, onAddPost: handleAddPost, searchQuery }}>
      {children}
    </PostContext.Provider>
  );
}

// Consume the context in a child component
function ListComponent() {
  const { posts } = useContext(PostContext);

  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

---

## Consuming the Context

### Removing Props with Context API

**Before: Prop Drilling**

```javascript
// Props passed through multiple levels
<App posts={posts} onClearPosts={handleClearPosts} />
  <Main posts={posts} onClearPosts={handleClearPosts} />
    <List posts={posts} onClearPosts={handleClearPosts} />
```

**After: Using Context**

```javascript
// Wrap components in provider
<PostProvider>
  <List />
  <Search />
  <Header />
</PostProvider>

// Consume directly in any component
function ListComponent() {
  const { posts } = useContext(PostContext);
  // Use posts directly
}
```

### Benefits of Refactoring with Context

| Benefit | Description |
|---------|-------------|
| Organized code | State logic centralized |
| Easier sharing | Share state across components |
| Reusable components | Components become more standalone |

---

## Advanced Pattern: A Custom Provider and Hook

### Problem

State and state updating logic spread across multiple components makes code messy.

### Solution

Create a custom Context Provider component and a custom hook for consuming the context.

### Implementation

```javascript
// Create a custom Context Provider component
export function PostProvider({ children }) {
  const [posts, setPosts] = useState([]);
  const [searchQuery, setSearchQuery] = useState("");

  const handleClearPosts = () => {
    setPosts([]);
  };

  const handleAddPost = () => {
    // Add a new post to the posts array
  };

  return (
    <PostContext.Provider value={{ posts, onClearPosts: handleClearPosts, onAddPost: handleAddPost, searchQuery }}>
      {children}
    </PostContext.Provider>
  );
}

// Create a custom hook for consuming the context
export function usePosts() {
  const context = useContext(PostContext);

  if (!context) {
    throw new Error("PostContext was used outside of the PostProvider.");
  }

  return context;
}
```

### Usage

```javascript
// Wrap components in provider
<PostProvider>
  <List />
  <Search />
  <Header />
</PostProvider>

// Consume context using custom hook
function ListComponent() {
  const { posts } = usePosts();
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

### Benefits

| Benefit | Description |
|---------|-------------|
| Organized code | State logic centralized |
| Error prevention | Prevents using context outside provider |
| Cleaner API | Custom hook provides simple interface |

---

## Thinking In React: Advanced State Management

### Types of State

| Category | Types |
|----------|-------|
| Accessibility | Local state, Global state |
| Domain | UI state, Remote state |

### State Placement Options

| Option | Use Case |
|--------|----------|
| Local component | useState, useReducer, useRef |
| Parent component | Lift state up |
| Context | Global UI state |
| Third-party library | Redux, React Query, SWR, Zustand |
| URL | Shareable and bookmarkable state |
| Local storage | State stored in user's browser |

### Tools for Managing State

| State Type | Tools |
|------------|-------|
| Local UI state | useState, useReducer |
| Local remote state | fetch + useState/useReducer |
| Global remote state | Context API, Redux, React Query, SWR, RTK Query |
| Global UI state | Context API, Redux |

### Tips for Choosing the Right Tool

| Application Size | Recommendation |
|------------------|----------------|
| Small | fetch inside useEffect + useState/useReducer |
| Bigger | React Query, SWR, or RTK Query for remote state |
| Global UI state | Context API + useState/useReducer |
| Need more performance | Redux for global UI state |

---

## Back to "WorldWise": Creating a CitiesContext

### Custom Cities Context

```javascript
// Create a custom context provider component
import { createContext, useState, useEffect } from "react";

const CitiesContext = createContext();

const CitiesProvider = ({ children }) => {
  const API_URL = "https://api.example.com/cities";

  const [cities, setCities] = useState([]);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const fetchCities = async () => {
      const response = await fetch(API_URL);
      const json = await response.json();
      setCities(json.cities);
      setIsLoading(false);
    };

    fetchCities();
  }, []);

  return (
    <CitiesContext.Provider value={{ cities, isLoading }}>
      {children}
    </CitiesContext.Provider>
  );
};

// Create a custom hook for consuming the context
const useCities = () => {
  const context = useContext(CitiesContext);

  if (!context) {
    throw new Error("CitiesContext was used outside of the CitiesProvider.");
  }

  return context;
};
```

### Usage

```javascript
// Wrap the application component in the CitiesProvider
<CitiesProvider>
  <App />
</CitiesProvider>

// Consume the context in any component using the useCities hook
const App = () => {
  const { cities, isLoading } = useCities();
  // Use cities and isLoading
};
```

---

## Consuming the CitiesContext

### Refactoring Steps

| Step | Action |
|------|--------|
| 1 | Create custom hook for consuming cities context |
| 2 | Import useCities hook into components |
| 3 | Remove cities and isLoading props |
| 4 | Consume state from useCities hook |
| 5 | Verify application still works |

### Example Refactoring

```javascript
// Before: Receiving props
function CityList({ cities, isLoading }) {
  // Component logic
}

// After: Using context
function CityList() {
  const { cities, isLoading } = useCities();
  // Component logic
}
```

---

## Finishing the City View

### City Context Implementation

```javascript
// Create a context provider component for the city state
import { createContext, useState, useEffect } from "react";

const CityContext = createContext();

const CityProvider = ({ children }) => {
  const [currentCity, setCurrentCity] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  const getCity = async (id) => {
    const response = await fetch(`/cities/${id}`);
    const city = await response.json();
    setCurrentCity(city);
    setIsLoading(false);
  };

  useEffect(() => {
    getCity(new URLSearchParams(window.location.search).get("id"));
  }, [window.location.search]);

  return (
    <CityContext.Provider value={{ currentCity, isLoading }}>
      {children}
    </CityContext.Provider>
  );
};

// Create a custom hook for consuming the city context
const useCity = () => {
  const context = useContext(CityContext);

  if (!context) {
    throw new Error("CityContext was used outside of the CityProvider.");
  }

  return context;
};
```

### Benefits

| Benefit | Description |
|---------|-------------|
| Centralized state | City state in CityProvider |
| No prop drilling | Access city state anywhere |
| Auto updates | City updates when URL changes |

---

## Advanced State Management System: Context + useReducer

### Combining Context with useReducer

This pattern creates a powerful and flexible state management system.

### Reducer Function

```javascript
const reducer = (state, action) => {
  switch (action.type) {
    case 'CITIES_LOADED':
      return {
        ...state,
        cities: action.payload,
        isLoading: false,
      };
    case 'CITY_LOADED':
      return {
        ...state,
        currentCity: action.payload,
      };
    case 'CITY_CREATED':
      return {
        ...state,
        cities: [...state.cities, action.payload],
      };
    case 'CITY_DELETED':
      return {
        ...state,
        cities: state.cities.filter(city => city.id !== action.payload),
      };
    default:
      throw new Error(`Unknown action type: ${action.type}`);
  }
};
```

### Initial State

```javascript
const initialState = {
  cities: [],
  isLoading: true,
  currentCity: {},
};
```

### Using useReducer

```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```

### Dispatching Actions

```javascript
dispatch({ type: 'CITIES_LOADED', payload: cities });
```

### Handling Asynchronous Data

```javascript
async function fetchCities() {
  const response = await fetch('/cities');
  const cities = await response.json();
  dispatch({ type: 'CITIES_LOADED', payload: cities });
}
```

### Provider with useReducer

```javascript
const CitiesProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <CitiesContext.Provider value={{ ...state, dispatch }}>
      {children}
    </CitiesContext.Provider>
  );
};
```

---

## Adding Fake Authentication: Setting Up Context

### Auth Context Implementation

```javascript
// AuthContext.js
import React, { createContext, useReducer } from 'react';

const AuthContext = createContext();

const initialState = {
  user: null,
  isAuthenticated: false,
};

const reducer = (state, action) => {
  switch (action.type) {
    case 'LOGIN':
      return {
        ...state,
        user: action.payload,
        isAuthenticated: true,
      };
    case 'LOGOUT':
      return {
        ...state,
        user: null,
        isAuthenticated: false,
      };
    default:
      throw new Error(`Unknown action type: ${action.type}`);
  }
};

const AuthProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <AuthContext.Provider value={{ ...state, dispatch }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = React.useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};

export default AuthContext;
```

---

## Adding Fake Authentication: Implementing Login

### Login and Logout Functions

```javascript
// AuthContext.js - Add login and logout functions
const AuthProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);

  const login = (email, password) => {
    // Fake authentication - hardcoded user
    if (email === 'user@email.com' && password === 'password') {
      dispatch({
        type: 'LOGIN',
        payload: { email, name: 'Test User' }
      });
    }
  };

  const logout = () => {
    dispatch({ type: 'LOGOUT' });
  };

  return (
    <AuthContext.Provider value={{ ...state, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};
```

### Login Button Component

```javascript
import { useAuth } from './AuthContext';

const LoginButton = () => {
  const { login } = useAuth();

  return (
    <button onClick={() => login('user@email.com', 'password')}>
      Login
    </button>
  );
};
```

### Logout Button Component

```javascript
import { useAuth } from './AuthContext';

const LogoutButton = () => {
  const { logout } = useAuth();

  return (
    <button onClick={() => logout()}>
      Logout
    </button>
  );
};
```

### Handling Navigation on Auth Change

```javascript
import { useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuth } from './AuthContext';

const App = () => {
  const { isAuthenticated } = useAuth();
  const navigate = useNavigate();

  useEffect(() => {
    if (isAuthenticated) {
      navigate('/', { replace: true });
    } else {
      navigate('/login');
    }
  }, [isAuthenticated, navigate]);

  // ...
};
```

---

## Adding Fake Authentication: Protecting a Route

### ProtectedRoute Component

```javascript
import React from "react";
import { useAuth } from "./auth";
import { useNavigate } from "react-router-dom";

const ProtectedRoute = ({ children }) => {
  const { isAuthenticated } = useAuth();
  const navigate = useNavigate();

  // Redirect to homepage if not authenticated
  if (!isAuthenticated) {
    navigate("/");
    return null;
  }

  // Otherwise render the children
  return children;
};

export default ProtectedRoute;
```

### Using ProtectedRoute

```javascript
// App.jsx
import React from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import ProtectedRoute from "./ProtectedRoute";
import AppLayout from "./AppLayout";

const App = () => {
  return (
    <Router>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route
          path="/*"
          element={
            <ProtectedRoute>
              <AppLayout />
            </ProtectedRoute>
          }
        />
      </Routes>
    </Router>
  );
};

export default App;
```

### Protecting Specific Routes

```javascript
import React from "react";
import { Route } from "react-router-dom";
import ProtectedRoute from "./ProtectedRoute";
import CitiesPage from "./CitiesPage";

const CitiesRoute = () => {
  return (
    <ProtectedRoute>
      <CitiesPage />
    </ProtectedRoute>
  );
};

export default CitiesRoute;
```

### How It Works

| Step | Description |
|------|-------------|
| 1 | ProtectedRoute checks authentication status |
| 2 | If not authenticated, redirects to homepage |
| 3 | If authenticated, renders the children components |
| 4 | All protected routes are wrapped in ProtectedRoute |

