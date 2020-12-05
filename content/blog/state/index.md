---
title: React state basics
date: "2020-12-05"
---

![Empire State Building](./roemer-overdiep-kvRhUfRqadE-unsplash.jpg "Photo by [roemer overdiep](https://unsplash.com/@rumor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/state?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this entry I will describe the basics of state in React.

## What is state?

State in React allows us hold and manage information about given component instance. Often it is used to describe what its current "situation" is. Few examples:

- an element from the list is selected or not
- a timer is running or not, and it indicates a specific amount of time left
- an input field is empty or contains some value, which might be correct or wrong

In React we can store these kind of information in components state. We can define a state with its initial value, read it and modify it according to our needs. Each component instance can have its own state, which is independent of states of other instances. If we add a state to component, this component becomes a stateful component.

Important thing to understand is that state is encapsulated. It is private to the component and cannot be accessed from other components. Also state can be modified only by the component itself.

The difference between state and props is that props are passed to the component from outside, state however is managed from within the component.

State can be added to both function and class components. In this post we will focus on class components. Managing state in function components will be described in a separate post, since it requires also introducing the concept of hooks in React.

## Adding state

Code below shows how to add state to a component. In the example we are making a simple Lamp component that renders the text `Lamp is off` or `Lamp is on`, depending on the current state:

```javascript
class Lamp extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      switchedOn: false,
    };
  }

  render() {
    return <p>Lamp is {this.state.switchedOn ? "on" : "off "}</p>;
  }
}
```

The initial state is defined within the constructor. State is a normal object that holds our state properties and their values. It can have as many properties as we need. In the example, we are defining one `switchedOn` state property with `false` as its initial value. In the render method we are accessing the state by pointing to a property of `this.state` object. At this point, when component is being rendered we see `Lamp is off` on the screen.

## Modifying state

To modify state, we should use special React `this.setState()` method. As an argument, we should provide an object with properties we want to modify and their new values. Let's consider a state object with following properties and their values:

```javascript
{
  humidity: 40,
  temperature: 22,
  pressure: 101
}
```

We can update whole state at once, e.g.:

```javascript
this.setState({
  humidity: 62,
  temperature: 28,
  pressure: 99,
});
```

We can also update only a part of the state:

```javascript
this.setState({
  temperature: 30,
});
```

In this case a shallow merge is performed, updating the property and keeping rest of the state as it was.

There are also situations when to update the state we need to know the previous state values, for example when we need to increment a counter. In cases like this, we should also use `this.setState()` method, but with different argument. Instead of passing an object, here we are passing a function that receives a previous state and props as an arguments, and returns object with new state:

```javascript
this.setState((prevState, props) => ({
  ...
}));
```

As an example, let's consider following state:

```javascript
{
  counter: 51;
}
```

To increment this counter, in the `setState()` method we are passing a function that receives two arguments. The first argument contains a state of component at the time of the update (in other words - previous state). Second argument contains props of the component at the time of the update. The function returns an object with state properties to update along with their new values:

```javascript
this.setState((prevState, props) => ({
  counter: prevState.counter + props.incrementValue,
}));
```

Going back to our Lamp example, code below shows how we could add two new features - switching the lamp on and off, and additionally counting how many times the switch was used:

```javascript
class Lamp extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      switchedOn: false,
      timesSwitched: 0,
    };
  }

  switch() {
    this.setState(prevState => ({ switchedOn: !prevState.switchedOn }));
    this.setState(prevState => ({
      timesSwitched: prevState.timesSwitched + 1,
    }));
  }

  render() {
    return (
      <>
        <p>Lamp is {this.state.switchedOn ? "on" : "off "}</p>
        <p>Switch was used {this.state.timesSwitched} times</p>
        <button onClick={() => this.switch()}>Switch</button>
      </>
    );
  }
}
```

We introduced new `timesSwitched` state property, with `0` as its initial value. We also created a `switch()` method, which is responsible for managing the state when lamp is switched. Inside the method we firstly update the `switchedOn` state property. The new value is a negation of previous one: `!prevState.switchedOn`. We are not passing the `props` argument to the function since we don't need it here. In next line we are incrementing the `timesSwitched` counter by one, also using the previous state value. Lastly, in the `render()` method we added new paragraph that displays the `timesSwitched` state property value. We also added a button that invokes the new `switch()` method every time we click on it.

See the working example on CodePen:
https://codepen.io/mkapica/pen/qBabeYy

## Passing state to children

As mentioned at the beginning, state is private to the component and cannot be accessed directly by other components. But nothing prevents us from passing the state down as a normal prop. In the example below, we are passing the `productPrice` state property to the `PriceComponent` as a `price` prop:

```javascript
<PriceComponent price={this.state.productPrice} />
```

Inside the `PriceComponent` this value is treated just as normal prop, and this component is not aware that this value comes from the state of its parent.

## Updating state from children components

Since state can only be modified by the component which holds it, we cannot directly update state from other components. But within our component we can define a method that will update the state, and pass this method as a property to the child component. Child component can then invoke the method according to its internal logic (e.g. when some event occurs). Example below shows this in action:

```javascript
class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 0,
    };
  }

  incrementValue() {
    this.setState({ value: this.state.value + 1 });
  }

  render() {
    return (
      <div>
        <Button handleClick={() => this.incrementValue()} />
        <Display value={this.state.value} />
      </div>
    );
  }
}

function Button(props) {
  return <button onClick={props.handleClick}>Click me!</button>;
}

function Display(props) {
  return <div>{props.value}</div>;
}
```

The `Parent` holds the state with `value` property. The `incrementValue()` method is responsible for updating the state. We pass this method to the child `Button` component within `handleClick` prop. The method is invoked when we click on the `button`, which updates the value by 1. The `Display` component is responsible for displaying the current value.

See working example on CodePen:
https://codepen.io/mkapica/pen/rNMezOM

## Things to avoid

There are some things we should avoid when working with state in React.

### Modifying state directly

First of all, we should never modify state directly like this:

```javascript
this.state.switchedOn = true;
```

Instead we should use `this.setState()` method:

```javascript
this.setState({ switchedOn: true });
```

The reason for that is because React needs to track the state changes in order to re-render the component when it is modified. If we use `this.setState()`, React detects that the state is to be changed and acts accordingly. If we mutate the state directly, the re-rendering will not happen.

### Accessing state directly during update

We should avoid accessing state directly when we are updating the value. Writing code like below is a bad practice:

```javascript
this.setState({ switchedOn: !this.state.switchedOn });
```

Instead we should use always the `this.setState()` method and pass function with previous state, as shown in previous example:

```javascript
this.setState(prevState => ({ switchedOn: !prevState.switchedOn }));
```

This is because the `this.setState()` method is not invoked immediately. Multiple `setState()` calls are internally batched and asynchronously invoked by React, which tracks changes to the state. If we follow the reccommended way, React will ensure that we are updating the state basing on factual previous state and basing on props that were passed to component at that time. Not following this may led to undesirable outcomes.
