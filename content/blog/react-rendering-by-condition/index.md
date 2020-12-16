---
title: Rendering elements by condition in React
date: "2020-12-16"
---

![Roads intersection](./kyle-glenn-IFLgWYlT2fI-unsplash.jpg "Photo by [Kyle Glenn](https://unsplash.com/@kylejglenn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/choice?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article I will describe what are the techniques for rendering elements by condition in React.

## If-else statement

Since our components are returning elements that should be rendered, we can utilise standard `if-else` statement. This will determine what will be returned according to a condition.

As an example, let's imagine that we have a content that should be visible only for subscribers. For non-subscribers, we want to show a text message.

```jsx
function Content(props) {
  return <p>A super interesting content!</p>;
}

function MessageForNonSubscribers(props) {
  return <p>Please subscribe to see the content</p>;
}

function Article(props) {
  if (props.user.subscriber) {
    return <Content />;
  } else {
    return <MessageForNonSubscribers />;
  }
}

function App(props) {
  return <Article user={{ subscriber: true }} />;
}
```

In the `Article` component there is an `if` statement. If the `user.subscriber` property value is `true`, we return the `Content` component. Otherwise, the `Message` component is returned.

## Ternary operator

The `if-else` statements are sometimes introducing more code than the returned JSX itself. In such cases, we can use the standard ternary operator which does the same thing with less code:

```jsx
function Article(props) {
  return props.user.subscriber ? <Content /> : <MessageForNonSubscribers />;
}
```

## Using variable

Sometimes we cannot simply return one element or the other. There are situations, when we want to return element that has only some part of it that changes. In this case we can assign this part to a variable accordingly, and use this variable within return statement.

To demonstrate this, let's imagine that we have a `CartPromotionInfo` component that shows the total price of products that we have in Cart. Additionally, we want to show a message (this is the variable part). If the total price of products is more than \$100, we show a message about applied promotion. Otherwise, we show text about a potential promotion.

```jsx
function CartPromotionInfo(props) {
  let freeShipmentMessage;

  if (props.cart.totalPrice > 100) {
    freeShipmentMessage = (
      <span className="success">You qualify for free shipment!</span>
    );
  } else {
    freeShipmentMessage = (
      <span className="info bold">
        Buy items for more than $100 to get free shipment.
      </span>
    );
  }

  return (
    <>
      <p>The total price is ${props.cart.totalPrice}.</p>
      <p>{freeShipmentMessage}</p>
    </>
  );
}

ReactDOM.render(
  <CartPromotionInfo cart={{ totalPrice: 95 }} />,
  document.getElementById("root")
);
```

We declared the `freeShipmentMessage` variable and assigned its value within `if-else` statement. Then we used this variable within return value of the component.

## Logical AND operator

In some situations we want to render an element only during certain conditions. And if the condition is not met, then we don't want to render anything else. This can be achieved using an expression with the logical AND (`&&`) operator. In the example below, we have a `TrainingsInfo` component. It shows a heading text. Additionally, if (_only_ if) user has some overdue trainings, we want to show a message.

```jsx
function TrainingsInfo(props) {
  return (
    <>
      <h1>Trainings</h1>
      {props.user.overdueTrainings.length && (
        <p>There are overdue trainings awaiting completion.</p>
      )}
    </>
  );
}

ReactDOM.render(
  <TrainingsInfo
    user={{
      overdueTrainings: [
        { id: 5, name: "Security fundamentals" },
        { id: "Learning resources" },
      ],
    }}
  />,
  document.getElementById("root")
);
```

To understand how this works, let's check the definition from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#Description):

```
expr1 && expr2
```

> If `expr1` can be converted to `true`, returns `expr2`; else, returns `expr1`.

So, if we add some JSX element after the `&&` operator, it will be returned only if the first operand can be converted to `true` (in other words if the first operand is truthy). There is small caveat here. If `expr1` is falsy, it will be returned as a result. Therefore, depending on what the `expr1` is, React might still render some output. In our example, if the `props.user.overdueTrainings.length` array is empty, first operand will be `0`:

```
0 && <p>...</p>
```

In this case, `0` will be returned from the operator (because first operand is falsy). React knows how to render numbers, so we will see `0` rendered in the output. Similar situation would happen with `NaN` value.

To avoid displaying falsy values, we need to ensure that the first expression is a real Boolean `false` value. React will not render anything when `false` is returned. To convert falsy values to Boolean `false`, we can use:

- double negation `!!`

```
!!props.user.overdueTrainings.length && <p>...</p>
```

- converting to Boolean

```
Boolean(props.user.overdueTrainings.length) && <p>...</p>
```

- using a condition

```
(props.user.overdueTrainings.length > 0) && <p>...</p>
```

## "Rendering nothing"

There is also a possibility to instantiate a component which will not render anything on the screen. To do it, we simply need to pass `null` as a return value:

```jsx
function Greetings(props) {
  return props.show ? <p>Greetings</p> : null;
}

ReactDOM.render(<Greetings show={false} />, document.getElementById("root"));
```

In the example above, if the `show` prop value is `false`, then we return `null` and nothing will be rendered on the screen. Important thing to be aware of is that the component itself is instantiated and mounted by React. It is just that there is nothing to display, so nothing is rendered on the screen. That is what I call _rendering nothing_ :blush:.
