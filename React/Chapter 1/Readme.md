# Chapter 1

- [Chapter 1](#chapter-1)
  - [Babel](#babel)
    - [Installation](#installation)
    - [Transpile](#transpile)
  - [JSX Basics](#jsx-basics)
    - [Important JSX Rules](#important-jsx-rules)

## Babel

Babel is a toolchain that is mainly used to convert ECMAScript 2015+ code into a backwards compatible version of JavaScript in current and older browsers or environments. Here are the main things Babel can do for you:

### Installation
```
yarn global add babel-cli
yarn add babel-preset-react babel-preset-env
```
### Transpile
```
babel src/app.js --out-file=public/scripts/app.js --presets=env,react
```
above command will convert the React JSX code in `src/app.js` into ES5 compatible app.js at `public/scripts/app.js`

```
babel src/app.js --out-file=public/scripts/app.js --presets=env,react  --watch
```
to watch for any change in `src/app.js` and automatically transile the file

## JSX Basics

### Important JSX Rules
1. We can only return one top-level element from a given component. This is usually known as a parent element and is used to group the content. 

```
var template = <h1>My React app</h1><p>Test</p>; // not allowed
```
In above template adjacent elements are placed without parent container, so JSX will give error

Above can be avoided by using below
```
var template = <div><h1>My React app</h1><p>Test</p></div>;
```
above can also be written as 
```
var template = (
  <div>  
    <h1>My React app</h1>
    <p>Test</p>
  </div>
);
```

2. Some elements in HTML do not have a closing tag. In React JSX, every tag, including those with no closing tags, must be closed. If you have an element that doesn’t have a closing tag, you have to add a slash at the end (e.g., `<hr/>`). Noting special here, simple HTML rule

3. A React component must be capitalized. Component names that do not begin with a capital letter are treated like built-in components, and it results in strings (“div”, “span”…). When the component name is capitalized, it is treated as an identifier instead of a string.
   
4. To include JavaScript expressions in JSX, we need to wrap them in curly braces. Content between the opening and closing curly braces will be evaluated as JavaScript.

5. The term “class” is a reserved keyword in JavaScript. In React, we must substitute className for class.
    1. [JSX DOM Elements attributes][domElements]
    2. [JSX Supported Events][jsxSupportedEvents]

6. undefined, null and false are ignored by JSX

undefined is returned implicitly by function, if expression evaluates to undefined nothing will be displayed

```
function getUserLocation(location) {
  if(location) {
    return <p>Location : {location}</p>;
  }
}

var template2 = (
  <div>  
    <h1>{user.name}</h1>
    <p>Age : {user.age}</p>
    {getUserLocation(user.location)} // will not print anything if the expression evalues to Undefined
  </div>
);
```

Explaination of above function
```
function getUserLocation(location) {
  if(location) {
    return <p>Location : {location}</p>;
  }
}
```

is equivalent to 
```
function getUserLocation(location) {
  if(location) {
    return <p>Location : {location}</p>;
  }
  return undefined;
}
```

7. Javascript objects are not valid as a React child

```
var user = {
  "name" : "Manish",
  "age" : 31,
  "location" : "Mumbai"
}

var template2 = (
  <div>  
    <h1>{user}</h1>
    <p>Age : 26</p>
    <p>Location : Mumbai</p>
  </div>
);
```
we can't use `user` object directly in react template

below example is a valid template
```
var template2 = (
  <div>  
    <h1>{user.name}</h1>
    <p>Age : {user.age}</p>
    <p>Location : {user.location}</p>
  </div>
);
```




[domElements]: https://reactjs.org/docs/dom-elements.html
[jsxSupportedEvents]:https://reactjs.org/docs/events.html