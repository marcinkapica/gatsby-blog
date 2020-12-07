---
title: Handling events in React
date: "2020-12-07"
---

![Confetti on a concert](./pablo-heimplatz-ZODcBkEohk8-unsplash.jpg "Photo by [Pablo Heimplatz](https://unsplash.com/@pabloheimplatz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/event-handling?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article I will describe how to handle events in React.

## Handling events

To handle an event in React we need to:

1. add an event listener to a native DOM element by referencing one of React built-in events,
2. provide the above listener with a handler function that will be invoked when the event occurs.

Before going into details, let's see this in action. The code below is representing a `Select` component. It displays a select form element with 4 options to choose from. Upon changing selected option an alert will be displayed:

```javascript
function Select() {
  function handleSelect(e) {
    alert(`"${e.target.value}" selected`);
  }
  return (
    <select onChange={handleSelect}>
      <option value="avocado">Avocado</option>
      <option value="carrot">Carrot</option>
      <option value="potato">Potato</option>
      <option value="banana">Banana</option>
    </select>
  );
}
```

See working example on CodePen:
https://codepen.io/mkapica/pen/yLaOQJG

The line that is the most important is this one `<select onChange={handleSelect}>`. Here we are doing the two things mentioned at the beginning of the article.

Firstly we are adding a listener to the `change` event on the `select` element. This is done by referencing the built-in `onChange` event. It is important to know that in React we are not referencing the standard DOM events. React added a wrapper around the native browser events. This wrapper is called `SyntheticEvent` and its role is to ensure that the events' behavior will be the same across different browsers. The events defined within `SyntheticEvent` are named using `camelCase` convention. This is why we are referencing `onChange` here instead of standard DOM `onchange` event. React supports multiple events, such as `onClick`, `onMouseLeave`, `onPaste`, `onSubmit` etc.

Secondly, we are passing a handler function to the event listener. In this example our handler is a reference to function `handleSelect`. Therefore, when the button is clicked, this function is invoked, running `alert` as a result.

## Event object

The previous example also demonstrates another important thing - the handler function receives event object as its argument. In the example we are using the `target.value` property of this object to get the value of selected option.

```javascript
function handleSelect(e) {
  alert(`"${e.target.value}" selected`);
}
```

As a reminder - this is not the standard `Event` type object. If we check the type of this object e.g. in browser console we see that it is in fact `SyntheticBaseEvent`. But the interface is the same as of the native browser event, therefore we can still use the standard methods such as `preventDefault()` or `stopPropagation()`.

## Passing methods to handlers

There is an additional thing to be aware of when we pass the method of a class component as an event handler. Let's consider a different example. Here we define a `Counter` component. It displays a counter value and a button that will increment it by 1 upon clicking:

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 0,
    };
  }

  increment() {
    this.setState(prevState => ({
      value: prevState.value + 1,
    }));
  }

  render() {
    return (
      <>
        <p>Value: {this.state.value}</p>
        <button onClick={() => this.increment()}>+1</button>
      </>
    );
  }
}
```

See the working example on CodePen:
https://codepen.io/mkapica/pen/BaLKqBo

In this example our handler is an anonymous arrow function `() => this.increment()`. Thus, when the button is clicked, this function is invoked, calling the `increment()` method of our component as a result.

### Passing arrow function

There is an important reason why an arrow function is used here, instead of directly passing reference to the method like this `onClick={this.increment}`. This is because when passing the method as a callback, we need to ensure the proper context is maintained, so when invoked the `this` keyword still points to our component instance. Using an arrow function will not modify `this`, so the code will work as expected. If we use this approach `onClick={this.increment}`, we will get a `Cannot read property 'setState' of undefined` error, because the callback will lose its previous context.

Using the anonymous arrow function approach may however result in a problem, because this creates a new callback every time the component renders. This might lead to unnecessary re-rendering if we pass this callback as a prop to children components. To avoid this, we can use two other approaches when using a method as an event handler.

### Binding method

Instead of using the arrow function approach, we can explicitly bind `this` to the method:

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 0,
    };
    this.increment = this.increment.bind(this);
  }

  increment() {
    this.setState(prevState => ({
      value: prevState.value + 1,
    }));
  }

  render() {
    return (
      <>
        <p>Value: {this.state.value}</p>
        <button onClick={this.increment}>+1</button>
      </>
    );
  }
}
```

In the constructor we are binding `this`, which allows passing method to handler callback without need for the arrow function wrapper. When using this approach we need to remember to bind every method that is used as a callback.

### Using public class fields syntax

There is another possibility if we don't want to use either arrow function or binding in the constructor. In this approach, we can utilize the _public class fields_ syntax. Here is how we can use it:

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 0,
    };
  }

  increment = () => {
    this.setState(prevState => ({
      value: prevState.value + 1,
    }));
  };

  render() {
    return (
      <>
        <p>Value: {this.state.value}</p>
        <button onClick={this.increment}>+1</button>
      </>
    );
  }
}
```

In this case we define `increment` as a class field that holds an arrow function with our logic. In this case the function will be bound to the instance, so we can pass it safely as a callback to the event handler. The important thing to know is that this is still an experimental feature, not yet part of the standard. Nevertheless, React documentation is recommending it already as a feasible solution.

## Passing arguments

Sometimes we need to pass one or multiple arguments to the event handler. For example our `increment` method from previous examples could be slightly modified to accept an argument with the value we want to increment by:

```javascript
increment(incrementVal) {
  this.setState(prevState => ({
    value: prevState.value + incrementVal,
  }));
};
```

There are two approaches recommended by React docs for passing the argument to the handler.

### Arrow function

A first approach is to use an anonymous arrow function:

```jsx
<button onClick={(e) => this.increment(this.props.incrementVal, e)}>
```

We need to remember about passing _event_ argument. In this case if we don't pass it explicitly, it won't be available.

### Using bind

A second approach is to use `bind()` method:

```jsx
  <button onClick={this.increment.bind(this, this.props.incrementVal)}>
```

As a first argument this method accepts an object that we want to pass as `this`, but what is important here, next optional arguments are [_arguments to prepend to arguments provided to the bound function when invoking func_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind). In other words, we can pass our explicit arguments here, and other arguments will be added after them. This is why we can omit explicitly adding event argument here, since it will be passed automatically at the end.

## Events work only on native DOM elements

In React, we can handle events only for the native DOM elements. The `onClick` event listener added to the native `button` DOM element will work as expected. However, if we try the same on the custom React component, we will not create an event listener. In this case we simply create an `onClick` prop on the component. Therefore, in React instead of listening to events directly on the component, we need to listen to them in the underlying DOM elements.

If we have a tree of components, and want to pass an event handler from parent to child component, then we should pass the handler as a prop. See example below, which is a slight modification of the `Counter` component we saw before. In this example, we have a child `Button` component that holds the `button` element:

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 0,
    };
  }

  increment = () => {
    this.setState(prevState => ({
      value: prevState.value + 1,
    }));
  };

  render() {
    return (
      <div>
        <p>Value: {this.state.value}</p>
        <Button onClick={this.increment} />
      </div>
    );
  }
}

function Button(props) {
  return <button onClick={props.onClick}>+1</button>;
}
```

In this example, the `onClick` on `Button` component is not an event listener - it is a normal prop. We could name it differently and there would be no difference in the behavior. We use this prop to pass down the `increment` method down. Inside the `Button` component, we have the factual event listener attached to the `button` element, where we reference the handler from the prop.
