---
title: Lists in React
date: "2020-12-17"
---

![A photo of beach of List Sylt](./hilthart-pedersen-Hi8K5UPBO9Q-unsplash.jpg "Photo by [Hilthart Pedersen](https://unsplash.com/@h3p?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/lists?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

In this article I will focus on rendering lists in React.

## Rendering array

In React we can render multiple elements by returning a normal array that contains them:

```jsx
function ArrayOfElements(props) {
  return [
    5,
    <span>span</span>
    <h1>Heading</h1>,
    <article>
      <p>Paragraph</p>
      <button>Button</button>
    </article>,
  ];
}

ReactDOM.render(<ArrayOfElements />, document.getElementById("root"));
```

This will render all of the elements from the array in order, one after another. See this on CodePen: https://codepen.io/mkapica/pen/MWjmzYa

Notice that number `5` and `<span>` are rendered on the same line, since both are inline elements.

## Rendering a list of elements

To render a list of elements in React, we simply create an array of these elements and return it. Typically, the final array is created by mapping another array containing the data we want to display. Let's see an example:

```jsx
function AnimalsList(props) {
  const listItems = props.animals.map(animal => <li>{animal}</li>);

  return <ul>{listItems}</ul>;
}

ReactDOM.render(
  <AnimalsList
    animals={["eagle", "frog", "crocodile", "elephant", "salmon"]}
  />,
  document.getElementById("root")
);
```

The `AnimalsList` component receives the `animals` array with the data through props. In the component, we map this array using the standard `map` method. The `map` method returns a new array with the results of declared function for each of the array elements. In our example, in the declared function we simply put the value in the `<li>` tags. So the `listItems` variable receives a following array:

```jsx
[
  <li>eagle</li>,
  <li>frog</li>,
  <li>crocodile</li>,
  <li>elephant</li>,
  <li>salmon</li>,
];
```

Finally, in the return statement in the component, we enclose the variable with the `<ul>` tag to have a valid list markup.

We could also skip the variable assignment and map the list directly in the return statement:

```jsx
return (
  <ul>
    {props.animals.map(animal => (
      <li>{animal}</li>
    ))}
  </ul>
);
```

## Key

Each dynamically generated list item has to receive a special `key` attribute. If we skip this, the list will be rendered, but we will get a warning in the browser console:

```
Warning: Each child in a list should have a unique "key" prop.
```

To add a key we simply need to pass it like it would be a normal prop:

```jsx
return (
  <ul>
    {props.animals.map((animal, index) => (
      <li key={index}>{animal}</li>
    ))}
  </ul>
);
```

The purpose of key is to provide extra data that React uses to detect which element should be re-rendered if the data changes. The keys should be unique (within the given list) and should not change for specific list item over time. By default, React will use array element index as a key, but we should avoid it since indices could change when the list data is modified. In the example above we used index as a key, which is not recommended. It would be best to have some kind of unique ID that we can use as a key. The example below shows using ID as a prop:

```jsx
function EmployeeList(props) {
  return (
    <ul>
      {props.employees.map(employee => (
        <li key={employee.employeeId}>{employee.name}</li>
      ))}
    </ul>
  );
}

ReactDOM.render(
  <EmployeeList
    employees={[
      { name: "Amy", employeeId: 5 },
      { name: "Carol", employeeId: 23 },
      { name: "Steve", employeeId: 11 },
      { name: "Steve", employeeId: 45 },
    ]}
  />,
  document.getElementById("root")
);
```

It would be incorrect to use `name` as key, since we can have two employees with the same name. It is also possible that the indices will change, e.g. when we add a new employee.

It is important to know that keys should be unique only in the context of the given array. We can have multiple lists with the same exact elements without any problems, as long as there is no duplicate keys within single list.

## List of components

Using the same approach, instead of list of `<li>` elements, we can return a list of React components. Example below shows how we can return a list of `Movie` components displaying title, year and rating of a given movie:

```jsx
function Movie(props) {
  return (
    <>
      <h1>{props.title}</h1>
      <span>Release year: {props.year}</span>
    </>
  );
}

function MovieList(props) {
  return props.movies.map(movie => (
    <Movie key={movie.id} title={movie.title} year={movie.year} />
  ));
}

ReactDOM.render(
  <MovieList
    movies={[
      { id: 23, title: "Cinema Paradiso", year: 1988 },
      { id: 345, title: "Plan 9 from Outer Space", year: 1957 },
      { id: 112, title: "North by Northwest", year: 1959 },
    ]}
  />,
  document.getElementById("root")
);
```

## Accessing key

It is worth noting that `key` is not passed to the components along with other props. For instance, if in the previous example we try to access key using `{props.key}`, we will get a following warning in the console:

```
Warning: Movie: `key` is not a prop. Trying to access it will result in `undefined` being returned. If you need to access the same value within the child component, you should pass it as a different prop.
```

So according to the instruction, if we need to use the value in our component, we need to pass it explicitly as a different prop, even if it is already passed in the key:

```jsx
<Movie key={movie.id} id={movie.id} title={movie.title} year={movie.year} />
```
