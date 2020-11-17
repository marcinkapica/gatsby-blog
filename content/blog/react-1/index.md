---
title: React - total basics
date: "2020-11-14T19:39:38Z"
---

![Coffee mug photo](./unsplash.jpg "Photo by [Danielle MacInnes](https://unsplash.com/@dsmacinnes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/beginning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

This post contains my notes about the React basics that I learned by doing the [Intro to React](https://reactjs.org/tutorial/tutorial.html) tutorial. Text will also contain simple code snippets to demonstrate the described aspects.

I am at the beginning of a journey to master React. The purpose of the text is to deepen my understanding of the concepts that I learned by writing about them (technique proposed on this [skutecznyprogramista.pl](https://skutecznyprogramista.pl/ucz-sie-na-glos/) blog post). There are many more React features and techniques that are not covered here at all - this post is all about the total basics 🙂.

## What is React

Let's start with basics. As stated in the tutorial:

> React is a declarative, efficient, and flexible JavaScript library for building user interfaces.

An important thing to note here is that React is a JavaScript **library**, not a framework. And it specifically is designed for building UI. So for example React will not enforce what library you should use to make an http request. It will also not tell you how to build the whole application. It focuses only on building the UI layer.

The second part of React definition introduces us to the concept of _component_.

> [React] lets you compose complex UIs from small and isolated pieces of code called “components”.

We use components to describe the elements that we want to see in the UI. By combining many components, we create whole UI. Each component takes in parameters called `props`, that we use to pass data to components. This makes them generic and reusable.

## Function and class components

In practice, React components are either defined as classess or functions.

This is how a function component looks:

```javascript
function Button(props) {
  return <button className="btn">{props.displayText}</button>;
}
```

By convention PascalCase is used for writing component names. This component renders a button that displays text passed in the `displayText` prop value. The button has also a `btn` CSS class. Below is the same component definition, but described with class syntax:

```javascript
class Button extends React.Component {
  render() {
    return <button className="btn">{this.props.displayText}</button>;
  }
}
```

Each class component must have a `render()` method, in which we have to return the component view description. Also, when referring to the instance properties we have to remember to use the `this` keyword.

One of the reasons for using class syntax is to create stateful components. This is described in later section in this post.

## JSX

The returned value which looks like HTML is actually a special syntax called _JSX_. During build it is transformed to JavaScript. In JSX within the braces we can put any JavaScript expression, e.g.:

```javascript
<div>{props.number % 2 == 0 ? "Even" : "Odd"}</div>
```

This code will print either _Even_ or _Odd_ depending on the `number` prop passed to the component.

## Using components and passing data as props

Declaring a component is like creating a new custom HTML tag which we can use. Thus to use the component we can simply refer to it within return value of other (parent) component:

```javascript
function App(props) {
  return (
    <div>
      <p>My awesome app</p>
      <Button displayText="Useless button" />
    </div>
  );
}
```

As shown, we use key-value pairs for passing data as props. The syntax looks somewhat similar to adding a HTML attribute. In the example above, we are adding a `displayText` prop with a string value of `Useless button`. If the component props change, React will automatically re-render the component and its children to reflect the changed view.

We can pass any other values through props, e.g. numbers, boolean values, arrays and even objects:

```javascript
<Summary show={true} characterLimit={500} options={{ language: 'en', addEllipsis={false} }} />
```

There is important concept to know - the props can be passed from parent component to its children components, but not the other way around. This is called one-way data flow.

## Simple interaction

In React we can easily define handlers that we want to invoke when particular event occurs. Here is an example:

```javascript
function Button(props) {
  return (
    <button className="btn" onClick={() => console.log("Clicked!")}>
      {props.displayText}
    </button>
  );
}
```

When user clicks the button, the event handler function invokes and logs _Clicked!_ in the console.

## Stateful components

A React component can have its own private state. State is an object that holds some data about the given component instance. To define a stateful component, we need to define it using the class syntax.

This is how we introduce a state to component:

```javascript
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      timesClicked: 0,
    };
  }

  render() {
    return (
      <button className="btn">
        {this.props.displayText} {this.state.timesClicked}
      </button>
    );
  }
}
```

Initial state is defined within the class constructor. In this example we are defining new `timesClicked` property in the state and assigning `0` as its initial value. We are also displaying this value inside the button.

## Updating state

To update the component state, we need to invoke special `this.setState()` method. As an argument, we need to pass the object with the new values of properties which we want to update. Here is an example of updating the state when clicking on the button:

```javascript
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      timesClicked: 0,
    };
  }

  render() {
    return (
      <button
        className="btn"
        onClick={() =>
          this.setState({ timesClicked: this.state.timesClicked + 1 })
        }
      >
        {this.props.displayText} {this.state.timesClicked}
      </button>
    );
  }
}
```

In the `onClick` handler we are incrementing the `timesClicked` state property. When state changes, React also re-renders the component and it's children, similarly as it does in case of prop change.

## Interaction between components

We have seen that we can pass data from parent component to its children using props. But sometimes the interaction is more complex. Let's imagine that we want to have two sibling components - `Button` and `Display` - that are both children of `Parent` component. `Display` should show how many times `Button` was clicked. But in React we cannot pass props between sibling components, and they are also not aware of its own states. So how to do it?

In that case we can **lift the state up** to the parent component. The state of parent will hold the number of times the button was clicked, and this state will be updated after next click. The state will be shared with the child components through props. So the children components will depend on the parents state, but will not interact between each other at all.

Passing state from `Parent` to `Display` using props is easy. But how to update `Parent`'s state when `Button` is clicked? To do it, firstly we need to create a function within `Parent` that will handle updating the state. Secondly, we need to pass this function as a prop to `Button`. Lastly, within `Button`, we need to call this function when it's clicked. Let's see how the code looks:

Parent component:

```javascript
class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      timesClicked: 0,
    };
  }

  handleClick() {
    this.setState({ timesClicked: this.state.timesClicked + 1 });
  }

  render() {
    return (
      <div>
        <Button onClick={() => this.handleClick()} />
        <Display value={this.state.timesClicked} />
      </div>
    );
  }
}
```

We do not need any state in the child components, so we can use the shorter function syntax when we declare them.

Button component:

```javascript
function Button(props) {
  return <button onClick={props.onClick}>Click me!</button>;
}
```

Display component:

```javascript
function Display(props) {
  return <div>{props.value}</div>;
}
```

## Dynamic lists in React

Lastly I want to mention two things about lists in React.

### Rendering lists

Firstly, let's see how to render a dynamic list that will reflect values from an array. To do it we can map through the array and return list the JSX item description for each element. Map will return a new array of these descriptions, and React will simply render them.

```javascript
function List(props) {
  return (
    <ul>
      {props.employees.map(employee => {
        return <li key={employee.employeeId}>{employee.name}</li>;
      })}
    </ul>
  );
}
```

In this example the `List` component receives an `employees` prop.
Its value is an array of objects with `name` and `employeeId` properties:

```javascript
<List
  employees={[
    { name: "Amy", employeeId: 5 },
    { name: "John", employeeId: 11 },
    { name: "Bob", employeeId: 23 },
  ]}
/>
```

### List key

Each dynamically generated list item has to receive a `key` prop. If we skip this, we will get an error in the browser console.

The purpose of key is to provide extra data that React uses to detect which element should be re-rendered if the data changes. The keys should be unique (within the given list) and should not change for specific list item over time. By default React will use array element index as a key, but we should avoid it since indices could change when the list data is modified.

## Next steps

The _Intro to React_ tutorial was quite short, but at the same time introduced the basic React concepts in very simple way. But it did not cover all React aspects, so the next step is to dive deeper and get more understanding about this library.
