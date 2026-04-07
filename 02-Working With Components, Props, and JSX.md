## Rendering the Root Component and Strict Mode

### Creating a React App from Scratch

**Importing React and ReactDOM**

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
```

**Defining the App Component**

```javascript
function App() {
  return <h1>Hello React!</h1>;
}
```

**Rendering the App Component to the DOM**

```javascript
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

**Using Strict Mode**

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

**React v18 Rendering**

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

**React v17 Rendering**

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<App />, document.getElementById('root'));
```

---

## Components as Building Blocks

### Key Concepts

| Concept | Description |
|---------|-------------|
| Component | A piece of UI with its own data, logic, and appearance |
| Component Tree | Hierarchical representation of all components |
| Parent Component | A component that contains other components |
| Child Component | A component contained within another component |
| Nesting | Placing components inside other components |
| Props | Passing data from parent to child |

### Code Example

```javascript
function Question(props) {
  return (
    <div className="question">
      <h2>{props.title}</h2>
      <p>{props.body}</p>
    </div>
  );
}

function VideoPlayer() {
  return (
    <div className="video-player">
      <video src="..." controls></video>
    </div>
  );
}

function Menu() {
  return (
    <ul className="menu">
      <li><a href="#">Home</a></li>
      <li><a href="#">About</a></li>
      <li><a href="#">Contact</a></li>
    </ul>
  );
}

function RefinedQuestions() {
  return (
    <div className="refined-questions">
      <Filters />
      <Question title="What is the capital of France?" body="Paris" />
      <Question title="What is the largest country in the world?" body="Russia" />
      <Question title="What is the highest mountain in the world?" body="Mount Everest" />
    </div>
  );
}

function App() {
  return (
    <div className="app">
      <VideoPlayer />
      <Menu />
      <RefinedQuestions />
    </div>
  );
}
```

### Summary

Components are essential for building complex and reusable user interfaces. Understanding components, component trees, parent/child relationships, nesting, and props helps you create React applications that are easy to manage and maintain.

---

## Creating and Reusing a Component

### Creating a New Component

```jsx
function Pizza() {
  return (
    <div>
      <h2>Pizza</h2>
      <p>Spinach</p>
      <img src="/public/spinaci.jpg" alt="Spinach pizza" />
    </div>
  );
}
```

### Including the Component

```jsx
import Pizza from './Pizza';

function App() {
  return (
    <div>
      <Pizza />
      <Pizza />
      <Pizza />
    </div>
  );
}
```

### Using Starter Data

```jsx
import Pizza from './Pizza';
import data from './data';

function App() {
  return (
    <div>
      {data.pizzas.map((pizza) => (
        <Pizza
          key={pizza.id}
          name={pizza.name}
          ingredients={pizza.ingredients}
        />
      ))}
    </div>
  );
}
```

### Reusing the Component

```jsx
import Pizza from './Pizza';
import data from './data';

function App() {
  return (
    <div>
      {data.pizzas.map((pizza) => (
        <Pizza
          key={pizza.id}
          name={pizza.name}
          ingredients={pizza.ingredients}
        />
      ))}
    </div>
  );
}
```

---

## What is JSX?

### What is JSX?

JSX is an extension to JavaScript that allows us to write HTML-like code in our JavaScript files. This makes it easier to describe the structure of our user interfaces and embed JavaScript expressions directly into HTML code.

### Why is JSX Important in React?

React is declarative - we describe what the UI should look like, and React updates the DOM. JSX is key to this declarative nature.

### How Does JSX Work?

When we write JSX, it is converted to JavaScript code by Babel. This JavaScript code tells React how to create the corresponding DOM nodes.

### Imperative vs Declarative Programming

| Type | Description |
|------|-------------|
| Imperative | Write step-by-step instructions on how to achieve a result |
| Declarative | Describe what we want, and the system figures out how to achieve it |

### JSX Example

```jsx
<div>
  <h1>Hello, world!</h1>
  <p>This is a paragraph.</p>
  <button onClick={() => console.log('Button clicked!')}>Click me</button>
</div>
```

### How JSX is Converted to JavaScript

```javascript
React.createElement('div', null, [
  React.createElement('h1', null, 'Hello, world!'),
  React.createElement('p', null, 'This is a paragraph.'),
  React.createElement('button', { onClick: () => console.log('Button clicked!') }, 'Click me'),
]);
```

---

## Creating More Components

### Header Component

```javascript
function Header() {
  return (
    <header>
      <h1>Fast React Pizza Company</h1>
    </header>
  );
}
```

### Menu Component

```javascript
function Menu() {
  return (
    <div className="menu">
      <h2>Our Menu</h2>
      <Pizza />
      <Pizza />
      <Pizza />
      <Pizza />
    </div>
  );
}
```

### Pizza Component

```javascript
function Pizza() {
  return (
    <div className="pizza">
      <img src="pizza.jpg" alt="Pizza" />
      <h3>Margherita</h3>
      <p>A classic pizza with tomato sauce, mozzarella cheese, and basil.</p>
      <p>$12.99</p>
    </div>
  );
}
```

### Footer Component

```javascript
function Footer() {
  return (
    <footer>
      <p>We're currently open. <br/> {new Date().toLocaleTimeString()}</p>
    </footer>
  );
}
```

### App Component

```javascript
function App() {
  return (
    <div className="App">
      <Header />
      <Menu />
      <Footer />
    </div>
  );
}
```

### Key Points

- JSX allows HTML-like code directly in JavaScript files
- React components are reusable pieces of UI
- React uses a virtual DOM to efficiently update the UI

---

## JavaScript Logic in Components

### Writing Logic Inside Components

- Components are just JavaScript functions
- Code executes when the component is initialized
- You can create variables and use any JavaScript

### Example: Displaying Open/Closed Status

```javascript
const hour = new Date().getHours();
const openHour = 12;
const closeHour = 22;

let isOpen;
if (hour >= openHour && hour <= closeHour) {
  isOpen = true;
} else {
  isOpen = false;
}

console.log(isOpen);
```

### Important Notes

| Concept | Explanation |
|---------|-------------|
| Strict Mode | Components render twice, so alerts may appear twice |
| Blocking JavaScript | alert() blocks execution until dismissed |
| Variable Scope | Variables declared outside if/else are accessible throughout |

---

## Separation of Concerns

### Why React Components Combine HTML, CSS, and JavaScript

Traditional web development used separate files for HTML, CSS, and JavaScript. With SPAs becoming more complex, JavaScript became tightly coupled with UI logic.

React components encapsulate data, logic, and appearance in a single unit. This follows the principle of co-location: things that change together should be placed close together.

### What a React Component Includes

| Part | Description |
|------|-------------|
| Data | State or props representing information to display |
| Logic | JavaScript functions handling events and updates |
| Appearance | JSX defining HTML structure and CSS styles |

### Separation of Concerns in React

Each component is responsible for a specific piece of the UI, encapsulating its data, logic, and appearance. This promotes code reusability and maintainability.

### Advantages of React Components

| Advantage | Description |
|-----------|-------------|
| Encapsulation | Self-contained units |
| Reusability | Can be reused across the application |
| Maintainability | Easier to manage and update |
| Declarative Style | Focus on desired state, not how to achieve it |

---

## Styling React Applications

### Key Points

- React components can contain CSS styles
- Many ways to apply CSS to React applications
- React doesn't care how you style components
- Options: inline styling, external CSS, CSS modules, styled components, Tailwind CSS

### Inline Styling

Enter JavaScript mode (curly braces) and create an object with CSS properties.

### External CSS

```jsx
import React from 'react';
import './index.css';

const App = () => {
  return (
    <div className="container">
      <header className="header">
        <h1>React Styling</h1>
      </header>
      <main className="menu">
        <ul>
          <li>Home</li>
          <li>About</li>
          <li>Contact</li>
        </ul>
      </main>
      <footer className="footer">
        <p>&copy; 2023 React Tutorial</p>
      </footer>
    </div>
  );
};

export default App;
```

### External CSS File

```css
.container {
  background-color: #f0f0f0;
  font-family: sans-serif;
  padding: 20px;
}

.header {
  background-color: #007bff;
  color: #fff;
  padding: 20px;
}

.menu {
  background-color: #f5f5f5;
  padding: 20px;
}

.footer {
  background-color: #333;
  color: #fff;
  padding: 20px;
  text-align: center;
}
```

### Important Note

Global styles are not scoped to each particular component. Use `className` instead of `class` in JSX.

---

## Passing and Receiving Props

### Introducing Props

Props allow passing data between components. They act as a communication channel between parent and child components.

### Passing Props from Parent to Child

Define props using HTML attribute-like syntax:

```javascript
<Pizza name="Pizza Spinaci" image="pizza-spinaci.jpg" ingredients="Tomato, Spinach, Mozzarella" price={10} />
```

### Receiving Props in Child Component

Accept a `props` parameter in the child component:

```javascript
function Pizza(props) {
  return (
    <div className="pizza">
      <img src={props.photoName} alt={props.name} />
      <div>
        <h2>{props.name}</h2>
        <p>{props.ingredients}</p>
        <span>Price: {props.price}</span>
      </div>
    </div>
  );
}
```

### CSS for Pizza Components

```css
.pizza {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.pizza img {
  width: 200px;
  height: auto;
  margin-bottom: 10px;
}

.pizza h2 {
  font-size: 24px;
  margin-bottom: 5px;
}

.pizza p {
  font-size: 16px;
  margin-bottom: 10px;
}

.pizza span {
  font-size: 18px;
  font-weight: bold;
}
```

### Flexibility of Props

Props can be any data type: objects, arrays, functions, or even other React components.

---

## Props, Immutability, and One-Way Data Flow

### Props vs State

| Aspect | Props | State |
|--------|-------|-------|
| Ownership | Parent component | Component itself |
| Mutability | Read-only, immutable | Can be updated |
| Data Flow | Parent to child only | Internal to component |

### Reasons for One-Way Data Flow

| Reason | Description |
|--------|-------------|
| Predictability | Easier to understand where data comes from |
| Debugging | Simpler to track data changes |
| Performance | More efficient than two-way binding |

---

## The Rules of JSX

### General JSX Rules

| Rule | Description |
|------|-------------|
| JavaScript Mode | Use curly braces to enter JavaScript mode |
| Expressions Only | Place any JavaScript expression, not statements |
| JSX as Expression | JSX produces a JavaScript expression |

### Single Root Element Rule

A piece of JSX can only have one root element. To return multiple elements, use React Fragments.

### JSX vs HTML Differences

| JSX | HTML |
|-----|------|
| Uses curly braces for JavaScript | No JavaScript mode |
| `className` instead of `class` | `class` attribute |
| Requires closing tags for self-closing elements | Some tags don't need closing |
| camelCase for attributes | lowercase attribute names |

---

## Rendering Lists

### Rendering Lists with map

Use the `map` method on an array to create a new array of components.

### Example

```jsx
const pizzas = [
  { name: "Pizza Spinaci", photoName: "pizza-spinaci.jpg", ingredients: "Tomato, Spinach, Mozzarella", price: 10 },
  { name: "Pizza Funghi", photoName: "pizza-funghi.jpg", ingredients: "Tomato, Mushrooms, Mozzarella", price: 12 },
  { name: "Pizza Margherita", photoName: "pizza-margherita.jpg", ingredients: "Tomato, Mozzarella", price: 9 },
];

const Pizza = (props) => (
  <div className="pizza">
    <img src={props.photoName} alt={props.name} />
    <div>
      <h2>{props.name}</h2>
      <p>{props.ingredients}</p>
      <span>Price: {props.price}</span>
    </div>
  </div>
);

const PizzaList = () => (
  <ul className="pizzas">
    {pizzas.map((pizza) => (
      <li key={pizza.name}>
        <Pizza {...pizza} />
      </li>
    ))}
  </ul>
);
```

### Why Not Use for Loops

JSX requires a consistent return value, which is not possible with for loops. Use `map` instead.

---

## Conditional Rendering With &&

### How It Works

The && operator uses short-circuiting. If the first operand is falsy, the second operand is not evaluated.

### Example

```jsx
const isOpen = currentHour >= 12 && currentHour <= 22;

return (
  <div>
    {isOpen && <p>Open until {closingHour}</p>}
  </div>
);
```

### Important Note

Do not use a number as the condition. Always use a true/false condition.

---

## Conditional Rendering With Ternaries

### Ternary Operator Syntax

```jsx
condition ? valueIfTrue : valueIfFalse
```

### Example

```jsx
const pizzas = pizzaData.length > 0 ? (
  <ul>
    {pizzas.map((pizza) => (
      <li key={pizza.name}>
        <h3>{pizza.name}</h3>
        <p>Price: ${pizza.price}</p>
      </li>
    ))}
  </ul>
) : (
  <p>We're still working on our menu. Please come back later.</p>
);
```

### Advantages of Ternary Operator

- More explicit than &&
- Can display an alternative when condition is false

### Why Not Use if-else

if-else statements do not produce a value. JSX requires a value to be returned.

---

## Conditional Rendering With Multiple Returns

### How It Works

Use multiple return statements inside a component, each returning different JSX based on a condition.

### Example

```jsx
function Pizza(props) {
  if (props.pizza.soldOut) {
    return null;
  }

  return (
    <li>
      <h3>{props.pizza.name}</h3>
      <p>Price: ${props.pizza.price}</p>
    </li>
  );
}
```

### Advantages

| Advantage | Description |
|-----------|-------------|
| Flexibility | Return different blocks or components |
| Clarity | Explicitly shows what renders under each condition |

### When to Use

Typically used for conditionally rendering entire components rather than small pieces of JSX.

---

## Extracting JSX Into a New Component

### Example

```jsx
// Footer.js
function Footer() {
  const openHour = 12;
  const closingHour = 22;

  return (
    <footer>
      <Order closingHour={closingHour} />
      <p>We're open until {closingHour}</p>
      <a href="#">Come visit us or order online!</a>
    </footer>
  );
}

// Order.js
function Order({ closingHour }) {
  return (
    <div className="order">
      <p>Open until {closingHour}</p>
      <button className="btn order">Order Now</button>
    </div>
  );
}
```

### Benefits

- Makes code more modular
- Easier to manage
- Promotes reusability

---

## Destructuring Props

### Before Destructuring

```javascript
function Pizza(props) {
  return (
    <div>
      <h2>{props.name}</h2>
      <p>{props.ingredients}</p>
      <span>{props.price}</span>
    </div>
  );
}
```

### After Destructuring

```javascript
function Pizza({ name, ingredients, price }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>{ingredients}</p>
      <span>{price}</span>
    </div>
  );
}
```

### Benefits

| Benefit | Description |
|---------|-------------|
| Conciseness | No need to write props repeatedly |
| Readability | Clear which props the component uses |
| Error Prevention | Clear which props are expected |

---

## React Fragments

### What is a React Fragment

A way to group multiple JSX elements without adding an extra HTML element to the DOM.

### When to Use

- When you want to render multiple elements without a parent element
- When you need to add a key to a list of JSX elements
- To improve code readability

### Syntax Options

**Standard Syntax**

```jsx
<React.Fragment>
  <p>This is a paragraph.</p>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</React.Fragment>
```

**Shorthand Syntax**

```jsx
<>
  <p>This is a paragraph.</p>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</>
```

**With a Key**

```jsx
<React.Fragment key="some-key">
  <li>Item 1</li>
  <li>Item 2</li>
</React.Fragment>
```

---

## Setting Classes and Text Conditionally

### Setting Text Content Conditionally

```jsx
<span>{pizza.soldOut ? "Sold Out" : pizza.price}</span>
```

### Setting Class Names Conditionally

```jsx
<li className={`pizza ${pizza.soldOut ? "sold-out" : ""}`}>
  <h3>{pizza.name}</h3>
  <p>{pizza.price}</p>
</li>
```

### Advantages

| Advantage | Description |
|-----------|-------------|
| Clarity | Clean and readable code |
| Flexibility | Can conditionally set many properties |

---

## Section Summary

### Concepts Learned

| Concept | Description |
|---------|-------------|
| Components | Building blocks of React applications |
| Component Trees | Hierarchical organization of components |
| Props | Share data between components (parent to child) |
| Lists | Created by mapping over arrays |
| Conditional Rendering | Render components only when conditions are met |

### Additional Concepts

- Imperative vs declarative programming
- Separation of concerns
- Immutability of props
- One-way data flow
