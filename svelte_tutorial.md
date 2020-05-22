# Svelte Tutorial

Notes from the [Svelte Tutorial](https://svelte.dev/tutorial/basics).

# Introduction

Svelte is a tool for making interactive web apps, similar to other frontend frameworks. Svelte
compiles your JavaScript at build time, instead of including a large runtime library.

Components are stored in `.svelte` files and contain HTML/CSS/JavaScript.

Example:

```html
<!-- in App.svelte file -->
<style>
  h1 {
    color: purple; /* styles are local to component */
  }
</style>

<script>
  let name = "world";
  let html = "<b>test</b>";
</script>

<h1>Hello {name.toUpperCase()}!</h1>
<p>{@html html}</p>
```

Svelte recommends using Rollup to build your app, via
[rollup-plugin-svelte](https://github.com/sveltejs/rollup-plugin-svelte).

To actually instantiate the app:

```js
import App from "./App.svelte";

const app = new App({
  target: document.body,
  props: {
    answer: 42
  }
});
```

# Reactivity

When a component's variable changes, Svelte automatically updates the UI. **It must change via
the `=` assignment operator**, note that `+=` or `-=` works too.

```html
<script>
  let count = 0;
  function handleClick() {
    count += 1;
  }
</script>

<button on:click={handleClick}>{count}</button>
```

If the left-hand side of an assignment depends on another variable (and you want it to be reactive),
you'll have to use a reactive declaration:

```js
let count = 0;
$: doubled = count * 2; // now you can use `doubled` in the template
$: console.log(`the count is ${count}`); // works with fn calls too, this one is dependent on count
$: { // works with blocks or if blocks too!
  console.log(`the count is ${count}`);
  alert(`I SAID THE COUNT IS ${count}`);
}
```

Reactivity only works with assignments to variables. So mutating changes to objects don't work,
such as `push` or `pop` on an Array. Assignments to properties also work. A simple rule: the
name of the updated variable must appear on the left hand side of the assignment.

```js
const foo = obj.foo;
foo.bar = 'baz';
```

The above won't update references to `obj.foo.bar`, it needs an `obj = obj` somewhere.

# Props

Component props are prefixed with `export`:

```html
<script> // in Nested.svelte
  export let answer = 'a mystery'; // default assignment works too
</script>

<!-- in App.svelte -->
<Nested answer={42} />
<Nested /> <!-- defaults to "a mystery" -->
<Nested {...obj} /> <!-- spreads an object as props, in a component all props can be accessed by $$props -->
```

# Logic

Svelte templates can express logic:

```html
{#if user.loggedIn}
  ...
{/if}

{#if !user.loggedIn}
  ...
{/if}

{#if x > 10}
  ...
{:else if x > 5}
  ...
{:else}
  ...
{/if}

{#each cats as cat}
  ...
{/each}

{#each cats as cat, index}
  ...
{/each}
```

`#` is used on opening tags, `/` on closing tags, and `:` are continuation tags.

It's a good idea to add ids to looped elements, so Svelte knows which element to add/remove on updates:

```html
{#each things as thing (thing.id)}
  <Thing current={thing.color} />
{/each}
```

Use a string or number for the id, as it's generally safer, although Svelte can handle any object.

To work with promises, use `await`:

```html
{#await promise}
  <p>...waiting</p>
{:then number}
  <p>The number is {number}</p>
{:catch error}
  <p>{error.message}</p>
{/await}

{#await promise then value} <!-- no need to handle loading/errors -->
  <p>The value is {value}</p>
{/await}
```

# Events

Events use the `on:` directive.

```html
<div on:mousemove={onMouseMove}></div>
<div on:mousemove={e => m = { x: e.clientX, y: e.clientY } }></div> <!-- compiler optimizes this for you -->
```

Svelte has modifiers for common actions too, so you can do something like:

```html
<button on:click|preventDefault>Click Me</button>
```

Modifiers are: `preventDefault`, `stopPropagation`, `passive` (improves performance on touch/wheel events),
`capture` (fire handler on capture instead of bubble), `once` (removes trigger after running once),
`self` (only trigger if event.target is itself). Modifiers can be combined: `on:click|once|capture={...}`.

Components can dispatch custom events:

```html
<script> // in Inner.svelte
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  function sayHello() {
    dispatch("message", {
      text: "Hello!"
    });
  }
</script>

<!-- Inner.svelte -->
<button on:click={sayHello}>Hello</button>

<!-- App.svelte -->
<Inner on:message={e => alert(e.detail.text)} />
```

Custom events don't bubble, they'll need to be forwarded manually. As a shortcut, you can just
specify `on:message` without a handler on a component and its default will be to forward it to
parent components. This works with al events, including DOM ones like `on:click` too.

# Bindings

Binding is done with the `bind:` directive:

```html
<input bind:value={name}>
<input type=number bind:value={a} min=0 max=10> <!-- Svelte automatically coerces to number for you -->
<input type=ranger bind:value={a} min=0 max=10>
<input type=checkbox bind:checked={yes}> <!-- Svelte coerces to boolean for you -->
<input type=radio bind:group={scoops} value={1}> <!-- Radio groups are handled for you too -->
<textarea bind:value={value}></textarea>
<textarea bind:value></textarea> <!-- shorthand, exactly the same as above -->
<select bind:value={selected}>
  <option value={question}>{question.text}</option> <!-- questions = [{id: ..., text: ...}] -->
</select>
<div contenteditable="true" bind:innerHTML={html}></div> <!-- textContent works too -->
```

Block level elements have `clientWidth`, `clientHeight`, `offsetWidth`, and `offsetHeight` properties
you can bind too also.

`this` is a special binding so your JavaScript can have access to the element:

```html
<script>
  import { onMount } from "svelte";
  let canvas;
  onMount(() => {
    // access to canvas here
  });
</script>

<canvas bind:this={canvas}></canvas>
```

Component props may also be binded. If the value changes in one component, it will be changed in
both parent and child components.

# Lifecycle

`onMount` - runs after component is first rendered in DOM. If function is returned here, that function
is called when component is destroyed.

`onDestroy` - runs when component is destroyed.

`beforeUpdate` - runs immediately before DOM is updated.

`afterUpdate` - runs immediately after DOM is updated.

`tick` - returns a promise that resolves as soon as any pending state changes have been applied to
the DOM (or immediately if no pending state changes).

# Stores

Stores should be used when application state exists outside of components.

```js
// in stores.js
import { writable } from "svelte/store";

export const count = writable(0); // initialize store with 0
```

```html
<!-- in Buttons.svelte -->
<script>
  import { count } from "./stores";

  function increment() {
    count.update(n => n + 1);
  }
  function decrement() {
    count.update(n => n - 1);
  }
  function reset() {
    count.set(0);
  }
</script>

<button on:click={increment}>+</button>
<button on:click={decrement}>-</button>
<button on:click={reset}>Reset</button>
```

```html
<!-- in App.svelte -->
<script>
  import { onDestroy } from "svelte";
  import { count } from "./stores";
  import Buttons from "./Buttons.svelte";

  let count_value;

  const unsubscribe = count.subscribe(value => {
    count_value = value;
  });
  onDestroy(unsubscribe);
</script>

<h1>The count is {count_value}</h1>
<Buttons />
```

The call to `subscribe` and `onDestroy(unsubscribe)` is such a common pattern that Svelte has a
compilation technique for it, prefixing the store with `$`:

```html
<script>
  import { count } from "./stores";
  import Buttons from "./Buttons.svelte";
</script>

<h1>The count is {$count}</h1> <!-- can also be used in script tag -->
<Buttons />
```

Svelte has readable stores also, which is readonly and works by setting an initial value. It also
has hooks to set new values on subscribe and unsubscribe.

Derived stores can be used to get a value based on one or more stores:

```js
export const elapsed = derived(
  time,
  $time => Math.round(($time - start) / 1000)
);
```

Stores are anything that implements the `subscribe` method, so you can implement your own:

```js
function createCount() {
  const { subscribe, set, update } = writable(0);
  return {
    subscribe,
    increment: () => update(n => n + 1),
    decrement: () => update(n => n - 1),
    reset: () => set(0)
  };
}
```

Writable stores can be bound. The `set` method will be called on change:

```html
<input bind:value={$name}>
```

# Classes

HTML classes are another attribute on an element. Svelte provides shortcuts for common use cases:

```html
<button class="{current === "foo" ? "active" : ""}">Click Me</button>
<button class:active={current === "foo"}>Click Me</button> <!-- same as above -->

<div class:big={big}></div> <!-- add "big" class if big var is set to true -->
<div class:big></div> <!-- same as above -->
```

# Component Composition

The `<slot>` element is used for children of components. For example:

```html
<!-- in Box.svelte -->
<div class="box"><slot>Default Children</slot></div>

<!-- in App.svelte -->
<Box><p>Inside the box</p></Box>
```

Slots can be named. The default one is named "default":

```html
<!-- in Box.svelte -->
<div class="box1">
  <slot name="one">First Slot Default</slot>
</div>
<div class="box2">
  <slot name="two">Second Slot Default</slot>
</div>

<!-- in App.svelte -->
<Box>
  <span slot="one">Inside Box One</span>
  <span slot="two">Inside Box Two</span>
</Box>
```

Slots can have props. It's useful to pass data from a component back to its parent.

```html
<!-- in Box.svelte -->
<div class="box">
  <slot hovering={hovering}></slot> <!-- some js to set hovering var above ... -->
</div>

<!-- in App.svelte -->
<Box let:hovering={hovering}>
  {#if hovering}
    ...
  {:else}
    ...
  {/if}
</Box>
```

# Context API

Context is used to pass reactive data between components and component hierarchies. It's useful
when you have shared data between a lot of components and don't want to pass via props. While stores
should be used for non-components, context should be used specifically for components.

```js
import { setContext } from "svelte";

setContext(key, {
  getMap: () => map
});
```

```js
import { getContext } from "svelte";

const context = getContext(key);
context.getMap();
```

# Special Elements

`<svelte:self>` - refers to the current component, useful for recursive components. Use the `this`
prop to change its constructor/type.

`<svelte:window>` - is the window object.

`<svelte:body>` - is the body object.

`<svelte:head>` - can be used to add items inside the `<head>` of your document.

`<svelte:options>` - can be used to specify compiler options.

# Module Context

All `<script>` tags in components are locally scoped. Sometimes you'll need to have shared scope:

```html
<script context="module">
  let current;
</script>
```

When multiple components are in the same context, they all share the `current` variable.

# Debugging

Use `{@debug var1, var2, var3, ...}` to trigger the debugger every time the values change.
