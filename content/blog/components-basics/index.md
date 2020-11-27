---
title: Components concept in React
date: "2020-11-27"
---

![Block](./hello-i-m-nik-qXakibuQiPU-unsplash.jpg "Photo by [Hello I'm Nik](https://unsplash.com/@helloimnik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/blocks-toy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article I will describe the general basics of components in React and some aspects of working with them.

## Understanding components

As we already know, React is a library for building user interfaces. And components in React are our building blocks. Components describe the specific elements that we want to see in the UI. By combining many components, we create whole UI.

> React lets you compose complex UIs from small and isolated pieces of code called “components”.

To give some example, lets imagine a profile card element that could be displayed on a website that shows keynote speakers on an upcoming conference:

![Example profile card](./profile-card.png)

It shows a person's photo, name, title, links to profiles on social media and a button to read more details.

This is not very complex component, but still it could be created as a composition of multiple simpler components. For example the button could be a separate component, as well as social media icons or the avatar image. Moreover, the card component itself could be used to compose other even more complex components, e.g. component that represents a list of all speakers.

## Defining components

There are two ways how we can define a component in React. We can use the function or class definition. Function syntax is very simple:

```javascript
function Button(props) {
  return <button>{props.displayText}</button>;
}
```

This component renders a button that displays text passed in the `displayText` prop (more on `props` in next section). Below is the same component definition, but described with class syntax:

```javascript
class Button extends React.Component {
  render() {
    return <button>{this.props.displayText}</button>;
  }
}
```

The differences and use-cases of function and class components will be described in a separate article, as this is more complex topic.

When naming components, we should use PascalCase. This will distinguish our components from the standard HTML tags.

As we can see, components are returning JSX _element_, which represents the view of given part of our UI. More about JSX can be found in [JSX overview](/jsx-overview) post.

### Props

Each component in React can receive an input from outside. The data is passed using so called `props` (properties). Component can use this data, perform some logic on it and return appropriate result. Thanks to props we can use one component definition that will return various results.

### Multiple elements returned from component

We cannot return multiple elements from one component. Something like this is not possible and will fail to compile:

```javascript
function Movie(props) {
  return (
    <h1>{props.title}</h1>
    <p>{props.description}</p>
    );
}
```

In this case we could enclose `<h1>` and `<p>` tags with additional `<div>...</div>`, but this would render additional node in DOM, which might not be desired. To avoid this, we can use `<React.Fragment>...</React.Fragment>` tag instead, which will not leave a trace in DOM:

```javascript
function Movie(props) {
  return (
    <React.Fragment>
      <h1>{props.title}</h1>
      <p>{props.description}</p>
    </React.Fragment>
  );
}
```

Instead of `<React.Fragment>...</React.Fragment>` we can use a shorter equivalent syntax `<>...</>`:

```javascript
function Movie(props) {
  return (
    <React.Fragment>
      <h1>{props.title}</h1>
      <p>{props.description}</p>
    </React.Fragment>
  );
}
```

## Using components

To use a component, we simply need to refer its name as it would be a normal HTML tag:

```jsx
let button = <Button displayText="Click here" />;
```

Here we are using the previously defined `Button` component. This example also shows passing data through props. In this case in the `displayText` prop we are passing `Click here` string value. When this code is executed, a function/class of given component is called. Props are being passed to the component in an object. In this case the component will receive `{displayText: "Click here"}` object. The component returns a JSX element. Thus, `<button>Click here</button>` element will be passed to the `button` variable.

As mentioned at the beginning, the components can be nested in other components. When building React app, we compose complex structures from multiple smaller and simpler components. In the example below we define a new `Modal` component, which uses previously defined `Button`:

```javascript
function Modal(props) {
  return (
    <div>
      <h1>{props.title}</h1>
      <p>{props.text}</p>
      <Button displayText="Click here" />
    </div>
  );
}
```

The modal could be nested in other component and so on. A typical React app is made of the tree of components that can have multiple levels and many branches. At the top of the component tree there is a single `App` component, which is rendered in the `root` DOM node. For more information about basics of rendering in React see [this](/rendering-basics) post.

## Components reusability

As stated at the beginning, components should be isolated. This means that they should be independent and as much generic as possible. This will allow us to use them multiple times in different places or in different contexts.

Let's use a simple example of a component that represents a price. It consists of two parts: numeric value and currency symbol. So it could show "200 €" or "0.63 PLN". The very basic definition could look like this:

```javascript
function PriceComponent(props) {
  return (
    <div>
      <span>{props.value}</span>
      &nbsp;
      <span>{props.symbol}</span>
    </div>
  );
}
```

The component would be called like this:

```javascript
<PriceComponent value="200" symbol="€" />
```

We can then use this component in many different places in our UI. It can be used as a standard product price, but it can be also used as a delivery fee price. It can be also used as a part of bigger more complex component that lists multiple tax values or discounts. With an extra flag and some styling, we could also use it as a previous crossed-out price in case of some promotion.

Of course in app with many countries and currencies this component would be more complex. For example, for some currencies we should display the symbol before the value, or maybe there should be no space between. When creating components, we should think about different ways these could be used.

Since we should create components to be generic and independent, component's props, variables and functions or methods should be named from the perspective of the component itself. So it would be bad to name our example props `productPriceValue` and ``productPriceSymbol`, because these names are related to the specific context of using this component as a product price.

## Complexity

We should make components to be as simple as possible. If components is very big and complex, that means it does many things. And if it does many things, there is very high possibility that at least some parts of it could be extracted to a separate component. Big components are hard to test and maintain.

## Conclusion

This post touched some basic aspects of components in React. The basic idea is very simple, but in practice creating components requires a lot of consideration. There are still many more other aspects not covered here, and these will be described in future posts.
