## Creating Our First App With Vite: "WorldWise"

### Overview

Vite is a modern build tool that is faster and more flexible than Create React App.

### File Structure Differences

| Create React App | Vite |
|------------------|------|
| index.js | Main.jsx |
| .js extension | .jsx extension required |

### Setting Up the Project

```bash
# Create new Vite project
npm create vite worldwise --template react

# Install dependencies
npm install

# Install ESLint plugins
npm install -D eslint vite-plugin-eslint eslint-config-react-app

# Start development server
npm run dev
```

### Running the Application

Open browser and navigate to: `http://localhost:3000/`

### ESLint Configuration

Install the following packages:
- eslint
- vite-plugin-eslint
- eslint-config-react-app

```javascript
// ESLint will warn about unused variables
const unusedVariable = 10; // Warning: 'unusedVariable' is defined but never used
```

---

## Routing and Single-Page Applications (SPAs)

### What is Routing?

Routing is matching different URLs to different views in the user interface.

### What are SPAs?

Single-page applications are executed entirely on the client. They are loaded once and updated dynamically as the user interacts with them.

### How SPAs Work

When a user clicks a link in an SPA:
1. URL in browser changes
2. DOM is updated by JavaScript
3. React Router handles updating the DOM based on new URL

### Benefits of SPAs

| Benefit | Description |
|---------|-------------|
| Improved user experience | Feels like native app, no page reloads |
| Improved performance | Faster than traditional web apps |
| Reduced bandwidth | Only initial page loaded, then dynamic updates |

### Basic SPA with React Router

```javascript
import React from 'react';
import { BrowserRouter, Route, Link } from 'react-router-dom';

const App = () => {
  return (
    <BrowserRouter>
      <div>
        <h1>My SPA</h1>
        <ul>
          <Link to="/home">Home</Link>
          <Link to="/about">About</Link>
        </ul>
        <Route path="/home" component={Home} />
        <Route path="/about" component={About} />
      </div>
    </BrowserRouter>
  );
};

const Home = () => {
  return (
    <div>
      <h2>Home</h2>
      <p>This is the home page.</p>
    </div>
  );
};

const About = () => {
  return (
    <div>
      <h2>About</h2>
      <p>This is the about page.</p>
    </div>
  );
};

export default App;
```

---

## Implementing Main Pages and Routes

### Defining Routes Declaratively

```javascript
import React from 'react';
import { BrowserRouter, Route } from 'react-router-dom';

const App = () => {
  return (
    <BrowserRouter>
      <div>
        <Route path="/" component={Homepage} />
        <Route path="/about" component={AboutPage} />
      </div>
    </BrowserRouter>
  );
};

const Homepage = () => {
  return <div><h1>Homepage</h1></div>;
};

const AboutPage = () => {
  return <div><h1>About Page</h1></div>;
};

export default App;
```

### Page Not Found (404) Route

```javascript
import React from 'react';
import { BrowserRouter, Route } from 'react-router-dom';

const App = () => {
  return (
    <BrowserRouter>
      <div>
        <Route path="/" component={Homepage} />
        <Route path="/about" component={AboutPage} />
        <Route path="*" component={NotFoundPage} />
      </div>
    </BrowserRouter>
  );
};

const NotFoundPage = () => {
  return <div><h1>Page Not Found</h1></div>;
};
```

---

## Linking Between Routes With Link and NavLink

### Link Component

The Link component creates links between pages without reloading the page.

```javascript
import React from 'react';
import { Link } from 'react-router-dom';

const App = () => {
  return (
    <div>
      <h1>Homepage</h1>
      <Link to="/pricing">Pricing</Link>
    </div>
  );
};
```

### NavLink Component

NavLink adds an `active` class to the currently active link.

```javascript
import React from 'react';
import { NavLink } from 'react-router-dom';

const App = () => {
  return (
    <div>
      <h1>Homepage</h1>
      <NavLink to="/pricing">Pricing</NavLink>
    </div>
  );
};
```

### CSS for Active Link

```css
.active {
  background-color: red;
}
```

### Benefits of Link Components

| Benefit | Description |
|---------|-------------|
| Seamless transitions | No page reload |
| URL updates | Shareable and bookmarkable URLs |
| Active links | Visual indication of current page |

---

## Styling Options For React Applications

### Inline CSS

```javascript
<div style={{ color: 'red', fontSize: '16px' }}>Hello</div>
```

### External CSS Files

```css
/* styles.css */
.button {
  background-color: blue;
}
```

```javascript
import './styles.css';
<button className="button">Click</button>
```

### CSS Modules

```css
/* Button.module.css */
.button {
  background-color: blue;
}
```

```javascript
import styles from './Button.module.css';
<button className={styles.button}>Click</button>
```

### CSS in JavaScript Libraries

Styled-components, Emotion, etc.

### Utility-First CSS Frameworks

Tailwind CSS, etc.

### UI Component Libraries

Material UI, Chakra UI, Mantine, etc.

### Recommendation for Beginners

Use CSS modules to learn scoping and prevent class name collisions.

---

## Using CSS Modules

### Creating a CSS Module

File must have `.module.css` extension.

```css
/* PageNav.module.css */
.nav {
  justify-content: space-between;
}
```

### How CSS Modules Work

When exported, classes are prefixed with a unique string to prevent collisions.

```css
/* Compiled output */
.nav--s4294f {
  justify-content: space-between;
}
```

### Using Classes in JSX

```javascript
import { nav } from './PageNav.module.css';

const PageNav = () => {
  return (
    <nav className={nav}>
      <ul>
        <li><a href="/pricing">Pricing</a></li>
      </ul>
    </nav>
  );
};
```

### Global CSS

For application-wide styles like font family.

```css
/* index.css */
body {
  font-family: sans-serif;
}
```

### Global Classes in CSS Modules

```css
/* PageNav.module.css */
:global(.test) {
  background-color: red;
}
```

### Benefits of CSS Modules

| Benefit | Description |
|---------|-------------|
| Scoped CSS | Prevents class name collisions |
| Modular | Each component has its own styles |
| Reusable | Components can be moved without style conflicts |

---

## Nested Routes and Index Route

### Implementing Nested Routes

```javascript
// Parent route element
<Route path="/app">
  {/* Child route elements */}
  <Route path="cities">
    <ul>
      <li>List of cities</li>
    </ul>
  </Route>
  <Route path="countries">
    <ul>
      <li>List of countries</li>
    </ul>
  </Route>
  <Route path="form">
    <form>
      {/* Form fields */}
    </form>
  </Route>
</Route>
```

### Using Outlet Component

The Outlet component displays child route elements.

```javascript
import { Outlet } from 'react-router-dom';

const AppLayout = () => {
  return (
    <div>
      <nav>Navigation</nav>
      <Outlet /> {/* Child routes render here */}
    </div>
  );
};
```

### Index Route

An index route is a default child route that matches when no other child routes match.

```javascript
<Route index>
  <ul>
    <li>List of cities</li>
  </ul>
</Route>
```

### Navigation Between Nested Routes

```javascript
<ul>
  <li>
    <Link to="/app/cities">Cities</Link>
  </li>
  <li>
    <Link to="/app/countries">Countries</Link>
  </li>
</ul>
```

---

## Storing State in the URL

### Two Ways to Store State in URL

| Method | Syntax | Use Case |
|--------|--------|----------|
| Parameters | `/cities/:city` | Passing data to next page |
| Query string | `/cities/?lat=38.71&lng=-9.13` | Global state accessible everywhere |

### Benefits of URL State Storage

| Benefit | Description |
|---------|-------------|
| Accessibility | State accessible to all components |
| Shareability | Share state by sharing URL |
| Persistence | State persists across page reloads |

### Example Links

```javascript
import { Link } from "react-router-dom";

// Parameter link
const cityLink = (city) => (
  <Link to={`/app/cities/${city}`}>{city}</Link>
);

// Query string link
const mapLink = (lat, lng) => (
  <Link to={`/app/map/?lat=${lat}&lng=${lng}`}>Map</Link>
);
```

---

## Dynamic Routes With URL Parameters

### Defining a Route with Parameters

```javascript
// Route with :id parameter
<Route path="/cities/:id" element={<City />} />
```

### Creating Links with Parameters

```javascript
<Link to="/cities/739">City 739</Link>
<Link to="/cities/4545">City 4545</Link>
```

### Reading Parameters with useParams

```javascript
import { useParams } from "react-router-dom";

const City = () => {
  const { id } = useParams();
  return <h1>City {id}</h1>;
};
```

### Complete Example

```javascript
// App.js
import { BrowserRouter, Route, Link } from "react-router-dom";

function City() {
  const { id } = useParams();
  return <h1>City {id}</h1>;
}

function App() {
  return (
    <BrowserRouter>
      <div>
        <h1>Cities</h1>
        <ul>
          <li><Link to="/cities/739">City 739</Link></li>
          <li><Link to="/cities/4545">City 4545</Link></li>
        </ul>
        <Route path="/cities/:id" element={<City />} />
      </div>
    </BrowserRouter>
  );
}

export default App;
```

---

## Reading and Setting a Query String

### Using useSearchParams Hook

```javascript
import { useSearchParams } from "react-router-dom";

const City = () => {
  const [searchParams, setSearchParams] = useSearchParams();
  const latitude = searchParams.get('lat');
  const longitude = searchParams.get('lng');

  return (
    <div>
      <p>Latitude: {latitude}, Longitude: {longitude}</p>
    </div>
  );
};
```

### Updating Query String

```javascript
const handleChangePosition = () => {
  setSearchParams({
    lat: 23,
    lng: 50
  });
};
```

### Complete Example

```javascript
import React from "react";
import { useParams, useSearchParams } from "react-router-dom";

const City = () => {
  const { id } = useParams();
  const [searchParams, setSearchParams] = useSearchParams();
  
  const latitude = searchParams.get('lat');
  const longitude = searchParams.get('lng');

  const handleChangePosition = () => {
    setSearchParams({
      lat: 23,
      lng: 50
    });
  };

  return (
    <div>
      <h1>City {id}</h1>
      <p>Position: {latitude}, {longitude}</p>
      <button onClick={handleChangePosition}>Change position</button>
    </div>
  );
};

export default City;
```

---

## Programmatic Navigation with useNavigate

### What is Programmatic Navigation?

Navigating to a different page without relying on the user clicking a link.

### Using useNavigate Hook

```javascript
import { useNavigate } from "react-router-dom";

const FormButton = () => {
  const navigate = useNavigate();

  const handleSubmit = () => {
    navigate("/form");
  };

  return <button onClick={handleSubmit}>Submit</button>;
};
```

### Going Back

```javascript
// Go back one page
navigate(-1);
```

### Preventing Form Submission

```javascript
import { useNavigate } from "react-router-dom";

const FormButton = () => {
  const navigate = useNavigate();

  const handleSubmit = (e) => {
    e.preventDefault();
    navigate("/form");
  };

  return <button onClick={handleSubmit}>Submit</button>;
};
```

---

## Programmatic Navigation with Navigate Component

### Declarative Navigation

The Navigate component is used inside JSX instead of in a JavaScript function.

```javascript
import { Navigate } from "react-router-dom";

const App = () => {
  return (
    <div>
      <Navigate to="/cities" />
      <h1>Hello, world!</h1>
    </div>
  );
};
```

### Using in Nested Routes

```javascript
import { useParams, Navigate } from "react-router-dom";

const City = () => {
  const { id } = useParams();

  // Redirect if id is invalid
  if (!id) {
    return <Navigate to="/cities" />;
  }

  return (
    <div>
      <h1>City {id}</h1>
    </div>
  );
};
```

### Using the replace Prop

Prevents user from going back to the previous page.

```javascript
<Navigate to="/login" replace />
```
