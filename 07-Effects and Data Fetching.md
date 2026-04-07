## The Component Lifecycle

### Three Phases of Component Lifecycle

| Phase | Description |
|-------|-------------|
| Mounting | Rendering a component for the first time |
| Re-rendering | Updating a component's render output based on state or props change |
| Unmounting | Removing a component from the DOM |

### Lifecycle Methods (Class Components)

```javascript
// Mounting
componentDidMount() {
  // Executed after the component is mounted
}

// Re-rendering
componentDidUpdate(prevProps, prevState) {
  // Executed after the component is re-rendered
}

// Unmounting
componentWillUnmount() {
  // Executed before the component is unmounted
}
```

### useEffect Hook (Functional Components)

```javascript
useEffect(callback, [dependencies]) {
  // Executed after component is mounted and on every re-render
}
```

### Example Component

```javascript
function MyComponent({ name }) {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('State:', count);
    console.log('Props:', name);
  }, [count, name]);

  componentDidMount() {
    console.log('Mounted!');
  }

  componentWillUnmount() {
    console.log('Unmounted!');
  }

  return <div>{count}</div>;
}
```

### Output Example

```
Mounted!
State: 0
Props: John Doe

// User clicks button to increment count
State: 1
Props: John Doe

// User navigates away, component unmounts
Unmounted!
```

---

## How NOT to Fetch Data in React

### Rules for Fetching Data

| Rule | Description |
|------|-------------|
| Never fetch in render logic | Can lead to infinite loops and performance problems |
| Use separate function or hook | Such as useEffect |
| Handle errors gracefully | Use try-catch or catch method |

### Correct Way to Fetch Data

```javascript
// Fetch data outside of render logic
const fetchMovies = async () => {
  const response = await fetch('https://www.omdbapi.com/?s=interstellar&apikey=YOUR_API_KEY');
  const data = await response.json();
  return data.search;
};

// Use useEffect hook to fetch data and set state
useEffect(() => {
  const movies = await fetchMovies();
  setMovies(movies);
}, []);
```

### Error Handling

```javascript
const fetchMovies = async () => {
  try {
    const response = await fetch('https://www.omdbapi.com/?s=interstellar&apikey=YOUR_API_KEY');
    const data = await response.json();
    return data.search;
  } catch (error) {
    // Handle error here
    console.error(error);
  }
};
```

---

## useEffect to the Rescue

### Basic useEffect Syntax

```javascript
useEffect(() => {
  // Side effect code here
}, []); // Empty dependency array = runs only on mount
```

### Fetching Data on Mount

```javascript
const fetchMovies = async () => {
  const response = await fetch('https://www.omdbapi.com/?s=interstellar&apikey=YOUR_API_KEY');
  const data = await response.json();
  return data.search;
};

useEffect(() => {
  const movies = await fetchMovies();
  setMovies(movies);
}, []);
```

### Benefits of Using useEffect for Data Fetching

| Benefit | Description |
|---------|-------------|
| Prevents infinite loops | Effect runs only when dependencies change |
| Readable code | Side effects are clearly separated |
| Scalable | Easy to add more effects |

---

## A First Look at Effects

### Effects vs Event Handlers

| Characteristic | Effect | Event Handler |
|----------------|--------|---------------|
| Purpose | Keep component in sync with external world | React to specific UI event |
| When to use | Specific moment of lifecycle (mount, re-render) | Response to user interaction |
| Example | Fetching data on mount | Updating state on button click |

### Best Practices

| Practice | Description |
|----------|-------------|
| Use event handlers when possible | Preferred way for side effects |
| Use useEffect only when needed | For lifecycle-specific side effects |

---

## Using an async Function

### Problem

Cannot directly use async function as effect callback because it returns a promise.

### Solution

Create a new async function inside the effect callback.

```javascript
// Convert effect callback to async function
useEffect(() => {
  const fetchMovies = async () => {
    const res = await fetch('https://www.omdbapi.com/?s=interstellar&apikey=YOUR_API_KEY');
    const data = await res.json();
    setMovies(data.Search);
  };

  fetchMovies();
}, []);
```

### Using Updated State Value

```javascript
useEffect(() => {
  console.log(movies);
}, [movies]);
```

### React 18 Strict Mode Note

In React 18 strict mode, effects are called twice to identify problems.

---

## Adding a Loading State

### Implementation

```javascript
// Create loading state variable
const [isLoading, setIsLoading] = useState(false);

// Set loading state before and after fetch
useEffect(() => {
  const fetchMovies = async () => {
    setIsLoading(true);

    const res = await fetch('https://www.omdbapi.com/?s=interstellar&apikey=YOUR_API_KEY');
    const data = await res.json();

    setMovies(data.Search);
    setIsLoading(false);
  };

  fetchMovies();
}, []);

// Conditional rendering
return (
  <>
    {isLoading && <Loader />}
    {!isLoading && <MovieList movies={movies} />}
  </>
);

// Loader component
const Loader = () => {
  return <p className="Loader">Loading...</p>;
};
```

---

## Handling Errors

### Try-Catch for Error Handling

```javascript
try {
  // Fetch the data
  const response = await fetch('https://api.example.com/movies');

  // Check if response was successful
  if (!response.ok) {
    throw new Error('Failed to fetch movies');
  }

  // Get data from response
  const data = await response.json();

  // Render the data
} catch (error) {
  // Handle the error
}
```

### Displaying Error Messages

```javascript
const [error, setError] = useState(null);

// Fetch the data
try {
  // ...
} catch (error) {
  setError(error.message);
}

// Render error message
if (error) {
  <div className="error">{error}</div>;
}
```

### Handling Specific Errors

```javascript
const [error, setError] = useState(null);

try {
  const response = await fetch('https://api.example.com/movies');

  // Check for specific status codes
  if (response.status === 404) {
    throw new Error('Movie not found');
  }

  if (!response.ok) {
    throw new Error('Failed to fetch movies');
  }

  const data = await response.json();
} catch (error) {
  setError(error.message);
}
```

---

## The useEffect Dependency Array

### Three Types of Dependency Arrays

| Type | Behavior |
|------|----------|
| Empty array [] | Effect runs only on mount |
| Non-empty array [dep1, dep2] | Runs on mount and when dependencies change |
| No array | Runs on every render (not recommended) |

### Example with Dependency Array

```javascript
const [title, setTitle] = useState('Interstellar');

useEffect(() => {
  document.title = title;
}, [title]); // Runs when 'title' changes
```

### When Effects Are Executed

Effects are executed asynchronously after the render has been painted to the screen. This prevents blocking the render process.

---

## Synchronizing Queries With Movie Data

### Effect to Synchronize Search Query

```javascript
useEffect(() => {
  if (query.length === 0 || query.length < 3) {
    setMovies([]);
    setError('');
    return;
  }

  fetchMovies(query);
}, [query]);

// Fetch movies from API
async function fetchMovies(query) {
  try {
    const response = await fetch(`https://api.example.com/movies?q=${query}`);
    const movies = await response.json();
    setMovies(movies);
  } catch (error) {
    setError(error.message);
  }
}
```

### Complete Component Example

```javascript
const App = () => {
  const [query, setQuery] = useState('');
  const [movies, setMovies] = useState([]);
  const [error, setError] = useState('');

  useEffect(() => {
    if (query.length === 0 || query.length < 3) {
      setMovies([]);
      setError('');
      return;
    }

    fetchMovies(query);
  }, [query]);

  async function fetchMovies(query) {
    try {
      const response = await fetch(`https://api.example.com/movies?q=${query}`);
      const movies = await response.json();
      setMovies(movies);
    } catch (error) {
      setError(error.message);
    }
  }

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      <ul>
        {movies.map((movie) => (
          <li key={movie.id}>{movie.title}</li>
        ))}
      </ul>
      {error && <p>Error: {error}</p>}
    </div>
  );
};
```

---

## Selecting a Movie

### Component to Display Selected Movie

```javascript
function MovieDetails({ selectedId }) {
  if (!selectedId) {
    return null;
  }

  return (
    <div className="detail">
      Selected ID: {selectedId}
    </div>
  );
}
```

### Handling Movie Selection

```javascript
// Handle selection
function handleSelectMovie(movieId) {
  setSelectedId(movieId);
}

// Close movie detail
function handleCloseMovie() {
  setSelectedId(null);
}

// Update selected ID based on current one
setSelectedId((previousSelectedId, id) => {
  if (id === previousSelectedId) {
    return null;
  }
  return id;
});
```

### Parent Component

```javascript
const App = () => {
  const [selectedId, setSelectedId] = useState(null);

  return (
    <div>
      <MovieList
        movies={movies}
        onSelectMovie={handleSelectMovie}
      />
      {selectedId && <MovieDetails selectedId={selectedId} />}

      <button className="btn-back" onClick={handleCloseMovie}>
        &larr;
      </button>
    </div>
  );
};
```

---

## Loading Movie Details

### Effect to Fetch Movie Details

```javascript
const [isLoading, setIsLoading] = useState(false);

useEffect(() => {
  async function fetchMovieDetails() {
    const response = await fetch(`/api/movies/${selectedId}`);
    const movie = await response.json();
    setMovie(movie);
    setIsLoading(false);
  }

  if (selectedId) {
    setIsLoading(true);
    fetchMovieDetails();
  }
}, [selectedId]);

// Loading indicator
if (isLoading) {
  return <div>Loading...</div>;
}

// Render movie details
return (
  <div className="movie-details">
    <img src={movie.poster} alt={movie.title} />
    <h2>{movie.title}</h2>
    <p>{movie.plot}</p>
    <p>Starring: {movie.actors}</p>
    <p>Directed by: {movie.director}</p>
    <StarRating rating={movie.imdbRating} maxRating={10} size={24} />
  </div>
);
```

---

## Adding a Watched Movie

### Function to Add Movie to Watched List

```javascript
function handleAddWatch(movie) {
  // Create new watched movie object
  const newWatchedMovie = {
    imdbID: movie.imdbID,
    title: movie.title,
    year: movie.year,
    poster: movie.poster,
    imdbRating: movie.imdbRating,
    userRating: movie.userRating,
    runtime: parseInt(movie.runtime.split(' ')[0]),
  };

  // Add to watched list
  setWatched((watched) => [...watched, newWatchedMovie]);

  // Close movie details
  handleCloseMovie();
}
```

### User Rating State

```javascript
const [userRating, setUserRating] = useState('');
```

### Passing Props to MovieDetails

```javascript
<MovieDetails
  selectedId={selectedId}
  watched={watched}
  onSetRating={setUserRating}
  onAddWatched={handleAddWatch}
/>
```

### Preventing Duplicate Movies

```javascript
// Check if movie is already in watched list
const isMovieInWatchedList = (movie) => {
  return watched.find((watchedMovie) => watchedMovie.imdbID === movie.imdbID);
};

// Pass to MovieDetails
<MovieDetails
  selectedId={selectedId}
  watched={watched}
  onSetRating={setUserRating}
  onAddWatched={handleAddWatch}
  isMovieInWatchedList={isMovieInWatchedList}
/>
```

---

## Adding a New Effect: Changing Page Title

### Basic Implementation

```javascript
import { useEffect } from 'react';

const [title, setTitle] = useState('');

useEffect(() => {
  document.title = `Movie: ${title}`;

  return () => {
    document.title = 'usePopcorn';
  };
}, [title]);
```

### Fetching Title on Mount

```javascript
useEffect(() => {
  const getMovieTitle = async () => {
    const response = await fetch(`/api/movies/${selectedId}`);
    const movie = await response.json();
    setTitle(movie.title);
  };

  getMovieTitle();
}, [selectedId]);
```

### Handling Undefined Title

```javascript
useEffect(() => {
  if (!title) {
    return;
  }

  document.title = `Movie: ${title}`;

  return () => {
    document.title = 'usePopcorn';
  };
}, [title]);
```

### Resetting Title on Navigation

```javascript
useEffect(() => {
  if (!selectedId) {
    document.title = 'usePopcorn';
  }

  document.title = `Movie: ${title}`;

  return () => {
    document.title = 'usePopcorn';
  };
}, [selectedId, title]);
```

---

## The useEffect Cleanup Function

### What is the Cleanup Function?

A function returned from a useEffect hook used to clean up side effects. Executed:
- Before effect is executed again (clean up previous side effect)
- After component instance has unmounted

### When to Use Cleanup Function

| Scenario | Example |
|----------|---------|
| HTTP requests | Cancel request if component unmounts |
| Subscriptions | Unsubscribe from API services |
| Timers | Stop intervals or timeouts |
| Event listeners | Remove DOM event listeners |

### Basic Cleanup Function Pattern

```javascript
useEffect(() => {
  // Create side effect

  // Return cleanup function
  return () => {
    // Clean up side effect
  };
}, []);
```

### HTTP Request Cleanup Example

```javascript
import { useEffect, useState } from "react";

const MovieDetails = () => {
  const [movie, setMovie] = useState();

  useEffect(() => {
    const fetchMovie = async () => {
      const response = await fetch("/api/movies/1");
      const data = await response.json();
      setMovie(data);
    };

    fetchMovie();

    // Cleanup function to cancel request if component unmounts
    return () => {
      fetchMovie.abort();
    };
  }, []);

  return (
    <div>
      <h1>{movie?.title}</h1>
    </div>
  );
};
```

---

## Cleaning Up the Title

### Resetting Page Title on Unmount

```javascript
const MovieDetails = () => {
  const [movie, setMovie] = useState();

  useEffect(() => {
    document.title = movie.title;

    // Cleanup function to reset page title
    return () => {
      document.title = "usePopcorn";
    };
  }, [movie]);

  return (
    <div>
      <h1>{movie?.title}</h1>
    </div>
  );
};
```

### Closures and Cleanup Function

The cleanup function is closed over variables from the effect, allowing access even after component unmounts.

---

## Cleaning Up Data Fetching

### Closure in Cleanup Functions

```javascript
const MovieDetails = () => {
  const [movie, setMovie] = useState();

  useEffect(() => {
    // Fetch movie from API
    // Set page title
    document.title = movie.title;

    // Cleanup function can still access movie variable
    return () => {
      document.title = movie.title;
    };
  }, [movie]);

  return (
    <div>
      <h1>{movie?.title}</h1>
    </div>
  );
};
```

The cleanup function retains access to the movie variable through closure, even after the component has unmounted.

---

## One More Effect: Listening to a Keypress

### Handling Escape Key Press

```javascript
import { useEffect, useState } from "react";

const MovieDetails = () => {
  const [movie, setMovie] = useState();

  useEffect(() => {
    // Listen for keydown event on document element
    document.addEventListener("keydown", (event) => {
      if (event.code === "Escape") {
        handleCloseMovie();
      }
    });

    // Cleanup function to remove event listener
    return () => {
      document.removeEventListener("keydown", (event) => {
        if (event.code === "Escape") {
          handleCloseMovie();
        }
      });
    };
  }, []);

  return (
    <div>
      <h1>{movie?.title}</h1>
    </div>
  );
};
```

### Why Cleanup is Necessary

Without cleanup, event listener remains attached to document element even after component unmounts, causing memory leaks and performance problems.

### Best Practices for Keypress Events

| Practice | Description |
|----------|-------------|
| Use useEffect | Listen for keydown event on document |
| Return cleanup | Remove event listener on unmount |
| Be specific | Listen for specific key codes |
| Avoid preventDefault | Can prevent other event handlers |
