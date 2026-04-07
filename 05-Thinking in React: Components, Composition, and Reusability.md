## How to Split a UI Into Components

### What is a Component?

A component is a reusable piece of code that renders HTML, CSS, and JavaScript. Components are the building blocks of React applications.

### Why Split a Huge Component?

| Reason | Description |
|--------|-------------|
| Reusability | Smaller components can be used in multiple places |
| Maintainability | Easier to understand and debug |
| Performance | React renders smaller components faster |

### How to Split a Component

| Step | Action |
|------|--------|
| 1 | Identify different pieces of functionality |
| 2 | Create a new component for each piece |
| 3 | Extract code from huge component to new component |
| 4 | Update huge component to use new components |

### Guidelines for Splitting Components

| Guideline | Description |
|-----------|-------------|
| Logical separation | Each component has one responsibility |
| Reusability | Make components that can be reused |
| Keep small | Components should be easy to understand |
| Descriptive names | Name should describe what component does |
| No nested declarations | Avoid declaring components inside other components |

### Code Example

```javascript
// Huge component (BEFORE)
const HugeComponent = () => {
  return (
    <div>
      <header>Header Content</header>
      <main>Main Content</main>
      <footer>Footer Content</footer>
    </div>
  );
};

// Smaller components (AFTER)
const HeaderComponent = () => {
  return <header>Header Content</header>;
};

const ContentComponent = () => {
  return <main>Main Content</main>;
};

const FooterComponent = () => {
  return <footer>Footer Content</footer>;
};

// Split huge component into smaller components
const NewHugeComponent = () => {
  return (
    <div>
      <HeaderComponent />
      <ContentComponent />
      <FooterComponent />
    </div>
  );
};
```

---

## Component Categories

### Stateless (Presentational) Components

Components that do not have any state. Their output is always the same for the same input props.

```javascript
const ButtonComponent = ({ text, onClick }) => {
  return (
    <button onClick={onClick}>
      {text}
    </button>
  );
};
```

### Stateful Components

Components that have state. Their output can change depending on their state.

```javascript
class CounterComponent extends React.Component {
  state = {
    count: 0,
  };

  incrementCount = () => {
    this.setState({
      count: this.state.count + 1,
    });
  };

  render() {
    return (
      <div>
        <h1>{this.state.count}</h1>
        <button onClick={this.incrementCount}>Increment</button>
      </div>
    );
  }
}
```

### Structural Components

Components that provide structure to the application, such as pages, layouts, and screens.

```javascript
const AppLayout = () => {
  return (
    <div>
      <Header />
      <Content />
      <Footer />
    </div>
  );
};
```

### Category Summary

| Category | Has State? | Purpose | Example |
|----------|------------|---------|---------|
| Stateless | No | Render UI elements | Button, Image |
| Stateful | Yes | Manage user input, data fetching | Counter, Form |
| Structural | Maybe | Provide app structure | Layout, Page |

---

## Prop Drilling

### Problem: Hardcoded Values

```javascript
// Before - hardcoded value
const NumResults = () => {
  return <span>X</span>;
};
```

### Solution: Lift State Up and Pass as Prop

```javascript
// After - dynamic calculation
const NumResults = ({ movies }) => {
  return <span>{movies.length}</span>;
};

const App = () => {
  const [movies, setMovies] = useState([]);

  return (
    <div>
      <NumResults movies={movies} />
      <MovieList movies={movies} />
    </div>
  );
};
```

### What is Prop Drilling?

Prop drilling is when we need to pass a prop through several nested child components to get data into a deeply nested component.

```javascript
// Prop drilling example
const MovieList = ({ movies }) => {
  return (
    <ListBox movies={movies}>
      <WatchedBox movies={movies} />
    </ListBox>
  );
};

const ListBox = ({ movies, children }) => {
  return <ul>{children}</ul>;
};

const WatchedBox = ({ movies }) => {
  // movies prop drilled through multiple levels
};
```

### Why Prop Drilling is a Problem

| Problem | Description |
|---------|-------------|
| Readability | Harder to read and maintain |
| Performance | Issues with large props passed many levels |
| Fragility | Changes to chain can break application |

### How to Avoid Prop Drilling

| Solution | Description |
|----------|-------------|
| Context | Share data without passing props |
| Component composition | Break down components to reduce drilling |
| State management library | Centralize state (Redux, Zustand) |

---

## Fixing Prop Drilling With Composition

### Problem Code

```javascript
const App = () => {
  const movies = [];

  return (
    <div>
      <NavBar movies={movies} />
      <Main movies={movies} />
    </div>
  );
};

const NavBar = ({ movies }) => {
  return (
    <div>
      <Search />
      <NumResults movies={movies} />
    </div>
  );
};

const Main = ({ movies }) => {
  return (
    <div>
      <ListBox movies={movies} />
      <WatchedBox movies={movies} />
    </div>
  );
};
```

### Solution Using Component Composition

```javascript
const App = () => {
  const movies = [];

  return (
    <div>
      <NavBar>
        <NumResults movies={movies} />
      </NavBar>
      <Main>
        <ListBox movies={movies} />
      </Main>
    </div>
  );
};

const NavBar = ({ children }) => {
  return (
    <div>
      <Search />
      {children}
    </div>
  );
};

const Main = ({ children }) => {
  return (
    <div>
      {children}
    </div>
  );
};

const ListBox = ({ movies }) => {
  return (
    <div>
      {movies.map((movie) => <div key={movie.id}>{movie.title}</div>)}
    </div>
  );
};
```

### Benefits of Component Composition

| Benefit | Description |
|---------|-------------|
| Readability | Easier to read and maintain |
| Reusability | Components can be reused |
| Modularity | Components are independent |

---

## Using Composition to Make a Reusable Box

### Problem: Duplicate Code

```javascript
// Before - duplicate code in both components
const WatchedBox = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div className="box">
      {isOpen && <WatchedList />}
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
    </div>
  );
};

const ListBox = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div className="box">
      {isOpen && <MovieList />}
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
    </div>
  );
};
```

### Solution: Reusable Box Component

```javascript
// After - reusable Box component
const Box = ({ children }) => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div className="box">
      {isOpen && children}
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
    </div>
  );
};

const WatchedBox = () => {
  return <Box children={<WatchedList />} />;
};

const ListBox = () => {
  return <Box children={<MovieList />} />;
};
```

### Benefits

| Benefit | Description |
|---------|-------------|
| Reduced duplication | Write once, use everywhere |
| Increased modularity | Each component has single responsibility |
| More reusable | Box can be used for any content |

---

## Passing Elements as Props (Alternative to children)

### Using Explicit Props

```javascript
// Parent component
const ParentComponent = () => {
  const [element, setElement] = useState(<MovieList />);

  return (
    <div>
      <Box element={element} />
    </div>
  );
};

// Child component
const Box = ({ element }) => {
  return (
    <div className="box">
      {element}
    </div>
  );
};
```

### children vs Explicit Props

| Approach | When to Use |
|----------|-------------|
| children | Single element, implicit, preferred way |
| Explicit props | Multiple elements, separate names needed |

---

## Handling Hover Events

### Problem

Display a temporary rating whenever a user hovers over stars.

### Solution

```javascript
// State
const [rating, setRating] = useState(0);
const [tempRating, setTempRating] = useState(0);

// Event handlers
const onHoverIn = (rating) => {
  setTempRating(rating);
};

const onHoverOut = () => {
  setTempRating(0);
};

// Render
return (
  <div>
    <Star
      rating={rating}
      onHoverIn={onHoverIn}
      onHoverOut={onHoverOut}
    />
    <div>{tempRating}</div>
  </div>
);
```

### Key Event Handlers

| Event | Description |
|-------|-------------|
| onMouseEnter | Triggers when mouse enters element |
| onMouseLeave | Triggers when mouse leaves element |

---

## Props as a Component API

### Thinking About Props

Props are the public API of a component. They should allow consumers to interact with the component without exposing too much complexity.

### Design Questions

| Question | Purpose |
|----------|---------|
| What functionality does the component need? | Define component purpose |
| What configuration options do consumers need? | Identify necessary props |
| How complex is internal logic? | Determine prop complexity |

### Simple Component Example

```javascript
// Simple weather component with one prop
function Weather({ location }) {
  const weatherData = fetchWeatherData(location);

  return (
    <div>
      <h1>Weather for {location}</h1>
      <p>{weatherData.temperature} degrees Celsius</p>
    </div>
  );
}
```

### Complex Component Example

```javascript
// More complex weather component with multiple props
function Weather({
  location,
  days,
  hourly,
  temperatureUnit,
  dataToDisplay,
}) {
  const weatherData = fetchWeatherData(location, days, hourly);

  return (
    <div>
      <h1>Weather for {location}</h1>
      <p>{weatherData.temperature} {temperatureUnit}</p>
      <ul>
        {dataToDisplay.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Balancing Props

| Too Few Props | Too Many Props |
|---------------|----------------|
| Not flexible enough | Overwhelming for consumers |
| Limited use cases | Hard to maintain |
| May not meet needs | Complex API |

---

## Improving Reusability With Props

### Making Components Flexible

Add props to allow consumers to specify:
- Colors
- Sizes
- Font style
- Message array
- Default rating

### Example: Reusable Rating Component

```javascript
const Rating = ({
  rating,
  color,
  size,
  messages,
  defaultRating,
}) => {
  const [stateRating, setStateRating] = useState(defaultRating || 0);

  const handleRate = (newRating) => {
    setStateRating(newRating);
  };

  const renderStars = () => {
    const stars = [];
    for (let i = 0; i < rating; i++) {
      stars.push(
        <Star
          key={i}
          color={color}
          size={size}
          onClick={() => handleRate(i + 1)}
        />
      );
    }
    return stars;
  };

  return (
    <div className="rating">
      <div className="stars">{renderStars()}</div>
      {messages && messages.length === rating && (
        <div className="message">{messages[rating - 1]}</div>
      )}
    </div>
  );
};
```

### Usage Example

```javascript
<Rating
  rating={3}
  color="red"
  size="48"
  messages={["Terrible", "Bad", "Okay", "Good", "Amazing"]}
  defaultRating={2}
/>
```

---

## PropTypes

### Adding Type Checking

Import and use PropTypes to validate component props.

```javascript
import PropTypes from 'prop-types';

const Rating = ({
  rating,
  color,
  size,
  messages,
  defaultRating,
  onSetRating,
}) => {
  // Component logic here

  // PropTypes definition
  Rating.propTypes = {
    rating: PropTypes.number.isRequired,
    color: PropTypes.string,
    size: PropTypes.number,
    messages: PropTypes.array,
    defaultRating: PropTypes.number,
    onSetRating: PropTypes.func.isRequired,
  };
};
```

### Common PropTypes Validators

| Validator | Description |
|-----------|-------------|
| PropTypes.string | String value |
| PropTypes.number | Number value |
| PropTypes.bool | Boolean value |
| PropTypes.array | Array value |
| PropTypes.object | Object value |
| PropTypes.func | Function value |
| PropTypes.node | Renderable value |
| PropTypes.element | React element |
| .isRequired | Marks prop as required |

### Benefits of PropTypes

| Benefit | Description |
|---------|-------------|
| Prevents bugs | Catches type mismatches |
| Documents API | Shows expected prop types |
| Better DX | Warning in console for invalid props |

### Notes on PropTypes

- PropTypes is a built-in React package
- Not required if using TypeScript
- Avoid marking props as required unless absolutely necessary
- Provide default values when possible
