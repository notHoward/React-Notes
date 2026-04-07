## Why Do Front-End Frameworks Exist?

### The Evolution of Web Development

**Before 2010: Server-Side Rendering (SSR)**
- Websites were assembled on the server
- Sent to the browser as complete HTML pages

**After 2010: Client-Side Rendering (CSR)**
- Rendering happens in the browser
- Creates Single-Page Applications (SPAs)
- Provides responsive, dynamic user experiences

### The Problem with Vanilla JavaScript

Building SPAs with plain JavaScript is challenging because:
- Managing data state is complex
- Manipulating the DOM is tedious
- Keeping UI and data in sync is error-prone

### DOM Manipulation Example (Vanilla JS)

```javascript
// Select the element with the id "myElement"
const element = document.getElementById('myElement');

// Toggle the class "active" on the element
element.classList.toggle('active');

// Append a text node to the element
element.appendChild(document.createTextNode('Hello, world!'));

// Modify the element's CSS style
element.style.color = 'red';
```

This code shows how intricate DOM manipulation can become in complex apps.

### How Frameworks Help

Front-end frameworks like React:
- Manage data state automatically
- Handle DOM manipulation for you
- Simplify UI updates
- Provide structured approaches to building SPAs

---

## React vs. Vanilla JavaScript

### The Key Difference

| Aspect | Vanilla JavaScript | React |
|--------|-------------------|-------|
| UI Updates | Manual DOM manipulation | Automatic on state change |
| Data Management | Manual tracking | State variables |
| Bug Risk | Higher in large apps | Lower due to structure |
| Best For | Small, simple projects | Large, complex apps |

### Vanilla JavaScript Implementation

```javascript
// Select DOM elements manually
const adviceElement = document.getElementById('advice');
const buttonElement = document.getElementById('getAdviceButton');
const countElement = document.getElementById('count');

// Get data
const getAdvice = () => {
  // Update state manually
  count++;
  advice = fetchAdvice();

  // Update UI manually (easy to forget or mess up)
  adviceElement.textContent = advice;
  countElement.textContent = count;
};

// Attach event listener
buttonElement.addEventListener('click', getAdvice);
```

### React Implementation

```javascript
// Define state variables
const [advice, setAdvice] = useState('');
const [count, setCount] = useState(0);

// Get data
const getAdvice = () => {
  // Just update state - React handles the UI
  setCount(count => count + 1);
  setAdvice(fetchAdvice());
};

// React automatically updates UI when state changes
<div>
  <h1>Advice: {advice}</h1>
  <button onClick={getAdvice}>Get Advice</button>
  <p>Count: {count}</p>
</div>
```

### When to Use Each

| Project Size | Recommended Tool |
|--------------|------------------|
| Small (a few interactions) | Vanilla JavaScript |
| Medium to Large | React |

---

## What is React?

### Core Concepts

**1. Component-Based**
- Build UIs using reusable components
- Like LEGO blocks for websites
- Each component manages its own data and appearance

**2. Declarative Syntax with JSX**
- Combines HTML, CSS, and JavaScript
- Describes what the UI should look like
- No direct DOM manipulation needed

**3. State Management**
- React tracks application data (state)
- When state changes, React automatically re-renders
- UI always matches the current data

**4. Virtual DOM & Fiber Tree**
- Efficiently updates only what changed
- Minimizes performance overhead
- Ensures smooth user experience

### Benefits of Using React

| Benefit | Description |
|---------|-------------|
| Simplified UI Development | Components make code reusable and maintainable |
| Data-Driven Updates | UI always reflects current data |
| Performance | Virtual DOM optimizes updates |
| Large Community | Many tutorials, docs, and libraries available |
| Job Demand | React skills are highly sought after |

### Popularity

React is the most popular JavaScript library for building user interfaces, with a large community, active development, and extensive third-party ecosystem.

---

## Pure React (Without Tooling)

### Creating a React App from Scratch

**Step 1: Create Project Folder**
- Create a folder named `01-pure-react` on your desktop

**Step 2: Create HTML File (index.html)**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Hello React!</title>
</head>
<body>
  <div id="root"></div>

  <script src="https://unpkg.com/react@17.0.2/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.development.js"></script>
  <script src="script.js"></script>
</body>
</html>
```

**Step 3: Create JavaScript File (script.js)**

```javascript
function App() {
  const [time, setTime] = React.useState(new Date().toLocaleTimeString());

  React.useEffect(() => {
    setInterval(() => {
      setTime(new Date().toLocaleTimeString());
    }, 1000);
  }, []);

  return (
    <header>
      <h1>Hello React!</h1>
      <p>It's {time}</p>
    </header>
  );
}

ReactDOM.createRoot(document.getElementById('root')).render(<App />);
```

**Step 4: Open in Browser**
- Open the `index.html` file in your web browser

### Code Explanation

| Code | What it does |
|------|--------------|
| `ReactDOM.createRoot(...).render(<App />)` | Creates root and renders App component |
| `React.useState()` | Creates state variable (time) |
| `React.useEffect()` | Runs side effect (timer) |
| `setInterval()` | Updates time every second |

### Why Modern Tooling Helps

This pure approach shows React fundamentals, but modern tooling automates:
- Build processes
- Module bundling
- Development servers
- Hot reloading

---

## Setting Up a New React Project: The Options

### Two Main Options

| Option | Description | Best For |
|--------|-------------|----------|
| Create-React-App | Complete starter kit with everything included | Quick setup, small apps |
| Vite | Modern, faster build tool | Larger projects, experienced devs |

### Create-React-App Includes

- Development server
- Webpack for module bundling
- ESLint (code linting)
- Prettier (code formatting)
- Jest (testing)
- Fast refresh (hot module replacement)

### Vite Notes

- Faster than Create-React-App
- Provides hot module replacement
- Requires manual setup of ESLint, Prettier, Jest

### Recommendation

| Stage | Recommended Tool |
|-------|------------------|
| Learning React | Create-React-App |
| Real-world projects | Vite |

### React Frameworks (Next.js, Remix)

These add solutions for:
- Routing
- Data fetching
- Server-side rendering

**Important:** Focus on learning React first. Frameworks come later.

---

## Setting Up a Project With Create-React-App

### Step-by-Step Setup

**1. Create the project**

Open terminal and run:

```bash
npx create-react-app pizza-menu
```

**2. Navigate to project folder**

```bash
cd pizza-menu
```

**3. Start the development server**

```bash
npm start
```

The app will open automatically in your browser.

### File Structure Explained

| File/Folder | Purpose |
|-------------|---------|
| package.json | Project info and dependencies |
| node_modules | Installed npm packages |
| src/ | Main development files (you work here) |
| public/ | Static assets (images, favicon) |

### Hot Module Replacement

1. Make a change to the App component
2. Save the file
3. Changes appear instantly in the browser
4. No page reload needed

This saves time and preserves component state during development.

---

## Summary: React + ReactDOM Together

### The Pizza Shop Example

**Components you create:**

| Component | Purpose |
|-----------|---------|
| PizzaCard | Shows one pizza (name, image, price, button) |
| Menu | Lists multiple PizzaCards |
| Cart | Shows selected items and total price |

### How They Work Together

**Step 1: Write the component (React)**

```javascript
function PizzaCard({ name, image, price }) {
  return (
    <div className="pizza-card">
      <img src={image} alt={name} />
      <h2>{name}</h2>
      <p>Price: ${price}</p>
      <button>Add to Cart</button>
    </div>
  );
}
```

**Step 2: Render it on screen (ReactDOM)**

```javascript
import ReactDOM from 'react-dom';
import PizzaCard from './PizzaCard';

ReactDOM.render(
  <PizzaCard name="Pepperoni" image="pep.jpg" price="12.99" />,
  document.getElementById('root')
);
```

### Behind the Scenes

| Step | What happens |
|------|---------------|
| 1 | React creates virtual representation in memory |
| 2 | ReactDOM compares virtual vs real DOM |
| 3 | ReactDOM updates only what's different |
| 4 | Result: Fast, responsive apps |

### Simple Summary

| Tool | Job |
|------|-----|
| React | "What should it look like?" (designs the component) |
| ReactDOM | "Make it appear in the browser" (renders it) |

