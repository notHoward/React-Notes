## Components, Instances, and Elements

### Three Key Concepts

| Concept | Description |
|---------|-------------|
| React Component | JavaScript function or class that describes a piece of UI |
| Component Instance | Created when React renders a component; has its own state and props |
| React Element | Immutable object returned from React.createElement() |

### Relationship Flow

```
React component -> Component instance -> React element -> DOM element
```

### Code Examples

**React Component**

```javascript
const Button = (props) => {
  return <div onClick={props.onClick}>{props.children}</div>;
};
```

**Component Instance**

```javascript
const buttonInstance = new Button({
  onClick: () => {
    console.log('Button clicked!');
  },
  children: 'Click me!',
});
```

**React Element**

```javascript
const buttonElement = React.createElement(Button, {
  onClick: () => {
    console.log('Button clicked!');
  },
  children: 'Click me!',
});
```

**DOM Element**

```html
<div onClick="console.log('Button clicked!')">Click me!</div>
```

### How React Renders Components

When React renders a component:
1. Creates a component instance for each time the component is used
2. Calls the render method on each instance
3. Returns a React element
4. Converts React element to DOM element
5. Renders to screen

---

## Instances and Elements in Practice

### Component Instances vs React Elements

**Component Instance**

```javascript
const buttonInstance = new Button({
  onClick: () => {
    console.log('Button clicked!');
  },
  children: 'Click me!',
});
```

**React Element**

```javascript
const buttonElement = React.createElement(Button, {
  onClick: () => {
    console.log('Button clicked!');
  },
  children: 'Click me!',
});
```

### Calling Components Directly (Not Recommended)

```javascript
// Direct call - NOT recommended
const tabContentElement = tabContent({
  item: {
    content: 'This is the content of the tab.',
  },
});
```

### Rendering Components Inside JSX (Recommended)

```javascript
// JSX rendering - Recommended
const tabContentElement = <tabContent item={{ content: 'This is the content of the tab.' }} />;
```

### Important Rule

Never call React components directly. Always render them inside JSX to ensure React sees them as component instances.

---

## How Rendering Works: Overview

### Three Phases of Rendering

| Phase | Description |
|-------|-------------|
| Triggering a render | Initial render or state update |
| Render phase | React calls component functions, figures out DOM updates |
| Commit phase | React updates DOM based on render results |

### Triggering a Render

A render is triggered when:
- The application starts up (initial render)
- A state update occurs in one or more component instances

### Render Phase

- React calls component functions
- Figures out how to update the DOM
- Does NOT actually update the DOM yet

### Commit Phase

- React updates the DOM based on render phase results
- Visual changes to UI are made here

### Browser Repaint

Once React updates the DOM, the browser repaints the screen. This is not React's responsibility.

### Additional Details

| Detail | Description |
|--------|-------------|
| Scheduling | Renders are scheduled when JavaScript engine has free time |
| Batching | Multiple state updates in same function trigger one render |
| Full tree | React looks at entire component tree, not just updated component |

---

## How Rendering Works: The Render Phase

### React Component Communication Methods

| Method | Description |
|--------|-------------|
| Props | One-way data flow from parent to child |
| State | Owned by defining component, accessed by children via props |
| Context | Share data between unrelated components |
| Refs | Access underlying DOM element |
| Callbacks | Pass functions from parent to child |

### Props Example

```javascript
// Parent component
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <Child count={count} />
    </div>
  );
}

// Child component
function Child({ count }) {
  return <div>{count}</div>;
}
```

### State Example

```javascript
// Parent component
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <Child count={count} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// Child component
function Child({ count }) {
  return <div>{count}</div>;
}
```

### Context Example

```javascript
// Create a context object
const ThemeContext = createContext();

// Parent component
function Parent() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={theme}>
      <Child />
    </ThemeContext.Provider>
  );
}

// Child component
function Child() {
  const theme = useContext(ThemeContext);
  return <div>{theme}</div>;
}
```

### Refs Example

```javascript
// Parent component
function Parent() {
  const inputRef = useRef();

  function focusInput() {
    inputRef.current.focus();
  }

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus input</button>
    </div>
  );
}
```

---

## How Rendering Works: The Commit Phase

### Two Main Phases

| Phase | Description |
|-------|-------------|
| Render Phase | Creates new virtual DOM tree, identifies updates needed |
| Commit Phase | Applies DOM updates identified in render phase |

### Code Example

```javascript
import React from 'react';

const Button = () => {
  const [count, setCount] = React.useState(0);

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <button onClick={handleClick}>Click me {count} times</button>
  );
};

export default Button;
```

### Phase Characteristics

| Phase | Synchronous/Asynchronous | Purpose |
|-------|-------------------------|---------|
| Render Phase | Asynchronous | Prioritize, split work, pause/resume |
| Commit Phase | Synchronous | All DOM updates performed at once |

---

## How Diffing Works

### Two Fundamental Assumptions

| Assumption | Description |
|------------|-------------|
| Different types | Two elements of different types produce different trees |
| Stable keys | Elements with stable key stay the same across renders |

### Different Elements at Same Position

```javascript
// Before
const element = <div><SearchBar /></div>;

// After
const element = <header><ProfileMenu /></header>;

// The <SearchBar /> component will be destroyed and removed from the DOM.
```

### Same Element at Same Position

```javascript
// Before
const element = <div><SearchBar /></div>;

// After
const element = <div><SearchBar /></div>;

// The <SearchBar /> component will be kept in the DOM.
```

### Attribute Updates

```javascript
// Before
const element = <div className="my-class"><SearchBar /></div>;

// After
const element = <div className="my-other-class"><SearchBar /></div>;

// The DOM element attribute will be updated.
```

### Key Prop

```javascript
// Before
const element = <ul><li>Item 1</li><li>Item 2</li></ul>;

// After
const element = <ul><li key="1">Item 1</li><li key="2">Item 2</li></ul>;

// The <li> elements will be kept in the DOM.
```

---

## Diffing Rules in Practice

### Tab Component Example

```javascript
import React from 'react';

const Tab = ({ tabNum, onTabChange }) => {
  const [isHidden, setIsHidden] = React.useState(true);
  const [likes, setLikes] = React.useState(4);

  const handleLikeClick = () => {
    setLikes(likes + 1);
  };

  const handleHideClick = () => {
    setIsHidden(true);
  };

  if (tabNum === 3) {
    return <DifferentContent />;
  }

  return (
    <div>
      <h1>Tab {tabNum}</h1>
      <button onClick={handleHideClick}>Hide details</button>
      {!isHidden && <p>This is the content for tab {tabNum}.</p>}
      <button onClick={handleLikeClick}>Likes: {likes}</button>
    </div>
  );
};

export default Tab;
```

### Key Points

- Component instance remains in same position → state preserved
- Component instance replaced → state reset

---

## The Key Prop

### When to Use Key Prop

| Use Case | Purpose |
|----------|---------|
| Lists | Improve performance by identifying moved elements |
| Reset state | Tell React to create new component instance |

### Key Prop in Lists

```javascript
const listItems = [
  { id: 1, name: 'Item 1' },
  { id: 2, name: 'Item 2' },
];

return (
  <ul>
    {listItems.map((item) => (
      <li key={item.id}>{item.name}</li>
    ))}
  </ul>
);
```

### Key Prop to Reset State

```javascript
class Question extends React.Component {
  state = {
    answer: '',
  };

  render() {
    return (
      <div>
        <p>Question: {this.props.question}</p>
        <input type="text" value={this.state.answer} />
      </div>
    );
  }
}

function App() {
  const [question, setQuestion] = React.useState('What is the capital of France?');

  return (
    <div>
      <Question key={question} question={question} />
    </div>
  );
}
```

---

## Resetting State With the Key Prop

### Tab Component with Key Prop

```javascript
class TabContent extends React.Component {
  state = {
    isHidden: true,
    likes: 0,
  };

  render() {
    const { content } = this.props;

    return (
      <div>
        <h1>{content.title}</h1>
        <button onClick={() => this.setState({ isHidden: true })}>
          Hide details
        </button>
        {!this.state.isHidden && <p>{content.summary}</p>}
        <button onClick={() => this.setState({ likes: this.state.likes + 1 })}>
          Likes: {this.state.likes}
        </button>
      </div>
    );
  }
}

function App() {
  const [currentTab, setCurrentTab] = React.useState(0);
  const tabs = [
    {
      title: 'Tab 1',
      content: {
        summary: 'This is the content for tab 1.',
      },
    },
    {
      title: 'Tab 2',
      content: {
        summary: 'This is the content for tab 2.',
      },
    },
  ];

  return (
    <div>
      <ul>
        {tabs.map((tab, index) => (
          <li key={index} onClick={() => setCurrentTab(index)}>
            {tab.title}
          </li>
        ))}
      </ul>
      <TabContent key={currentTab} content={tabs[currentTab]} />
    </div>
  );
}
```

### How It Works

When tab changes, key prop changes → React treats component as new instance → state resets.

---

## Using the Key Prop to Fix Our Eat-'N-Split App

### Problem

Bill value not reset when moving from one friend to another.

### Solution

Make each component instance unique by providing a key that changes across re-renders.

### Code Fix

```javascript
// FormSplitBill.js
const FormSplitBill = ({ selectedfriend }) => {
  return (
    <form>
      <input type="number" placeholder="Bill amount" />
      <button type="submit">Split bill</button>
    </form>
  );
};

// App.js
import FormSplitBill from './FormSplitBill';

const App = () => {
  const [selectedFriend, setSelectedFriend] = useState(null);

  return (
    <div>
      <h1>Eat-'N-Split</h1>
      <FormSplitBill key={selectedFriend?.id} selectedfriend={selectedFriend} />
    </div>
  );
};
```

### Tips for Choosing Unique Keys

| Do | Don't |
|----|-------|
| Use unique identifier (database ID) | Use non-unique values |
| Use stable values | Use indexes (if list can reorder) |
| Use meaningful IDs | Use random numbers |

---

## Rules for Render Logic: Pure Components

### What is Render Logic?

Code that determines what is displayed on the screen. Executed by the render method of a React component.

### Render Logic vs Event Handlers

| Render Logic | Event Handlers |
|--------------|----------------|
| Executed independently of user interactions | Executed when user interacts with UI |
| Determines what to display | Responds to user actions |

### Rules for Render Logic

| Rule | Description |
|------|-------------|
| Pure | No state modification, same result for same inputs |
| Declarative | Describe what should be rendered, not how |
| Side-effect free | No operations that change outside world |

### Code Example

```javascript
function MyComponent({ name }) {
  return <h1>Hello, {name}!</h1>;
}
```

### Best Practices

| Practice | Description |
|----------|-------------|
| Use components | Organize render logic into components |
| Conditional rendering | Display different content based on state |
| Loops | Render lists of items with map |
| Refs | Access DOM elements when needed |

---

## State Update Batching

### What is State Update Batching?

React batches state updates in the same event handler into a single state update. Only one render and commit triggered.

### Key Points

| Point | Description |
|-------|-------------|
| Asynchronous | Updated state not available immediately after setState |
| React 18+ | Automatic batching for all state updates |
| React 17- | Automatic batching only in event handlers |

### Code Example

```javascript
function MyComponent() {
  const [answer, setAnswer] = useState('');
  const [best, setBest] = useState('');
  const [solved, setSolved] = useState(false);

  function reset() {
    setAnswer('');
    setBest('');
    setSolved(false);

    // answer still holds current state here
    console.log(answer); // ''
  }
}
```

### Batching Behavior

| React Version | Where Batching Happens |
|---------------|------------------------|
| React 17 and earlier | Inside event handlers only |
| React 18 and later | All state updates |

---

## State Update Batching in Practice

### Example

```javascript
// This code will only cause the component to re-render once
const [count, setCount] = useState(0);

function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
}
```

### Best Practices

| Practice | Description |
|----------|-------------|
| Use batching | When updating multiple state variables quickly |
| Callback functions | Use when updating state based on current state |
| Event handlers | Ensure batching in React 17 and earlier |

---

## How Events Work in React

### React Event Delegation

All events are delegated to the root DOM container and handled by React. More performant than attaching handlers to each element.

### Synthetic Events

Wrappers around native DOM event objects. Same interface but fix browser inconsistencies.

### Differences from Vanilla JavaScript

| Aspect | React | Vanilla JavaScript |
|--------|-------|---------------------|
| Event prop naming | camelCase (onClick) | lowercase without 'on' prefix (click) |
| Prevent default | event.preventDefault() | return false |

### Code Example

```javascript
// React
const onClick = (event) => {
  event.preventDefault();
};

function Button() {
  return <button onClick={onClick}>Click me!</button>;
}

// Vanilla JavaScript
const button = document.querySelector('button');

button.addEventListener('click', () => {
  event.preventDefault();
});
```

---

## Libraries vs Frameworks & The React Ecosystem

### Frameworks

| Feature | Description |
|---------|-------------|
| Complete structure | Everything needed for large-scale application |
| Batteries included | Routing, styling, HTTP requests built-in |
| Trade-off | Stuck with framework's tools and conventions |
| Examples | Next.js, Remix, Gatsby |

### Libraries

| Feature | Description |
|---------|-------------|
| Piece of code | Shared for other developers to use |
| React is a view library | Only draws components onto UI |
| Freedom | Choose exactly the libraries you like |
| Examples | React Router, React Query, Redux, styled-components, Tailwind CSS |

### Comparison Table

| Feature | Framework | Library |
|---------|-----------|---------|
| Completeness | Complete structure | Not complete, need additional libraries |
| Tools | Fixed set | Flexible, choose what you like |
| Examples | Next.js, Remix, Gatsby | React Router, Redux, Tailwind |

### Recommendation

| Experience Level | Recommendation |
|------------------|----------------|
| New to React | Start with a library |
| Experienced with React | Consider using a framework |

---

## Practical Takeaways

### Key Takeaways

| Concept | Key Point |
|---------|-----------|
| Components | Blueprint for UI; instances are physical manifestations |
| Rendering | Calling component functions, not writing to DOM |
| Re-rendering | Happens when state changes or parent re-renders |
| Reconciliation | Comparing current DOM to desired DOM |
| Diffing | Deciding which DOM elements need changes |
| Keys | Uniquely identify React elements |
| Nesting components | Avoid to prevent performance and state issues |
| Render logic | No side effects |
| Side effects | Only in event handlers and useEffect |
| Committing | Updating DOM (done by ReactDOM) |
| Batching | Multiple state updates applied at once |
| Asynchronous state | State not updated immediately |
| Synthetic events | Ensure consistent behavior across browsers |
| React is a library | Flexibility to choose third-party libraries |

### Important Rules

- Declare new components at top level of file, never inside another component
- Render logic should contain no side effects
- Never call React components directly - always use JSX
