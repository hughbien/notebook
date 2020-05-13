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

TypeScript supports generics or type parameterization.

```ts
function identity<T>(arg: T): T {
  return arg;
}
let output = identity<string>("myString");
let output = identity("myString"); // can be inferred

interface GenericIdentityFn {
  <T>(arg: T): T;
}
let myIdentity: GenericIdentityFn = identity;

class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}
let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

Generic types can have constraints also:

```ts
interface Lengthwise {
  length: number;
}
function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

function getProperty<T, K extends keyof T>(obj: T, key: K) { // key must exist on obj
  return obj[key];
}
getProperty({ a: 1 }, "a"); // okay
getProperty({ a: 1 }, "m"); // error
```

# Enums

Enums have number values:

```ts
enum Direction {
  Up, // starts with 0, increments by 1; set your own by doing `Up = 1,`
  Down,
  Left,
  Right
}
function move(direction: Direction) { ... }
move(Direction.Up);
```

Enums can have string values too. They can also be initialized with a constant enum expression,
a subset of TypeScript that can be evaluated at compile time.

Enum members can also serve as types:

```ts
enum ShapeKind {
  Circle,
  Square
}

interface Circle {
  kind: ShapeKind.Circle;
  radius: number;
}

interface Square {
  kind: ShapeKind.Square;
  sideLength: number;
}
```

Use `keyof typeof` to get a Type that represents all Enum keys as strings.

```ts
type LogLevelStrings = keyof typeof LogLevel;
```

You can reverse map members with `[]`:

```ts
enum Enum { A }
let a = Enum.A;
let nameOfA = Enum[a];
```

# Type Compatibility

TypeScript uses structural typing instead of nominal typing. If two different types have the same
properties/definitions, they're considered compatible.

```ts
interface Named {
  name: string;
}
class Person {
  name: string;
}
let p: Named;
p = new Person();
```

This works for functions too, even if the argument list is a subset of another argument list. This
is because it's a common pattern in JavaScript to leave off arguments in function calls, such
as optional callbacks.

# Advanced Types

Use `&` to intersect two types: `Person & Serializable`

Use `|` to union two types: `number | string`

To differentiate between types (eg if a variable can be a number or string) you'll need to
either cast and check for a property or use type guards:

```ts
let pet : Fish | Bird = getSmallPet();
if ((pet as Fish).swim) {
  (pet as Fish).swim();
} else if ((pet as Bird).fly) {
  (pet as Bird).fly();
}

function isFish(pet: Fish | Bird): pet is Fish { // type predicate, pet is Fish
  return (pet as Fish).swim !== undefined;
}
if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}

if ("swim" in pet) {
  pet.swim();
} else {
  pet.fly();
}
```

You can also use `typeof` or `instanceof`.

Nullable types can be done via adding `| null` to a type. This is handy with optional chaining:
`let x = foo?.bar.baz();` which returns undefined when it hits null or undefined.

Use `type` for type aliases: `type Name = string;`. They're similar to interfaces, but they don't
create a new name -- error messages will still use the original type name.

String literals can be types too: `type Easing = "ease-in" | "ease-out" | "ease-in-out";` for
enum-like behavior. It can also be used to distinguish overloads. This works for numbers too.

Conditional types can be set via the ternary operator: `T extends U ? X : Y`. These can be nested:

```ts
type TypeName<T> =
  T extends string ? "string" :
  T extends number ? "number" :
  T extends boolean ? "boolean" :
  T extends undefined ? "undefined" :
  T extends Function ? "function" :
  "object";
type T0 = TypeName<string>; // "string"
type T1 = TypeName<"a">; // "string"
type T2 = TypeName<true>; // "boolean"
```

# Symbols, Iterators, Generators

`symbol` is another primitive type and works just like in JavaScript.

```ts
let sym1 = Symbol();
let sym2 = Symbol("key");

const sym = Symbol();
let obj = {
  [sym]: "value"
};
console.log(obj[sym]); // "value"
```

Iteration and generation work just like in JavaScript. It looks like type inference works well
here, so you can mostly leave off type annotations.

# Modules

`import` and `export` work just like in JavaScript. Any declaration can be exported/imported,
including type aliases or interfaces.

```ts
// StringValidator.ts
export interface StringValidator {
  isAcceptable(s: string): boolean;
}

// ZipCodeValidator.ts
import { StringValidator } from "./StringValidator";
```

You can explicitly import a type with `import type`, although just `import` works too. Using the
former helps compilers remove it from your production code.

# Namespaces

TypeScript supports namespaces:

```ts
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }
}

let validator: Validation.StringValidator;
```

Namespaces occupy the global namespace. They can span multiple files.

Don't use a namespace if you're writing code for a module. Modules already provide a namespace when
imported:

```ts
// shapes.ts
export namespace Shapes {
  export class Triangle { ... }
  export class Square { ... }
}
// shapeConsumer.ts
import * as shapes from "./shapes";
let t = new shapes.Shapes.Triangle(); // repeating shapes
```

# Declaration Merging

Repeated interfaces/namespaces will be merged into one interface/namespace.

Classes cannot be merged with other classes. Use Mixins instead.

You can monkey-patch by importing existing objects and then updating them:

```ts
// observable.ts
export class Observable<T> { ... }
// map.ts
import { Observable } from "./observable";
Observable.prototype.map = function (f) { ... }
```

# Utility Types

Some useful utility types, available globally:

* `Partial<T>` - wraps a type and sets all properties to optional
* `Readonly<T>` - sets all properties to readonly
* `Pick<T,K>` - pick a set of properties K from T
* `Omit<T,K>` - omit a set of properties K from T
* `NonNullable<T>` - removes all null and undefined definitions from properties
* `Required<T>` - makes all properties of T required
