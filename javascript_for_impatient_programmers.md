# JavaScript for Impatient Programmers

Notes on [JavaScript for Impatient Programmers](https://exploringjs.com/impatient-js/)
by Dr. Axel Rauschmayer.

# First Steps

## Big Picture

Book covers JavaScript in web browser and Node. For references, use:

* MDN web docs: <https://developer.mozilla.org/en-US/>
* Node.js docs: <https://nodejs.org/en/docs/>
* ExploringJS.com (author's site)

## Syntax

```js
// single line comment
/*
Multi-line comment
*/

// booleans
true
false

// Numbers (single type for numbers)
-123
1.141

// Strings
'abc'
"abc"

// assertions and console calls
assert.equal(7 + 1, 8);
console.log("Hello, World!");

// Operators
true && false // and
true || false // or
1 + 2 - 3 * 4 / 5 // math
"a" + "b" // "ab" string concat

// Comparison
3 < 4
3 <= 4
"abc" === "abc"
"abc" !== "def"

// Declaring Variables
let x; // declare mutable
x = 3 * 5; // assignment
let y = 3 * 5; // declare and assign
const z = 8; // declare immutable

// Conditional
if (x < 0) {
  x = -x;
}

// Functions
function add1(a, b) {
  return a + b;
}
add1(5, 2);

const add2 = (a, b) => { return a + b }; // body is code block
add2(5, 2);

const add3 = (a, b) => a + b; // body is an expression
add3(5, 2);

// Object Literal
const obj = {
  first: "Jane", // property
  last: "Doe",
  getFullName() { // property method
    return this.first + " " + this.last;
  }
};
obj.first; // "Jane"
obj.getFullName(); // "Jane Doe"

// Arrays
const arr = ["a", "b", "c"];
arr[1]; // "b"
arr[1] = "β"; // setting

// Modules, each module is in its own file
// in file-tools.mjs:
export function isTextFilePath(filePath) {
  return filePath.endsWith(".txt");
}
// in main.mjs, importing:
import * as path from "path"; // import whole module as namespace object `path`
import {isTextFilePath} from "./file-tools.mjs"; // import single export
```

Legal characters for property names and variables are:

* unicode A-Z, a-z, etc...
* $
* _
* digits (0-9) (vars can't start with digit)

Casing styles:

* camel case: threeConcatenatedWords
* underscore case or snake case: `three_concatenated_words`
* dash case or kebab case: `three-concatenated-words`

In general, use camel case except for constants. Start with lowercase letter for functions,
variables, methods, and CSS. Use uppercase for classes, constants.

Variables/parameters that start with underscore usually aren't used. Properties that start with
underscore tend to be private.

JavaScript has two modes: sloppy and strict. Sloppy is default in scripts. Strict is default in
modules and classes. Putting `'use strict'` as the first line of a script will switch modes. This
can also be done as the first line of a function.

## Consoles

Browsers have developer consoles for a REPL. Use `console.log(string)` to print out to it.

Node has a REPL you can start with `node`. Use the same function to print to it.

The `console.*` API:

* `console.log(...values: any[]): void` prints to stdout
* `console.log(pattern: string, ...values: any[]): void`
* `console.error(... same as above ...)` prints to stdout

Patterns can have placeholders, like:

```js
console.log("Test: %s %j", 123, "abc");
console.log("%s %s", "abc", 123);
```

* `%s` for strings
* `%o` for object
* `%j` for JSON
* `%%` for a single "%"
* use `JSON.stringify()` for printing nested objects

## Assert

In Node:

```js
import {strict as assert} from "assert";
assert.equal(3 + 5, 8);
```

Quick reference:

```js
// normal equality
assert.equal(3+3, 6);
assert.notEqual(3+3, 22);

// deep equality
assert.deepEqual([1,2,3], [1,2,3]);
assert.notDeepEqual([1,2,3], [1,2]);

// exceptions
assert.throws(() => { null.prop; });
assert.throws(() => { null.prop; }, TypeError);
assert.throws(() => { null.prop; }, { name: "TypeError", message: "..." });
try {
  functionThatThrows();
  assert.fail();
} catch (_) {
  // Success
}
```

# Variables and Values

## Variables and Assignments

`let` is used for mutable variables, `const` for immutable ones. This only refers to binding, so
the underlying object itself may be mutated.

`const` can be used with `for-of` loops, a new binding is created for each iteration:

```js
const arr = ["hello", "world"];
for (const elem of arr) {
  console.log(elem);
}
```

Plain loops must use `let`:

```js
for (let i=0; i<arr.length; i++) {
  console.log(arr[i]);
}
```

Variables are block-scoped. You can't declare the same variable twice at the same level. If a new
variable is declared with the same name, in an inner scope, it's said to `shadow` the outer one.

`<script>` tags do not create new scopes. A declared variable enters the global scope at the top
most level. `globalThis` is a new feature that lets you access the global object. Older ways to
access the global object include: `window` (doesn't work in Node or Web Workers), `self` (doesn't
work in Node), or `global` (only works in Node).

It's recommended to leave off the global scope whenever possible, eg don't prefix with `window.`.

Variables are scoped and activated. Scope means when an entity can be seen. Activated means when
an entity can be accessed. `const`, `let`, `function`, `class` are all block scoped. `import` is
module scoped. `var` is function scoped. `const`, `let`, `clas` are declaration activated. `function`
and `var` are activated at start (eg variable hoisting).

Within a scope, there are bound and free variables. Bound variables are declared within it, parameters
or local. Free are declared externally, also known as non-local variables.

A **closure** is a function plus a connection to the variables that exist at its birth place.

```js
function funcFactory(value) {
  return () => {
    return value;
  };
}

const func = funcFactory("abc");
assert.equal(func(), "abc");
```

## Values

JavaScript's type hierarchy:

```
(any)
  | (primitive)
     | undefined
     | null
     | boolean
     | number
     | string
     | symbol
  | (object)
     | Object
        | Array
        | Map
        | Set
        | Function
        | RegExp
        | Date
```

The specification only has seven types:

1. undefined, where undefined is the only element
2. null, where null is the only element
3. boolean: true, false
4. number: -123, 3.14, etc...
5. string: "abc", etc...
6. symbol: Symbol("My Symbol"), etc...
7. object: all objects, but different from `Object` which is the type of all instances of class `Object`

Primitives are immutable, passed by value, and compared by value.

Objects are mutable (by default), passed by reference, and compared by reference.

`typeof` returns one of the seven types. `instanceof` tests if a value is of a given class.

ES6 introduced classes, before there were only constructor functions:

## Operators

Operators coerce operands to appropriate values and mostly only work with primitive values.

```js
"7" * "3" // 21
const obj = { "true": 123 };
obj[true] // 123
[1,2,3] + [4,5,6] // "1,2,34,5,6"
```

`+` coerces operands to String (if any is a String) -- otherwise converts them to Numbers.

`=` initializes variable declarations or handles assignments. It can be used in a compound nature:
`+=`.

`==`, `!=` is for loose equality. It coerces operands. So `[1,2,3] == "1,2,3"` is true. It also
considers undefined/null to be equal.

`===`, `!==` is for strict equality. It never coerces. It does not consider undefined/null to be
equal. Recommendation is to always use strict, except for if you consider undefined/null equal OR
want the coercion.

Comparison operators: `<, <=, >, >=`.

Comma operator: `,` - evaluates both expressions and returns the last one.

# Primitive Values

## undefined and null

`undefined` means not initialized or not existing (property on object).

`null` means intentional absence of any object value. JSON doesn't support undefined, only null.

To check for it:

```js
if (x === null)
if (x === undefined)
if (x !== undefined && x !== nulll)
if (x) // truthy
if (!x) // falsy
```

For both, accessing properties will result in an TypeError exception.

If a function is called without an argument, the parameter value will be undefined.

## Booleans

`boolean` type has two values: `true` and `false`. You can convert with `Boolean(value)` or `!!value`.

| x             | Boolean(x)                        |
| ------------- | --------------------------------- |
| undefined     | false                             |
| null          | false                             |
| boolean value | no change                         |
| number value  | 0/NaN -> false, otherwise -> true |
| string value  | "" -> false, otherwise -> true    |
| object value  | always true                       |

`if(x)` does a coercion on `x`, so truthy values will pass. It's equivalent to
`if (Boolean(value) === true)`.

Truthiness checks are imprecise. `if (obj.prop)` is skipped if no property exists, but also if
prop has a falsy value like 0 or "".

To check if a property exists, use the `in` operator: `if ("path" in fileDesc)`.

The conditional operator: `<condition> ? <thenExpression> : <elseExpression>`. Also called the
ternary operator because it has three operands.

Logical and/or are: `&&` and `||`. `!` is unary for not.

## Numbers

JavaScript only has floating point numbers. `98 === 98.0` is true.

Number literals can be expressed like:

```js
0b11 // base 2, 3
0o10 // base 8, 8
35 // base 10, 35
0xE7 // base 16, 231

35.0 // 35
3e2 // 3x10^2
```

Accessing properties after an integer literal can be done by wrapping it in parenthesis (otherwise
it's interpreted as a decimal dot). Eg `(7).toString()`.

Math operators: `+`, `-`, `*`, `/`, `%`, `**` (exponent), `++`, `--`.

To coerce to a number, use `Number(value)` or `+value`. Avoid `parseFloat(value)`.

| x         | Number(x)                        |
| --------- | -------------------------------- |
| undefined | NaN                              |
| null      | 0                                |
| boolean   | false -> 0, true -> 1            |
| number    | no change                        |
| string    | "" -> 0, otherwise parsed number |
| object    | configurable via `.valueOf()`    |

`NaN` stands for not a number, but is of type number. `Infinity` is another number.

## Strings

String literals are delimited by single/double quotes.

Special characters in strings: `\n` Unix line break, `\r\n` Windows line break, `\t` tab, `\\`
backslash.

Characters can be accessed via `[]` like `str[1]`. Conversion can be done via `String(value)` or
`""+value` or `value.toString()`. That last one doesn't work for undefined/null. An Object can
define the `toString()` method to customize its string value.

## Templates

Template literals use backticks:

```js
`Count: ${num}!`
```

Tagged templates are template literals that follow a function. It leads to the function being
called, with arguments derived from the contents of the template literal:

```js
const getArgs = (...args) => args;
assert.deepEqual(
  getArgs`Count: ${5}!`,
  [["Count: ", "!"], 5]
);
```

String interpolation is done via `${}`. Tagged templates can also receive a raw version (as opposed
to the cooked version above):

```js
function cookedRaw(templateStrings, ...subs) {
  return {
    cooked: [...templateStrings],
    raw: templateStrings.raw,
    subs
  };
}
```

Use `String.raw` for raw literal strings where backslashes don't do anything. Backticks can be used
for multi-line strings. Use the `dedent` function from standard library to dedent lines.

## Symbols

Symbols are created via `Symbol()`. They're used for values for constants or unique property keys.

```js
const COLOR_BLUE = "Blue";
const MOOD_BLUE = "Blue";
assert.equal(COLOR_BLUE, MOOD_BLUE); // not what we want

const COLOR_BLUE = Symbol("Blue");
const MOOD_BLUE = Symbol("Blue");
assert.notEqual(COLOR_BLUE, MOOD_BLUE);
```

Meta-level keys can be used so they don't interfere with base-level properties:

```js
const specialMethod = Symbol("specialMethod");
const obj = {
  _id: "kf12oi",
  [specialMethod]() {
    return this._id;
  }
};
```

# Control Flow and Data Flow

## Statements

`if` checks if a value is truthy and executes a block if it is. Can be chained with `else` and
`else if`.

`switch` has a body with `case` clauses and an optional `default` clause. Don't forget the return
or break, because case clauses will continue further until explicitly stopped. This works well
with empty case clauses.

`break` and `continue` can be used to stop a loop or skip to the next iteration. Breaks can be
labeled:

```js
foo: { // label
  if (condition) break foo; // back to label
}
```

`while`, `do while`, `for` iterate just like other imperative languages.

`for of` iterates over an iterable (something that supports the iteration protocol). You can also
use [index, element] entries for Arrays: `for (const [index, elem] of arr.entries())`.

`for await of` works with async iterables: `for await (const item of asyncIterable)`.

`for in` should be avoided. It's used to access object keys, but it's almost always better to just
use `for (const key of Object.keys(obj))`.

## Exception Handling

`throw` works like `throw new Error("Problem!)`. You can subclass and create your own errors:

```js
class MyError extends Error {
}
function func() {
  throw new MyError("Problem!");
}
```

Exception handling works like:

```js
try {
  // try statements
} catch (error) {
  // catch statements
} finally {
  // finally statements
}
```

## Callable Values

Ordinary function can be: function, method, constructor.

There are specialized functions: arrow function can only be a function, method can only be a method,
class can only be a constructor function.

A real function is invoked via a function call, a method is stored on a property, and constructor
is invoked via `new`.

Ordinary functions:

```js
function ordinary(a, b, c) {
  // ...
}
const ordinary = function(a, b, c) {
  // ...
};
const ordinary = function name(a, b, c) {
  // name accessible here ...
};
```

Specialized functions:

```js
const arrow = () => { return 123 };
const obj = { method() { return "abc" } };
class MyClass { }
const instance = new MyClass();
```

Whereas ordinary functions can be anything, the other three are specialized. Arrow functions `this`
is lexical, which is defined by its surroundings. A method's `this` is undefined when used as
a regular function call. Recommendation is to prefer specialized functions when possible. If you
don't use `this`, ordinary and arrow functions are mostly the same.

Arrow functions were introduced for: being concise, being able to access the `this` outside of its
scope. When an ordinary function is defined, `this` will always be the instance of the object it
belongs to. For arrow functions, `this` has the previous value before the arrow function's
definition.

Arrow functions:

```js
const f = function (x, y, z) { return 123 };
const f = (x, y, z) => { return 123 };
const f = (x, y, z) => 123;
const id = x => x; // single parameter can omit parenthesis
[1,2,3].map(x => x+1);

const func1 = () => ({a: 1}); // returning object literal requires parenthesis though, otherwise
                              // it's interpreted as a code block with break label "a"
```

JavaScript doesn't complain if a function receives too few or too many arguments. Extra arguments
are ignored. Missing parameters are `undefined`.

You can set defaults: `function f(x, y=0)`.

You can set variable number of arguments: `function f(x, ...y)` where y becomes an array. Using `...`
in a function call spreads an array into multiple arguments.

JavaScript doesn't have named parameters, but can be simulated via object literals.

# Modularity

## Modules

Exporting:

```js
// Named exports
export function f() {}
export const one = 1;
export {foo, b as bar};

// Default exports
export default function f() {}
export default 123;

// Re-exporting
export * from "./some-module.mjs";
export {foo, b as bar} from "./some-module.mjs";
```

Importing:

```js
// Named imports
import {foo, bar as b} from "./some-modules.mjs";
// Namespace import
import * as someModule from "./some-module.mjs";
// Default import
import someModule from "./some-module.mjs";

// Combinations:
import someModule, * as someModule from "./some-module.mjs";
import someModule, {foo, bar as b} from "./some-module.mjs";

// Empty import (For modules with side effects)
import "./some-module.mjs";
```

* scripts are code fragments that browsers run in global scope
* CommonJS modules are a format mainly used on servers
* AMD modules are mainly used in browsers
* ECMAScript modules are JavaScript's built-in module formats, use this when possible

Each module can have exactly one default export. A module can have both named and default exports,
but the author recommends sticking to one style per module.

While you can prefix `export default` to function and class definitions, you cannot prefix it to
const. This is because a const can have multiple values: `const foo = 1, bar = 2, baz = 3`.

`npm` is the package manager for Node. It installs packages to `node_modules/` directory. Bundling
tools let you use it on the frontend.

## Single Objects

Objects have a set of properties. They're used as records and dictionaries in JavaScript. Maps are
usually better for dictionaries.

```js
const jane = {
  first: "Jane",
  last: "Doe"
};
const first = "Jane", last = "Doe";
const jane = { first, last };

jane.first; // "Jane"
jane.last = "Smith";
jane.last; // "Smith"

const jane = {
  // ...
  says(text) {
    return `${this.first} says "${text}"`;
  },
  get full() {
    return `${this.first} ${this.last}`;
  },
  set full(fullName) {
    const parts = fullName.split(" ");
    this.first = parts[0];
    this.last = parts[1];
  }
};
jane.says("Hello");
jane.fullName; // Jane Doe
jane.fullName = "Jane Smith";
```

Object literal features also include spreading and destructuring:

```js
const obj = {foo: 1, bar: 2};
{...obj, baz: 3}; // {foo:1, bar:2, baz:3}
```

It's common to use spreading to shallow copy objects: `const copy = {...original};`.

It's common to use spreading for default values: `const data = {...DEFAULTS, ...providedData}`.
The second spread overrides the first. It can also be used as a non-destructive merge.

You can use `.call()` to specify a custom this value: `method.call(thisValue, arg1, arg2)`.

`bind` lets you bind a this value and arguments.

Computed property keys can be set via `[]`: `const obj = {["Hello World!"]: true};`

There are pitfalls. `in` also gets inherited properties, like `__proto__`. Whenever you can, use
Maps instead. If you can't, use an object without prototype.

## Prototype Chains and Classes

JavaScript objects have all and own properties. All properties include inherited ones.

```js
const proto = {
  protoProp: "a"
};
const obj = {
  __proto__: proto,
  objProp: "b"
};
Object.keys(obj); // ["objProp"]
obj.protoProp = "x";
Object.keys(obj); // ["objProp", "protoProp"]
proto.protoProp === "a";
obj.protoProp === "x";
```

Classes are a compact syntax for setting up prototype chains:

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  describe() {
    return "Person named " + this.name;
  }
}
const jane = new Person("Jane");
const tarzan = new Person("Tarzan");
jane.describe(); // Person named Jane
tarzane.describe(); // Person named Tarzan
```

Classes can also be expressions: `const Person = class { ... };`.

Under the hood, `jane.__proto__` points to the Person prototype, which has a constructor function
that is `Person()`. That's why `typeof(Person) === "function"`. `__proto__` is a pseudo property
for accessing the prototype of an object. `Person.prototype` points to the prototype of all instances
of Person.

Classes can have static properties:

```js
class Bar {
  static staticMethod() {
    return "staticMethod";
  }
  static get staticGetter() {
    return "staticGetter";
  }
}
```

Classes can subclass via the `extends` keyword.

JavaScript only supports single inheritance, but there is a mixin technique:

```js
const S1 = (Sup) => class extends Sup { /*...*/ };
const S2 = (Sup) => class extends Sup { /*...*/ };
class C extends S2(S1(Object)) {
  /* ... */
}
```

# Collections

## Synchronous Iteration

The type definitions for iteration (in Typescript):

```js
interface Iterable<T> {
  [Symbol.iterator]() : Iterator<T>
}
interface Iterator<T> {
  next() : IteratorResult<T>;
}
interface IteratorResult<T> {
  value: T;
  done: boolean;
}

const iterable = ["a", "b"];
const iterator = iterable[Symbol.iterator]();
function logAll(iterable) {
  const iterator = iterable[Symbol.iterator]();
  while (true) {
    const {value, done} = iterator.next();
    if (done) break;
    console.log(value);
  }
}
```

Conforming to the protocol lets the object be used in `for of` loops.

## Arrays

## Maps & WeakMaps

## Sets & WeakSets

## Destructuring

# Asynchronicity

## Async Programming

## Promises

## Async Functions

## Async Iteration
