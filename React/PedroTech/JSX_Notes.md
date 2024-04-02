### JSX (JavaScript XML) Rules

#### Adjacent JSX elements must be wrapped in an enclosing tag</
```jsx
var template = <h1>My React app</h1><p>Test</p>; // not allowed
		
// above adjacent tags can be wrapped in one root/parent div tag
var template = <div><h1>My React app</h1><p>Test</p></div>;  // can also be written as below
var template = (
  <div>  
    <h1>My React app</h1>
    <p>Test</p>
  </div>
);
```
#### Objects are not valid as a React child
Error in Console : Objects are not valid as a React child (found: object with keys {name, age, location}). If you meant to render a collection of children, use an array instead.
```jsx
var user = {
  "name" : "Manish",
  "age" : 31,
  "location" : "Mumbai"
}

var template2 = (
  <div>  
    <h1>{user}</h1>
    <p>Age : {userAge}</p>
    <p>Location : {userLocation}</p>
  </div>
);
```
above template will not render as we are trying to print whole user object

```jsx
var template2 = (
  <div>  
    <h1>{user.name}</h1>
    <p>Age : {user.age}</p>
    <p>Location : {user.location}</p>
  </div>
);
```

####  undefined, null and false are ignored by JSX
```jsx
var user = {
  "name" : "Manish",
  "age" : 31,
  "location" : "Mumbai"
}

function getUserLocation(location) {
  if(location) {
    return <p>Location : {location}</p>;
  }
}

var template2 = (
  <div>  
    <h1>{user.name}</h1>
    <p>Age : {user.age}</p>
    {getUserLocation(user.location)} // will not print anything if the expression resolves to Undefined
  </div>
);

```

`getUserLocation(location)` is equivalent to below
```jsx
function getUserLocation(location) {
  if(location) {
    return <p>Location : {location}</p>;
  }
  return undefined;
}
```

### JSX Arrays
In JSX, you can render arrays of elements just like you would with individual elements. This allows you to dynamically generate multiple elements based on the contents of an array. Here's how you can work with arrays in JSX:

#### Rendering Arrays Directly
You can directly render an array of JSX elements by placing the array within curly braces `{}`.
```jsx
function MyComponent() {
  const items = ['Apple', 'Banana', 'Orange'];

  return (
    <div>
      {items.map((item, index) => (
        <p key={index}>{item}</p>
      ))}
    </div>
  );
}

```
In this example, each item in the items array is mapped to a `<p>` element using the map function. The key prop is required for each rendered element to help React identify each item uniquely and optimize rendering performance.

#### Fragments with Arrays
If you're rendering an array of elements without a parent container, you can use React fragments to avoid unnecessary wrapper elements.

```jsx
function MyComponent() {
  const items = ['Apple', 'Banana', 'Orange'];

  return (
    <>
      {items.map((item, index) => (
        <p key={index}>{item}</p>
      ))}
    </>
  );
}
```
Here, the `<>...</>` syntax creates a React fragment, allowing you to return multiple elements without adding an additional DOM element.

#### Conditional Rendering with Arrays
You can conditionally render elements from an array using JavaScript's conditional operators or methods like filter.

```jsx
function MyComponent({ showFruits }) {
  const items = ['Apple', 'Banana', 'Orange'];

  return (
    <div>
      {showFruits &&
        items.map((item, index) => (
          <p key={index}>{item}</p>
        ))}
    </div>
  );
}
```
In this example, the fruits are rendered only if the `showFruits` `prop` is `true`.

#### Using JSX Spread Attributes with Arrays
If you have an array of JSX elements and you want to pass props to each element, you can use JSX spread attributes.

```
function MyComponent() {
  const items = [
    { name: 'Apple', color: 'red' },
    { name: 'Banana', color: 'yellow' },
    { name: 'Orange', color: 'orange' },
  ];

  return (
    <div>
      {items.map((item, index) => (
        <Fruit key={index} {...item} />
      ))}
    </div>
  );
}

function Fruit({ name, color }) {
  return <p style={{ color }}>{name}</p>;
}

```
In this example, each item in the `items` array is an object with `name` and `color` properties. We use JSX spread attributes to pass these properties as props to the `Fruit` component.

Using arrays in JSX allows you to dynamically generate elements based on data, making your components more flexible and reusable.

### Conditional Rendering
Conditional rendering in JSX can be achieved using logical AND (`&&`) and the ternary operator (`? :`). Both methods allow you to conditionally render elements based on certain conditions. Here's how each method works:

#### Using Logical AND (`&&`)
The logical AND (`&&`) operator can be used to conditionally render elements in JSX. If the expression before && evaluates to true, the element after && is rendered; otherwise, it's skipped.

<ul>
<li>Useful for simple conditions where you only want to render one element.</li>
<li>Can't render different elements based on different conditions.</li>
</ul>

```jsx
function MyComponent({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn && <p>Welcome, user!</p>}
      {/* If isLoggedIn is true, the paragraph element will be rendered */}
    </div>
  );
}

```
#### Using Ternary Operator (`? :`)
The ternary operator (`? :`) can also be used for conditional rendering in JSX. It's particularly useful when you need to render different elements based on different conditions.
<ul>
<li>Useful for more complex conditions where you need to render different elements based on different conditions.</li>
<li>Offers more flexibility and control over what gets rendered.</li>
</ul>

```jsx
function MyComponent({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? (
        <p>Welcome, user!</p>
      ) : (
        <button>Login</button>
      )}
      {/* If isLoggedIn is true, the paragraph element will be rendered;
          otherwise, the login button will be rendered */}
    </div>
  );
}

```