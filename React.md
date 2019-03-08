

### Immutability benefits

* Complex Features Become Simple. (For example undoing a certain action)

* Detecting Changes (Because we have the original unmuted data)

* Determining When to Re-Render in React. It helps to build `pure components` in React. 

  * helps to determine when a component requires re-rendering (with shouldComponentUpdate()).

* Event loop: Refers to the loop that checks if there are more messages in the message queue.

---

### Code-splitting

* Using dynamic import(): The webpack will split the codes automatically


  ```javascript
  //Before:
  import { add } from './math';

  console.log(add(16, 26));


  //After:
  import("./math").then(math => {
    console.log(math.add(16, 26));
  });
  ```

* React.lazy
  * If the module containing the OtherComponent is not yet loaded by the time MyComponent renders, we must show some fallback content while we’re waiting for it to load - such as a loading indicator. This is done using the Suspense component.

  ```javascript
  //Before:
  import OtherComponent from './OtherComponent';

  function MyComponent() {
    return (
      <div>
        <OtherComponent />
      </div>
    );
  }

  //After:
  const OtherComponent = React.lazy(() => import('./OtherComponent'));
  //This must return a Promise which resolves to a module with a default export containing a React component. 

  function MyComponent() {
    return (
      <div>
  	<Suspense fallback={<div>Loading...</div>}>
             <OtherComponent />
          </Suspense>
        <OtherComponent />
      </div>
    );
  }

  ```

  * Use `Error boundaries` if the module failed to load.

  ```javascript
  import MyErrorBoundary from './MyErrorBoundary';
  const OtherComponent = React.lazy(() => import('./OtherComponent'));
  const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

  const MyComponent = () => (
    <div>
      <MyErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <section>
            <OtherComponent />
            <AnotherComponent />
          </section>
        </Suspense>
      </MyErrorBoundary>
    </div>
  );
  ```

* You can perform Route-based code splitting too.

---

### Context

* Context provides a way to pass data through the component tree without having to pass props down manually at every level.
  * To share data that can be considered “global” for a tree of React components (current authenticated user, theme, or preferred language)

  ```javascript
  //theme-context.js
  // Make sure the shape of the default value passed to
  // createContext matches the shape that the consumers expect!
  export const ThemeContext = React.createContext({
    theme: themes.dark,
    toggleTheme: () => {},
  });


  //theme-toggler-button.js
  import {ThemeContext} from './theme-context';
  function ThemeTogglerButton() {
    // The Theme Toggler Button receives not only the theme
    // but also a toggleTheme function from the context
    return (
      <ThemeContext.Consumer>
        {({theme, toggleTheme}) => (
          <button
            onClick={toggleTheme}
            style={{backgroundColor: theme.background}}>
            Toggle Theme
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
  export default ThemeTogglerButton;


  //app.js
  import {ThemeContext, themes} from './theme-context';
  import ThemeTogglerButton from './theme-toggler-button';
  
  class App extends React.Component {
    constructor(props) {
      super(props);
  
      this.toggleTheme = () => {
        this.setState(state => ({
          theme:
            state.theme === themes.dark
              ? themes.light
              : themes.dark,
        }));
      };
  
      // State also contains the updater function so it will
      // be passed down into the context provider
      this.state = {
        theme: themes.light,
        toggleTheme: this.toggleTheme,
      };
    }
    render() {
      // The entire state is passed to the provider
      return (
        <ThemeContext.Provider value={this.state}>
          <Content />
        </ThemeContext.Provider>
      );
    }
  }
  function Content() {
    return (
      <div>
        <ThemeTogglerButton />
      </div>
    );
  }  
  ReactDOM.render(<App />, document.root);

  ```

---

### Error boundaries

* React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed. They catch errors during rendering, in lifecycle methods, and in constructors of the whole tree below them.

  * Error boundaries do not catch errors for:

    * Event handlers (learn more)
    * Asynchronous code (e.g. setTimeout or requestAnimationFrame callbacks)
    * Server side rendering
    * Errors thrown in the error boundary itself (rather than its children)

  * A class component becomes an error boundary by defining one or both of theese lifecycles:
    * static getDerivedStateFromError(): To render a fallback UI after an error has been thrown. 
    * componentDidCatch(): To log error information.
    
  ```javascript
  class ErrorBoundary extends React.Component {
    constructor(props) {
      super(props);
      this.state = { hasError: false };
    }

    static getDerivedStateFromError(error) {
      // Update state so the next render will show the  fallback UI.
      return { hasError: true };
    }

    componentDidCatch(error, info) {
      // You can also log the error to an error reporting   service
      logErrorToMyService(error, info);
    }

    render() {
      if (this.state.hasError) {
        // You can render any custom fallback UI
        return <h1>Something went wrong.</h1>;
      }

      return this.props.children; 
    }
  }
  ```
---

### Refs

* Provide a way to access DOM nodes or React elements created in the render method.
  * Managing focus, text selection, or media playback.
  * Triggering imperative animations.
  * Integrating with third-party DOM libraries.

  ```javascript
    class CustomTextInput extends React.Component {
    constructor(props) {
      super(props);
      // create a ref to store the textInput DOM element
      this.textInput = React.createRef();
      this.focusTextInput = this.focusTextInput.bind(this);
    }

    focusTextInput() {
      // Explicitly focus the text input using the raw DOM  API
      // Note: we're accessing "current" to get the DOM   node
      this.textInput.current.focus();
    }

    render() {
      // tell React that we want to associate the <input>   ref
      // with the `textInput` that we created in the  constructor
      return (
        <div>
          <input
            type="text"
            ref={this.textInput} />

          <input
            type="button"
            value="Focus the text input"
            onClick={this.focusTextInput}
          />
        </div>
      );
    }
  }
  ```

---

### Forwarding Refs

* A technique for automatically passing a ref through a component to one of its children.

[Read more](https://reactjs.org/docs/forwarding-refs.html)

---

### Higher-Order Components (HOC)

* An advanced technique in React for reusing component logic. HOCs are not part of the React API. They are a pattern that emerges from React’s compositional nature.

  * HOCs are common in third-party React libraries, such as Redux’s connect and Relay’s createFragmentContainer.

[Read more](https://reactjs.org/docs/higher-order-components.html)

---

### Portal

* Provide a first-class way to render children into a DOM node that exists outside the DOM hierarchy of the parent component

[Read more](https://reactjs.org/docs/portals.html)

---

### Render props

* The term “render prop” refers to a technique for sharing code between React components using a prop whose value is a function.

  ```javascript
  <DataProvider render={data => (
    <h1>Hello {data.target}</h1>
  )}/>
  ```

[Read more](https://reactjs.org/docs/render-props.html)

---

### Strict mode

* StrictMode is a tool for highlighting potential problems in an application. Like Fragment, StrictMode does not render any visible UI. It activates additional checks and warnings for its descendants.

  * Only in development mode
  * StrictMode currently helps with:
    * Identifying components with unsafe lifecycles
    * Warning about legacy string ref API usage
    * Warning about deprecated findDOMNode usage
    * Detecting unexpected side effects
    * Detecting legacy context API

  ```javascript
  import React from 'react';

  function ExampleApplication() {
    return (
      <div>
        <Header />
        <React.StrictMode>
          <div>
            <ComponentOne />
            <ComponentTwo />
          </div>
        </React.StrictMode>
        <Footer />
      </div>
    );
  }
  ```

---

### Uncontrolled Components

* In most cases, we recommend using controlled components to implement forms. In a controlled component, form data is handled by a React component. The alternative is uncontrolled components, where form data is handled by the DOM itself.


* [Read more](https://reactjs.org/docs/uncontrolled-components.html)


---

### Hook

* Hooks are a new addition in React 16.8. They let you use state and other React features without writing a class.

  ```javascript
  import React, { useState } from 'react';

  function Example() {
    // Declare a new state variable, which we'll call   "count"
    const [count, setCount] = useState(0);

    return (
      <div>
        <p>You clicked {count} times</p>
        <button onClick={() => setCount(count + 1)}>
          Click me
        </button>
      </div>
    );
  }
  ```

---

### React.memo and PureComponent

* Class components can bail out from rendering when their input props are the same using PureComponent or shouldComponentUpdate. Now you can do the same with function components by wrapping them in React.memo.

  ```javascript
  const MyComponent = React.memo(function MyComponent(props) {
    /* only rerenders if props change */
  });
  ```

---

### Semantic versioning (semver)

* Given a version number MAJOR.MINOR.PATCH, increment the:
  * MAJOR version when you make incompatible API changes,
  * MINOR version when you add functionality in a backwards-compatible manner, and
  * PATCH version when you make backwards-compatible bug fixes.