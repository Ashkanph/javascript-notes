

* Basic hooks: useState, useEffect, useContext
* Additional hooks: useReducer, useCallback, useMemo, useRef, useImperativeHandle, useLayoutEffect, useDebugValue

--- 

* useState
  * previous and initial value:
    ```javascript
    const [count, setCount] = useState(initialCount);

    setCount(prevCount => prevCount - 1)
    setCount(initialCount)
    ```

  * Unlike the setState method found in class components, useState does not automatically merge update objects:
    ```javascript
    setState(prevState => {
      // Object.assign would also work
      return {...prevState, ...updatedValues};
    });
    ```
  * اگر هر دو استیت برابر باشند ری‌اکت رندر نمی‌کند (React uses the `Object.is` comparison algorithm.)

* useReducer
  * usually preferable to useState when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one. useReducer also lets you optimize performance for components that trigger deep updates because you can pass dispatch down instead of callbacks.
    ```javascript
    const [state, dispatch] = useReducer(reducer, initialArg, init);
    // Accepts a reducer of type (state, action) => newState,
    ```

* useCallback
  * Returns a memoized callback.
    ```javascript
    const memoizedCallback = useCallback(
      () => {
        doSomething(a, b);
      },
      [a, b],
    );
    ```
  * `useCallback(fn, deps)` is equivalent to `useMemo(() => fn, deps)`
  * One rudimentary way to measure the position or size of a DOM node is to use a callback ref
    ```javascript
    function MeasureExample() {
      const [height, setHeight] = useState(0);

      const measuredRef = useCallback(node => {
        if (node !== null) {
          setHeight(node.getBoundingClientRect().height);
        }
      }, []);

      return (
        <>
          <h1 ref={measuredRef}>Hello, world</h1>
          <h2>The above header is {Math.round(height)}px tall</h2>
        </>
      );
    }
    ```


* useMemo
  * Returns a memoized value.
  * تنها در صورتی که مقادیر داخل آرایه تغییر کرده باشند دوباره محاسبه می‌کند. اگر آرایه را خالی بگذاریم فقط یک بار و اگر آرایه‌ای ندیم هربار با هر رندر دوباره اجرا می‌شود و هر بار اون مقدار رو از سر می‌سازه
    ```
    const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
    ```
  * This optimization helps to avoid expensive calculations on every render.
  * If no array is provided, a new value will be computed on every render.
  * `‍‍‍‍useMemo` runs during rendering. Don’t do anything there that you wouldn’t normally do while rendering. For example, side effects belong in `useEffect`, not `useMemo`.
  * You may rely on `useMemo` as a performance optimization, not as a semantic guarantee

* useRef
  * useRef returns a mutable ref object whose .current property is initialized to the passed argument (initialValue)
  * A common use case is to access a child imperatively:
    ```javascript
    function TextInputWithFocusButton() {
      const inputEl = useRef(null);
      const onButtonClick = () => {
        // `current` points to the mounted text input element
        inputEl.current.focus();
      };
      return (
        <>
          <input ref={inputEl} type="text" />
          <button onClick={onButtonClick}>Focus the input</button>
        </>
      );
    }
    ```
  * `useRef` doesn’t notify you when its content changes. Mutating the .current property doesn’t cause a re-render. If you want to run some code when React attaches or detaches a ref to a DOM node, you may want to use a callback ref (`useCallback`) instead.

* useImperativeHandle
  * برای استفاده از رف‌های فرزند در والد استفاده می‌شود
  * در جاهایی که از فورواردرف استفاده شده
  * مثل همیشه بهتره در بیشتر موارد ازش صرف نظر بشه
    ```javascript
    function FancyInput(props, ref) {
      const inputRef = useRef();
      useImperativeHandle(ref, () => ({
        focus: () => {
          inputRef.current.focus();
        }
      }));
      return <input ref={inputRef} ... />;
    }
    FancyInput = forwardRef(FancyInput);
    ```

* useLayoutEffect
  * identical to `useEffect`, but it fires synchronously after all DOM mutations.
  * Prefer the standard `useEffect` when possible to avoid blocking visual updates.
  * اگر به دام و لایوت نیاز داریم باید از این استفاده کنیم تا از یوزافکت
  * هیچکدام از این دو تا زمان دانلود کامل باندل قابل اجرا نیستند پس باید در سرورساید رندرینگ این موضوع را در نظر بگیریم. 
  * To exclude a component that needs layout effects from the server-rendered HTML, render it conditionally with `showChild && <Child />` and defer showing it with `useEffect(() => { setShowChild(true); }, [])`. This way, the UI doesn’t appear broken before hydration.

* useDebugValue
  * Can be used to display a label for custom hooks in React DevTools.
  * بهتره فقط برای کاستوم هوک‌هایی استفاده بشه که بطور مشترک استفاده می‌شوند
  * پارامتر دومی هم میگیره که برای فرمت کردن استفاده میشه:
    ```javascript
    // برای لاگ کردن دیت، ولی قبلش تبدیلش به دیت‌استرینگ
    useDebugValue(date, date => date.toDateString());
    ```

---

```javascript
const { useState, useEffect, useLayoutEffect } = React;
const Parent = props => {
    console.log("Parent");
    useEffect(() => console.log("Parent useEffect"));
    useLayoutEffect(() => console.log("Parent useLayoutEffect"));

    return (
        <div>
            <h2>Parent</h2>
            <Child />
        </div>
    );
};
const Child = () => {
    console.log("Child");
    useEffect(() => console.log("Child useEffect"));
    useLayoutEffect(() => console.log("Child useLayoutEffect"));

    return <p>Child</p>
};
ReactDOM.render(<Parent />, document.getElementById("root"));

/*
 * What will be logged?
 *
 * Parent, Child, Child useLayoutEffect, Parent useLayoutEffect, Child useEffect, Parent useEffect
 *
 * https://codepen.io/dshook/pen/eYpNLMZ?editors=1011
 */
```
