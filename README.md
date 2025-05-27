# ReactNotes

## What are the lifecycle methods of ReactJS class components?

**1. Mounting Phase (Birth of the Component)**

These methods are called in the following order when an instance of a component is being created and inserted into the DOM:

*   **`constructor(props)`**
    *   **Purpose:** Initialize state and bind event handlers.
    *   **When:** Called before the component is mounted.
    *   **Usage:**
        *   Call `super(props)` first.
        *   Initialize `this.state`.
        *   Bind event handler methods to `this` (e.g., `this.handleClick = this.handleClick.bind(this);`).
    *   **Caution:** Do not cause side effects (e.g., HTTP requests) here.

*   **`static getDerivedStateFromProps(props, state)`**
    *   **Purpose:** Allows a component to update its internal state as the result of changes in props.
    *   **When:** Called right before `render()`, both on the initial mount and on subsequent updates.
    *   **Usage:** Return an object to update the state, or `null` to update nothing.
    *   **Note:** This method is `static`, so it has no access to `this`. It's a rare use case, often indicating that your component might be better controlled by its parent or by deriving data directly from props in `render`.

*   **`render()`**
    *   **Purpose:** Describes the UI (what you want to see on the screen).
    *   **When:** Called during mounting and updating phases.
    *   **Usage:**
        *   Must return one of the following: React elements (e.g., `<div />`), arrays and fragments, portals, string and numbers (rendered as text nodes), booleans or `null` (render nothing).
        *   Should be a pure function of `props` and `state`. This means given the same props and state, `render()` should always return the same result.
    *   **Caution:** Do not modify component state or interact with the DOM directly here (use `componentDidMount` or `componentDidUpdate` for that).

*   **`componentDidMount()`**
    *   **Purpose:** Perform side effects after the component is mounted (inserted into the DOM).
    *   **When:** Called immediately after a component is mounted.
    *   **Usage:**
        *   Load data from a remote endpoint (API calls).
        *   Set up subscriptions (e.g., timers, event listeners).
        *   DOM manipulations (though usually best avoided or done carefully).
    *   **Note:** If you `setState` here, it will trigger an extra rendering, but it will happen before the browser updates the screen.

**2. Updating Phase (Growth/Change of the Component)**

An update can be caused by changes to props or state. These methods are called in the following order when a component is being re-rendered:

*   **`static getDerivedStateFromProps(props, state)`**
    *   (Same as in mounting, called on every update as well)

*   **`shouldComponentUpdate(nextProps, nextState)`**
    *   **Purpose:** Lets React know if a component’s output is not affected by the current change in state or props. For performance optimization.
    *   **When:** Called before rendering when new props or state are being received. Defaults to `true`.
    *   **Usage:** Return `true` (default) to re-render, `false` to skip re-rendering.
    *   **Note:** If this method returns `false`, `render()`, `getSnapshotBeforeUpdate()`, and `componentDidUpdate()` will not be invoked.
    *   **Caution:** Use with care. Incorrectly returning `false` can lead to UI inconsistencies. `React.PureComponent` implements a shallow prop and state comparison for this method.

*   **`render()`**
    *   (Same as in mounting)

*   **`getSnapshotBeforeUpdate(prevProps, prevState)`**
    *   **Purpose:** Enables your component to capture some information from the DOM (e.g., scroll position) before it is potentially changed.
    *   **When:** Called right before the most recently rendered output is committed to the DOM.
    *   **Usage:** The value returned by this lifecycle method will be passed as the third parameter to `componentDidUpdate()`.
    *   **Note:** Rare use case, typically for things like managing scroll position in a chat window.

*   **`componentDidUpdate(prevProps, prevState, snapshot)`**
    *   **Purpose:** Perform side effects after the component updates in the DOM.
    *   **When:** Called immediately after updating occurs. Not called for the initial render.
    *   **Usage:**
        *   Good place for network requests (e.g., if props changed and you need new data).
        *   DOM manipulations (use with caution).
    *   **Caution:** You may call `setState()` here, but **wrap it in a condition** (e.g., `if (prevProps.userID !== this.props.userID)`) to avoid an infinite loop. `snapshot` is the value returned from `getSnapshotBeforeUpdate()`.

**3. Unmounting Phase (Death of the Component)**

This method is called when a component is being removed from the DOM:

*   **`componentWillUnmount()`**
    *   **Purpose:** Perform any necessary cleanup.
    *   **When:** Called immediately before a component is unmounted and destroyed.
    *   **Usage:**
        *   Invalidate timers.
        *   Cancel network requests.
        *   Clean up any subscriptions that were created in `componentDidMount()`.
    *   **Caution:** Do not call `setState()` here because the component will never be re-rendered.

**4. Error Handling Phase (When things go wrong)**

These methods are called when there is an error during rendering, in a lifecycle method, or in the constructor of any child component.

*   **`static getDerivedStateFromError(error)`**
    *   **Purpose:** To render a fallback UI after an error has been thrown by a descendant component.
    *   **When:** Called during the "render" phase, so side-effects are not permitted.
    *   **Usage:** Return a state update to render a fallback UI.

*   **`componentDidCatch(error, info)`**
    *   **Purpose:** To log error information.
    *   **When:** Called during the "commit" phase, so side-effects are permitted.
    *   **Usage:** Log the `error` and `info` (which contains a `componentStack` key with the component stack trace).

**Deprecated Lifecycle Methods (Avoid in New Code):**

These were common in older React versions but are now considered unsafe or problematic, especially with asynchronous rendering, and have better alternatives:

*   `componentWillMount()` (Use `constructor()` or `componentDidMount()`)
*   `componentWillReceiveProps(nextProps)` (Use `static getDerivedStateFromProps()` or `componentDidUpdate()`)
*   `componentWillUpdate(nextProps, nextState)` (Use `getSnapshotBeforeUpdate()` or `componentDidUpdate()`)



## Which method is called after render method in react life cycle method?
Okay, the method called after `render()` in the React class component lifecycle depends on whether it's the **initial render (mounting)** or a **re-render (updating)**.

Here's the breakdown:

1.  **During the Mounting Phase (Initial Render):**
    *   `constructor()`
    *   `static getDerivedStateFromProps()`
    *   **`render()`**
    *   **`componentDidMount()`** ← This is called *after* `render()` and after the component has been mounted to the DOM.

    So, on initial mount, `componentDidMount()` is called after `render()`.

2.  **During the Updating Phase (Re-render due to props or state change):**
    *   `static getDerivedStateFromProps()`
    *   `shouldComponentUpdate()`
    *   **`render()`**
    *   **`getSnapshotBeforeUpdate(prevProps, prevState)`** ← This is called right *after* `render()` but *before* the changes from the render are committed to the DOM. Its return value is passed as the third argument to `componentDidUpdate()`. This method is optional; if you don't define it, it's skipped.
    *   **`componentDidUpdate(prevProps, prevState, snapshot)`** ← This is called *after* `render()` (and after `getSnapshotBeforeUpdate` if it exists) and after the updates have been committed to the DOM.

    So, during an update:
    *   If you have `getSnapshotBeforeUpdate()`, it's called immediately after `render()`.
    *   `componentDidUpdate()` is called after `render()` and after the DOM updates (and after `getSnapshotBeforeUpdate()` if present).

**In summary:**

*   **After initial `render()` (Mounting):** `componentDidMount()`
*   **After subsequent `render()` (Updating):**
    *   `getSnapshotBeforeUpdate()` (if defined) is called immediately after `render` *before* DOM commit.
    *   `componentDidUpdate()` is called after `render` *and after* DOM commit.
 
## Compare React context api with useContext React Hook??
Okay, let's compare the **React Context API** (specifically the `Context.Consumer` component) with the **`useContext` React Hook**.

It's important to understand that **`useContext` is a way to *consume* context provided by the Context API**. It doesn't replace the need to create a context (`React.createContext`) or provide a context value (`<MyContext.Provider>`). Instead, `useContext` offers a cleaner and more modern way to access that context value within functional components, as an alternative to using the `Context.Consumer` component.

Here's a breakdown:

**React Context API (Overall Mechanism)**

1.  **`React.createContext(defaultValue)`:**
    *   This function creates a Context object.
    *   When React renders a component that subscribes to this Context object, it will read the current context value from the closest matching `Provider` above it in the tree.
    *   The `defaultValue` is only used when a component does not have a matching `Provider` above it in the tree.

2.  **`Context.Provider`:**
    *   Every Context object comes with a Provider React component (e.g., `MyContext.Provider`).
    *   It accepts a `value` prop to be passed to consuming components that are descendants of this Provider.
    *   All consumers that are descendants of a Provider will re-render whenever the Provider’s `value` prop changes.
    *   Providers can be nested to override values deeper in the tree.

    ```javascript
    // 1. Create Context
    const ThemeContext = React.createContext('light'); // 'light' is the default

    function App() {
      const [theme, setTheme] = useState('dark');
      return (
        // 2. Provide Context Value
        <ThemeContext.Provider value={theme}>
          <Toolbar />
          <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
            Toggle Theme
          </button>
        </ThemeContext.Provider>
      );
    }
    ```

3.  **Consuming Context (Two main ways):**

    *   **a) `Context.Consumer` (The "older" way, works in both class and functional components):**
        *   A React component that subscribes to context changes.
        *   It requires a **render prop** as a child: a function that receives the current context value and returns a React node.
        *   Can lead to deeply nested component structures (sometimes called "wrapper hell" or "pyramid of doom").

        ```javascript
        // Consuming with Context.Consumer
        function ThemedButton() {
          return (
            <ThemeContext.Consumer>
              {themeValue => ( // Render prop function
                <button style={{ background: themeValue === 'dark' ? '#333' : '#FFF', color: themeValue === 'dark' ? '#FFF' : '#333' }}>
                  I am styled by theme: {themeValue}
                </button>
              )}
            </ThemeContext.Consumer>
          );
        }
        ```

    *   **b) `static contextType` (For class components only):**
        *   Assign a context object to the `static contextType` property of a class.
        *   This lets you consume the nearest current value of that Context type using `this.context`.
        *   You can only subscribe to a single context using this mechanism.

        ```javascript
        class ThemedButtonClass extends React.Component {
          static contextType = ThemeContext; // Assign context
          render() {
            const themeValue = this.context; // Access context value
            return (
              <button style={{ background: themeValue === 'dark' ? '#333' : '#FFF', color: themeValue === 'dark' ? '#FFF' : '#333' }}>
                I am styled by theme (class): {themeValue}
              </button>
            );
          }
        }
        ```

**`useContext` React Hook (Introduced in React 16.8)**

*   **Purpose:** A Hook that provides a cleaner and more direct way to consume context values within **functional components only**.
*   **How it works:** `useContext(MyContext)` accepts a context object (the return value of `React.createContext`) and returns the current context value for that context.
*   The component calling `useContext` will always re-render when the context value (from the nearest `Provider`) changes.

```javascript
// Consuming with useContext Hook (in a functional component)
import React, { useContext } from 'react'; // Don't forget to import useContext

// Assume ThemeContext is created elsewhere as:
// const ThemeContext = React.createContext('light');

function ThemedButtonHook() {
  const themeValue = useContext(ThemeContext); // Directly get the value

  return (
    <button style={{ background: themeValue === 'dark' ? '#333' : '#FFF', color: themeValue === 'dark' ? '#FFF' : '#333' }}>
      I am styled by theme (hook): {themeValue}
    </button>
  );
}
```

**Comparison: `Context.Consumer` vs. `useContext`**

| Feature          | `Context.Consumer`                                   | `useContext` Hook                                         |
| :--------------- | :--------------------------------------------------- | :-------------------------------------------------------- |
| **Component Type** | Functional Components & Class Components           | **Functional Components Only**                            |
| **Syntax**       | Render prop pattern (function as a child)            | `const value = useContext(MyContext);`                    |
| **Readability**  | Can lead to nesting and be less direct               | Generally cleaner, more readable, less nesting            |
| **Code Structure** | Introduces an extra layer of component nesting       | Flat, value is directly available in the component scope  |
| **Logic Placement**| Logic using context value is inside the render prop  | Logic using context value is directly in the component body, can easily be combined with other Hooks (`useEffect`, `useState`) |
| **Multiple Contexts** | Requires nesting multiple `<Consumer>` components | Can call `useContext` multiple times for different contexts |
| **Introduced**   | Part of the original Context API (React 16.3)        | React 16.8 (with Hooks)                                   |

**Why `useContext` is Generally Preferred (in Functional Components):**

1.  **Readability and Simplicity:** It avoids the "render prop" syntax, making the code look more like standard JavaScript and easier to follow.
2.  **Reduced Nesting:** No need for an extra `<Consumer>` component wrapper, leading to a flatter component tree in your JSX.
3.  **Easier Integration with Other Hooks:** You can easily use the context value alongside `useState`, `useEffect`, etc., within the same functional component scope.
    ```javascript
    function UserProfile() {
      const currentUser = useContext(UserContext);
      const [posts, setPosts] = useState([]);

      useEffect(() => {
        if (currentUser) {
          // fetchPostsForUser(currentUser.id).then(setPosts);
        }
      }, [currentUser]); // Re-run effect if currentUser (from context) changes

      if (!currentUser) return <p>Please log in.</p>;
      return <div>Welcome, {currentUser.name}! {/* Display posts */}</div>;
    }
    ```
4.  **Improved Developer Experience:** It's generally considered more ergonomic and aligns better with the modern Hooks-based way of writing React components.

**In Summary:**

*   The **React Context API** is the overall system for sharing state across components without prop drilling. It involves creating a context and providing a value.
*   **`useContext`** is a Hook that provides a convenient and clean way for *functional components* to *consume* values from that Context API. It's the modern replacement for using the `<Context.Consumer>` component in functional components.
*   You will still use `React.createContext()` and `<MyContext.Provider>` regardless of whether you consume with `<Context.Consumer>` or `useContext`.
*   For class components, you'd still use `<Context.Consumer>` or `static contextType`.



