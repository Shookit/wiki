React
=====


Basics
------

The JSX extension allows standard-formatted HTML inside of javascript. This also allows for the use of templated calls to other JS functions, which cuts down on extra typing significantly:


```
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

Since JSX is not a standard web language, babel is required to convert JSX to plain Javascript. Babel compiles JSX elements down to React.createElement() calls:


```
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);

//becomes

const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);

```

The React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state. Hence, if you re-render an element with, say, the just content of an `<H1>` changed, react's virtual DOM diffing functionality will only update what changed in the actual DOM, rather than re-rendering the entire element.


Components
----------

Conceptually, components are like JavaScript functions. They accept arbitrary inputs (called "props") and return React elements describing what should appear on the screen.

```
//Function-based component: (props) => element
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

//Class-based component
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Can also use user-defined components, which are denoted via capitalization. Any attributes passed in become attached to the props object.

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

React is very explicit in emphasizing that

> All React components must act like pure functions with respect to their props


Component state
---------------

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

1. When <Clock /> is passed to ReactDOM.render(), React calls the constructor of the Clock component. Since Clock needs to display the current time, it initializes this.state with an object including the current time. We will later update this state.
2. React then calls the Clock component's render() method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the Clock's render output.
3. When the Clock output is inserted in the DOM, React calls the componentDidMount() lifecycle hook. Inside it, the Clock component asks the browser to set up a timer to call tick() once a second.
4. Every second the browser calls the tick() method. Inside it, the Clock component schedules a UI update by calling setState() with an object containing the current time. Thanks to the setState() call, React knows the state has changed, and calls render() method again to learn what should be on the screen. This time, this.state.date in the render() method will be different, and so the render output will include the updated time. React updates the DOM accordingly.
5. If the Clock component is ever removed from the DOM, React calls the componentWillUnmount() lifecycle hook so the timer is stopped.

You should never manually modify props. You can add additional fields to the class, as long as they aren't used for the visual output (e.g., in `render()`). Never manually modify `this.state`; call `this.setState()` with the changed state objects.

Because this.props and this.state may be updated asynchronously, don't rely on their values for calculating the next state. Instead, use a second form of setState() that accepts a function instead of an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:

As a rule, you should only contain state within the lowest common component. So if you have two forms that you want to keep in sync, you should use a parent component to hold state.

```
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});

// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

Neither parent nor child components can know if a certain component is stateful or stateless, and they shouldn't care whether it is defined as a function or a class. This is why state is often called local or encapsulated. It is not accessible to any component other than the one that owns and sets it. This is commonly called a "top-down" or "unidirectional" data flow. Any state is always owned by some specific component, and any data or UI derived from that state can only affect components "below" them in the tree.


Events
------

```
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```


Lists and Keys
--------------

Lists of elements can be rendered directly to the DOM.

```
class NumberList extends React.Component {
  constructor(props){
      const numbers = props.numbers;
  }

  // Might not be valid location for listItems, but you get the idea
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
        {number}
    </li>
  );

  render (){
    <ul>{listItems}</ul>;
  }
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

The only item to note is that a 'key' should be provided when using lists of elements. Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity.  The best way to pick a key is to use a string that uniquely identifies a list item among its siblings. Most often you would use IDs from your data as keys. When you don't have stable IDs for rendered items, you may use the item index as a key as a last resort. A good rule of thumb is that elements inside the map() call need keys.

This is used by the tree diffing system to avoid re-rendering entire trees when just a single element is added.


Forms
-----

React takes over standard form handling (including typing), so you will manually handle changes made by the user via keyboard.
This is true for radio buttons and text fields.

If you use `value`, that element is immutable (except by react). `defaultValue` allows changing values in the form via normal form interaction.

```
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite La Croix flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```


Rendering notes
---------------

Don't put much logic inside `render` besides basic elements and properties; all logic should get figured out by in component functions (or sub-components' functions). Can maybe put a 'map' in render, but use sparingly. Render must return a react element. A list is not a react element, but a list encapsulated in a DOM element is.

Only two sources of re-renders comes from state and props changes. Anything in constructor is constant and will never trigger a re-render; just use the constructor for constants.

```javascript
//Bad idea; this.groups will never change (or cause a rendering update) due to a change in props
class Group extends React.Component {
    constructor (props) {
        super(props);
        this.groups = props.groups;
    }

    render () {
        return (
            {this.groups}
        );
    }
}

//Good; render funct must handle re-rendering due to changes in props
class Group extends React.Component {
    constructor (props) {
        super(props);
    }

    render () {
        return (
            {this.props.groups}
        );
    }
}
```


Binds
-----

Callbacks (e.g., onClick) requires the function to be bound if 'this' is anywhere in the callback.
There are 4 ways of accomplishing this:

```
// Fat arrow notation in render
<p onClick={() => this.handleChange()}>+</p>

// Bind in render
<p onClick={this.handleChange.bind(this)}>+</p>

// Bind in constuctor
constructor(props) {
  super(props);
  this.handleChange = this.handleChange.bind(this);
}

// Fat arrow in function declaration (may cause syntax error in linter)
handleChange = () => {
  // call this function from render
  // and this.whatever in here works fine.
};

```


propTypes
---------

Proptypes let you do dynamic typechecking on components constructors. This is useful for documenting and checking constructor parameters.


```
SomeComponent.propTypes = {
    "id": React.PropTypes.number.isRequired,
    "lastWeight": React.PropTypes.string,
    "lbsKg": React.PropTypes.string.isRequired
};
```
