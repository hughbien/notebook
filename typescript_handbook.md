# TypeScript Handbook

Notes for the [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/basic-types.html).

# Basic Types

TypeScript supports the same primitives as JavaScript. It adds a few like Tuple and Enum.

```ts
let isDone: boolean = false;
let decimal: number = 6;
let hex: number = 0xf00d;
let color: string = "blue";
let list: number[] = [1, 2, 3];
let list2: Array<number> = [1, 2, 3];
let pair: [string, number] = ["hello", 10]; // tuple with known size/types

enum Color {Red, Green, Blue} // Red=0, Green=1, Blue=2
let c: Color = Color.Green;

// enums can set their own value too
enum Color {Red = 1, Green = 2, Blue = 4}

let notSure: any = 4; // can be any type

function warnUser(): void { // returns nothing
  console.log("This is a warning message");
}

// only possible values for null and undefined
let u: undefined = undefined;
let n: null = null;

// anything that is non-primitive, always use lowercase object instead of Object
let o: object = { prop: 0 }
```

You can use type assertions to cast a type:

```ts
let strLength: number = (<string>someValue).length;
let strLength2: number = (someValue as string).length;
```

# Variable Declarations

`let`, `const` are block scoped just like JavaScript. Use them whenever possible as opposed to `var`.

Shadowing and destructuring rules work just like JavaScript also.

Default properties have a `?` suffix in type declarations:

```ts
function keepWholeObject(wholeObject: { a: string, b?: number }) {
  let { a, b = 1001 } = wholeObject;
}

type C = { a: string, b?: number };
function f({ a, b=0 } = { a: "" }: C): void {
}
f({ a: "yes" }); // ok, default b = 0
f(); // ok, default to { a: "" }, which then defaults to b = 0
f({}); // error, "a" is required
```

# Interfaces

TypeScript focuses on the shape that values have. It's similar to duck typing, values that adhere
to an interface will work fine -- it doesn't need to be a specific type.

```ts
function printLabel(labeledObj: { label: string }) {
  console.log(labeledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj); // has label prop, works fine

// can be moved to interface
interface LabeledValue {
  label: string;
}
function printLabel(labelObj: LabeledValue) { ... }
```

Note in the above example, there was an extra `size` property. The compiler won't error because
the shape conforms. But object literals are treated differently. An object literal cannot specify
unknown properties, so: `printLabel({size: 10, label: ""})` would error. You can get around this
by either casting `{...} as LabeledValue`, assigning to a variable, or specifying an interface to
have unknown properties:

```ts
interface LabeledValue {
  label: string;
  [propName: string]: any; // indexable type
}
```

Indexable types work with arrays too:

```ts
interface StringArray {
  [index: number]: string;
}
```

Note when indexing, the return value must match all other property types. So for the above,
all other properties in StringArray must be a kind of `string`.

Optional properties are suffixed with `?`. Readonly properties are prefixed with `readonly`:

```ts
interface Point {
  readonly x: number;
  readonly y: number;
}
```

Functions can also have interfaces:

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) { // parameter names don't need to match
  let result = source.search(subString);
  return result > -1;
}
```

TypeScript's contextual typing can also infer the parameter types from the interface signature,
so they're optional.

Class interfaces describe the public side of a class. Also if you want to type-check the static-side
of an interface (as opposed to the instance-side), you'll need a second interface.

```ts
interface ClockConstructor { // static side
  new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface { // instance side
  currentTime: Date;
  setTime(d: Date): void;
  tick(): void;
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
  currentTime: Date = new Date();
  constructor(h: number, m: number) { }
  setTime(d: Date) {
    this.currentTime = d;
  }
  tick() {
    console.log("beep beep");
  }
}

class AnalogClock implements ClockInterface {
  currentTime: Date = new Date();
  constructor(h: number, m: number) { }
  setTime(d: Date) {
    this.currentTime = d;
  }
  tick() {
    console.log("tick tock");
  }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

Interfaces can extend each other:

```ts
interface Shape {
  color: string;
}
interface PenStroke {
  penWidth: number;
}
interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

Interfaces can handle hybrid types, such as function objects:

```ts
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = (function (start: number) { }) as Counter;
  counter.interval = 123;
  counter.reset = function () { };
  return counter;
}
```

Interfaces can extend classes. It inherits the members of the class but not the implementations.

```ts
class Control {
  private state: any;
}
interface SelectableControl extends Control {
  select(): void;
}
class Button extends Control implements SelectableControl {
  select() { }
}
class TextBox extends Control {
  select() { }
}
class Image implements SelectableControl { // Error: Property 'state' is missing in type 'Image'
  private state: any;
  select() { }
}
class Location {
}
```

# Classes

Syntax is very similar to JavaScript, with some additional type annotation in function declaration
and instance variables:

```ts
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    return "Hello, " + this.greeting;
  }
}
let greeter = new Greeter("world");

class Animal {
  move(distanceInMeters: number = 0) {
    console.log(`Animal moved ${distanceInMeters}m.`);
  }
}
class Dog extends Animal {
  bark() {
    console.log("Woof! Woof!");
  }
}
const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

Use `super` to access the superclass. By default, all members and methods are public. You can add
modifiers: `public`, `private`, and `protected`. TypeScript also supports the `#` prefix for
private fields. The `readonly` modifier can be used on properties also.

Shorthand property assignments in constructors:

```ts
constructor(theName: string) {
  this.name = theName;
}
// is the same as
constructor(readonly name: string) {
}
```

Getters/setters work just like JavaScript with the `get` and `set` modifiers.

The `static` modifier also works just like JavaScript, and can be used on properties.

TypeScript supports abstract classes, which may not be instantiated directly:

```ts
abstract class Animal {
  abstract makeSound(): void; // must be implemented by child class
  move(): void {
    console.log("roaming the earth...");
  }
}
```

# Functions

TypeScript supports named/anonymous functions just like JavaScript. Scoping rules (closures) also
work the same. Parameters and return values can have type annotation:

```ts
function add(x: number, y: number): number {
  return x + y;
}
let myAdd = function(x: number, y: number): number { return x + y; };
let myAdd: (x: number, y: number) => number = function(x: number, y: number): number { return x + y };
```

TypeScript has contextual types, so they can be left off if they can be inferred.

Optional parameters are suffixed with `?`. Default values and rest parameters work just like in
JavaScript.

```ts
function buildName(firstName: string, lastName?: string) { ... }
function buildName(firstName: string, lastName = "Smith") { ... }
function buildName(firstName: string, ...restOfName: string[]) { ... }
```

`this` scoping and rules work just like JavaScript. The TypeScript compiler will error when you
call unknown methods or access unknown properties on `this`, which is handy when you accidentally
use `function` instead of `() =>`.

You can use `this` parameters to explicitly say what type `this` is too:

```ts
function f(this: void) {
  // cannot use this here
}
function (this: Deck) { // callee must be of type Deck
  return () => {
    // this is of type Deck here
  };
}
```

A function name may be re-used with different type annotations. This is fine, TypeScript supports
function overloading or dynamic dispatch.

# Generics

# Enums

# Type Inference

# Type Compatibility

# Advanced Types

# Symbols

# Iterators and Generators

# Modules

# Namespaces

# Namespaces and Modules

# Module Resolution

# Declaration Merging

# Decorators

# Mixins

# Triple-Slash Directives

# Type Checking JavaScript Files

# Utility Types
