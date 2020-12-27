---
title: Controlled form components in React
date: "2020-12-20"
---

![Paper form documents](./kelly-sikkema-tQQ4BwN_UFs-unsplash.jpg "Photo by [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/forms?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article I will describe what are controlled form components and how they can be used.

## What are controlled form components

When creating a form in our app, often we need an access and control over the values that are being provided to the inputs or submitted from the form. Thus, typically we link custom methods with the `change` or `submit` events. In React we use very similar approach, but with an important twist. This approach is called _controlled components_. Here is an overview how to create controlled component:

1. Create a stateful React component that represents form element and its fields, e.g. `<input>`, `<select>`, `<textarea>` etc.
2. Create respective state properties and assign them to be the values of the form fields. This will make state to represent the values of the form fields, e.g. text in the `<textarea>` or selected options from `<select>` field.
3. Create a method(s) for handling form changes and link them with the respective events, e.g. `onChange`.
4. When a value is changed, retrieve this value from event object (passed to method) and update the state accordingly.

The _twist_ I mentioned before is that we are not directly updating the form field values. Instead, we firstly update the state according to the event that occurred (e.g. selecting specific `<option>` in `<select>` element), and only then as a consequence of state change the form element is updated, since it's value is linked to the state (see point #2).

The downside of controlled components technique is that it requires writing a chunk of extra code. The advantage is that it gives us a lot of flexibility, since we have the form elements values in the state that is easy to access and modify.

## Text input

Here is an example of controlled form component with a text `<input>` field:

```jsx
class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      title: "",
    };
  }

  handleChange = e => {
    this.setState({ title: e.target.value });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log("Entered value:", this.state.title);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type="text"
          value={this.state.title}
          onChange={this.handleChange}
        />
        <button>Submit</button>
      </form>
    );
  }
}

ReactDOM.render(<Form />, document.getElementById("root"));
```

Here we are rendering a form containing an input field for providing a text value (title) and a button for submitting the form. In the components state we define a `title` property. This code is responsible for assigning it as a value for the input:

```jsx
<input
  ...
  value={this.state.title}
  ...
/>
```

Therefore, the value of this field will be always the value of the state `title` property.

Since we told React explicitly what the field value should be, we need also a way of updating it. Without this, user will not be able to change the value. This is why we have the `handleChange()` method. We assign it as handler for the `onChange` event. When user tries to change the text value, the method is invoked with `event` object passed as an argument. Then we simply read the value and update the state. This will cause the input to reflect the change. Finally, we have also `handleSubmit()` method which is handling form submission on the `onSubmit` event.

## Textarea

There is a small difference in comparison to standard HTML when using `<textarea>` in React. Instead of putting the value within the tag as its children (e.g. `<textarea>Lorem ipsum dolor</textarea>`) we pass it in the `value` attribute like this `<textarea value="Lorem ipsum dolor">`. This is consistent with how we assign the value to text `<input>`, as shown in previous example. Thus, we can use the `<textarea>` in practically the same way:

```jsx
class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      description: "",
    };
  }

  handleChange = e => {
    this.setState({ description: e.target.value });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log("Entered value:", this.state.description);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <textarea value={this.state.description} onChange={this.handleChange} />
        <button>Submit</button>
      </form>
    );
  }
}

ReactDOM.render(<Form />, document.getElementById("root"));
```

The code above is exactly the same as in previous example - only differences are using the `<textarea>` instead of `<input>` and different state property for holding the value.

## Form with multiple fields

If we need to handle multiple fields with one handler function, a `name` attribute can be used to distinguish between the fields. Example below shows using both `<input>` and `<textarea>` fields from previous examples used in single form and handled by single method:

```jsx
class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      title: "",
      description: "",
    };
  }

  handleChange = e => {
    const target = e.target;
    this.setState({ [target.name]: target.value });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log("Entered values:", this.state);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <div>
          <label htmlFor="title">Title</label>
          <input
            name="title"
            type="text"
            value={this.state.title}
            onChange={this.handleChange}
          />
        </div>
        <div>
          <label htmlFor="description">Description</label>
          <textarea
            name="description"
            value={this.state.description}
            onChange={this.handleChange}
          />
        </div>
        <button>Submit</button>
      </form>
    );
  }
}

ReactDOM.render(<Form />, document.getElementById("root"));
```

For each field we added `name` attribute - `title` for `<input>` and `description` for `<textarea>`. Both fields have the same handler method for handling `onChange` event. In this method we are reading the value and updating the state as before. The difference between previous example is that here we are using the `name` attribute to compute the state property that should be updated. The requirement here is that the `name` attribute value and the state property name of corresponding field must be the same. See the working example on CodePen:
https://codepen.io/mkapica/pen/xxErWpL

## Select

Using `<select>` in a controlled form is very similar as other field types shown above. We assign a state property as the `value` attribute, which reflects value of the selected `<option>`:

```jsx
class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      bookType: "paperback",
    };
  }

  handleChange = e => {
    this.setState({
      bookType: e.target.value,
    });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log("Entered value:", this.state.bookType);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <select value={this.state.bookType} onChange={this.handleChange}>
          <option value="ebook">Ebook</option>
          <option value="paperback">Paperback</option>
          <option value="hardcover">Hardcover</option>
        </select>
        <button>Submit</button>
      </form>
    );
  }
}
```

In this example one of the options is initially selected. This is because the `bookType` state property is initialized with a value of `paperback`. This will cause the `<option value="paperback">Paperback</option>` to be selected as a default.

### Select with multiple options

Sometimes we need a select field that allows selecting multiple options at the same time. Steps below describe what needs to be done if we wanted to alter the example from above to allow this.

1. The `multiple` attribute on `<select>` element has to be set to true:

```jsx
<select>
  multiple={true}
  ...
</select>
```

2. Since multiple options can be selected, in the state we need an array of values instead of a single value:

```jsx
this.state = {
  bookType: ["paperback"],
};
```

3. When setting the state, we need to extract value of each of the selected options. Firstly we create an array from the `selectedOptions` collection, and then we use `map` method to return an array of values:

```jsx
handleChange = e => {
  this.setState({
    bookTypes: [...e.target.selectedOptions].map(option => option.value),
  });
};
```

See the working code on CodePen:
https://codepen.io/mkapica/pen/zYKzVvr

## Checkbox

Checkbox is slightly different than fields from the previous examples. Instead of `value` attribute, we use the `checked` attribute that accepts Boolean values:

```jsx
class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      cookiePolicyAccepted: false,
    };
  }

  handleChange = e => {
    this.setState({
      cookiePolicyAccepted: e.target.checked,
    });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log("Cookie policy accepted:", this.state.cookiePolicyAccepted);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label htmlFor="cookiePolicy">I accept the cookie policy</label>
        <input
          type="checkbox"
          name="cookiePolicy"
          onChange={this.handleChange}
          checked={this.state.cookiePolicyAccepted}
        />
        <br />
        <button>Submit</button>
      </form>
    );
  }
}
```

In the event handler we set the state with the value of `e.target.checked` property, which is `true` when checking the checkbox, and `false` when unchecking it.

## Radio

When using Radio buttons, we need to use the `name` attribute to group the buttons under one radio group. This will ensure that selecting one butto will also cause other buttons from the same radio group to be unselected. Below is an example of controlled form with radio buttons:

```jsx
const SUBSCRIPTION_TYPE = {
  basic: "basic",
  premium: "premium",
};
const SUBSCRIPTION_RADIO_GROUP = "subscription";

class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      selectedSubscriptionType: SUBSCRIPTION_TYPE.basic,
    };
  }

  handleChange = e => {
    this.setState({
      selectedSubscriptionType: e.target.value,
    });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log(
      "Selected subscription type:",
      this.state.selectedSubscriptionType
    );
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <span>Subscription type:</span>
        <label>
          <input
            type="radio"
            value={SUBSCRIPTION_TYPE.basic}
            name={SUBSCRIPTION_RADIO_GROUP}
            checked={
              this.state.selectedSubscriptionType === SUBSCRIPTION_TYPE.basic
            }
            onChange={this.handleChange}
          />
          Basic
        </label>
        <label>
          <input
            type="radio"
            value={SUBSCRIPTION_TYPE.premium}
            name={SUBSCRIPTION_RADIO_GROUP}
            checked={
              this.state.selectedSubscriptionType === SUBSCRIPTION_TYPE.premium
            }
            onChange={this.handleChange}
          />
          Premium
        </label>
        <br />
        <button>Submit</button>
      </form>
    );
  }
}

ReactDOM.render(<Form />, document.getElementById("root"));
```

As we can see, the main idea is the same as in previous examples of other form field types - we store the value of selected radio in the state. Additionally, we want the radio to be preselected according to the initial value in the state. This is why for the `checked` attributes we evaluate an expression that will return `true` if the value from the state corresponds to given input.

Since the values of `name` and `value` radio input attributes are used in several places in the code, we use constants instead of typing the string values directly.

### Input as child component

The code from the example above can be simplified. Instead of repeating the same code for each individual `<label>` and `<input>`, we can extract this to a separate child component. Code below shows how this could be done.

```jsx
const SUBSCRIPTION_TYPE = {
  basic: "basic",
  premium: "premium",
};
const SUBSCRIPTION_RADIO_GROUP = "subscription";

function Radio({ value, label, name, isChecked, onChange }) {
  return (
    <label>
      <input
        type="radio"
        value={value}
        name={name}
        onChange={onChange}
        checked={isChecked}
      />
      {label}
    </label>
  );
}

class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      selectedSubscriptionType: SUBSCRIPTION_TYPE.basic,
    };
  }

  handleChange = e => {
    this.setState({
      selectedSubscriptionType: e.target.value,
    });
  };

  handleSubmit = e => {
    e.preventDefault();
    console.log(
      `Selected subscription type: ${this.state.selectedSubscriptionType}`
    );
  };

  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <span>Subscription type:</span>
          <Radio
            value={SUBSCRIPTION_TYPE.basic}
            label="Basic"
            name={SUBSCRIPTION_RADIO_GROUP}
            isChecked={
              this.state.selectedSubscriptionType === SUBSCRIPTION_TYPE.basic
            }
            onChange={this.handleChange}
          />
          <Radio
            value={SUBSCRIPTION_TYPE.premium}
            label="Premium"
            name={SUBSCRIPTION_RADIO_GROUP}
            isChecked={
              this.state.selectedSubscriptionType === SUBSCRIPTION_TYPE.premium
            }
            onChange={this.handleChange}
          />
          <br />
          <button>Submit</button>
        </form>
      </div>
    );
  }
}
```

We created a new stateless `Radio` component, which contains the markup for the label and the input. Attribute values and the reference for `onChange` handler are passed through props. See working example on CodePen:
https://codepen.io/mkapica/pen/KKgvNqe
