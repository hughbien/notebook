# Functional React

Notes on Functional React by Cristian Salcescu.

# Functional JavaScript

Functions in JavaScript are treated as first class values:

```js
const sum = function(x, y) {
  return x + y;
}
const sum = (x, y) => x + y; // or alternatively

sum(1, 2); // 3
```

Pure functions are computations that return the same result given the same input. There are no
side-effects. Immutable objects cannot be modified after its creation. They can be created using
`Object.freeze()`, which does a shallow freeze.

```js
const product = Object.freeze({
  name: "apple",
  quantity: 1
});
product.quantity = 2; // cannot assign readonly property
```

Instead you'll need to create a clone, here's an example using the spread syntax:

```js
const newProduct = Object.freeze({
  ...product,
  quantity: 2
});
```

JavaScript includes functional method calls on arrays, so you don't have to use a for loop. This
includes:

* filter - selects values from list using predicate function
* map - transforms list of values
* reduce - reduces list to single value

Functional methods are useful with method chaining.

A closure is an inner function that has access to variables from the outer function, even after the
outer function has executed.

A high order function takes another function as input, returns a function, or does both.

Currying is the process of transforming a function with several parameters into a series of functions,
each taking a single parameter.

# Functional Components

React lets you express UI using functions:

```js
import React from "react";

function Header() {
  return (
    <header>
      <h1>A Shopping Cart</h1>
    </header>
  );
}

export default Header;
```

Functional components can take properties too:

```js
function ProductItem(props) {
  return (
    <div>{props.product.name}</div>
  );
}
```

Functional React elements are immutable. `ReactDOM.render(element, containerDOM)` renders a React
element and all of its children. ReactDOM will update the DOM based on React elements. Since DOM
updates are expensive, React uses a Virtual DOM and a diff algorithm to identify necessary updates.
It only applies them to the real DOM when necessary. Use the React Developer Tools for access to
the virtual DOM.

# Brief Introduction to JSX

JSX stands for JavaScript XML. It's transpiled to JavaScript:

```js
ReactDOM.render(
  <header>
    <h1>A Shopping Cart</h1>
  </header>,
  document.getElementById("root");
);

// transpiled to JavaScript
ReactDOM.render(
  React.createElement("header", null,
    React.createElement("h1", null, "A Shopping Cart")
  ),
  document.getElementById("root")
);
```

JSX accepts any valid JavaScript expression in curly brackets. It does automatic escaping of string
expressions. Function components can return a single tag. All tags need to be closed, since this
is XML. Lower-cased tag names are considered HTML tags, not React components. Attributes use
camelCase. Some HTML attributes have different names: `class` is `className` and `for` is `htmlFor`.

# List Components

While a functional component transforms a value into a React element, a list component transforms
a list of values into a list of React elements, usually using `map()`.

```js
import ProductItem from "./ProductItem";

function ProductList({products}) {
  return (
    <div>
      {products.map(product =>
        <ProductItem
          key={product.id}
          product={product}
        />
      )}
    </div>
  );
}

export default ProductList;
```

React requires a key to uniquely identify each item in a list. It's a special attribute that helps
the React diff algorithm.

Use the spread attribute to help pass all props to child elements:

```js
<ProductItem
  key={product.id}
  {...product}
/>
```

# Communication between Components

Components are organized in a tree-like structure. Communication happens from parent to children and
child to parent. We see this already with props. Functions can also be a prop. This works well for
callbacks.

```js
function ProductItem({product, onAddClick}) {
  return (
    <div>
      <div>{product.name}</div>
      <div>
        <button type="button" onClick={() => onAddClick(product)}>
          Add
        </button>
      </div>
    </div>
  );
}
```

Sometimes you'll want to use partial application of a callback. For example, a list component might
have a callback that needs to know the specific item it was performed on:

```js
<button onClick={onAddClick.bind(null, product)} />
```

So communication from parent to child happens via plain data objects through the props property.
Communication from child to parent happens through callbacks.

# Presentation Components

Presentation components transform data into visual representation, communicating only through their
own props. Every component so far has been a presentation component. They're best implemented
via pure functions. They take two kinds of inputs: plain data objects and function callbacks. The
plain objects are transformed into the UI. The callback functions are event handlers.

# Styling Components

Create React App uses Webpack for assets. One CSS file per component is a good standard to use.
You can import the CSS file via:

```js
import "./ProductItem.css";
```

Then you can set the `className` attribute on HTML tags to style your components.

# Stateful Functional Components

State is data that is stored and can be changed. React Hooks enables functional components that
store state. It's a collection of hook functions that all start with the word `use`. The first
function we'll use is `useState()`.

```js
import React, { useState } from "react";

function App({ products }) {
  const [shoppingMap, setShoppingMap] = useState({}); // returns var holding state and setter fn

  function addToCart(product) {
    setShoppingMap(map => addProductToMap(product, map));
  }

  function removeFromCart(product) {
    setShoppingMap(map => removeProductFromMap(product, map));
  }

  return (
    <div>
      <div className="content">
        <ProductList
          products={products}
          onAddClick={addToCart} />
        <ShoppingCart
          cart={toCartView(shoppingMap)}
          onRemoveClick={removeFromCart} />
      </div>
    </div>
  );
}
```

`setShoppingMap()` can take a mapping function as an argument. The mapping function takes the
previous state value and returns the new state value.

```js
function addProductToMap(product, map) {
  const newMap = { ...map };
  const quantity = getProductQuantity(product, map) + 1;
  newMap[product.id] = { ... product, quantity };
  return Object.freeze(newMap);
}

function removeProductFromMap(product, map) [
  const newMap = { ...map };
  delete newMap[product.id];
  return Object.freeze(newMap);
]
```

These pure functions can be extracted out from the component and exported to be test.

# Custom Store

A store is an object whose main purpose is to store and manage data. The store emits events every
time its state changes. Components can subscribe to these events to update the UI. For this chapter,
we'll use the micro event emitter library: `npm install micro-emitter --save`.

```js
const eventEmitter = new MicroEmitter();
const CHANGE_EVENT = "change";

eventEmitter.emit(CHANGE_EVENT);
eventEmitter.on(CHANGE_EVENT, handler);
```

The implementation for a `ShoppingCartStore` would look like:

```js
import MicroEmitter from "micro-emitter";

function ShoppingCartStore() {
  const eventEmitter = new MicroEmitter();
  const CHANGE_EVENT = "change";

  let shoppingMap = {};

  function addToCart(product) {
    shoppingMap = addProductToMap(product, shoppingMap);
    eventEmitter.emit(CHANGE_EVENT);
  }

  function removeFromCart(product) {
    shoppingMap = removeProductFromMap(product, shoppingMap);
    eventEmitter.emit(CHANGE_EVENT);
  }

  function onChange(handler) {
    eventEmitter.on(CHANGE_EVENT, handler);
  }

  function offChange() {
    eventEmitter.off(CHANGE_EVENT);
  }

  function get() {
    return toCartView(shoppingMap);
  }

  return Object.freeze({
    addToCart,
    removeFromCart,
    get,
    onChange,
    offChange
  });
}

export default ShoppingCartStore;
```

`ShoppingCartStore` is a factory function. It can be used like:

```js
// in index.js
import ShoppingCartStore from "./ShoppingCartStore";

const shoppingCartStore = ShoppingCartStore();

ReactDOM.render(<App products={products} shoppingCartStore={shoppingCartStore} />,
  document.getElementById("root"));

// in component
const [cart, setCart] = useState({list: []});
useEffect(subscribeToStore, []);

function subscribeToStore() {
  shoppingCartStore.onChange(reload);
  return function cleanup() {
    shoppingCartStore.offChange();
  };
}

function reload() {
  const cart = shoppingCartStore.get();
  setCart(cart);
}
```

The `useEffect` hook performs side effects in function components. Any communication with the outside
environment is a side effect. The effect is a function that runs after DOM updates. It has access
to the state variables. It runs after every render. There's a second optional argument, which can
be used to skip running an effect if values haven't changed between re-renders. If you want to run
the effect only once, pass an empty array as a second argument.

If the effect function returns another function, that function is run at clean up time. If the effect
runs more than once, React cleans up the effects from previous renders before running the next effect.

# Redux Store

Redux does state management following functional principles:

```sh
npm install redux --save
npm install react-redux --save
```

Redux has a single store that manages all state in an application state tree. The store has a
few methods:

* `getState()` for reading all state as a read-only immutable value
* `dispatch()` for dispatching actions
* `subscribe()` for listening to state changes

You can only change state by dispatching actions. There are no setters. Actions are plain immutable
objects, for example:

```js
{
  type: "ADD_TO_CART", // type property required, usually a string
  product
}
```

Common practice is to encapsulate the code to create actions as a pure function, an action creator:

```js
function addToCart(product) {
  return {
    type: "ADD_TO_CART",
    product
  };
}
function removeFromCart(product) {
  return {
    type: "REMOVE_FROM_CART",
    product
  };
}
export default { addToCart, removeFromCart };
```

Reducers are pure functions that manage the state. They take the previous state and an action, and
return the new state. The store applies reducers whenever an action is dispatched:

```js
export default function (shoppingMap  {}, action) {
  switch (action.type) {
    case "ADD_TO_CART":
      return addToCart(shoppingMap, action.product);
    case "REMOVE_FROM_CART":
      return removeFromCart(shoppingMap, action.product);
    default:
      return shoppingMap;
  }
}
```

Redux requires one root reducer. Reducers can be combined with `combineReducers()`.

```js
export default combineReducers({
  shoppingCart
});
```

Selectors are pure functions that take the state as input, return a part of that state or compute
derived values from it. `toCartView()` selector extracts from the state and returns a list of all
products and the total price:

```js
function toCartView({shoppingCart}) {
  const list = Object.values(shoppingCart);
  return Object.freeze({
    list,
    total: list.reduce(addPrice, 0)
  });
}

function addPrice(totalPrice, line) {
  return totalPrice + line.price * line.quantity;
}

export { toCartView };
```

The entry point to our app using Redux will look like:

```js
import React from "react";
import ReactDOM from "react-dom";
import { createStore } from "redux";
import { Provider } from "react-redux";
import rootReducer from "./reducers";
import App from "./App";

const products = [ ... ];
const store = createStore(rootReducer);

ReactDOM.render(
  <Provider store={store}>
    <App products={products} />
  </Provider>,
  document.getElementById("root")
);
```

Components using state will need to use the `connect()` utility from `react-redux`. It connects a
component to the Redux store. The component takes state from the store, processes it with a selector,
then sends the result to the presentation component as props. `mapStateToProps()` does that. It also
defines actions to be dispatched when events are triggered via `mapDispatchToProps()`.

```js
import { connect } from "react-redux";
import { addToCart } from "../actions/ShoppingCartActions";
import ProductList from "../ProductList";

function mapDispatchToProps(dispatch) {
  return {
    onAddClick: function(product) {
      dispatch(addToCart(product));
    }
  };
}

export default connect(
  null,
  mapDispatchToProps
)(ProductList);
```

The data flow looks like this:

* read from store: store -> container component -> selector -> presentation component
* writes to store: presentation component -> container component -> action creator -> reducers

# Redux Actions and Immutable.Js

This chapter will do a bit of refactoring and introducing immutable data structures. First, we'll
refactor the action creators using Redux Actions:

```js
import { createAction } from "redux-actions";

const addToCart = createAction("ADD_TO_CART");
const removeFromCart = createAction("REMOVE_FROM_CART");

export default { addToCart, removeFromCart }
```

The `handleActions` utility helps create a new function without a giant switch/case:

```js
import { handleActions } from "redux-actions";
import actions from "../actions/ShoppingCartActions";

export default handleActions({
    [actions.addToCart]: addToCart,
    [actions.removeFromCart]: removeFromCart
  },
  {}
);
```

Immutable.js provides immutable data structures like `Map` and `List`. `Map()` creates an immutable
map, `set(key, value)` can be used to set new key/value pairs and `remove(key)` can be used to remove
pairs.

```js
function addToCart(map, action) {
  const product = action.payload;
  const quantity = getProductQuantity(map, product) + 1;
  const newProduct = { ...product, quantity };
  return map.set(product.id, newProduct);
}

function removeFromCart(map, action) {
  const product = action.payload;
  return map.remove(product.id);
}

export default handleActions({
    [actions.addToCart]: addToCart,
    [actions.removeFromCart]: removeFromCart
  },
  Map()
});
```

# Fetching with Redux Thunk

Redux Thunk middleware lets you write action creators that return a function instead of a plain
action object. Middlware lets you write async logic that interacts with a store, which is ideal
for working with network calls. A common pattern is to make a network request to fetch all products
from the REST API and display them. We need an action for resetting all products with new ones:

```js
// in productActions.js
import { createAction } from "redux-actions";

const resetProducts = createAction("resetProducts");

export default { resetProducts };

// in another js file
import api from "../api/productsAPI";
import actions from "../actions/productActions";

function fetchProducts() {
  return function(dispatch) {
    return api.fetchProducts()
      .then(actions.resetProducts)
      .then(dispatch);
  };
}

// in reducers.js
import { handleActions } from "redux-actions";
import { List } from "immutable";
import actions from "../actions/productsActions";

function resetProducts(products, action) {
  return List(action.payload);
}

export default handleActions({
    [actions.resetProducts]: resetProducts
  },
  List()
});
```

Redux Observable is another middleware option to orchestrate asynchronous actions in Redux.

# Form Components

Form components are made up of input form elements inside a form element.

```js
import React, { useState } from "react";

function ProductSearch({ onSearch }) {
  const [text, setText] = useState("");

  function submitSearch(e) {
    e.preventDefault();
    onSearch({ text });
  }

  return (
    <form className="search-form" onSubmit={submitSearch}>
      <input value={text} onChange={e => setText(e.target.value)} placeholder="Product" type="text" name="text" />
      <button type="search" className="search-button">Search</button>
    </form>
  );
}

export default ProductSearch;
```

The above is an example of controlled inputs, which have a bidirectional relationship with the
associated state.

**Custom hooks** let you extract out component logic into reusable functions.

```js
import { useState } from "react";

function useInputState(initialValue) {
  const [value, setValue] = useState(initialValue);

  function setValueFromEvent(e) {
    setValue(e.target.value);
  }

  return [value, setValueFromEvent];
}

export { useInputState };
```

`useInputState` is a generic hook that can be used for form objects:

```js
import { useInputState } from "./hooks";

function ProductSearch({ onSearch }) {
  const [text, setText] = useInputState("");
  // ...
  <input
    value={text}
    onChange={setText}
    placeholder="Product"
    type="text"
    name="text"
  />
  // ...
}
```

# UI State in Redux Store

Let's implement the search functionality in Redux.

You'll need an action creator:

```js
import { createAction } from "redux-actions";

const setQuery = createAction("setQuery");

export default { setQuery };
```

And a reducer:

```js
import { handleActions } from "redux-actions";
import actions from "../actions/queryActions";

function setQuery(state, action) {
  return action.payload;
}

export default handleActions({
    [actions.setQuery]: setQuery
  },
  { text: "" }
);
```

The ProductSearch component doesn't change. But we need to connect it to the store, by creating
a new container component:

```js
import { connect } from "react-redux";
import actions from "../actions/queryActions";
import ProductSearch from "../ProductSearch";

function mapStateToProps(state) {
  return {
    query: state.search
  };
}

function mapDispatchToProps(dispatch) {
  return {
    onSearch: function(query) {
      dispatch(actions.setQuery(query));
    }
  };
}

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(ProductSearch);
```

We'll need selectors to extract products and the current search query:

```js
function filterProducts({products, query}) {
  return products.filter(isInQuery(query));
}

function isInQuery(query) {
  return function(product) {
    return product.name.includes(query.text);
  };
}

export default { filterProducts };
```
