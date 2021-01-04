---
title: React children prop
date: "2021-01-04"
---

![Jars with food and spices](./itay-verchik-WtG6jdx2Jc8-unsplash.jpg "Photo by [itay verchik](https://unsplash.com/@itayverchik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/jar-pickles?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article we will see what is the `children` prop in React and how can we use it for component composition.

## The `children` prop

When using a component in JSX, there are two syntax options we can use. We can use the single self-closing tag or use both opening and closing tags:

```jsx
<MyComponent /> // self closing tag
<MyComponent></MyComponent> // opening and closing tags
```

Like in standard HTML, the second option allows us to put some content between the tags like this:

```jsx
<MyComponent>Hello</MyComponent>
```

In React, the content added between the tags can be accessed within the component as a `props.children` prop:

```jsx
function MyComponent(props) {
  return <div>{props.children}</div>; //returns `<div>Hello</div>`
}

ReactDOM.render(
  <MyComponent>Hello</MyComponent>,
  document.getElementById("root")
);
```

So to sum this up, the `props.children` is a special prop that contains anything that was passed as a content between the component opening and closing tags. Later in the article we will see how we can use this in component composition.

As a side note, we could also use the self-closing tag syntax and still pass some child to the component, using the standard props approach:

```jsx
<MyComponen children="Hello" />
```

This is however not as readable as the syntax with two separate tags and the content between them.

## How it works internally

Internally, Babel transpiles JSX to JavaScript using the `React.createElement(type, props, children)` method call. For example, this JSX:

```jsx
<MyComponent myProp="myValue">Hello</MyComponent>
```

will be transpiled to following JavaScript call:

```javascript
React.createElement(
  MyComponent,
  {
    myProp: "myValue",
  },
  "Hello"
);
```

As we can see, our content is passed as the third `children` parameter. Such call results with an object that React uses for the DOM operations. And the content from between the tags is stored in the `props` object under the `children` property:

```jsx
{
  ...
  key: null,
  props: {
    children: "Hello",
    myProp: "myValue",
  },
  ref: null,
  ...
};
```

This is why we can access the `props.children` prop even without explicitly declaring it on our component.

## Types of content within children

Depending on what we put between the opening and closing component tags, the `props.children` will hold different types of data. If there are multiple items there, they will be stored in an array. The comments in the code below tell what type of data will be stored in the `props.children` in given situation:

```jsx
// undefined
<MyComponent></MyComponent>

// a string literal
<MyComponent>Hello</MyComponent>

// a JSX element
<MyComponent>
  <p>Some text ... </p>
</MyComponent>

// an array of three JSX elements
<MyComponent>
  <MyOtherComponent />
  <h1>Title</h1>
  <p>Some text ... </p>
</MyComponent>

// an array with string literal, a number and a JSX element
<MyComponent>
  Hello
  {5}
  <p>Text ... </p>
</MyComponent>

// a function
<MyComponent>
  {number => <span>The number is {number % 2 === 0 ? "even" : "odd"}</span>}
</MyComponent>
```

Basically, since we can evaluate JavaScript expressions within JSX, the value in `props.children` could be anything, including `null`, `NaN` etc. It can even a function as we can see in the last example, so not always the children can be directly rendered without any prior processing. It is up to us how we use the value of `props.children` within the component itself.

## Using `props.children` for component composition

Most common usage of the `children` prop is for component composition, which is a broad topic in itself. In simple terms, the idea behind component composition pattern in React is to make components by combining other components. This approach allows us to write less code by using more generic and reusable components. Composition is used often in case of components that work as containers for displaying other components, such as dialog boxes or sidebars. In cases like these we can utilize the `children` prop.

Let's use an example of a collapsible sidebar component, which should be able to display different kind of components, e.g. author bio or popular articles listing. So we need a way to create a single `Sidebar` component that could contain and display either one of them (or any other component for all intents and purposes). Let's firstly define simple `AuthorBio` and `ArticlesListing` components:

```jsx
function AuthorBio({ author }) {
  return (
    <>
      <img src={author.imageUrl} alt={author.name} />
      <h1>{author.name}</h1>
      <p>{author.shortDescription}</p>
    </>
  );
}

function ArticlesListing({ articles }) {
  return (
    <>
      <h1>Other popular articles</h1>
      <ul>
        {articles.map(article => (
          <li key={article.id}>
            <a href={article.url}>{article.title}</a>
          </li>
        ))}
      </ul>
    </>
  );
}
```

And here is the code for our `Sidebar`:

```jsx
class Sidebar extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      collapsed: false,
    };
  }

  toggleCollapse = () => {
    this.setState(prevState => ({ collapsed: !prevState.collapsed }));
  };

  render() {
    return (
      <>
        {this.props.collapsible && (
          <button onClick={this.toggleCollapse}>Toggle sidebar</button>
        )}
        <aside className={`sidebar ${this.state.collapsed ? "collapsed" : ""}`}>
          {this.props.children}
        </aside>
      </>
    );
  }
}
```

Notice that in the `render()` method we have the markup that belongs to the sidebar itself (e.g. toggle button), but the main content comes from outside. To show it, we are using the `props.children`. Therefore, any content that will be added between the `Sidebar`'s opening and closing tags will be displayed there within the `aside` tag.

Code below shows how we are passing the `AuthorBio` and `ArticlesListing` components as children to our sidebar:

```jsx
<Sidebar collapsible={true}>
  <AuthorBio author={...} />
</Sidebar>

<Sidebar collapsible={false} >
  <ArticlesListing articles={[...]} />
</Sidebar>
```

Since our sidebar is reusable and generic, we can also use it with other components or custom content, e.g.:

```jsx
<Sidebar collapsible={true}>
  <h1>Quote of the day</h1>
  <figure>
    <blockquote>
      <p>Patience is bitter, but its fruit is sweet…</p>
    </blockquote>
    <figcaption>-Aristotle</figcaption>
  </figure>
</Sidebar>
```

## Helpful React.Children API

As we saw earlier, the `children` prop can contain content of different data types. This can become problematic, because it can be cumbersome to process that data. For example, let's say we want to count the number of children of a component. Let's consider using the `length` property of an array like this:

```jsx
function MyComponent(props) {
  return props.children.length;
}
```

This would not be reliable, because depending on what content is, we will get totally different values. The comments in the code below explain what the `props.children.length` would be for given case:

```jsx
// length is `3`, because `props.children` is an array with 3 items
<MyComponent>
  <p>Huey</p>
  <p>Dewey</p>
  <p>Louie</p>
</MyComponent>;

// length is `undefined`, because `props.children` is a JSX element that does not have the `length` property
<MyComponent>
  <p>Dewey</p>
</MyComponent>;

// length is `5`, because the `props.children` contains string literal with 5 characters
<MyComponent>Louie</MyComponent>;

// An error is thrown - TypeError: Cannot read property 'length' of undefined
<MyComponent></MyComponent>;
```

To help us in similar situations, React creators provided us with a 5 helpful `React.Children` API methods. One of these methods is `React.Children.count()` which does exactly what we need - returns a number of children items. To use this method, we simply need to pass the `props.children` as an argument:

```jsx
function MyComponent(props) {
  return React.Children.count(props.children);
}
```

This code would return a correct length values for our previous examples - `3`,`1`,`1`, and `0` respectively.

Other `React.Children` API utilities are `map`, `forEach`, `only` and `toArray`. The details are described in the <a href="https://reactjs.org/docs/react-api.html#reactchildren" target="_blank">React API reference</a> page.
