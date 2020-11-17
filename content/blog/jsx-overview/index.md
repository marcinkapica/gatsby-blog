---
title: JSX overview
date: "2020-11-17"
---

![Display showing source code](./image-jsx-overview.jpg "Photo by [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/jsx?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

This post is about JSX in React. We will take a closer look at what it is and what are some of its basic aspects that we should be aware of.

## What is JSX?

JSX (JavaScript XML) is a syntax that helps us write views in React. The output of JSX is called an _element_. Let's see some example of a component definition that uses JSX:

```javascript
function HelloWorld() {
  return <div>Hello World</div>;
}
```

This function returns a `<div>` with a text `Hello World`. At first glance the return value `<div>Hello World</div>` looks like plain HTML string, but after closer examination we can see there are no quotes enclosing it. That's JSX.

React uses Babel to transpile JSX to a JavaScript function call. We will take a look at it later in this article, but firstly let's see what some key JSX aspects that we should be aware of.

## JavaScript expressions within JSX

One of the superpowers of JSX is that it can contain any valid JavaScript expression. As a reminder - expression is a snippet of code that evaluates to a value. Let's see an example:

```javascript
function Year(props) {
  return <div>Current year is: {new Date().getFullYear()}</div>;
}
```

Here we have a JSX that holds a string `Current year is: ` followed by a JavaScript expression. Expressions in JSX must be contained within curly braces. In this example the expression creates a new `Date` object instance and calls its method to get a year. So at the end the content within `<div>` will be rendered as `Current year is: 2020`.

Important thing to be aware is that the JavaScript code within JSX is not some new special syntax added on top of HTML. We are using a standard JavaScript expressions here.

## JSX as an expression

Another useful aspect of JSX is that we can use it as an expression too. This gives us much flexibility. See following example:

```javascript
function wrapWithDiv(element) {
  return <div>{element}</div>;
}

function MyComponent() {
  const btn = <button>Click</button>;
  return wrapWithDiv(btn);
}
```

In `MyComponent` the JSX expression is assigned to a `btn` variable. Then in the return statement we are using this variable as an argument to a function. This function wraps our JSX element with a `<div>` and returns another JSX element.

We can also use JSX in loops or conditionals. Here is a snippet demonstrating usage of JSX in a ternary operator:

```javascript
class ClickButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      clicked: false,
    };
  }

  render() {
    return this.state.clicked ? <p>Already clicked</p> : <button>Click</button>;
  }
}
```

In this example, depending on the state we either return a button or a paragraph.

## Attributes/props in JSX

To add an attribute/prop to element we are using name-value pairs, similarly as in normal HTML.

If we want the value to be a string, we need to enclose it with quotes. If we want to add an expression as a value, we need to enclose it with curly braces. See example below:

```jsx
<Weather
  className="weather-component"
  showWeather={true}
  maxLocations={5}
  cities={["New York", "Beijing", "Nairobi", "Helsinki"]}
  options={{ temperature: true, humidity: false, rain: true }}
/>
```

First attribute value is passed as string. Rest are expressions with boolean, number, array and an object respectively.

Because JSX is transpiled to JavaScript function, some standard attribute names, e.g. `class` or `for`, are not valid. In this case we need to use other names - `className` or `htmlFor`. The attribute names should be written using `camelCase` naming convention.

Additionally, React introduces few new attributes that are not present in normal DOM. One of this attributes is `key` which is used within dynamically generated lists.

## Transpiling JSX to a function

As mentioned in the first section, React uses Babel to transpile JSX to a JavaScript function call:

```javascript
React.createElement(type, props, children);
```

First argument should be an HTML tag name or a React component. Second should be an object containing properties/attributes (`null` if none). Last argument expects the content of the element. It can also be another element or a `null` if there is no content to be added.

As an example, here is some JSX element definition:

```javascript
<article className="blogpost" show={true}>
  Really interesting content
</article>
```

And here is the corresponding function call:

```javascript
React.createElement(
  "article",
  {
    className: "blogpost",
    show: true,
  },
  "Really interesting content"
);
```

Such call results with an object that represents the element. Under the hood React uses these objects for DOM operations. Here is the object returned from our example function call:

```json
{
  "type": "article",
  "key": null,
  "ref": null,
  "props": {
    "className": "blogpost",
    "show": true,
    "children": "Really interesting content"
  },
  "_owner": null,
  "_store": {}
}
```

## JSX is optional

Using JSX is is not required in React. All elements can be written by using the `createElement()` function directly. JSX is only a syntactic sugar that allows us to write elements easily.
