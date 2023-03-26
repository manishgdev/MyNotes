# Chapter 2 : React Components

- [Chapter 2 : React Components](#chapter-2--react-components)
  - [ES6 Some more concepts](#es6-some-more-concepts)
    - [ES6 Classes](#es6-classes)
    - [ES6 Template Strings](#es6-template-strings)
    - [ES6 Subclassing](#es6-subclassing)
    - [ES6 method return shorthand](#es6-method-return-shorthand)
  - [React Class Based Components](#react-class-based-components)
    - [Nesting Components](#nesting-components)
    - [Component Properties](#component-properties)
    - [Add Event Handlers](#add-event-handlers)
    - [Method binding](#method-binding)
    - [Event Handlers without bind](#event-handlers-without-bind)
    - [Using Constructors to bind events](#using-constructors-to-bind-events)
  - [Component State](#component-state)
    - [Props vs State](#props-vs-state)
  - [Functional Components](#functional-components)
    - [Default Properties](#default-properties)
    - [Lifecycle methods](#lifecycle-methods)

## ES6 Some more concepts

### ES6 Classes
  - starts with keyword class
  - constructor is defined using constructor keyword and using ES6 method syntax	
      ```
      contructor()
      {}
      ```
  - `this` in constructor refers to instance of class, used to set attributes in the class
      ```
      contructor(name)
      {
          this.name = name
      }
      ```
  - default value for parameter can be defined by ES6 functions defaults syntax
      ```
      constructor(name = "Anonymous", age = 0) {
          // this.name = name || 'DefaultName'; // default value for variable
          this.name = name;
          this.age = age;
      }
      ```

### ES6 Template Strings
  - Template Strings can be defined using backticks (**\`**)
      A simple String concatenated string as below
      ```
      getDescription() {
          return ${this.name} + " is + " + ${this.age} + " year(s) old";
      }
      ```
      can be written using template String
      ```
      getDescription() {
          return `${this.name} is ${this.age} year(s) old`;
      }
      ```

### ES6 Subclassing
  - defined using `extends` keyword
  - parent constructor can be accessed by `super` keyword and should be first statement in constructor
    ```
    class Person {
        constructor(name = "Anonymous", age = 0) {
            // this.name = name || 'DefaultName'; // default value for variable
            this.name = name;
            this.age = age;
        }
    }
    ```
    for above `Person` class a `Student` class extending `Person` can be defined as below
    ```
    class Student extends Person {
        constructor(name, age, major) {
            super(name, age);
            this.major = major;
        }
    }

    ```
    In above example `name` and `age` are taken from `Person` class and set using `super` keyword

  - overriding method
    ```
    class Person {
        constructor(name = "Anonymous", age = 0) {
            // this.name = name || 'DefaultName'; // default value for variable
            this.name = name;
            this.age = age;
        }
        getDescription() {
            return `${this.name} is ${this.age} year(s) old`;
        }
    }
    ```
    `Student` can override and also use the parent's `getDescription` method as below
    ```
    class Student extends Person {
        constructor(name, age, major) {
            super(name, age);
            this.major = major;
        }

        getDescription() {
            let description = super.getDescription();
            if(this.hasMajor()) {
                description += `, their major is ${this.major}`;
            }
            return description;
        }
    }

    ```

    In above using `super.getDescription();` we get the description from `Person` class and add our text for `Student` object

### ES6 method return shorthand
  - Below simple method return statement
    ```
     this.setState((prevState) => {
        return {
            count: prevState.count + 1
        };
    });
    ```
    can be converted into shorthand return statement as below

    ```
     this.setState((prevState) => ({
        count: prevState.count + 1
     }));
    ```
    the object which is getting returned `{count: prevState.count + 1}` can be enclosed in `()` as `({count: prevState.count + 1})`

## React Class Based Components
  - class should extend React.Component
  - should provide implementation for render() method
  - Component should have the first letter capital, it is with this convention that react distinguishes between native HTML and react tags

    ```
    class Header extends React.Component {
		render() {
			return (
				<div>
					<h1>Indecission</h1>
					<h2>Computer will take decission on your behalf</h2>
				</div>
			);
		}
	}
	
	const jsx = (
		<div>
			<Header />
		</div>
	);

	ReactDOM.render(jsx, document.getElementById("app"))
    ```

    after transpile of JSX
    ```
    var jsx = React.createElement(
        "div",
        null,
        React.createElement(Header, null)
    );

    ReactDOM.render(jsx, document.getElementById("app"));
    ```

### Nesting Components
  - Let's say we have an `Option` component to be rendered inside `Options` as below
    ```
    <Options>
        <p> My Options are
        <Option>Some text</Option>
    </Option>
    ```
    above can be achieved by writing nested Components
    ```
    class Options extends React.Component {
        render() {
            return (
                <div>
                    <p>Options are to be displayed here</p>
                    <Option />
                </div>
            );
        }
    }

    class Option extends React.Component {
        render() {
            return (
                <div>
                    <p>Option Component Here</p>
                </div>
            );
        }
    }

    
    const jsx = (
        <div>
            <Options />
        </div>
    );

    ReactDOM.render(jsx, document.getElementById("app"))
    ```

### Component Properties
- Properties are converted into {key, value} pairs, data can be passed into the component similar to defining HTML attributes
    ```
    const jsx = (
        <div>
            <Header title="ABC" />
            
        </div>
    );

    
    class Header extends React.Component {
        console.log(this.props); // {"title" : "ABC"}
        const title = "Custom Title"
        render() {
            return (
                <div>
                    <h1>Indecission {title}</h1>
                    <h2>Computer will take decission on your behalf</h2>
                </div>
            );
        }
    }
    ```
    properties are converted into key-value pair in the Header component class

### Add Event Handlers
  - for the event to be triggered we can reference the method to be trigger inside `{}`
    ```
    class Actions extends React.Component {
        actionClicked() {
            alert("actionClicked method triggered");
        }
        render() {
            return (
                <div>
                    <button onClick={this.actionClicked}>Action</button>
                </div>
            );
        }
    }

    ```
    When the Action button is clicked `actionClicked` will be triggered via the `onClick` event handler on the `button` tag

  - Getting form element's value
    ```
    class AddOption extends React.Component {
        handleAddOption(e) {
            e.preventDefault(); // to avoid page reload
            const option = e.target.elements.option.value.trim()

            if(option)
                alert(option)
        }
        render() {
            return (
                <form>
                    <input type="text" name="option" />
                    <button>Add Option</button>
                </form>
            );
        }
    }
    ```

### Method binding
  - bind() <br/>
    [How bind works - Mozilla Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)<br/>
    Consider below block
    ```
    const obj = {
		name:"Manish",
		getName() {
			return this.name;
		}
	}

	console.log(obj.getName())
    ```
    When we call `getName()` on `obj`, the `this` inside the `getName` function is bound to `obj` and returns Manish

    But when we assign getName() to some other reference as
    ```
    const getName = obj.getName()
    ```
    when we invoke `getName()` as below, the `this` is no longer bound to `obj` and binding is broken, the context of `obj` is not transferred
    ```
    console.log(obGetName()) // "this" will cause error as the scope of "this" is lost inside getName function in obj
    ```

    Using bind we can pass the context to the reference
    ```
    const obGetName = obj.getName.bind(obj);
	console.log(obGetName())  // Manish
	const obGetName2 = obj.getName.bind({name:"Piyush"});
	console.log(obGetName2()) // Piyush
    ```

### Event Handlers without bind
  - When we are using event handlers for react component they work fine until we try to access some property of the componets
    ```
    class Options extends React.Component {
        actionClicked() {
            console.log(this.props.options)
        }
        render() {
            return (
                <div>
                    <button onClick={this.actionClicked}>Action</button>
                </div>
            );
        }
    }
    ```
    `this.props.options` will give error as `this.props` will be `undefined` because context of `this` is lost. <br/>
    to recover from above we can bind `this` to the `actionClicked` event inline as below
    ```
    <button onClick={this.actionClicked.bind(this)}>Action</button>
    ```
    Now `this` is bind to the component but this is in-efficient as the re-binding happens everytime the component is re-rendered

### Using Constructors to bind events
  - In the constructor we can bind the event as below
    ```
    class Options extends React.Component {
        constructor(props)
        {
            super(props);
            this.actionClicked = thi.actionClicked.bind(this);
        }

        actionClicked() {
            console.log(this.props.options)
        }
        render() {
            return (
                <div>
                    <button onClick={this.actionClicked}>Action</button>
                </div>
            );
        }
    }
    ```
    With `this.actionClicked = thi.actionClicked.bind(this);` in the constructor the binding happens only once when the component is rendered first time

## Component State
  - Components state allows component to manage data
  - Implement State for Component <br/>
    1. Setup default state object `{count: 1}`
	2. Component rendered with default state values (done internally by react) - UI will show `count` as 1
	3. change state based on events `count = count + 1`
	4. Component re-rendered using new state values (done internally by react) - UI will show `count` as 2
	5. Start again at Step 3
  - Initial State and setState
    ```
    class Counter extends React.Component {
        constructor(props) {
            super(props);
            this.handleAddOne = this.handleAddOne.bind(this);
            this.handleMinusOne = this.handleMinusOne.bind(this);
            this.handleReset = this.handleReset.bind(this);
            this.state = {
            count: 0
            };
        }
        handleAddOne() {
            this.setState((prevState) => {
                return {
                    count: prevState.count + 1
                };
            });
        }
        handleMinusOne() {
            this.setState((prevState) => {
                return {
                    count: prevState.count - 1
                };
            });
        }
        handleReset() {
            this.setState(() => {
                return {
                    count: 0
                };
            });
        }
        render() {
            return (
            <div>
                <h1>Count: {this.state.count}</h1>
                <button onClick={this.handleAddOne}>+1</button>
                <button onClick={this.handleMinusOne}>-1</button>
                <button onClick={this.handleReset}>reset</button>
            </div>
            );
        }
    }

    ReactDOM.render(<Counter />, document.getElementById('app'));
    ```
    - In `this.setState()` we don't need to mention all the properties present in state
        ```
        state = {
            count : 0,
            name : "Manish"
        }
        ```

        for above state, we can only modify the `count` as below by updating the `count` only, the value for `name` will not change
        ```
        handleAddOne() {
            this.setState((prevState) => {
                return {
                    count: prevState.count + 1
                };
            });
        }
        ```
    - `prevState` gives access to old state in above method definition
    - we don't need to define prevState in definition mandatorily, we only need to define prevState when we need to update the value with reference to previous value
        ```
        handleReset() {
            this.setState(() => {
                return {
                    count: 0
                };
            });
        }
        ```
        In above example we are resetting the count, so we don't need to access the `prevState`

    - `this.setState()` is asynchronus
        ```
        handleReset() {
            this.setState({count : 0});
            this.setState({count : this.state.count + 1})
        }
        ```
        for above code, the expected value is 1, everytime `handleReset` is triggered the value should become 0 and then 1, but doesn't happens that way because react works in background to set the value as 0 and the next line comes in to increment previous state by 1
    - to avoid above it is always recommended to use the updater function
        ```
        handleReset() {
            this.setState(() => {
                return {count : 0};
            });
            this.setState((prevState) => {
                return {count : prevState.count + 1};
            });
        }
        ```
        above will work perfectly fine, it will first set the count to 0 and the final count will be 1

### Props vs State
  - Similarities <br/>
   
	|	Props	    |				State             |
	|---------------|---------------------------------|
	|	An Object	|An object                     |
	|	Can be used when rendering |	Can be used when rendering    |
	|	Changes (from above/parent) causes re-render	|	Changes causes re-render      |

  - Differences 
    |	Props	    |				State             |
	|---------------|---------------------------------|
	|	Comes from parent								| 	Defined in component itself   |
	|	Can't be changed by component itself			|	Can be changed by componet    |
    
## Functional Components
- Stateless functional component
- concerned with managing presentation of component
- can be used with simple components
- lifecycle methods not available for Functional Component

    ```
    const User = (props) => {

        return (
            <div>
                <p>Name : {props.name}</p>
                <p>Age : {props.age}</p>
            </div>
        );
    }

    ReactDOM.render(<User name="Manish" age="31"/>, document.getElementById("app"))

    ```
- They are faster than Class based component as they don't have to extend React.Component and extra code comes with Class based components
- Functional Components are good option when state of the component is not required to be managed
- Below is Stateless Class Component 
    ```
    class Header extends React.Component {
        render() {
            return (
                <div>
                    <h1>{this.props.title}</h1>
                    <h2>{this.props.subtitle}</h2>
                </div>
            );
        }
    }
    ```
    this can be converted to a functional component as below
    ```
    const Header = (props) => {
        return (
            <div>
                <h1>{props.title}</h1>
                <h2>{props.subtitle}</h2>
            </div>
        );
    }
    ```
    `this` doesn't need to be used anymore, the content of `render()` from Class based component can be copied directly in the function

### Default Properties
for below functional component
```
const Header = (props) => {
    return (
        <div>
            <h1>{props.title}</h1>
            <h2>{props.subtitle}</h2>
        </div>
    );
}
```
we can define default values if we want to set default values for any keys which may or may not get value when setting attribute for the component as below
```
Header.defaultProps = {
   subtitle : "Default Subtitle" 
}
```
In above we have set the default properties for subtitle only, `<Header title="MyApp" />` will render below html
```
<div>
    <h1>MyApp</h1>
    <h2>Default Subtitle</h2>
</div>
```

**It works for both Functional as well as Class based components**


### Lifecycle methods
  - accessible only in Class based components
  - componetDidMount (when component is mounted)
  - componentDidUpdate (when state/props changes), it has access to prevProps and prevState
    ```
    componentDidUpdate(prevProps, prevState){

    }
    ```
  - componentWillUnmount (when component is removed from DOM)
  - further lifecycle docs can be found [here](https://legacy.reactjs.org/docs/react-component.html#the-component-lifecycle)



 
 