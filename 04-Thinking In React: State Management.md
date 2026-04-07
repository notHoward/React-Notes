## What is "Thinking in React"?

### Mental Model of React Tools

| Concept | Description |
|---------|-------------|
| Components | Building blocks of UI |
| State | Data that changes over time |
| Props | Data passed from parent to child |
| Data flow | One-way from parent to child |
| Effects | Side effects like data fetching |

### Key Mindset Shifts

| From | To |
|------|-----|
| Element mutations | State transitions |
| Imperative DOM updates | Declarative UI description |
| Separate HTML/CSS/JS | Co-located components |

### Component Tree Example

```javascript
const App = () => {
  return (
    <div>
      <Header />
      <Content />
      <Footer />
    </div>
  );
};

const Header = () => {
  return <h1>My React App</h1>;
};

const Content = () => {
  return <p>This is the content of my React app.</p>;
};

const Footer = () => {
  return <p>Copyright © 2023 My Name</p>;
};
```

### State Management Example

```javascript
const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};
```

### Data Flow Example

```javascript
const Parent = () => {
  const [state, setState] = useState({
    name: 'John Doe',
  });

  return (
    <div>
      <Child state={state} />
    </div>
  );
};

const Child = ({ state }) => {
  const [name, setName] = useState(state.name);

  return (
    <div>
      <p>My name is {name}</p>
      <button onClick={() => setName('Jane Doe')}>Change name</button>
    </div>
  );
};
```

### Key Principles

| Principle | Description |
|-----------|-------------|
| Static version | Build without state first |
| Reusability | Components can be reused |
| Composability | Components can contain other components |
| One-way data flow | Data flows down, events go up |

---

## Fundamentals of State Management

### Types of State

| Type | Description |
|------|-------------|
| Local state | Needed in one component or a few components |
| Global state | Needed across many components in the app |

### Decision Process: When to Create State

```
Does the data change at some point in time?
  |
  +-- No --> Use a regular variable
  |
  +-- Yes --> Can it be computed from existing state or props?
                |
                +-- Yes --> Derive the state (no new state needed)
                |
                +-- No --> Create a new piece of state
```

### Decision Process: Where to Place State

```
Where is the state needed?
  |
  +-- Only in current component --> Leave it there
  |
  +-- Also in a child component --> Pass down via props
  |
  +-- Also in sibling or parent components --> Lift state up to first common parent
  |
  +-- Many components across the app --> Use global state
```

### Benefits of Local State

| Benefit | Description |
|---------|-------------|
| Easier to reason about | Less complexity |
| More performant | Fewer re-renders |
| More scalable | Better code organization |

### When to Use Global State

- State needed by many different components across the app
- State is complex and difficult to manage with local state

---

## Thinking About State and Lifting State Up

### What is Lifting State Up?

Lifting state up is moving state from a child component to its closest common parent component. This is done when multiple sibling components need to access the same state.

### Why Lift State Up?

| Benefit | Description |
|---------|-------------|
| Prevents duplicate state | Single source of truth |
| Centralized management | Easier to maintain |
| Enables sibling communication | Share state between siblings |

### How to Lift State Up

| Step | Action |
|------|--------|
| 1 | Move state variable to closest common parent |
| 2 | Pass state as prop to child components that need it |
| 3 | Update child components to use prop instead of local state |

### Example Code

```javascript
// App component (parent)
const App = () => {
  const [items, setItems] = useState([]);

  const handleAddItems = (newItem) => {
    setItems([...items, newItem]);
  };

  return (
    <div>
      <Form onAddItems={handleAddItems} />
      <PackingList items={items} />
    </div>
  );
};

// Form component (child)
const Form = ({ onAddItems }) => {
  const handleSubmit = (event) => {
    event.preventDefault();
    const newItem = { id: Date.now(), description: "New item" };
    onAddItems(newItem);
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Add Item</button>
    </form>
  );
};

// PackingList component (child)
const PackingList = ({ items }) => {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.description}</li>
      ))}
    </ul>
  );
};
```

---

## Reviewing "Lifting Up State"

### Why Lift State Up?

React has one-way data flow - data can only flow down from parents to children. To share state between sibling components, we lift state up to their common parent.

### Child-to-Parent Communication

Child-to-parent communication allows child components to update state in the parent component.

**How it works:**
1. Parent creates a setter function
2. Parent passes the setter function as a prop to child
3. Child calls the setter function when needed

### Example Code

```javascript
// Checkout component (parent)
const Checkout = () => {
  const [coupons, setCoupons] = useState([]);

  return (
    <div>
      <Promotions setCoupons={setCoupons} />
      <Total coupons={coupons} />
    </div>
  );
};

// Promotions component (child)
const Promotions = ({ setCoupons }) => {
  const handleAddCoupon = (event) => {
    event.preventDefault();
    const newCoupon = { id: Date.now(), code: "SAVE10" };
    setCoupons([...coupons, newCoupon]);
  };

  return (
    <form onSubmit={handleAddCoupon}>
      <button type="submit">Add Coupon</button>
    </form>
  );
};

// Total component (child)
const Total = ({ coupons }) => {
  const discount = coupons.length * 10;
  return <p>Discount: ${discount}</p>;
};
```

---

## Deleting an Item: More Child-to-Parent Communication

### Goal

Delete items from a list by clicking a delete button next to each item.

### Implementation Steps

| Step | Action |
|------|--------|
| 1 | Create handleDeleteItem function in parent component |
| 2 | Pass function as prop to child component |
| 3 | Call function in child component when delete is clicked |

### Example Code

```javascript
// App component
const App = () => {
  const [items, setItems] = useState([]);

  const handleDeleteItem = (id) => {
    setItems(items.filter((item) => item.id !== id));
  };

  return (
    <PackingList items={items} onDeleteItem={handleDeleteItem} />
  );
};

// PackingList component
const PackingList = ({ items, onDeleteItem }) => {
  return (
    <ul>
      {items.map((item) => (
        <Item key={item.id} item={item} onDeleteItem={onDeleteItem} />
      ))}
    </ul>
  );
};

// Item component
const Item = ({ item, onDeleteItem }) => {
  return (
    <li>
      {item.name}
      <button onClick={() => onDeleteItem(item.id)}>X</button>
    </li>
  );
};
```

---

## Updating an Item: Complex Immutable Data Operation

### Goal

Toggle the packed status of items using a checkbox.

### How to Toggle Packed Status

1. Create a new array from the current array
2. Toggle the packed status of the item with the specified ID
3. Set state to the new array

### Example Code

```javascript
// App component
const App = () => {
  const [items, setItems] = useState([]);

  const toggleItem = (id) => {
    setItems(items.map((item) => {
      if (item.id === id) {
        return { ...item, packed: !item.packed };
      }
      return item;
    }));
  };

  return (
    <PackingList items={items} onToggleItem={toggleItem} />
  );
};

// PackingList component
const PackingList = ({ items, onToggleItem }) => {
  return (
    <ul>
      {items.map((item) => (
        <Item key={item.id} item={item} onToggleItem={onToggleItem} />
      ))}
    </ul>
  );
};

// Item component
const Item = ({ item, onToggleItem }) => {
  return (
    <li>
      <input
        type="checkbox"
        checked={item.packed}
        onChange={() => onToggleItem(item.id)}
      />
      {item.name}
    </li>
  );
};
```

### Key Pattern

When updating an item in an array, use map to create a new array with the updated item. Never mutate the original array directly.

---

## Derived State

### What is Derived State?

Derived state is state that is computed from an existing piece of state or props.

### Benefits of Derived State

| Benefit | Description |
|---------|-------------|
| Reduces complexity | Eliminates dependent state variables |
| Improves performance | Avoids unnecessary re-renders |
| Prevents bugs | Single source of truth |

### Example Without Derived State (Bad)

```javascript
const [cart, setCart] = useState([]);
const [numItems, setNumItems] = useState(0);
const [totalPrice, setTotalPrice] = useState(0);

// Must update all three manually
setCart([...cart, { name: 'Item 1', price: 10 }]);
setNumItems(numItems + 1);
setTotalPrice(totalPrice + 10);
```

### Example With Derived State (Good)

```javascript
const [cart, setCart] = useState([]);
const numItems = cart.length;
const totalPrice = cart.reduce((sum, item) => sum + item.price, 0);

// Only update cart - derived values update automatically
setCart([...cart, { name: 'Item 1', price: 10 }]);
```

### When to Use Derived State

| Situation | Action |
|-----------|--------|
| Value can be computed from existing state | Derive it |
| Value cannot be computed from existing state | Create new state |

---

## Calculating Statistics as Derived State

### Example: Packing List Statistics

Calculate number of items, packed items, and percentage from the items state.

### Code Example

```javascript
const Stats = ({ items }) => {
  // Derived state
  const numItems = items.length;
  const packedItems = items.filter(item => item.packed);
  const percentagePacked = (packedItems.length / numItems) * 100;

  // Early return for empty list
  if (!items.length) {
    return <p className="footer">Start adding some items to your packing list!</p>;
  }

  return (
    <div className="stats">
      <span>
        You have {numItems} items on your list, {packedItems.length} of which are packed
        ({percentagePacked}% packed).
      </span>
    </div>
  );
};
```

### Early Returns

An early return is a conditional statement that returns from a function early if a certain condition is met. This simplifies code by avoiding unnecessary calculations.

---

## Sorting Items

### Feature

Allow users to sort items by three criteria:
- Input order (original)
- Description (alphabetical)
- Packed status

### Implementation

```javascript
const PackingList = ({ items, onDeleteItem, onToggleItem }) => {
  const [sortBy, setSortBy] = useState('input');

  // Derived state - sorted items
  const sortedItems = sortBy === 'input'
    ? items
    : sortBy === 'description'
      ? items.slice().sort((a, b) => a.description.localeCompare(b.description))
      : items.slice().sort((a, b) => a.packed - b.packed);

  return (
    <div>
      <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
        <option value="input">Sort by input order</option>
        <option value="description">Sort by description</option>
        <option value="packed">Sort by packed status</option>
      </select>
      <ul>
        {sortedItems.map((item) => (
          <Item
            key={item.id}
            item={item}
            onDeleteItem={onDeleteItem}
            onToggleItem={onToggleItem}
          />
        ))}
      </ul>
    </div>
  );
};
```

### Benefits of Derived State for Sorting

| Benefit | Description |
|---------|-------------|
| No extra state | Sorted items computed on demand |
| Performance | Only recalculates when sortBy changes |
| Simplicity | No need to keep sorted array in sync |

---

## Clearing the List

### Goal

Add a button to clear all items from the list with a confirmation message.

### Implementation

```javascript
const App = () => {
  const [items, setItems] = useState(['shorts', 'charger']);

  const handleClearList = () => {
    const confirmed = window.confirm('Are you sure you want to delete all items?');
    if (confirmed) {
      setItems([]);
    }
  };

  return (
    <div>
      <PackingList items={items} />
      <button onClick={handleClearList}>Clear list</button>
    </div>
  );
};
```

### Key Points

| Point | Description |
|-------|-------------|
| Confirmation | Use window.confirm to prevent accidental deletion |
| Set to empty array | Clear by setting state to [] |
| Event handler | Function passed to onClick prop |

---

## Moving Components Into Separate Files

### Steps to Split Components

| Step | Action |
|------|--------|
| 1 | Identify different components in App.js |
| 2 | Create a new file for each component |
| 3 | Cut and paste component code into new file |
| 4 | Export component from each file |
| 5 | Import components into App.js |

### Folder Structure

```
src/
├── App.js
├── components/
│   ├── Logo.js
│   ├── Form.js
│   ├── PackingList.js
│   ├── Item.js
│   └── Stats.js
```

### Example Files

**App.js**

```javascript
import Logo from './components/Logo';
import Form from './components/Form';
import PackingList from './components/PackingList';
import Stats from './components/Stats';

function App() {
  return (
    <div>
      <Logo />
      <Form />
      <PackingList />
      <Stats />
    </div>
  );
}

export default App;
```

**Logo.js**

```javascript
export default function Logo() {
  return <img src="/logo.png" alt="Logo" />;
}
```

**Form.js**

```javascript
import { useState } from 'react';

export default function Form({ onAddItems }) {
  const [description, setDescription] = useState('');

  function handleSubmit(e) {
    e.preventDefault();
    if (!description) return;
    onAddItems({ id: Date.now(), description, packed: false });
    setDescription('');
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={description}
        onChange={(e) => setDescription(e.target.value)}
      />
      <button type="submit">Add</button>
    </form>
  );
}
```

---

## The "children" Prop: Making a Reusable Button

### What is the Children Prop?

The children prop is a special prop automatically passed to every React component. It contains all JSX elements between the component's opening and closing tags.

### Basic Example

```javascript
function Button({ children }) {
  return (
    <button className="button">
      {children}
    </button>
  );
}

// Usage
<Button>
  <span>Click Me</span>
</Button>
```

### Reusable Button Component

```javascript
function Button({ children, onClick }) {
  return (
    <button className="button" onClick={onClick}>
      {children}
    </button>
  );
}

// Usage with different content
<Button onClick={handlePrevious}>
  <span>Previous 👈</span>
</Button>

<Button onClick={handleNext}>
  <span>Next 👉</span>
</Button>

<Button onClick={handleSubmit}>
  Submit Form
</Button>
```

### StepMessage Component Example

```javascript
// StepMessage.js
function StepMessage({ step, children }) {
  return (
    <div className="step-message">
      <h3>Step {step}</h3>
      {children}
    </div>
  );
}

export default StepMessage;

// Usage in App.js
<StepMessage step={1}>
  <p>This is the content for step 1</p>
  <button>Learn more</button>
</StepMessage>

<StepMessage step={2}>
  <p>This is the content for step 2</p>
  <button>Take a quiz</button>
</StepMessage>
```

### When to Use Children Prop

| Situation | Recommendation |
|-----------|----------------|
| Component needs to render unknown content | Use children |
| Creating wrapper or layout components | Use children |
| Making reusable button or card components | Use children |
| Content is fixed and known | Use regular props |

### Benefits of Children Prop

| Benefit | Description |
|---------|-------------|
| Flexibility | Component can render any JSX |
| Reusability | Same component used for different content |
| Composition | Build complex UIs from simple components |

