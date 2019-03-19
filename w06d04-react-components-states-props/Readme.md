# React Components, Props, States

## Learning Competencies
By the end of this day you should be able to know
- What is Component and why to use it in react
- How to combine components to make the app easier to maintain
- What are states and props in react
- How to validate props in React
- What are Component API lifecycle methods

## Overview

## Components

React is all about components. They are the heart and soul of React.You need to think of everything as a component. This will help you maintain the code when working on larger scale projects.

#### What Wikipedia Says 

Components are one of the things that make React...well, React! They are one of the primary ways you have for defining the visuals and interactions that make up what people see when they use your app. Let's say this is what your finished app looks like:

![alt text](https://github.com/schoolofacceleratedlearning/soal-student-workflow/blob/Day-27-React-Components/daily-schedule/29-react-components-states-props/react-1.png)

This is the finished sausage. During development, viewed from the lens of a React project, things might look a little less appealing. Almost every part of this app's visuals would be wrapped inside a self-contained module known as a component. To highlight what "almost every" means here, take a look at the following diagram:

![alt text](https://github.com/schoolofacceleratedlearning/soal-student-workflow/blob/Day-27-React-Components/daily-schedule/29-react-components-states-props/react-2.png)

A component's mutable state is maintained by its state (which is private to each component). state is also highly reactive: every single time you call this.setState() the component re-renders itself and while doing so, the underlying UI component(s) can take advantage of the freshly set state. All this magic is handled by the virtual DOM that React maintains where as Parent-children components can communicate via props. props, unlike state, are completely immutable (i.e. you would get a runtime Error if you tried to modify them)

### Stateless Example 

Our first component in example below is App. This component is owner of Header and Content. We are creating Header and Content separately and just adding it inside JSX tree in our App component. Only App component needs to be exported.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   render() {
      return (
         <div>
            <Header />
            <Content />
         </div>
      );
   }
}

class Header extends React.Component {
   render() {
      return (
         <div>
            <h1>Header</h1>
         </div>
      );
   }
}

class Content extends React.Component {
   render() {
      return (
         <div>
            <h2>Content</h2>
            <p>The content text!!!</p>
         </div>
      );
   }
}

export default App;
```

To be able to render this on page, we need to import it in main.js file and call reactDOM.render(). We already did it when we were setting environment.

main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App />, document.getElementById('app'));
```

### Stateful Example

In this example we will set the state for owner component (App). The Header component is just added like in the last example since it doesn't need any state. Instead of content tag, we are creating table and tbody elements where we will dynamically insert TableRow for every object from the data array. You can see that we are using EcmaScript 2015 arrow syntax (⇒) which looks much cleaner then the old JavaScript syntax. This will help us create our elements with fewer lines of code. It is especially useful when you need to create list with a lot of items.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   constructor() {
      super();
		
      this.state = {
         data: 
         [
            {
               "id":1,
               "name":"Foo",
               "age":"20"
            },
				
            {
               "id":2,
               "name":"Bar",
               "age":"30"
            },
				
            {
               "id":3,
               "name":"Baz",
               "age":"40"
            }
         ]
      }
   }
	
   render() {
      return (
         <div>
            <Header/>
            <table>
               <tbody>
                  {this.state.data.map((person, i) => <TableRow key = {i} data = {person} />)}
               </tbody>
            </table>
         </div>
      );
   }
}

class Header extends React.Component {
   render() {
      return (
         <div>
            <h1>Header</h1>
         </div>
      );
   }
}

class TableRow extends React.Component {
   render() {
      return (
         <tr>
            <td>{this.props.data.id}</td>
            <td>{this.props.data.name}</td>
            <td>{this.props.data.age}</td>
         </tr>
      );
   }
}

export default App;
```
main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```
**NOTE**: Notice that we are using `key = {i}` inside `map()` function. This will help React to update only necessary elements instead of re-rendering entire list when something change. It is huge performance boost for larger number of dynamically created elements.

## States and props 

**State** is the place where the data comes from. We should always try to make our state as simple as possible and minimize the number of stateful components. If we have, for example, ten components that need data from the state, we should create one container component that will keep the state for all of them.

The main difference between `state` and `props` is that props are immutable. This is why the container component should define the state that can be updated and changed, while the child components should only pass data from the state using props.

### Using `state`

Code sample below shows how to create stateful component using EcmaScript2016 syntax.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   constructor(props) {
      super(props);
		
      this.state = {
         header: "Header from state...",
         "content": "Content from state..."
      }
   }
	
   render() {
      return (
         <div>
            <h1>{this.state.header}</h1>
            <h2>{this.state.content}</h2>
         </div>
      );
   }
}

export default App;
```
main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App />, document.getElementById('app'));
```

### Using `props`

When you need immutable data in your component you can just add props to reactDOM.render() function in main.js and use it inside your component.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   render() {
      return (
         <div>
            <h1>{this.props.headerProp}</h1>
            <h2>{this.props.contentProp}</h2>
         </div>
      );
   }
}

export default App;
```
main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App headerProp = "Header from props..." contentProp = "Content from props..."/>,
	 document.getElementById('app'));

export default App;
```

### Default Props
You can also set default property values directly on the component constructor instead of adding it to reactDom.render() element.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   render() {
      return (
         <div>
            <h1>{this.props.headerProp}</h1>
            <h2>{this.props.contentProp}</h2>
         </div>
      );
   }
}

App.defaultProps = {
   headerProp: "Header from props...",
   contentProp:"Content from props..."
}

export default App;
main.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```

### Using both `states` and `props`

The example below shows how to combine state and props in your app. We are setting state in our parent component and passing it down the component tree using props. Inside render function, we are setting headerProp and contentProp that are used in child components.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   constructor(props) {
      super(props);
		
      this.state = {
         header: "Header from props...",
         "content": "Content from props..."
      }
   }
	
   render() {
      return (
         <div>
            <Header headerProp = {this.state.header}/>
            <Content contentProp = {this.state.content}/>
         </div>
      );
   }
}

class Header extends React.Component {
   render() {
      return (
         <div>
            <h1>{this.props.headerProp}</h1>
         </div>
      );
   }
}

class Content extends React.Component {
   render() {
      return (
         <div>
            <h2>{this.props.contentProp}</h2>
         </div>
      );
   }
}

export default App;
```

main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```

The result will again be the same as in previous two examples, the only thing that is different is the source of our data, which is now originally coming from the state. When we want to update it, we just need to update state, and all child components will be updated.

### Validating Props
In this example we are creating App component with all the props that we need. App.propTypes is used for props validation. If some of the props aren't using correct type that we assigned, we will get console warning. After we specified validation patterns, we are setting App.defaultProps.

App.jsx
```js
import React from 'react';

class App extends React.Component {
   render() {
      return (
         <div>
            <h3>Array: {this.props.propArray}</h3>
            <h3>Bool: {this.props.propBool ? "True..." : "False..."}</h3>
            <h3>Func: {this.props.propFunc(3)}</h3>
            <h3>Number: {this.props.propNumber}</h3>
            <h3>String: {this.props.propString}</h3>
            <h3>Object: {this.props.propObject.objectName1}</h3>
            <h3>Object: {this.props.propObject.objectName2}</h3>
            <h3>Object: {this.props.propObject.objectName3}</h3>
         </div>
      );
   }
}

App.propTypes = {
   propArray: React.PropTypes.array.isRequired,
   propBool: React.PropTypes.bool.isRequired,
   propFunc: React.PropTypes.func,
   propNumber: React.PropTypes.number,
   propString: React.PropTypes.string,
   propObject: React.PropTypes.object
}

App.defaultProps = {
   propArray: [1,2,3,4,5],
   propBool: true,
   propFunc: function(e){return e},
   propNumber: 1,
   propString: "String value...",
	
   propObject: {
      objectName1:"objectValue1",
      objectName2: "objectValue2",
      objectName3: "objectValue3"
   }
}

export default App;
```
```js
main.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```

You probably noticed that we use isRequired when validating propArray and propBool. This will give us error if one of those two don't exist. If we delete propArray: [1,2,3,4,5] from the App.defaultProps object, the console will log warning.
If we set the value of propArray: 1, React will warn us that the propType validation has failed, since we need an array and we got a number.

## Component API

### Set State

`setState()` method is used for updating the state of the component. This method will not replace the state but only add changes to original state.
```js
import React from 'react';

class App extends React.Component {
   constructor() {
      super();
		
      this.state = {
         data: []
      }
	
      this.setStateHandler = this.setStateHandler.bind(this);
   };

   setStateHandler() {
      var item = "setState..."
      var myArray = this.state.data;
      myArray.push(item)
      this.setState({data: myArray})
   };

   render() {
      return (
         <div>
            <button onClick = {this.setStateHandler}>SET STATE</button>
            <h4>State Array: {this.state.data}</h4>
         </div>
      );
   }
}

export default App;
```
We started with empty array. Every time we click the button, the state will be updated.

### Force Update
Sometimes you want to update the component manually. You can achieve this by using forceUpdate() method.
```js
import React from 'react';

class App extends React.Component {
   constructor() {
      super();
      this.forceUpdateHandler = this.forceUpdateHandler.bind(this);
   };

   forceUpdateHandler() {
      this.forceUpdate();
   };

   render() {
      return (
         <div>
            <button onClick = {this.forceUpdateHandler}>FORCE UPDATE</button>
            <h4>Random number: {Math.random()}</h4>
         </div>
      );
   }
}

export default App;
```
We are setting random number that will be updated every time the button is clicked.	

### Find Dom Node

For DOM manipulation, we can use `ReactDOM.findDOMNode()` method. First we need to import `react-dom`.
```js
import React from 'react';
import ReactDOM from 'react-dom';

class App extends React.Component {
   constructor() {
      super();
      this.findDomNodeHandler = this.findDomNodeHandler.bind(this);
   };

   findDomNodeHandler() {
      var myDiv = document.getElementById('myDiv');
      ReactDOM.findDOMNode(myDiv).style.color = 'green';
   }
	
   render() {
      return (
         <div>
            <button onClick = {this.findDomNodeHandler}>FIND DOME NODE</button>
            <div id = "myDiv">NODE</div>
         </div>
      );
   }
}

export default App;
```
The color of myDiv element is changed to green, once the button is clicked.

Read about the Component Lifecycle [here](https://medium.com/react-ecosystem/react-components-lifecycle-ce09239010df)

## Exploration

- Read about props and states [here](https://themeteorchef.com/tutorials/understanding-props-and-state-in-react) and [here](http://lucybain.com/blog/2016/react-state-vs-pros/)
- [Components and props](https://facebook.github.io/react/docs/components-and-props.html)
- [Prop validation in React](https://medium.com/@MoeSattler/better-prop-validation-in-react-cc83590d311f)
- [Component API](http://www.react.express/component_api)
- [Component Lifecycle](http://www.react.express/lifecycle_api)
