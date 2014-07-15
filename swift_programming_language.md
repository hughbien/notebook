Swift Programming Language
==========================

Swift is the new programming language used for iOS and OS X applications.  This guide is by Apple.

## The Basics

Declare constants/variables with `let`/`var`:

    let maxLogin = 10
    var loginAttempts = 0
    var x = 0.0, y = 0.0, z = 0.0

Type annotations can be made with a colon, below reads "welcome message of type string" - although
Swift can usually infer the type from constant/variable initialization.

    var welcomeMessage: String
    welcomeMessage = "Hello!"
    println(welcomeMessage)

Comments are specified with `//` or `/* ... */`, nested multiline comments are enabled:

    /* starting comment
    /* nested */
    ending comment*/

Semicolons are optional, most example code is written without it.  You can use it to combine
multiple statements into one line though:

    let message = "Hello!"; println(message)

Integers are whole numbers, can be signed/unsigned in 8,16,32,64 bit forms.  Example types are
`UInt8` or `Int32`.  Bounds are specified like `UInt8.min` or `Int32.max`.  `Int` defaults to
current 32 or 64 bit platform.  You'll usually want `Int` or `UInt`.

Floats are numbers with fractional components and can be stored with: `Double` for 64-bit floating
point number or `Float` for 32-bit.

Numeric literals can be inferred with prefixes: no prefix is decimal, `0b` for binary, `0o` for
octal, `0x` for hex.  Suffixes can be `e` or `p` meaning `*10^n` or `*2^n`.  So `1.25e2` means
`1.25 * 10^2` and `15p2` means `15 * 2^2`.

Define type aliases with `typealias` keyword:

    typealias AudioSample = UInt16
    var maxAmp = AudioSample.min

Bool types represent boolean values: `true` and `false`.  They're useful with `if` statements:

    if turnipsAreDelicious {
        // ...
    }

Tuples are multiple values grouped together:

    let http404Error = (404, 'Not Found')
    let (statusCode, _) = http404Error // underscore can be used to ignore decomposition
    println("The status code is \(statusCode)")
    println("The status code is \(http404Error.0)")

Tuples accept naming too:

    let http200Status = (statusCode: 200, description: "OK")
    println("The status code is \(http200Status.statusCode)")

Optionals can be used when value may not be present and is specified with a suffix `?` like `Int?`.
They can be used in `if` statements like booleans.  Use the suffix `!` to force unwrap the
optional's underlying value.  A common pattern is optional binding:

    if let actualNum = possibleNum.toInt() {
        // ... use actualNum ..
    }

`nil` is the empty value for Swift and can be assigned to optional constants/variables.  It's also
the default when initializing optional constants/variables.

Implicitly unwrapped optionals are declared using `!` suffix.  They're used like optionals but
already have a value.

Assertions are made with `assert()`.

## Basic Operators

Swift's basic operators are similar to C's, except modulus is allowed on floating point numbers
and Swift includes range operators (`..` and `...`).

There are 3x categories: unary/prefix/postfix, binary/infix, and ternary.

* `=` for assignment
* `+`, `-`, `*`, `/` for arithmetic
* `%` for remainder
* `++`, `--` for increment/decrement
* `-` for unary negate
* `+` for unary plus, doesn't do anything but can be useful for code annotation
* `=+` or any other arithmetic operators for compound assignments
* `==`, `!=`, `>`, `>=`, `<`, `<=` for comparisons
* `a ? b : c` for ternary conditional
* `a...b` for closed range (includes a and b, eg `for i in 1...5`)
* `a..<b` for half-closed ranged (includes a but not b, eg `for i in 1..<5`)
* `!`, `&&`, `||` for logical NOT, AND, OR

## Strings and Characters

String literals are placed in double quotes, `"Some string literal value"`.  Use a backslash to
escape characters.  Use `\()` for string interpolation.

Strings are value types and are passed as values to methods.  Strings/characters can be concatenated
with `+`.  Specify a character like so:

    let character1: Character = "!"

Useful methods and properties:

* `countElements(str)` - returns length
* `str.isEmpty`
* `str.hasPrefix(str2)`
* `str.hasSuffix(str2)`
* `str.uppercaseString`
* `str.lowercaseString`
* `str.utf8` - collection of UInt8 character representations
* `str.utf16` - collection of UInt16 character representations
* `str.unicodeScalars` - collection of UnicodeScalar

## Collection Types

Swift provides two: arrays and dictionaries.

Arrays are specified like: `Array<SomeType>` or `[SomeType]`:

    var shoppingList: [String] = ["Eggs", "Milk"]
    var shoppingList = ["Eggs", "Milk"] // type inferred

Useful methods, properties, etc:

* `arr.count`
* `arr.isEmpty`
* `arr.append(obj)`
* `arr.insert(obj, atIndex: index)`
* `arr.removeAtIndex(index)`
* `arr.removeLast()`
* `arr += obj`
* `arr += [obj1, obj2, obj3, ...]`
* `arr + arr2` concats arrays
* `arr[index]`
* `arr[index] = obj`
* `arr[index1...index2] = [obj1, obj2, obj3, ...]`
* `for obj in arr { }`
* `for (index, obj) in enumerate(arr) { }`
* `var arr = [SomeType]()` declare/initializes empty array
* `var threeDoubles = [Double](count: 3, repeatedValue: 0.0)` initializes repeated array

Dictionaries are specified like: `Dictionary<KeyType, ValueType>` or `[KeyType: ValueType]`:

    var airports: [String: String] = ["TYO": "Tokyo", "DUB": "Dublin"]

Useful methods, properties, etc:

* `d.count`
* `d[key]` can be nil, so returns optional type
* `d[key] = value`
* `d[key] = nil` removes entry
* `d.updateValue(val, forKey: key)` expression returns old value
* `for (key, val) in d { }`
* `d.keys` please note it's unordered
* `d.values` please note it's unordered
* `var d = [Int: String]()` declare/initializes empty dictionary
* `d = [:]` resets to empty dictionary if type can be inferred

A KeyType must be hashable (conforms to `Hashable` protocol) to be used as a key.  All of Swift's
basic types are hashable.

## Control Flow

There's the standard `for-condition-increment` loop found in C.  There's also the new `for-in` loop
for iterating over collections:

    for index in 1...5 {
        // index only exists within this scope, use `_` in place of index to ignore
    }
    for val in anArray { ... }
    for (key, val) in aDictionary { ... }

Swift also implements the `while` and `do while` loops found in C.

`if`, `else if`, and `else` are also all implemented.

`switch` is implemented but differs a bit.  Swift uses type checking to make sure the statement is
exhaustive or contains a `default`.  There is no implicit fall through.  Multiple matches are
separated with a comma.  You can also use range or tuple matching.  With tuple matching, use an
underscore to denote any possible value.

Value binding can be used in switches:

    switch point {
    case (let x, 0):
        ...
    case (0, let y):
        ...
    case let (x, y) where x == -y: // where can be used for additional conditionals
        ...
    case let (x, y):
    }

Swift has 4x control transfer statements:

* `continue`
* `break`
* `fallthrough` used in switch case statements to fall through to next case
* `return`

Switch and loop statements can be labeled to work with the control transfer statements:

    label labelName : while condition {
        // ...
        switch value {
        case valueOne:
            break labelName
        // ...
        }
    }

## Functions

## Closures

## Enumerations

## Classes and Structures

## Properties

## Methods

## Subscripts

## Inheritance

## Deinitialization

## Automatic Referencing Counting

## Optional Chaining

## Type Casting

## Nested Types

## Extensions

## Protocols

## Generics

## Advanced Operators
