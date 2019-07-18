# Unit Testing



## Unit Testing
We're using two libraries:

* __Jest__  
  The test runner that executes our test and supplies several helpers like an assertion library.
* __Enzyme__  
  Renders React components in a sandbox and offers several helpers that help probing the rendered output.



# Jest
<img src="media/jest.svg" style="border:none;box-shadow:none;width:20%">



## Jest: Test structuring

* <!-- .element: class="fragment" -->Defines a DSL, that is used to structure tests.  
* <!-- .element: class="fragment" -->Consists of two functions:
  * <!-- .element: class="fragment" -->`describe(title, suiteFn)`: a suite of tests
  * <!-- .element: class="fragment" -->`it(title, testFn)`: a single test case
* <!-- .element: class="fragment" -->Describes can be nested arbitrarily.


````javascript
describe('my first test suite', () => {
  it('my first test', () => {
    ...
  });

  describe('sub suite', () => {
    it('should test', () => {
      ...
    });
  });
});
````



## Jest: Assertions
An assertion consists of two parts:
* selection: defines what's to be tested
* matcher: defines the expection for this selection

````javascript
const sum = (a, b) => a + b;

it('should sum up', () => {
  expect(sum(1, 2)).toEqual(3);
});
````

There are a lot of matchers, we are only concentrating on the most important ones.



### Assertions: toBe / toEqual
When a certain value is expected, the tests `toBe()` and `toEqual()` are used:

````javascript
const a = {method: 'park'};
const b = {method: 'park'};

it('should succeed because of deep equality', () => {
  expect(a).toEqual(b); 
});

it('should fail because not identical', () => {
  expect(a).toBe(b);
});
````



### Assertions: .not
When a test should be reversed, the `.not` keyword can be used

````javascript
const a = {method: 'park'};
const b = {method: 'park'};

it('should succeed because a and b are not identical', () => {
  expect(a).not.toBe(b);
});
````




### Assertions: resolves / rejects
When testing for promises Jest helps with an appropriate assertion

````javascript
function sayYes() {
  return new Promise(resolve => 
    setTimeout(() => resolve('yes!'), 100)
  );
}

it('should say yes!', () => {
  // the return is essential!
  return expect(sayYes()).resolves.toEqual('yes!');
});
````




### More assertions!
Find all assertions with detailed explanations here:
https://facebook.github.io/jest/docs/en/expect.html




## Snapshot Testing
Jest incorporates the possibility to do snapshot tests of any data structure
that can be serialized (anything!)

````javascript
const a = {method: 'park'};

it('should check for the snapshot', () => {
  expect(a).toMatchSnapshot();
});
````

Jest generates a snapshot of the serializable data structure that looks like this:

````javascript
exports[`should check for the snapshot 1`] = `
Object {
  "method": "park",
}
`;
````



# Enzyme
Enzyme is the good buddy of Jest made by Airbnb.<br><br>
Its job is to take React components, interpret/run them in a sandbox and to offer a range
of possibilities to interact with the generated markup.




## Enzyme: Usage
````javascript
import {shallow} from 'enzyme';

function BruceLee() {
  return <div>I like Bruce Lee!</div>
}

it('should check for bruce lee', () => {
  const bruce = shallow(<BruceLee />);

  console.log(bruce);
});
````

output:

````html
  console.log src/components/__tests__/bruce.test.js:11
    <div>
      I like Bruce Lee!
    </div>
````



## Enzyme: Render Modes
* <!-- .element: class="fragment" -->shallow
  * only renders the tested component itself, does not render child components
  * react aware
* <!-- .element: class="fragment" -->mount
  * renders the complete component sub tree.
  * react aware
* <!-- .element: class="fragment" -->render
  * renders a full dom
  * react unaware, does not retain any react references


````javascript
function Verb(props) {
  return <span>{props.what === 'love' ? '<3' : 'hate'}</span>;
}

function Beard() {
  return <div>I <Verb what="love" /> beards</div>;
}

it('should show the difference between mount and shallow', () => {
  let shallowBeard = shallow(<Beard />);
  let mountedBeard = mount(<Beard />);

  console.log(shallowBeard.debug());
  console.log(mountedBeard.debug());
});
````


````html
    console.log src/components/__tests__/beard.test.js:28
      <div>
        I
        <Verb what="love" />
         beards
      </div>

    console.log src/components/__tests__/beard.test.js:29
      <Beard>
        <div>
          I
          <Verb what="love">
            <span>
              &lt;3
            </span>
          </Verb>
           beards
        </div>
      </Beard>
````




## Enzyme: Selectors
When a component has been rendered, the `find()` method can be used to look
for certain elements in the component tree to perform assertions on them.



### Selectors: CSS
Like in the good old jQuery days we can use CSS selectors to select for elements

* class syntax: `.foo`
* element syntax: `div`
* id syntax: `#foo`
* combinations: `.navigation>li.active`


````javascript
function BruceLee() {
  return <div>I like Bruce Lee!</div>;
}

it('should check for bruce lee', () => {
  // render the bruce component
  const bruce = shallow(<BruceLee />);

  // look for the div in the rendered tree
  const bruceDiv = bruce.find('div');

  // check the content of the rendered div
  expect(bruceDiv.text()).toEqual('I like Bruce Lee!');
});
````




### Selectors: Properties
We can use the properties that are assigned to a component to pick it from the component tree.

These notations both will find us components that are assigned the property `foo=3`:

````javascript
shallowRendered.find('[foo=3]');
shallowRendered.find({foo: 3});
````




### Selectors: Component
We also can use a component itself to query for its instances inside a component tree:

````javascript
function Verb(props) {
  return <span>{props.what === 'love' ? '<3' : 'hate'}</span>;
}

function Beard() {
  return <div>I <Verb what="love" /> beards</div>;
}

it('should show the querying by component', () => {
  const shallowBeard = shallow(<Beard />);
  const verb = shallowBear.find(Verb);

  expect(verb.props()).toEqual({what: 'love'});
});
````




## Enzyme: Querying
When a component has been rendered (either via `shallow()` or `mount()`) there are a lot of
methods that can be used, the most important are discussed here.

Find a complete list at <http://airbnb.io/enzyme/docs/api/>




### Querying: text() / html()
`text()` and `html()` are used to check the rendered content of a selected DOM node

````javascript
const bruceDiv = shallow(<BruceLee />).find('div');

expect(bruceDiv.text()).toEqual('I like Bruce Lee!');
expect(bruceDiv.html()).toEqual('<div>I like Bruce Lee!</div>');
````




### Querying: props() / prop(key)
Can be used to check for properties that have been assigned to a rendered component

````javascript
const shallowBeard = shallow(<Beard />);

const beardVerb = shallowBeard.find(Verb);

expect(beardVerb.prop('what')).toEqual('love');
expect(beardVerb.props()).toEqual({what: 'love'});
````



### Querying: state([key])
Can be used to check for the internal state of a component




### Querying: hasClass(className)
Asserts the existence of a class on a rendered component

````javascript
function Baltimore(props) {
  const className = props.how_much === 'very_much' ? 'important' : '';

  return <div className={className}>I love Baltimore!</div>;
}
````
<!-- .element: class="fragment" -->

````javascript
it('should have a class', () => {
  const shallowBaltimore = shallow(<Baltimore how_much="very_much" />);
  const baltimoreDiv = shallowBaltimore.find('div');

  expect(baltimoreDiv.hasClass('important')).toBe(true);
});
````
<!-- .element: class="fragment" -->



## Interaction with rendered markup

When a component has been rendered it is possible to manipulate it by injecting new props or by resetting the state.

We only show the most interesting methods here as there are a lot more possibilities: http://airbnb.io/enzyme/docs/api/




### Interaction: setProps(...)
Use the method `setProps(...)` to reset properties on a component

````javascript
function Beer(props) {
  return <span>I {props.verb} beer!</span>;
}

it('should show the interaction with a wrapped component', () => {
  let beerWrapper = shallow(<Beer verb="hate" />);
  expect(beerWrapper.find('span').text()).toEqual('I hate beer!');

  beerWrapper.setProps({verb: 'love'});
  expect(beerWrapper.find('span').text()).toEqual('I love beer!');
});
````



### Interaction: setState(...)
By using `setState(...)` the current state of a component can be manipulated.

````javascript
class Borgs extends React.Component {
  constructor() {
    super();
    this.state = {verb: 'hate'};
  }

  render() {
    return <span>I {this.state.verb} borgs!</span>;
  }
}

it('should demonstrate the interaction with a wrapped component', () => {
  let borgWrapper = shallow(<Borgs verb="hate" />);
  expect(borgWrapper.find('span').text()).toEqual('I hate borgs!');

  borgWrapper.setState({verb: 'really hate'});
  expect(borgWrapper.find('span').text()).toEqual('I really hate borgs!');
});
````



## Usage:

Execute via: `npm test`

By default only runs tests for changed (`git`!) files
![](media/jest_run.PNG)


#### Coverage Report
get a coverage report: `npm test -- --coverage`


#### Executed Files
* every `*.js` file that sits in a `__tests__` folder
* files with a `*.test.js` suffix
* files with a `*.spec.js` suffix
* makes it easier to colocate testing files next to the file that's actually tested



## ASSIGNMENT TIME!!!
![](media/dance.webp)
