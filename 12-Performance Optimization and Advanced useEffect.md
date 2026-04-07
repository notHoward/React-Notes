## Performance Optimization and Wasted Renders

### Overview of React Optimization

Three main areas of React optimization:
1. Preventing wasted renders
2. Improving overall app speed and responsiveness
3. Reducing bundle size

### What is a Wasted Render?

A wasted render occurs when a component re-renders but doesn't cause any changes to the DOM, resulting in unnecessary calculations and potential performance issues.

### Component Re-rendering Triggers

| Trigger | Description |
|---------|-------------|
| State Changes | When a component's state changes |
| Context Changes | When a subscribed context value changes |
| Parent Re-rendering | When parent component re-renders, children also re-render |

### Preventing Wasted Renders

**1. Memoize Components with memo**

```javascript
import React from 'react';
import { memo } from 'react';

const MyComponent = ({ prop }) => {
  return <div>{prop}</div>;
};

const MyMemoizedComponent = memo(MyComponent);
```

**2. Memoize Objects and Functions with useMemo and useCallback**

```javascript
import React, { useState, useMemo, useCallback } from 'react';

const MyComponent = () => {
  const [data, setData] = useState([]);

  const memoizedData = useMemo(() => {
    // Expensive data processing here
    return data;
  }, [data]);

  const memoizedCallback = useCallback(() => {
    // Expensive function here
  }, []);

  return (
    <div>
      {memoizedData.map((item) => <div key={item.id}>{item}</div>)}
      <button onClick={memoizedCallback}>Perform Operation</button>
    </div>
  );
};
```

**3. Pass Elements as Children or Props**

```javascript
import React from 'react';

const MyComponent = ({ children }) => {
  return <div>{children}</div>;
};

const MyApp = () => (
  <MyComponent>
    <div>Child 1</div>
    <div>Child 2</div>
  </MyComponent>
);
```

### Improving Perceived App Speed

| Technique | Description |
|-----------|-------------|
| useMemo | Memoize expensive computations |
| useCallback | Memoize functions between renders |
| useTransition | Delay non-critical updates for smoother animations |

### Reducing Bundle Size

| Technique | Description |
|-----------|-------------|
| Minimize packages | Use fewer third-party packages |
| Code splitting | Load only necessary code on demand |
| Lazy loading | Load components when needed |

### Avoiding Wasted Renders

| Strategy | Description |
|----------|-------------|
| Optimize component structure | Avoid nested components |
| Minimize state updates | Update state only when necessary |
| Memoize components | Use memo, useMemo, useCallback |

---

## The Profiler Developer Tool

### What is the Profiler?

React Developer Tools Profiler analyzes how components render and re-render to identify performance bottlenecks.

### Recording a Profile

1. Click record button in Profiler tab
2. Interact with your application
3. Stop recording to view data

### Viewing Profile Data

| View | Description |
|------|-------------|
| Flamegraph | Visual representation of component hierarchy (more yellow = longer render time) |
| Ranked Table | List of components sorted by render time |

### Reasons Components Re-render

| Reason | Description |
|--------|-------------|
| State Update | Component's own state was updated |
| Context Update | A consumed context value was updated |
| Parent Re-render | Parent component re-rendered |

---

## A Surprising Optimization Trick With Children

### The Problem

When a parent component updates its state, all nested children re-render, even expensive ones.

### The Solution

Pass the expensive component as a prop (children) instead of nesting it directly.

```javascript
// Problem: SlowComponent re-renders when Counter state changes
const Counter = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <SlowComponent />
    </div>
  );
};

// Solution: Pass SlowComponent as children prop
const Counter = ({ children }) => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      {children}
    </div>
  );
};

const App = () => (
  <Counter>
    <SlowComponent />
  </Counter>
);
```

### How It Works

React creates the component instance before rendering the parent component, making it immune to parent state changes.

---

## Understanding memo

### What is Memoization?

Memoization caches the results of pure functions. In React, memo prevents unnecessary re-renders of components.

### The memo Function

```javascript
import { memo } from 'react';

const MemoizedComponent = memo(MyComponent);
```

Memoized components only re-render if their props have changed.

### When to Use Memoization

| Criterion | Description |
|-----------|-------------|
| Heavy component | Computationally expensive to render |
| Frequently rendered | Re-renders often in the application |
| Same props | Props remain unchanged between renders |

### Limitations of Memoization

- Only considers props (not state or context)
- Does shallow comparison of props
- Overuse can be counterproductive

---

## memo in Practice

### Basic Memoization

```javascript
import React from 'react';
import { memo } from 'react';

const Archive = ({ show }) => {
  const posts = new Array(10000).fill({
    id: Math.random(),
    title: 'Lorem ipsum',
    content: 'Dolor sit amet',
  });

  return (
    <div>
      {show && (
        <ul>
          {posts.map((post) => (
            <li key={post.id}>
              <h2>{post.title}</h2>
              <p>{post.content}</p>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
};

const MemoizedArchive = memo(Archive);
```

### Custom Equality Function for Objects

```javascript
const MemoizedArchive = memo(Archive, (prevProps, nextProps) => {
  return (
    prevProps.show === nextProps.show &&
    prevProps.archiveOptions.show === nextProps.archiveOptions.show &&
    prevProps.archiveOptions.title === nextProps.archiveOptions.title
  );
});
```

---

## Understanding useMemo and useCallback

### The Problem

memo does not work as expected when passing objects or functions as props because React considers them new on each render.

### The Solution

| Hook | Purpose |
|------|---------|
| useMemo | Memoize values (objects, arrays, computed values) |
| useCallback | Memoize functions |

### Basic Syntax

```javascript
// Memoize a value
const memoizedValue = useMemo(() => {
  return expensiveComputation();
}, [dependencies]);

// Memoize a function
const memoizedFunction = useCallback(() => {
  doSomething();
}, [dependencies]);
```

### Use Cases

| Use Case | Hook |
|----------|------|
| Making objects stable for memo | useMemo |
| Making functions stable for memo | useCallback |
| Avoiding expensive recalculations | useMemo |
| Memoizing values for dependency arrays | useMemo/useCallback |

---

## useMemo in Practice

### Fixing Object Props

```javascript
import React from 'react';
import { useMemo, memo } from 'react';

const Archive = ({ show, posts }) => {
  const filteredPosts = posts.filter((post) => post.title.includes('Lorem'));

  const archiveOptions = useMemo(() => {
    return {
      show: show,
      title: `Post Archive (${filteredPosts.length})`,
    };
  }, [show, filteredPosts.length]);

  return (
    <div>
      {show && (
        <ul>
          {filteredPosts.map((post) => (
            <li key={post.id}>
              <h2>{post.title}</h2>
              <p>{post.content}</p>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
};

const MemoizedArchive = memo(Archive);
```

---

## useCallback in Practice

### Fixing Function Props

```javascript
import React from 'react';
import { useMemo, useCallback, memo } from 'react';

const Archive = ({ show, posts, handleAddPost }) => {
  const filteredPosts = posts.filter((post) => post.title.includes('Lorem'));

  const archiveOptions = useMemo(() => {
    return {
      show: show,
      title: `Post Archive (${filteredPosts.length})`,
    };
  }, [show, filteredPosts.length]);

  const memoizedHandleAddPost = useCallback(() => {
    handleAddPost();
  }, [handleAddPost]);

  return (
    <div>
      {show && (
        <ul>
          {filteredPosts.map((post) => (
            <li key={post.id}>
              <h2>{post.title}</h2>
              <p>{post.content}</p>
            </li>
          ))}
        </ul>
      )}
      <button onClick={memoizedHandleAddPost}>Add Post</button>
    </div>
  );
};

const MemoizedArchive = memo(Archive);
```

---

## Optimizing Context Re-Renders

### Guidelines for Context Optimization

| Guideline | Description |
|-----------|-------------|
| Memoize direct children | Prevent re-renders of context provider children |
| Memoize context value object | Prevent context recreation |
| Create separate contexts | One context per state to isolate re-renders |

### Memoizing Direct Children

```javascript
import React from 'react';
import { memo } from 'react';

const PostContextProvider = React.createContext();

const PostList = memo(() => {
  // Render list of posts
});

const App = () => {
  return (
    <PostContextProvider.Provider value={{ posts: [] }}>
      <PostList />
    </PostContextProvider.Provider>
  );
};
```

### Memoizing Context Value

```javascript
import React from 'react';
import { memo } from 'react';

const PostContextProvider = React.createContext();

const value = memo(() => ({
  posts: [],
  searchQuery: '',
}));

const App = () => {
  return (
    <PostContextProvider.Provider value={value}>
      <PostList />
    </PostContextProvider.Provider>
  );
};
```

### Separate Contexts for Each State

```javascript
import React from 'react';
import { createContext } from 'react';

const PostContext = createContext({ posts: [] });
const SearchQueryContext = createContext('');

const PostList = () => {
  const { posts } = useContext(PostContext);
  const searchQuery = useContext(SearchQueryContext);
  // Render filtered posts
};

const App = () => {
  return (
    <PostContext.Provider value={{ posts: [] }}>
      <SearchQueryContext.Provider value={''}>
        <PostList />
      </SearchQueryContext.Provider>
    </PostContext.Provider>
  );
};
```

### When to Optimize Context

Only optimize if ALL are true:
- State in context changes all the time
- Context has many consumers
- App is actually slow and laggy

---

## Optimizing Bundle Size With Code Splitting

### What is Code Splitting?

Breaking the bundle into smaller chunks so the browser loads only code needed for the current page.

### React.lazy and Suspense

```javascript
import React, { Suspense, lazy } from 'react';

const Homepage = lazy(() => import('./pages/Homepage'));
const Product = lazy(() => import('./pages/Product'));
const Pricing = lazy(() => import('./pages/Pricing'));
const Login = lazy(() => import('./pages/Login'));
const AppLayout = lazy(() => import('./pages/AppLayout'));
const PageNotFound = lazy(() => import('./pages/PageNotFound'));

const App = () => {
  return (
    <Suspense fallback={<SpinnerFullPage />}>
      <Routes>
        <Route path="/" element={<Homepage />} />
        <Route path="/product" element={<Product />} />
        <Route path="/pricing" element={<Pricing />} />
        <Route path="/login" element={<Login />} />
        <Route path="*" element={<PageNotFound />} />
      </Routes>
    </Suspense>
  );
};
```

### Benefits of Code Splitting

| Benefit | Description |
|---------|-------------|
| Reduced initial load time | Less code to download first |
| Improved perceived performance | Faster page interactive |
| Efficient resource usage | Load only what's needed |
| Easier maintenance | Smaller, focused bundles |

---

## Don't Optimize Prematurely

### Dos

| Do | Description |
|----|-------------|
| Profile first | Identify real performance bottlenecks |
| Memoize expensive renders | Only for heavy components |
| Memoize context value | When context has many consumers and updates often |
| Memoize provider children | Direct child components of context provider |
| Use separate contexts | One for state, one for updates |
| Code split routes | All routes in SPAs |

### Don'ts

| Don't | Description |
|-------|-------------|
| Optimize prematurely | Fix real problems, not hypothetical ones |
| Wrap everything in memo | Only for components that benefit |
| Overuse useMemo/useCallback | Makes code unreadable |
| Optimize context unnecessarily | Only if slow and many consumers |

### Key Principle

Use the profiler to identify performance bottlenecks. Focus on fixing real performance issues, not optimizing everything.

---

## useEffect Rules and Best Practices

### Dependency Array Rules

**Rule 1: Include all reactive values in the dependency array**

Reactive values include: state, props, context values, and any value referencing them.

```javascript
// Correct
useEffect(() => {
  console.log(count);
}, [count]);

// Incorrect - missing dependency
useEffect(() => {
  console.log(count);
}, []);
```

**Rule 2: Do not use objects or arrays as dependencies**

Objects and arrays have different references each render.

```javascript
// Problem
useEffect(() => {
  // effect logic
}, [{ name: 'John' }]); // Re-runs every render

// Solution - use primitive values
useEffect(() => {
  // effect logic
}, [obj.name]);
```

**Rule 3: Use useCallback for helper functions as dependencies**

```javascript
const fetchData = useCallback(() => {
  // fetch logic
}, [dependencies]);

useEffect(() => {
  fetchData();
}, [fetchData]);
```

**Rule 4: Use only primitive values from objects as dependencies**

```javascript
// Instead of passing entire object
useEffect(() => {
  // effect using user.id and user.name
}, [user.id, user.name]);
```

**Rule 5: Use useReducer for multiple related reactive values**

Reducer can help make dependency problems go away.

**Rule 6: Do not include setState or dispatch in dependency array**

React guarantees these functions are stable between renders.

**Rule 7: Use effects as a last resort**

Effects are an escape hatch to step outside of React.

### Common useEffect Overuse Cases

| Case | Better Alternative |
|------|-------------------|
| Responding to user events | Event handlers |
| Fetching data on mount | Data fetching libraries (React Query) |
| Synchronizing state changes | Derived state and event handlers |

---

## Setting State Based on Other State Updates

### Problem

Using useEffect to update state based on other state can cause multiple renders.

### Solution Pattern

```javascript
import React, { useState, useEffect } from 'react';

const calculateDuration = () => {
  // Calculate duration based on number, sets, speed, breakDuration
};

function App() {
  const [number, setNumber] = useState(1);
  const [sets, setSets] = useState(3);
  const [speed, setSpeed] = useState(30);
  const [breakDuration, setBreakDuration] = useState(30);
  const [duration, setDuration] = useState(0);

  useEffect(() => {
    setDuration(calculateDuration());
  }, [number, sets, speed, breakDuration]);

  function handleInc() {
    setDuration(prev => Math.floor(prev) + 1);
  }

  function handleDec() {
    setDuration(prev => Math.max(Math.floor(prev) - 1, 0));
  }

  return (
    <div>
      <p>Duration: {duration}</p>
      <button onClick={handleInc}>Increase</button>
      <button onClick={handleDec}>Decrease</button>
    </div>
  );
}
```

### Consideration

Using useEffect to update state based on another state update can result in multiple renders. Avoid when possible, especially for performance-critical components.

---

## Using Helper Functions In Effects

### Problem

Playing sound in both event handlers and effects causes duplicate sounds and state resets.

### Solution

Move sound-playing logic to a separate effect.

```javascript
const playSound = (allowSound, clickSound) => {
  if (allowSound) {
    clickSound.play();
  }
};

useEffect(() => {
  playSound(allowSound, clickSound);
}, [duration, allowSound]);

// Remove playSound calls from event handlers
```

### Benefits

| Benefit | Description |
|---------|-------------|
| Clear separation | Sound logic isolated from business logic |
| Better understanding | Easier to see hook interactions |
| No duplicates | Single source of sound triggers |

---

## Closures in Effects

### What is a Stale Closure?

A stale closure occurs when a function captures outdated values from the time it was created.

### Problem Example

```javascript
useEffect(() => {
  // This captures the value of 'number' at creation time
  document.title = `Your ${number} exercise workout`;
}, []); // Missing 'number' dependency
```

### Solution

Always include all state variables the effect depends on in the dependency array.

```javascript
useEffect(() => {
  document.title = `Your ${number} exercise workout`;
}, [number]); // Correct
```

### Key Points

| Point | Description |
|-------|-------------|
| Dependency array | Tells React which values the effect needs |
| Snapshot | Effect captures current snapshot of dependencies |
| Stale closure | Occurs when dependencies are missing |
