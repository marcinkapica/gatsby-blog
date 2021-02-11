---
title: React useState hook
date: "2021-02-11"
---

![A photo of a jacket and a paddle on the wall](./clark-street-mercantile-RfghQeRGr2I-unsplash.jpg "Photo by [Clark Street Mercantile](https://unsplash.com/@mercantile?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/hook?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article we will take a look at the `useState` React hook.

## Introduction

Before hooks were introduced to React, introducing state to component required us to rewrite it and use the class syntax. In the version 16.8 React introduced new hooks API along with few ready-to-use hooks. The `useState` hook is one of them. It allows us to introduce state to function components, which makes React apps a little simpler and in my opinion also more readable.

## Basic usage

Code below shows and example of basic usage of the `useState` hook. In this example we are making a simple component that renders the text `Lamp is off` or `Lamp is on`, depending on the current state which we can toggle using a button:

```jsx
import React, { useState } from "react";

const Lamp = () => {
  const [switchedOn, setSwitchedOn] = useState(false);
  const handleClick = () => {
    setSwitchedOn(!switchedOn);
  };

  return (
    <>
      <p>Lamp is {switchedOn ? "on" : "off "}</p>
      <button onClick={handleClick}>Switch</button>
    </>
  );
};
```

Firstly, we need to import the hook from `react` module. As visible, we are creating a plain function component which will handle our logic. The most important part is the `const [switchedOn, setSwitchedOn] = useState(false)` fragment. In this line we are using the `useState` hook. The hook accepts an initial state value and returns an array of two values - the current state value and a function to update it (a setter). We are using array destructuring syntax to assign the value and the setter to constants that we can use in our component. The important thing to note here is that when using hooks our state does not need to be an object. When using `useState` hook, we are declaring a so-called "state variable" that can store any value - it may be an object, but also a single value such as a boolean value as we can see in our example. The important thing to be aware of regarding state variables is that their values are preserved by React between re-renders, similarly as it was in case of state object in class components.

After creating our state variable and its setter, we can both read the state value and update it. As shown in the example, we are accessing our state variable in the ternary operator in the paragraph tag:

```jsx
<p>Lamp is {switchedOn ? "on" : "off "}</p>
```

We simply refer to the state variable as any other variable.

Updating of the state is done through the previously created setter, as we can see in our `handleClick` function:

```jsx
setSwitchedOn(!switchedOn);
```

We just need to provide new value of the state as an argument. The setter will replace the old value with the new provided one. If our new value depends on the current state, we simply refer to the state variable, as shown in the example. There is no need for special function that uses `prevState` as it was in case of class components.

When a state variable is updated through a setter function, the component will be re-rendered.

## Multiple state variables

A great aspect of React hooks is that we can use them multiple times in a single component. The same applies to the `useState` hook. We can therefore declare multiple state variables that will hold different state values. The example below expands our `Lamp` component and adds a new state variable that holds the number of times the button was clicked:

```jsx
const Lamp = () => {
  const [switchedOn, setSwitchedOn] = useState(false);
  const [timesSwitched, setTimesSwitched] = useState(0);
  const handleClick = () => {
    setSwitchedOn(!switchedOn);
    setTimesSwitched(timesSwitched + 1);
  };

  return (
    <>
      <p>Lamp is {switchedOn ? "on" : "off "}</p>
      <p>Switch was used {timesSwitched} times</p>
      <button onClick={handleClick}>Switch</button>
    </>
  );
};
```

As we can see, we simply declare new pair of constants holding state value (`timesSwitched`) and respective setter function (`setTimesSwitched`) and invoke `useState` again to initialize the values. The important thing to be aware of is that each state variable is independent - there is no merging them to a single state object.

## Updating objects in state variables

The important difference between using hook and managing state in class components is that in case we keep an object as a value of a state variable, when updating we need to update it fully. This is because a setter always replaces the old state variable value with new one - there is no merging. To show an example, lets assume that we initialize a state variable with following object:

```jsx
const [animal, setAnimal] = useState({ name: "Barky", age: 3 }); // `animal` state variable initialized with two properties
```

Even when updating e.g. the `age` property, we still need to provide full object — both `age` and `name` — to the setter:

```jsx
// CORRECT
const handleChange = () => {
  setAnimal({ name: "Barky", age: 4 });
};

// INCORRECT
const handleChange = () => {
  setAnimal({ age: 4 }); // this will override the object and afterwards `animal` will contain only the `age` property
};
```
