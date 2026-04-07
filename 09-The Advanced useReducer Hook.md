## Yet Another Hook: useReducer

### What is useReducer?

The useReducer hook is a more advanced way of managing state than useState. It works with a reducer function, which is a pure function that takes the previous state and an action and returns the next state.

### Basic Syntax

```javascript
import React, { useReducer } from "react";

const initialState = {
  count: 0,
};

const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return {
        ...state,
        count: state.count + 1,
      };
    case "decrement":
      return {
        ...state,
        count: state.count - 1,
      };
    case "set_count":
      return {
        ...state,
        count: action.payload,
      };
    default:
      return state;
  }
};

const App = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h1>Counter</h1>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>Increment</button>
      <button onClick={() => dispatch({ type: "decrement" })}>Decrement</button>
      <input
        type="number"
        value={state.count}
        onChange={(e) => dispatch({ type: "set_count", payload: e.target.value })}
      />
    </div>
  );
};

export default App;
```

### Benefits of useReducer

| Benefit | Description |
|---------|-------------|
| Scalability | Better for complex state logic |
| Performance | More performant in some cases |
| Centralized logic | All state updates in one place |

### When to Use useReducer

| Situation | Recommendation |
|-----------|----------------|
| Complex state logic | Use useReducer |
| Next state depends on previous | Use useReducer |
| Performance critical | Consider useReducer |
| Simple state | Use useState |

---

## Managing Related Pieces of State

### Adding Step State to Reducer

**Update Initial State**

```javascript
const initialState = {
  count: 0,
  step: 1,
};
```

**Update Reducer Function**

```javascript
const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return {
        ...state,
        count: state.count + state.step,
      };
    case "decrement":
      return {
        ...state,
        count: state.count - state.step,
      };
    case "set_count":
      return {
        ...state,
        count: action.payload,
      };
    case "set_step":
      return {
        ...state,
        step: action.payload,
      };
    default:
      throw new Error("Unknown action");
  }
};
```

**Update Event Handlers**

```javascript
function handleIncrement() {
  dispatch({ type: "increment" });
}

function handleDecrement() {
  dispatch({ type: "decrement" });
}

function handleSetStep() {
  const step = parseInt(event.target.value);
  dispatch({ type: "set_step", payload: step });
}
```

### Resetting State

**Add Reset Action**

```javascript
const reducer = (state, action) => {
  switch (action.type) {
    // ... other cases
    case "reset":
      return initialState;
    default:
      throw new Error("Unknown action");
  }
};
```

**Add Reset Handler**

```javascript
function handleReset() {
  dispatch({ type: "reset" });
}
```

---

## Managing State With useReducer

### Key Concepts

| Concept | Description |
|---------|-------------|
| Reducer | Pure function that takes state and action, returns next state |
| Action | Object describing how state should be updated (type + payload) |
| Dispatch | Function used to trigger state updates |
| State | Current state object returned by useReducer |

### Reducer Rules

| Rule | Description |
|------|-------------|
| Pure function | Cannot mutate state or have side effects |
| Returns new state | Always return a new state object |
| Handles all actions | Use default case for unknown actions |

### Action Structure

```javascript
// Action without payload
{ type: "increment" }

// Action with payload
{ type: "set_count", payload: 5 }
```

### Analogy

| Part | Analogy |
|------|---------|
| State | Bank vault |
| Dispatch | Customer |
| Reducer | Bank teller |
| Action | Withdrawal request |

---

## Loading Questions from a Fake API

### Setting Up json-server

```bash
npm install json-server
json-server --watch data/questions.json
```

### Reducer for Data Fetching

```javascript
const initialState = {
  questions: [],
  status: "loading", // loading, ready, error
};

const reducer = (state, action) => {
  switch (action.type) {
    case "dataReceived":
      return {
        ...state,
        questions: action.payload,
        status: "ready",
      };
    case "dataFailed":
      return {
        ...state,
        status: "error",
      };
    default:
      throw new Error("Unknown action");
  }
};
```

### Component with Data Fetching

```javascript
const MyComponent = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  useEffect(() => {
    fetch("http://localhost:8000/questions")
      .then(response => response.json())
      .then(data => dispatch({ type: "dataReceived", payload: data }))
      .catch(error => dispatch({ type: "dataFailed" }));
  }, []);

  if (state.status === "loading") {
    return <div>Loading...</div>;
  }

  if (state.status === "error") {
    return <div>Error fetching questions</div>;
  }

  return (
    <ul>
      {state.questions.map(question => (
        <li key={question.id}>{question.text}</li>
      ))}
    </ul>
  );
};
```

---

## Handling Loading, Error, and Ready Status

### Status Types

| Status | Description |
|--------|-------------|
| loading | Initial state, fetching data |
| ready | Data loaded, ready to start |
| error | Error occurred during fetch |

### Conditional Rendering

```javascript
const status = useState("loading");

return (
  <div>
    {status === "loading" && <Loader />}
    {status === "ready" && <StartScreen />}
    {status === "error" && <Error />}
  </div>
);
```

### Derived State for Number of Questions

```javascript
const questions = useState([/* quiz questions */]);

// Derived state - calculated, not stored
const numQuestions = questions.length;

return (
  <StartScreen numQuestions={numQuestions} />
);
```

---

## Setting Up a Timer With useEffect

### Adding Timer State to Reducer

```javascript
// reducer.js
export const reducer = (state, action) => {
  switch (action.type) {
    case "tick": {
      return {
        ...state,
        secondsRemaining: state.secondsRemaining - 1,
      };
    }
    case "finish": {
      return {
        ...state,
        status: "finished",
      };
    }
    default:
      return state;
  }
};
```

### Timer Component

```javascript
// Timer.js
import { useDispatch } from "react-redux";

const Timer = ({ secondsRemaining }) => {
  const dispatch = useDispatch();
  const intervalId = useRef();

  useEffect(() => {
    intervalId.current = setInterval(() => {
      dispatch({ type: "tick" });
    }, 1000);

    return () => {
      clearInterval(intervalId.current);
    };
  }, []);

  return <div>{secondsRemaining}</div>;
};
```

### Checking for Timer End

```javascript
// reducer.js - check if timer reached zero
case "tick": {
  if (state.secondsRemaining === 0) {
    return {
      ...state,
      status: "finished",
    };
  }
  return {
    ...state,
    secondsRemaining: state.secondsRemaining - 1,
  };
}
```

### Rendering Finished Screen

```javascript
// App.js
const App = () => {
  const { status } = useSelector((state) => state);

  if (status === "finished") {
    return <div>Game over!</div>;
  }

  return <div><Timer /></div>;
};
```

---

## useState vs. useReducer

### Comparison Table

| Feature | useState | useReducer |
|---------|----------|------------|
| Ideal for | Single independent pieces of state | Related or complex state |
| Logic location | Event handlers, effects | Centralized in reducer |
| Style | More imperative | More declarative |
| Ease of use | Easy | Requires writing reducer |

### Decision Framework

| Question | Yes → | No → |
|----------|-------|------|
| Do you only need one piece of state? | useState | Next question |
| Do states need to be updated together? | Consider useReducer | Next question |
| Are you willing to write a reducer? | Consider useReducer | useState |
| Need 3-4+ related state pieces? | useReducer | Next question |
| Too many confusing event handlers? | useReducer | useState |

### Default Choice

**useState should be your default choice.** Only switch to useReducer when useState causes problems.

### Example Decision

```javascript
// Game example - multiple related states updated together
// USE useReducer

const reducer = (state, action) => {
  switch (action.type) {
    case "startGame": {
      return {
        ...state,
        status: "active",
        questions: questions,
        currentQuestion: 0,
      };
    }
    default:
      return state;
  }
};

// App component
const App = () => {
  const dispatch = useDispatch();
  const { status } = useSelector((state) => state);

  return (
    <div>
      <button onClick={() => dispatch({ type: "startGame" })}>
        Start Game
      </button>
    </div>
  );
};
```

### Summary

| Use useState When | Use useReducer When |
|-------------------|---------------------|
| Simple state | Complex state logic |
| Independent pieces | Related pieces of state |
| Few state updates | Many state updates |
| Default choice | useState causes problems |

