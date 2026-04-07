## React Hooks and Their Rules

### What are React Hooks?

React Hooks are special functions that allow us to use state and other React features in function components. They were introduced in React 16.8.

### Why are React Hooks Useful?

| Benefit | Description |
|---------|-------------|
| Concise code | Write more readable code than class components |
| Simplicity | Easier to understand and maintain |
| Reusability | Custom hooks can share logic across components |

### The Two Rules of React Hooks

| Rule | Description |
|------|-------------|
| Top level only | Hooks can only be called at the top level of a component (not inside conditions, loops, or nested functions) |
| React functions only | Hooks can only be called from React functions (not regular JavaScript functions or class components) |

### How Hooks Work

React Hooks rely on a linked list of hooks associated with each component instance. This linked list tracks the order in which hooks were called.

### Why Same Order on Every Render?

Hooks need to be called in the same order on every render so React can correctly associate each hook with its value. The order uniquely identifies the hook.

### Code Example (Violates Rules)

```javascript
function MyComponent() {
  if (true) {
    const [stateA, setStateA] = useState(23); // VIOLATION: conditional hook
  }

  const [stateB, setStateB] = useState('');

  useEffect(() => {
    // ...
  }, [stateA]);

  return (
    <div>
      <h1>{stateA}</h1>
      <h1>{stateB}</h1>
    </div>
  );
}
```

---

## The Rules of Hooks in Practice

### Breaking the Rules Example

```javascript
function MovieDetails() {
  const imdbRating = 8.7;

  if (imdbRating > 8) {
    const [isTop, setIsTop] = useState(true); // VIOLATION
  }

  return (
    <div>
      <h1>{imdbRating}</h1>
    </div>
  );
}
```

### Error Message

When a conditional hook is called, React throws an error indicating that the order of hooks has changed.

### Important Notes

| Note | Description |
|------|-------------|
| ESLint can warn | ESLint can detect conditional hook usage |
| Early returns | Can also break the linked list of hooks |
| Use custom hooks | For conditional logic that needs hooks |

---

## More Details of useState

### Important Details

| Detail | Description |
|--------|-------------|
| Initial value only on first render | useState only looks at initial value on initial render |
| Updates are asynchronous | State updates are batched for performance |
| Callback for updated state | Use callback to access updated state after set |
| Callback for initialization | Use callback for expensive initial state computation |

### Code Example 1: Initial Value Only on First Render

```javascript
const isTop = imdbRating > 8; // false on first render

// useState only looks at initial value on initial render
const [isTopState, setIsTopState] = useState(isTop);

console.log(isTopState); // false
```

### Code Example 2: Asynchronous State Updates

```javascript
const [avgRating, setAvgRating] = useState(0);

// setAvgRating is asynchronous
setAvgRating(imdbRating);

console.log(avgRating); // 0 (not updated yet)
```

### Code Example 3: Callback for Updated State

```javascript
const [avgRating, setAvgRating] = useState(0);

setAvgRating((currentAvgRating) => {
  return currentAvgRating + imdbRating;
});

console.log(avgRating); // 8.6
```

### Code Example 4: Callback for Initialization

```javascript
const [avgRating, setAvgRating] = useState(() => {
  return imdbRating;
});
```

---

## Initializing State With a Callback (Lazy Initial State)

### Persisting Watch List in Local Storage

**Store watched movies in local storage when updated**

```javascript
useEffect(() => {
  const watched = JSON.stringify(watchedMovies);
  localStorage.setItem('watched', watched);
}, [watchedMovies]);
```

**Read data from local storage on mount**

```javascript
const [watchedMovies, setWatchedMovies] = useState(() => {
  const watched = localStorage.getItem('watched');
  if (watched) {
    return JSON.parse(watched);
  } else {
    return [];
  }
});
```

### Why Use Callback for Initialization

| Reason | Description |
|--------|-------------|
| Executed once | Only runs when component mounts |
| Performance | Avoids expensive computation on every render |
| Local storage | Perfect for reading from localStorage |

---

## useState Summary

### Creating State

| Method | Syntax | When to Use |
|--------|--------|-------------|
| Simple | `useState(initialValue)` | Basic values, no expensive computation |
| Callback | `useState(() => initialValue)` | Expensive computation or reading from storage |

### Updating State

| Method | Syntax | When to Use |
|--------|--------|-------------|
| Simple | `setState(newValue)` | New value independent of current state |
| Callback | `setState(prev => newValue)` | New value depends on current state |

### Important Rule

Never mutate objects or arrays when updating state. Create a new object or array with the changes.

```javascript
// WRONG
state.count = 5;
setState(state);

// CORRECT
setState({ ...state, count: 5 });
```

---

## How NOT to Select DOM Elements in React

### Manual DOM Selection (Not Recommended)

```javascript
useEffect(() => {
  const el = document.querySelector('.search');
  el.focus();
}, []);
```

### Problems with Manual Selection

| Problem | Description |
|---------|-------------|
| Not declarative | Imperative approach not idiomatic React |
| Requires classes/IDs | Need to add selectors just for selection |
| Re-selection | Element must be re-selected on each render |

### Solution

Use refs instead of manual DOM selection.

---

## Introducing Another Hook: useRef

### What is a Ref?

A ref is a reference to a DOM element or React element. It is a mutable value that persists across renders.

### How to Use useRef

```javascript
import React, { useRef } from "react";

const ref = useRef();

// Assign to DOM element
<input ref={ref} />
```

### Two Main Use Cases for Refs

| Use Case | Description |
|----------|-------------|
| Persist data across renders | Store data that doesn't trigger re-renders |
| Access DOM elements directly | Focus, dimensions, etc. |

### Differences Between State and Refs

| Characteristic | State | Refs |
|----------------|-------|------|
| Mutability | Immutable | Mutable |
| Causes re-render | Yes | No |
| Updates | Asynchronous | Synchronous |
| Use for rendering | Yes | No |

### When to Use State vs Refs

| Use State For | Use Refs For |
|---------------|--------------|
| Data that affects UI | Data that doesn't affect UI |
| Values that trigger re-renders | Values that persist between renders |
| Form inputs | DOM element references |
| UI state | Timer IDs, previous values |

---

## Refs to Select DOM Elements

### Focusing an Input with useRef

```javascript
import React, { useRef, useEffect } from "react";

const MyComponent = () => {
  const inputRef = useRef();

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return <input ref={inputRef} />;
};
```

### Getting DOM Element Dimensions

```javascript
import React, { useRef } from "react";

const MyComponent = () => {
  const divRef = useRef();

  const dimensions = () => {
    return {
      width: divRef.current.clientWidth,
      height: divRef.current.clientHeight,
    };
  };

  return (
    <div ref={divRef}>
      <p>Width: {dimensions().width}px, Height: {dimensions().height}px</p>
    </div>
  );
};
```

### Getting Selected Item in a List

```javascript
import React, { useRef } from "react";

const MyComponent = () => {
  const listRef = useRef();

  const selectedItem = () => {
    return listRef.current.querySelector(".selected");
  };

  return (
    <ul ref={listRef}>
      <li>Item 1</li>
      <li className="selected">Item 2</li>
      <li>Item 3</li>
    </ul>
  );
};
```

---

## Refs to Persist Data Between Renders

### Storing Click Count with useRef

```javascript
import React, { useRef } from "react";

const MyComponent = () => {
  const clickCountRef = useRef(0);

  const handleClick = () => {
    clickCountRef.current++;
  };

  const handleSubmit = () => {
    const clickCount = clickCountRef.current;
    // Do something with click count
  };

  return (
    <div>
      <button onClick={handleClick}>Click Me</button>
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
};
```

### Storing Timer ID with useRef

```javascript
import React, { useRef } from "react";

const MyComponent = () => {
  const timerIdRef = useRef();

  const startTimer = () => {
    timerIdRef.current = setInterval(() => {
      // Do something
    }, 1000);
  };

  const stopTimer = () => {
    clearInterval(timerIdRef.current);
  };

  return (
    <div>
      <button onClick={startTimer}>Start Timer</button>
      <button onClick={stopTimer}>Stop Timer</button>
    </div>
  );
};
```

---

## What are Custom Hooks? When to Create One?

### What are Custom Hooks?

Custom hooks are JavaScript functions that start with the word `use` and use one or more built-in React hooks. They allow reusing stateful logic between multiple components.

### When to Create a Custom Hook

Create a custom hook when you have logic that:
- Contains one or more built-in React hooks
- Needs to be reused across multiple components
- Is self-contained and non-visual

### Benefits of Custom Hooks

| Benefit | Description |
|---------|-------------|
| Reusability | Reuse logic across multiple components |
| Abstraction | Hide complex logic behind simple API |
| Portability | Share hooks across different projects |
| Testing | Easier to test isolated logic |

### Example: useFetch Custom Hook

```javascript
import { useState, useEffect } from "react";

const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch(url);
        const data = await response.json();
        setData(data);
        setIsLoading(false);
      } catch (error) {
        setError(error);
        setIsLoading(false);
      }
    }

    fetchData();
  }, [url]);

  return { data, isLoading, error };
};

// Usage
const MyComponent = () => {
  const { data, isLoading, error } = useFetch("https://api.example.com/users");
  // ...
};
```

---

## Creating our First Custom Hook: useMovies

### Steps to Extract a Custom Hook

| Step | Description |
|------|-------------|
| 1 | Identify logic to extract (self-contained, reusable, non-visual) |
| 2 | Create new file with function name starting with `use` |
| 3 | Add necessary imports |
| 4 | Move logic into the function |
| 5 | Return values needed by components |
| 6 | Update component to use the custom hook |

### useMovies Custom Hook

```javascript
// useMovies.js
import { useState, useEffect } from "react";

const useMovies = (query) => {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchMovies() {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/search/movie?query=${query}&api_key=YOUR_API_KEY`);
        const data = await response.json();
        setMovies(data.results);
        setIsLoading(false);
      } catch (error) {
        setError(error);
        setIsLoading(false);
      }
    }

    fetchMovies();
  }, [query]);

  return { movies, isLoading, error };
};
```

### Using the Custom Hook

```javascript
// App.js
import React from "react";
import useMovies from "./useMovies";

const App = () => {
  const { movies, isLoading, error } = useMovies("star wars");

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error.message}</div>;
  }

  return (
    <ul>
      {movies.map((movie) => (
        <li key={movie.id}>{movie.title}</li>
      ))}
    </ul>
  );
};
```

---

## Creating useLocalStorageState

### Custom Hook for Local Storage

```javascript
// useLocalStorageState.js
import { useState, useEffect } from "react";

const useLocalStorageState = (key, initialState) => {
  const [state, setState] = useState(() => {
    const storedValue = localStorage.getItem(key);
    return storedValue ? JSON.parse(storedValue) : initialState;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [state, key]);

  return [state, setState];
};
```

### Usage

```javascript
// App.js
import React from "react";
import useLocalStorageState from "./useLocalStorageState";

const App = () => {
  const [watched, setWatched] = useLocalStorageState("watched", []);

  return (
    <ul>
      {watched.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
};
```

### How It Works

| Step | Description |
|------|-------------|
| Initialization | Reads from localStorage or uses initialState |
| Persistence | Saves to localStorage whenever state changes |
| Reusability | Can be used by any component needing persisted state |

---

## Creating useKey

### Custom Hook for Handling Key Presses

```javascript
// useKey.js
import { useEffect } from "react";

const useKey = (key, callback) => {
  useEffect(() => {
    const handleKeyPress = (event) => {
      if (event.key === key) {
        callback(event);
      }
    };

    window.addEventListener("keydown", handleKeyPress);

    return () => {
      window.removeEventListener("keydown", handleKeyPress);
    };
  }, [key, callback]);
};
```

### Usage

```javascript
// App.js
import React from "react";
import useKey from "./useKey";

const App = () => {
  const onCloseMovie = () => {
    // Close the movie
  };

  useKey("Escape", onCloseMovie);

  return (
    <div>
      <h1>Movie App</h1>
      <button onClick={onCloseMovie}>Close Movie</button>
    </div>
  );
};
```

### Benefits of useKey Custom Hook

| Benefit | Description |
|---------|-------------|
| Reusability | Use in any component that needs keyboard shortcuts |
| Cleanup | Automatically removes event listeners |
| Abstraction | Hides event listener complexity |
