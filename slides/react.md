# React <br><img src="media/react.svg" style="border:none;box-shadow:none;width:35%">



# Concepts
## jQuery vs. React



## Code example
````js
const listOfTodos = [
  {name: 'first task', done: false},
  {name: 'second task', done: true}
];
````



### imperative definition
````js
function buildList(listOfTodos) {
  const $ul = $('<ul>');

  listOfTodos.forEach(todo => {
    $('<li>')
      .text(todo.name)
      .addClass(entry.done ? 'done' : '')
      .appendTo($ul);
  });

  return $ul;
}

$('#root').empty();
$('#root').append(buildList(listOfTodos));
````
Describes the things that have to happen



### declarative definition
````js
function List({listOfTodos}) {
  let entries = [];
  for (let i = 0; i < listOfTodos.length; i++) {
    let className = todo.done ? 'done' : '';
    entries.push(<li className={className}>{todo.name}</li>);
  }

  return <ul>{entries}</ul>;
}

ReactDOM.render(<List listOfTodos={listOfTodos} />,
  document.getElementById('root'));
````
Describes how the result should look like



### jQuery solution
* <!-- .element: class="fragment" -->tears down the complete tree and regenerates at every update (expensive)
* <!-- .element: class="fragment" -->alternatively would have to keep track of the current state and figure out differences and necessary updates



### react solution
* rendered component creates a virtual structure of the desired document tree<!-- .element: class="fragment" -->
````json
  {
    "node": "ul",
    "children": [
      {"node": "li", "text": "first task", "className": ""},
      {"node": "li", "text": "second task", "className": "done"}
    ]
  }
````
<!-- .element: class="fragment" -->
* the framework compares the desired and the current state  <!-- .element: class="fragment" -->
* figures out what the differences are...  <!-- .element: class="fragment" -->
* ...and the minimal number of necessary updates  <!-- .element: class="fragment" -->



<!-- .slide: data-transition="slide-in fade-out" -->
<img src="media/virtualDOM1.PNG" style="width:80%">



<!-- .slide: data-transition="fade-in slide-out" -->
<img src="media/virtualDOM2.PNG" style="width:80%">



# JSX



JSX is a JavaScript superset.
````javascript
function List() {
  return (
    <ul>
      <li>first entry</li>
      <li>second entry</li>
    </ul>
  );
}
````
Syntactic sugar,<br>will be compiled down to executable JavaScript
````javascript
function List() {
  return (
    React.DOM.ul(null,
      React.DOM.li(null, "first entry"),
      React.DOM.li(null, "second entry")
    )
  );
}
````



## JSX: Variable Interpolation
````javascript
function Beans() {
  const verb = 'like';

  return <div>I {verb} beans!</div>;
}
````



## JSX: Subcomponent References
````javascript
function Like() {
  return <span>like</span>;
}

function Bunnies() {
  return <div>I <Like /> bunnies</div>;
}
````



## JSX: Control Structures
Other popular frameworks implement "new" languages to handle conditional or repeating structures



### Control Structures: Conditions
#### Angular
````html
<div id="context">
  <div *ngIf="isValid">Data is valid.</div>
</div>
````

#### Ember
````html
<div id="context">
  {{#if isValid}}
    <div>Data is valid.</div>
  {{/if}}
</div>
````



### Control Structures: Repeater
#### Angular
````html
<ul>
  <li *ngFor="let person of persons">
    {{person}}
  </li>
</ul>
````

#### Ember
````html
<ul>
  {{#each persons as |person|}}
    <li>{{person}}</li>
  {{/each}}
</ul>
````



Since JSX is a JavaScript superset you can do everything you can do with JavaScript:
````javascript
function DataValid(props) {
  let message = null;
  if (props.isValid) {
    message = <div>Data is valid.</div>;
  }

  return <div id="context">{message}</div>;
}
````

````javascript
function Persons(props) {
  const persons = props.persons.map(person => {
    return <li>{person}</li>;
  });

  return <ul>{persons}</ul>;
}
````



# Components



### Components
* <!-- .element: class="fragment" -->React is component based.
* <!-- .element: class="fragment" -->Components are used to separate the parts of a UI into manageable (testable!) building blocks.
* <!-- .element: class="fragment" -->Components can not only be used for UI concerns but also for application logic.
* <!-- .element: class="fragment" -->Two ways of defining components:
  * Functional
  * Class Based



### Components: Functional
````javascript
function Bears() {
  return <div>I like bears!</div>;
}
````

* no internal state
* simple a transformation of input into output
* pure functions

<table>
  <tr>
    <td style="width:50%">
      <h4>pros</h4>
      <ul>
        <li>easy to test</li>
        <li>little/no boilerplate</li>
      </ul>
    </td>
    <td style="width:50%">
      <h4>contra</h4>
      <ul>
        <li>no influence on component lifecycle</li>
        <li>no internal state</li>
      </ul>
    </td>
  </tr>
</table>



### Components: Class Based
````javascript
class Beards extends React.Component {
  render() {
    return <div>I like beards!</div>;
  }
}
````

* inherits from `React.Component`
* normal ES2015 class support
  * constructor
  * methods
  * properties
* lifecycle hooks can be implemented



## Components:<br>Properties (props)
* the way of getting "things" into a component<!-- .element: class="fragment" -->
  ````javascript
  <Birds verb="love" />
  ````
* props can be everything (functions, strings, numbers, objects, other components...)<!-- .element: class="fragment" -->
* readonly<!-- .element: class="fragment" -->
* two ways of prop access, depending on the component declaration<!-- .element: class="fragment" -->



### Components:<br>Properties (functional)
Props are an object passed as the first function parameter

````javascript
function Birds(props) {
  return <div>I {props.verb} birds</div>;
}

ReactDOM.render(<Birds verb="hate" />, root);

//result: <div>I hate birds</div>
````



### Components:<br>Properties (class based)
Props are a property of the class instance
````javascript
class Bricks extends React.Component {
  render() {
    return <div>I {this.props.verb} bricks!</div>;
  }
}

ReactDOM.render(<Bricks verb="like" />, root);

//result: <div>I like bricks</div>
````



### Components: <br>Special Property Children
* allows for the access to children components
* `children` can be
  * invoked
  * manipulated
  * ignored
  * ...



````javascript
function BeefLiker(props) {
  return <li>{props.name}</li>;
}

function BeefLikers(props) {
  return (
    <div>
      <h3>There are a lot of people who like beef:</h3>
      <ul>{props.children}</ul>
    </div>
  );
}
````

````xml
<BeefLikers>
  <BeefLiker name="Joe" />
  <BeefLiker name="William" />
  <BeefLiker name="Jack" />
  <BeefLiker name="Averell" />
</BeefLikers>
````



## Components: State
* <!-- .element: class="fragment" -->components can hold internal, non global state (e.g. a dropdown menu) 
* <!-- .element: class="fragment" -->state initialization happens in the constructor 
* <!-- .element: class="fragment" -->state manipulation is only allowed via `this.setState({foo: 'bar'})`
* <!-- .element: class="fragment" -->state manipulation triggers a _local_ rerender



## Components: State
````javascript
class Clickr extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 0};
  }

  increase() {
    this.setState({count: this.state.count + 1});
  }

  render() {
    return <button onClick={() => this.increase()}>{this.state.count}</button>);
  }
}
````



### Components:<br>State vs. Props
* Props: data that has been assigned by the parent component
* State: Internal data that *may* be derived from props



## Components:<br>Lifecycle Methods
During its "life" a component goes through several steps.

At each of these steps we can add our own implementation to influence the behaviour of a component.



### Components:<br>Lifecycle Methods
![](media/lifecycles_simple.PNG)
<div class="source">Source: http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/</div>



#### Lifecycle Methods: Creation
1. <!-- .element: class="fragment" -->The constructor is called, initialization of component state
2. <!-- .element: class="fragment" -->The render method is called
3. <!-- .element: class="fragment" -->React updates the DOM
4. <!-- .element: class="fragment" -->Invocation of `componentDidMount()`
   Helpful for additional initializations e.g. starting a timer or interval, data retrieval (AJAX, Websockets), binding EventEmitters, ...



#### Lifecycle Methods: Updating
1. <!-- .element: class="fragment" -->Updates of a component are initialized in three different ways:
    1. <!-- .element: class="fragment" -->Assignment of new properties from the parent component
    2. <!-- .element: class="fragment" -->via a `setState()`call, when the local state is updated (e.g. a button click)
    3. <!-- .element: class="fragment" -->the method `forceUpdate()` forces React to rerender the component<br> _(not recommended and usually not necessary!)_
2. <!-- .element: class="fragment" -->the lifecycle hook `componentDidUpdate()` is called



#### Lifecycle Methods: Example
````javascript
class Countr extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 0};
    this.intervalHandle = null;
  }
  
  render() {
    return <p>count: {this.state.count}</p>;
  }

  componentDidMount() {
    this.intervalHandle = setInterval(() => 
      this.setState({count: this.state.count + 1}),
    1000);
  }

  componentWillUnmount() {
    clearInterval(this.intervalHandle);
  }
}
````



#### Lifecycle Methods: More Hooks
![](media/lifecycles_all.PNG)
<div class="source">Source: http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/</div>



#### More Hooks: getDerivedStateFromProps()
* <!-- .element: class="fragment" -->`getDerivedStateFromProps()` allows for an implicit state update based on the properties
* <!-- .element: class="fragment" -->Especially interesting when updates of props should be shown
* <!-- .element: class="fragment" -->static declaration




#### More Hooks: shouldComponentUpdate()
* <!-- .element: class="fragment" -->With `shouldComponentUpdate()` it is possible to prevent the rerendering of a component
* <!-- .element: class="fragment" -->Parameters are the new props and the new state (possibly influenced by `getDerivedStateFromProps()`)
* <!-- .element: class="fragment" -->A "manual" comparison of `this.props` and `this.state` is therefore possible
* <!-- .element: class="fragment" -->Is usually not necessary, only for hardcore performance optimizations




## Events and Eventhandling
* <!-- .element: class="fragment" -->Event handlers are simply component attributes
* <!-- .element: class="fragment" -->Instead of a value a method is assigned
* <!-- .element: class="fragment" -->The parameter of an event handler is a synthetic event, which closely resembles the native event, but without browser incompatibilities.



````javascript
class Togglr extends React.Component {
  constructor(props) {
    super(props);
    this.state = {on: false};
  }

  toggle() {
    this.setState({on: !this.state.on});
  }

  render() {
    const caption = this.state.on ? 'ON' : 'OFF';
    return <button onClick={() => this.toggle())}>{caption}</button>;
  }
}
````



onCopy onCut onPaste onCompositionEnd onCompositionStart onCompositionUpdate `onKeyDown` onKeyPress `onKeyUp` `onFocus` onBlur onChange onInput onInvalid onSubmit `onClick` onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit onDragLeave onDragOver onDragStart onDrop onMouseDown `onMouseEnter` `onMouseLeave` `onMouseMove` onMouseOut onMouseOver onMouseUp onPointerDown onPointerMove onPointerUp onPointerCancel onGotPointerCapture onLostPointerCapture onPointerEnter onPointerLeave onPointerOver onPointerOut onSelect onTouchCancel onTouchEnd onTouchMove onTouchStart onScroll onWheel onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncrypted onEnded onError onLoadedData onLoadedMetadata onLoadStart onPause onPlay onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend onTimeUpdate onVolumeChange onWaiting onLoad onError onAnimationStart onAnimationEnd onAnimationIteration onTransitionEnd onToggle




# Gotchas



## Gotchas:<br>React in Scope
Since JSX gets compiled down to JS which contains a reference to the React module it is necessary that the import of `React` is always in scope.

````javascript
import React from 'react';

function Bear() {
  return <span>Bear</span>;
}
````

For the sake of clarity the import is omitted in most examples.



## Gotchas:<br>Unbound `this`
Javascript binds the `this` context of a function at runtime, dependent on its call context.

````javascript
class DoSomethingButton extends React.Component {
  doSomething() {
    //this is unbound when called from the onClick handler
    this.setState({...});
  }

  render() {
    return <button onClick={this.doSomething}>Do something!</button>;
  }
}
````
Won't work!



## Gotchas:<br>Unbound `this`
The (recommended) solution is an explicit `bind()` in the constructor

````javascript
class DoSomethingButton extends React.Component {
  constructor(props) {
    super(props);
    this.doSomething = this.doSomething.bind(this);
  }

  ...
}
````



## Gotchas: Lists
When updating a list of things it is not always possible for React to clearly differentiate between the current state and the desired state.


![](media/virtualDOM3.PNG)



## Gotchas: Lists
A key can be used to provide the framework with a hint.


![](media/virtualDOM4.PNG)



## Gotchas: Lists

The same goes for sorting things, React is much more efficient when resorting
DOM elements in contrast to destroying the whole list and recreating it.

_Don't worry, in **dev** mode React will pester you with prompts to supply keys
if not present..._



## Gotchas:<br>className vs. class

`class` is a reserved keyword in JavaScript, therefore it is necessary to use
`className` instead.

````javascript
function Batman() {
   return <div className="batman-liker">I like batman.</div>;
}

// result: <div class="batman-liker">I like batman.</div>
````

_This may change in future React versions._



## Gotchas:<br>Component Root Element
It is recommended that every component returns exactly one root element.

It is possible though to return lists of DOM elements (array) or to use React fragments.



## Gotchas:<br>Component Names
Component names always need to be capitalized, otherwise they'd be interpreted as a DOM element.

````javascript
function Bags() {
   return <span>bags</span>;
}

function BagLiker() {
   return (
     <div>
      I like <bags />. {/* will not do what you might expect */}
      I like <Bags />. {/* here you go */}
     </div>
   );
}
````



# React Patterns



## Patterns: Behaviour Components

Components are not necessarily only meant to be used for the rendering of parts of a UI but also can be used to implement logic.


````javascript
class BeansLoader extends React.Component {
  constructor(props) {
    super(props);
    this.state = {beans: []};
  }

  componentDidMount() {
    fetch('https://bean.base/beans')
      .then(beans => this.setState({beans}));
  }

  render() {
    return <BeansList beans={this.state.beans} />;
  }
}
````


Initially an empty list of beans will be rendered, but when the fetch call returns a result React will rerender the `<BeansList />` component and therefore will update the UI.



## Patterns: Prop Types

React offers the possibility to define the type of properties which helps in development.
Typechecks will not be executed in a production environment.

````javascript
import PropTypes from 'prop-types';

function Bacon(props) {
  return <h1>Hello {props.name}, I like Bacon!</h1>;
}

Bacon.propTypes = {name: PropTypes.string};
````
PropTypes live in a separate package.
https://reactjs.org/docs/typechecking-with-proptypes.html



## Patterns: Default Props

For each property a default can be defined:

````javascript
function Bacon(props) {
  return <h1>Hello {props.name}, I like Bacon!</h1>;
}

Bacon.defaultProps = {name: 'Garfield'};
````



## ASSIGNMENT TIME!!!
![](media/dance.webp)