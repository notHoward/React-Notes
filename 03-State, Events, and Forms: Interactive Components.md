## Handling Events the React Way

### Declarative Approach

Instead of using the imperative addEventListener method, React takes a declarative approach to event handling. Event handlers are specified directly on elements using props like onClick or onMouseEnter. Event handlers are functions, not function calls.

### Button Click Event Example

```jsx
<button onClick={() => alert("Previous")}>Previous</button>
```

The onClick prop specifies the event handler function. The arrow function executes when the button is clicked.

### Hover Event Example

```jsx
<button onMouseEnter={() => alert("TEST")}>Hover</button>
```

The onMouseEnter prop specifies the event handler for mouse hover events.

### Defining Event Handler Functions Separately

It is common practice to define event handler functions separately from JSX markup to keep code organized.

```jsx
function handlePrevious() {
  alert("Previous");
}

<button onClick={handlePrevious}>Previous</button>
```

### Key Points

| Concept | Description |
|---------|-------------|
| Declarative | Event handlers specified directly on elements |
| Function Reference | Pass the function, not the function call |
| Organization | Define handlers separately from JSX |
| State | Needed to change values based on events |

---

## What is State in React?

### What is State?

State is data that a component can hold over time. It stores information that a component needs to remember throughout its lifecycle. State is the "memory" of a component.

### Examples of State

| Example | Description |
|---------|-------------|
| Notification count | Number of unread notifications |
| Input field text | Content typed by user |
| Active tab | Currently selected tab |
| Shopping cart | Items added by user |

### Key Characteristics of State

| Characteristic | Description |
|----------------|-------------|
| User-changeable | Can be modified by user interaction |
| Persistent | Lasts throughout component lifecycle |
| Component-specific | Belongs to a single component |

### Updating State Triggers Re-rendering

Whenever state is updated, React re-renders the component, creating a new updated view on the screen.

### State as a Synchronization Tool

State is how React synchronizes the user interface with data. Changing the state changes the UI.

### Benefits of State

| Benefit | Description |
|---------|-------------|
| Updates view | Re-renders component automatically |
| Persists data | Maintains local variables between renders |

---

## Creating a State Variable With useState

### Creating the State Variable

Use the useState hook to define a state variable.

```javascript
const [step, setStep] = useState(1);
```

Destructure the returned array to access the state variable and setter function.

```javascript
const currentStep = step;
const setStepFunction = setStep;
```

### Using State in the Component

```javascript
<span>{currentStep}</span>

<button onClick={() => setStepFunction(currentStep + 1)}>Next</button>
<button onClick={() => setStepFunction(currentStep - 1)}>Previous</button>
```

### Updating State in Event Handlers

```javascript
function handleNext() {
  setStepFunction(step + 1);
}

function handlePrevious() {
  setStepFunction(step - 1);
}
```

### Preventing Invalid State Updates

Add conditions to prevent invalid state updates.

```javascript
function handleNext() {
  if (step < 3) {
    setStepFunction(step + 1);
  }
}

function handlePrevious() {
  if (step > 1) {
    setStepFunction(step - 1);
  }
}
```

### Hook Rules

| Rule | Description |
|------|-------------|
| Top level | Hooks must be called at top level of component |
| No conditions | Cannot be inside if statements or loops |
| No nested functions | Cannot be inside nested functions |
| Use setter | Only update state using the setter function |

### Advantages of React State Management

| Advantage | Description |
|-----------|-------------|
| No DOM manipulation | Avoids imperative DOM updates |
| Reusability | Enhances component reusability |
| Predictability | Enables maintainable state updates |

---

## Don't Set State Manually

### React's Immutable State Approach

React enforces immutability for state management. Direct mutation of state variables is not allowed. State updates must be performed using setter functions.

### Breaking React by Mutating State

Attempting to mutate state directly will not cause an error but will prevent updates from being reflected in the UI. React relies on the setter function to recognize state changes and trigger re-renders.

### Proper State Update Mechanism

Use the setter function to update state values. Pass the new state value to the setter function.

### Code Examples

**Direct Mutation (Incorrect)**

```javascript
const [step, setStep] = useState(1);

handleNext() {
  step++; // Direct mutation - WRONG
}
```

**Correct State Update Using Setter Function**

```javascript
handleNext() {
  setStep(step + 1); // Correct
}
```

**Object Mutation (Incorrect)**

```javascript
const [test, setTest] = useState({ name: 'Jonas' });

handleNext() {
  test.name = 'Fred'; // Direct mutation - WRONG
}
```

**Correct State Update with New Object**

```javascript
handleNext() {
  setTest({ name: 'Fred' }); // Correct
}
```

### Core Principle

Treat state as immutable in React. Avoid direct state mutation and always use the provided setter functions.

---

## The Mechanics of State

### React's Declarative Approach

React updates the UI by re-rendering components instead of directly manipulating the DOM. This separates UI representation from underlying data.

### Re-rendering as the Mechanism for UI Updates

React re-renders a component whenever its underlying data changes, including state updates. Re-rendering creates a new view for the component, replacing the old view.

### Preserving Component State During Re-rendering

React maintains the component's state across re-renders. State persists even when the component is re-rendered multiple times.

### Key Points

| Concept | Description |
|---------|-------------|
| Re-rendering | Creates new view when data changes |
| State persistence | State maintained across re-renders |
| Automatic updates | State changes trigger re-rendering automatically |
| Synchronization | React keeps UI in sync with data |

---

## Adding Another Piece of State

### Implementing Open and Close Functionality

**Creating the State Variable**

```javascript
const [isOpen, setIsOpen] = useState(true);
```

Initialize isOpen to true as the default value.

**Conditional Rendering**

Use conditional rendering to display or hide content based on state.

```javascript
return (
  <div>
    {isOpen && (
      <div className="steps-container">
        {/* Step content */}
      </div>
    )}
    <button className="close" onClick={() => setIsOpen(!isOpen)}>
      <span>Close</span>
    </button>
  </div>
);
```

### Updating State

Create an event handler to update isOpen to the opposite of its current value.

```javascript
<button onClick={() => setIsOpen(!isOpen)}>Close</button>
```

### Key Points

| Concept | Description |
|---------|-------------|
| State isolation | Changing one component's state doesn't affect others |
| State persistence | State maintained across re-renders |
| Conditional rendering | Display content based on state value |

---

## Updating State Based on Current State

### The Problem with Directly Updating State

Modifying state directly based on its current value can lead to unintended behavior, especially when multiple updates occur in quick succession.

### Using Callback Functions to Update State

React provides a more reliable approach using callback functions. Callback functions receive the current state value as an argument, allowing for updates based on the most up-to-date state information.

### Implementation

1. Replace direct state update with a callback function
2. Pass the callback function instead of a new state value
3. Receive current state as an argument
4. Calculate new state based on current state
5. Return updated state value

### Code Snippets

**Direct state update (NOT RECOMMENDED)**

```javascript
setState(step - 1);
```

**State update using callback function (RECOMMENDED)**

```javascript
setState((prevState) => prevState - 1);
```

### Benefits of Callback Functions

| Benefit | Description |
|---------|-------------|
| Consistency | Ensures predictable updates |
| Reliability | Works correctly with multiple consecutive updates |
| Best practice | Adheres to React guidelines |

---

## More Thoughts About State + State Guidelines

### Key Concepts

**State Isolation**

Each component maintains its own independent state. State changes in one component do not affect other components.

**State as a Reflection of Data**

The entire UI can be viewed as a function of state. The UI reflects the current state of all components.

**Declarative Approach to UI Building**

State provides a declarative way to describe the UI. React handles DOM manipulations based on state changes.

### Practical Guidelines for State Management

| Guideline | Description |
|-----------|-------------|
| Create state for dynamic data | Use state for data that changes over time |
| Update state for dynamic behavior | Update state when behavior or appearance needs to change |
| Visualize state-driven UI | Imagine UI as reflection of state |
| Avoid unnecessary state | Use regular variables for data that doesn't need re-renders |
| Master state management | Essential for advanced React development |

---

## Building a Form and Handling Submissions

### Form Structure and Elements

Use standard HTML form elements to build forms in React. Create a form element with input fields and a submit button.

### Event Handling for Form Submission

Create an event handler function named handleSubmit. Attach it to the form's onSubmit prop. Prevent default form submission using event.preventDefault.

### Code Example

```javascript
// Submit event handler
function handleSubmit(event) {
  event.preventDefault();
  console.log(event.target); // Access form data from event object
}

// Form with event handler
<form onSubmit={handleSubmit}>
  <select>
    {Array.from({ length: 20 }, (_, i) => (
      <option key={i + 1}>{i + 1}</option>
    ))}
  </select>
  <input type="text" placeholder="Item Description" />
  <button type="submit">Add</button>
</form>
```

### Key Takeaways

| Concept | Description |
|---------|-------------|
| Standard HTML forms | Use regular form elements |
| Event handlers | Handle form submissions with onSubmit |
| Prevent default | Use event.preventDefault() to avoid page reload |
| Controlled elements | Better approach for form data management |

---

## Controlled Elements

### Concept and Purpose

Controlled elements manage form input data in React. Instead of relying on the DOM to store input values, controlled elements store values in the component's state. This provides better control over form data and enables validation.

### Implementation Steps

| Step | Description |
|------|-------------|
| 1 | Create state variables for each input field |
| 2 | Bind state variables to input values using value prop |
| 3 | Attach onChange handlers to update state |

### Example Code

```javascript
// State variables for form data
const [description, setDescription] = useState('');
const [quantity, setQuantity] = useState(5);

// Controlled input elements
<input 
  type="text" 
  value={description} 
  onChange={(event) => setDescription(event.target.value)} 
/>

<select 
  value={quantity} 
  onChange={(event) => setQuantity(Number(event.target.value))}
>
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
</select>
```

### Benefits of Controlled Elements

| Benefit | Description |
|---------|-------------|
| Centralized state | Form data stored in one location |
| Validation | Easy to validate input values |
| Error handling | Display error messages easily |
| Data consistency | State updates automatically reflected in UI |

---

## State vs. Props

### Key Differences

| Aspect | State | Props |
|--------|-------|-------|
| Ownership | Internal data owned by component | External data owned by parent component |
| Mutability | Mutable, can be updated by component | Read-only, cannot be modified by receiving component |
| Purpose | Makes components interactive | Configures child components |
| Re-rendering | Causes re-render when updated | Causes re-render when updated if passed as prop |

### Example Code

```javascript
// Parent component
const [upVotes, setUpVotes] = useState(0);

<Question upVotes={upVotes} />

// Child component
function Question(props) {
  const { upVotes } = props;
  return (
    <div>
      <p>This question has {upVotes} upvotes.</p>
      <button onClick={() => setUpVotes(upVotes + 1)}>Upvote</button>
    </div>
  );
}
```

### When State and Props Interact

If a piece of state is passed as a prop to a child component and the state is updated, both the component owning the state and the component receiving the state as a prop will be re-rendered. This ensures the child component stays in sync with the updated state.

### Conclusion

| Concept | Purpose |
|---------|---------|
| State | Manage internal data, make components interactive |
| Props | Pass data from parent to child, configure behavior |

