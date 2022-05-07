---
slug: react-custom-hooks
title: React Custom Hooks
authors: [mahesh]
tags: [React]
---

## Definition

Custom Hook is a JavaScript function which we create by ourselves, when we want to share logic between other JavaScript functions.

It allows you to reuse some piece of code in several parts of your app.

## Useful points

- The main reason to write a custom hook is for code reusability.
- For example, instead of writing the same code across multiple components that use the same common stateful logic (say a “setState” or localStorage logic), you can put that code inside a custom hook and reuse it.
- Custom Hooks are functions. Usually, they start with the word “use” (important convention).
- Custom Hooks allow us to access the React ecosystem in terms of hooks, which means we have access to all the known hooks like useState, useMemo, useEffect, etc.
- This mechanism enables the separation of logic and view.

## Rules of custom hooks

** *Note: The same rules of react hooks will be applicable to the custom hooks.**

### 1. Only Call Hooks at the Top Level

**Always use Hooks at the top level of your React function.** By following this rule, you ensure that Hooks are called in the same order each time a component renders. That's what allows React to correctly preserve the state of Hooks between multiple useState and useEffect calls.

### 2. Only Call Custom hooks from React Functions

**Don't call custom hooks from regular Javascript functions.** Instead, you can

- ✅ Call Custom hooks from React function components
- ✅ Call Custom hooks from other Custom hooks

### 3. Don't call custom hooks conditionally

- If you want to run an effect conditionally, we can put that condition inside our Custom hook

### 4. Don't call custom hooks in loops

- If you want to run an effect without leading bugs, we can put that loops inside our Custom hook

## Hooks are better than classes

- Hooks allow you to use local state and other React features without writing a class.
- Hooks are special functions that let you “hook onto” React state and lifecycle features inside function components.

## Example

### useEventListener

- If you find yourself adding a lot of event listeners using useEffect you might consider moving that logic to a custom hook.
- In the recipe below we create a useEventListener hook that handles checking if addEventListener is supported, adding the event listener, and removal on cleanup.

### 1. Create `useEventListener.js` file

```js
import { useEffect, useRef } from "react";

export default function useEventListener(eventName, handler, element = window) {
  // create a ref that store handler
  const savedHandler = useRef(null);

  // update ref.current value if handler changes
  // this allows our effect below to always get latest handler
  // without us needing to pass it in effect dependencies array
  // and potentially cause effect to re-run every render
  useEffect(() => {
    savedHandler.current = handler;
  }, [handler]);

  useEffect(() => {
    // make sure element support addEventListener
    const isSupported = element && element.addEventListener;
    if (!isSupported) return;

    // create event listener that calls handler function stored in ref
    const eventListener = (event) => savedHandler?.current(event);

    // add event listener to element
    element.addEventListener(eventName, eventListener);

    // remove event listener on cleanup
    return () => {
      element.removeEventListener(eventName, eventListener);
    };
  }, [eventName, element]);
}
```

### 2. Create `App.js` file

- Import `useEventListener` in `App.js` file

```js
import React, { useState, useEffect, useRef, useCallback } from "react";
import useEventListener from "./useEventListener";

export default function App() {
  const [coords, setCoords] = useState({ x: 0, y: 0 });
  const [targetElement, setTargetElement] = useState(null);

  const buttonRef = useRef(null);
  const headingRef = useRef(null);

  // event utilizing useCallback, so that reference never changes
  const getCoordinatesOfWindow = useCallback((event) => {
    const { clientX, clientY } = event;
    // update coordinates
    setCoords({ x: clientX, y: clientY });
  });

  // event utilizing useCallback, so that reference never changes
  const getClickedTargetElementTagName = useCallback((event) => {
    const { currentTarget } = event;
    // update element tag name
    setTargetElement(currentTarget.tagName);
  });

  // this hook is called when your mouse point is changing and give the coordinates of the mouse pointer 
  // further we are showing the mouse pointer coordinates in UI
  useEventListener("mousemove", getCoordinatesOfWindow);

  // this hook is called when you click on <button></button> and give the tag name of the button element
  // further we are showing in the UI
  useEventListener("click", getClickedTargetElementTagName, buttonRef?.current);

  // this hook is called when you click on <h1></h1> and give the tag name of the h1 element
  // further we are showing in the UI
  useEventListener("click", getClickedTargetElementTagName, headingRef?.current);

  return (
    <div>

      <h1>
        The mouse coordinates ({coords.x}, {coords.y})
      </h1>

      <button ref={buttonRef}>Button</button>

      <h1 style={{ cursor: "pointer" }} ref={headingRef}>
        Heading
      </h1>

      <h2>You clicked the {targetElement}</h2>

    </div>
  );
}
```

## Conclusion

I hope this post aids you in better understanding the custom hooks, and will allow you to creating custom hook. If it has been useful to you, please share and spread the word.

[Edit on StackBlitz ⚡️](https://stackblitz.com/edit/react-xz2srv) or check the [github repo link](https://github.com/maheshmuttinti/custom-hooks)
