Programming in Objective-C
==========================

Stephen Kochan teaches us the basics of Objective-C, a superset of C that adds
object oriented programming.

Introduction to Objective-C
===========================

The first program we'll write:

    #import <Foundation/Foundation.h>

    int main(int argc, const char * argv[]) {
      NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
      NSLog(@"Programming is fun!");
      [pool drain];
      return 0;
    }

Objective-C source code files use the `.m` file extension.  You can walk through
Xcode to setup a "Command Line Tool" and compile and run it.  To compile source
code from the terminal:

    % gcc -framework Foundation [files] -o [progname]

To compile and run our source code:

    % gcc -framework Foundation main.m -o prog1
    % ./prog1
    2008-06-08 18:48:44:210 prog1[7985:10b] Programming is fun!

`NSLog` can display values of variables:

    NSLog(@"The sum of 50 and 25 is %i", 50 + 25);


Classes, Objects, Methods
=========================

Objective-C includes language features for classes, objects, instance methods,
and class methods.  The syntax for message passing is:

    [ClassOrInstance method];
    [receiver message];

Arguments are specified within the method name separated with a colon:

    [yourCar setSpeed:55];
    [yourCar setSpeed:55 andSetTopDown:YES];

Here's an example class for dealing with fractions:

    #import <Foundation/Foundation.h>
    
    @interface Fraction : NSObject {
      int numerator;
      int denominator;
    }
    - (void) print;
    - (void) setNumerator:(int)n;
    - (void) setDenominator:(int)d;
    @end

    @implementation Fraction
    - (void) print {
      NSLog(@"%i/%i", numerator, denominator);
    }

    - (void) setNumerator:(int)n {
      numerator = n;
    }

    - (void) setDenominator(int)d {
      denominator = d;
    }
    @end

    int main(int argc, char *argv[]) {
      NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

      Fraction myFraction = [[Fraction alloc] init];
      [myFraction setNumerator:1];
      [myFraction setDenominator:3];

      NSLog(@"The value of myFraction is:");
      [myFraction print];
      [myFraction release];

      [pool drain];
      return 0;
    }

`@interface` describes the class while `@implementation` contains the code that
implements it.  Here's the general syntax for defining a new class:

    @interface NewClassName : ParentClassName {
      memberDeclarations;
    }
    methodDeclarations;
    @end

Variables must begin with a letter or underscore and can include any
alphanumeric character or underscores.  Reserved words cannot be used.
Objective-C is case sensitive and class names usually start with an uppercase
by convention.  Method and instance variables start with a lowercase by
convention.  Both use camel case by convention.  Objective-C programmers tend
to use long, descriptive names.

The `-` sign as in `- (void) print;` denotes an instance method.  Use `+` for
a class method.

The general syntax for the `@implementation` section is:

    @implementation NewClassName
      - (returntype) methodName:(argtype)argname {
        methodBody...;
      }
    @end

The `alloc` class method allocates memory for your object and is inherited from
`NSObject`.  `init` will initialize all instance variables to zero.  The
`release` method frees the object from memory.  Whenever you allocate memory
for an object, you're responsible for releasing the memory it uses.

Instance variables are private by default.  If you want access to them, you'll
have to define getter methods.  By convention, these methods are named after
the variables they access:

    - (int) numerator {
      return numerator;
    }

    - (int) denominator {
      return denominator;
    }

Data Types and Expressions
==========================

Objective-C includes four basic data types: `int`, `float`, `double`, and
`char`.  Any literal number, single character, or character string is a
`constant` (eg `58` or `@"Programming is fun"`).  Expressions with only
constants are called `constant expressions`.

The range for an int is machine dependent.  Currently, it's likely to be 32 or
64 bits.  Same for float and double.  To print out basic data types, use these
substitution codes in NSLog:

* %i for ints
* %f for floats
* %e or %g for doubles
* %c for chars

Objective-C also includes qualifiers from the C language:

* long
* long long (as in long long int or long double)
* short
* unsigned
* signed

The `id` type is a generic object.  It's kind of a pointer to anything:

    id myObject = [[AnyClass alloc] init];

Arithmetic operators are the same as other languages:

* + for adding
* - for subtracting
* * for multiplying
* / for dividing
* % for modulus

The type cast operator lets you explicitly convert types.  It's a unary operator
just like `-`:

    f1 = (float) i1 / 100;  // i1 is an int

Assignment operators can be combined with several arithmetic operators:

* i += 10 // equivalent to i = i + 10
* i -= 10
* i *= 10
* i /= 10
* i %= 10

Program Looping
===============

Objective-C includes `for`, `while`, and `do` loops.  It's a superset of C, so
just use them as you would in C.

    for(initExpression; loopCondition; loopExpression)
      programStatement;

Relational operators you can use for the loopCondition are:

* == for equal to
* != not equal to
* <  less than
* <= less than or equal to
* >  greater than
* >= greater than or equal to

    while(expression)
      programStatement;

    do
      programStatement;
    while(expression);

Some statements to use for loops are `break` and `continue`.  `break` will break
out of the loop and stop completely.  `continue` will break the current loop
and continue with the next one.

Making Decisions
================

The decision making constructs for Objective-C are from C:

* if statement
* switch statement
* conditional operator

    if(expression)
      programStatement
 
The `if-else` construct lets you combine if statements:

    if(expression)
      programStatement
    else if(expression2)
      programStatement2
    else
      programStatement3

Conditional expressions can be combined to form compound expressions:

    condition1 && condition2 // true if both are true
    condition1 || condition2 // true if either are true

The `switch` statement compares values to form conditions:

    switch(expression) {
      case value1:
        programStatement;
        break;
      case value2:
        programStatement;
        break;
      default:
        programStatement;
        break;
    }

The switch expression is matched against the values to determine which
statements to run.

In C, non-zero values are true and zero is false.  Objective-C includes a
`BOOL` type with values `YES` or `NO` for true or false.

The conditional operator or ternary operator can also be used for decision
making:

    condition ? expression1 : expression2

The return value is expression1 if the condition is true, otherwise it's
expression2.

More on Classes
===============

Classes usually have separate interface and implementation files.  The interface
file has the file extension `.h` and the implementation file has the file
extension `.m`.  Your implementation file can import the interface:

    #import "ClassName.h"

Double quotes indicate a local file.  We import the Foundation framework using
angle brackets.  Angle brackets mean system files.

Objective-C 2.0 includes a feature to automatically create getters and setters
using the `@synthesize` directive:

    @interface Fraction : NSObject {
      int numerator;
      int denominator;
    }
    @end

    @implementation Fraction
    @synthesize numerator, denominator;
    @end

Now you can use the methods `numerator`, `setNumerator`, `denominator`, or
`setDenominator`.  You can also use the dot operator:

    [fraction numerator];
    fraction.numerator;

    [fraction setNumerator:10];
    fraction.numerator = 10;
 
The argument names are actually optional in method declarations/calls.  These
are equivalent:

    - (void) setTo:(int)n over:(int)d;
    [fraction setTo:10 over:10];

    - (void) set:(int)n :(int)d;
    [fraction set:10 :10];

You can use local variables within methods.  You can use static local variables
or static global variables.

Using the `self` keyword refers to the current instance.  It's the equivalent
of **this** in other languages.

Inheritance
===========

The root class is `NSObject`, all classes are descendants of it.  Methods and
variables are inherited as normal.  Objective-C uses the terms `superclass`
for a parent class and `subclass` for a child class.  Methods are inherited
from superclasses to subclasses.

The `@class` directive tells the compiler about a class.  It's used in interface
files so you don't have to import other interface files:

    @class XYPoint;
    @interface Rectangle : NSObject {
      XYPoint *origin;
    }

The compiler knows that `XYPoint` is a class.  Using `@class` is more efficient
than importing the file `XYPoint.h`.

You can't remove methods in subclasses, but you can override them.  Just define
a method with the same name.

When you subclass something, be sure to override the `dealloc` method.  It's
called when an object is deallocated from memory.  You should release any
objects that have been retained by your instance variables.  The `super` keyword
gives access to the superclass:

    - (void) dealloc {
      if (origin) {
        [origin release];
      }
      [super dealloc];
    }

When you extend a class, you can add new instance variables and methods.

Polymorphism, Dynamic Typing, and Dynamic Binding
=================================================

Polymorphism lets objects from different classes share the same name for
methods.  Dynamic typing delays typing until runtime.  Dynamic binding delays
the binding of an actual method until runtime.

Let's look at polymorphism:

    @interface Fraction {
      // ...
    }
    - (Fraction *) add:(Fraction *)f;
    @end

    @interface Complex {
      // ...
    }
    - (Complex *) add:(Complex *)c;
    @end

    int main(int argc, char *argv[]) {
      // ...
      [complex add:complex2];
      [fraction add:fraction2];
      // ...
    }

The runtime system determines which add method to use by the class type of
the receiver.  That's how it knows to use Complex's add method or Fraction's
add method.

Dynamic binding can be done with the `id` type:

    id dataValue;
    dataValue = [[Fraction alloc] init];
    [dataValue print];
    dataValue = [[Complex alloc] init];
    [dataValue print];

This defers some checks until runtime.  With the `id` type, you won't get a
compile error if a method doesn't exist.  That's why it's usually best to
document the type if at all possible.

Sometimes, you'll want to get class/method information from objects/classes.
A `selector` is an instance of a method.  It's of `SEL` type.  Some methods
to work with dynamic typing:

* `- (BOOL) isMemberOfClass:`
* `- (BOOL) isKindOfClass:   // member of or descendant`
* `- (BOOL) respondsToSelector:`
* `+ (BOOL) instancesRespondToSelector:`
* `+ (BOOL) isSubclassOfClass:`
* `- (id) performSelector:`
* `- (id) performSelector: withObject:`
* `- (id) performSelector: withObject: withObject:`

For example:

    [myFraction isMemberOfClass:[Fraction class]];
    [Fraction instancesRespondToSelector:@selector(setTo:over:)];

If a message is sent to a receiver which it can't respond to (undefined method),
it will throw an exception.  Objective-C includes exception handling:

    @try {
      statement
      ...
    } @catch (NSException *exception) {
      statement
      ...
    }

There's an optional `@finally` block which runs whether or not an exception is
thrown.  `@throw` lets you throw your own exceptions.

More on Variables and Data Types
================================

Initializer methods start with **init**.  If a class has more than one, one of
them is a designated initializer and all the other initialization methods should
use it (usually the most complex one).  Subclasses only need to override that
single initialization method.

    initWithArray:
    initWithArray:copyItems:
    initWithContentsOfFile:
    initWithContentsOfURL:
    initWithObjects:
    initWithObjects:count:

You can control the scope of instance variables with a few directives:

    @protected  // can access in class or in sub classes
    @private    // only access in class
    @public     // always have access
    @package    // for 64bit images, access within image that implements class

For example:

    @interface Printer : NSObject {
    @private
      int pageCount;
      int tonerLevel;
    @protected
      // other instance variables
    }
    ...
    @end

If a variable is defined outside of a method or class definition, it will be
a global variable to anything in that file.  Use the `extern` keyword in other
files to access it as an external variable.  By convention, a lowercase g is
prefixed:

    int gMoveNumber = 0;
    // in a different file
    - (void) setgMoveNumber:(int)val {
      extern int gMoveNumber; 
      gMoveNumber = val;
    }

The `static` keyword can be used to scope global variables to the local file,
disabling any external use.

    static int gCounter = 0;

The C language includes `enums` for enumerated data types:

    enum flag { false, true };
    enum flag endOfData = true;
    endOfData = false;

Unfortunately, the compiler won't warn you if you try to assign a value other
than `true` or `false`.  You can also assign specific integers to enum values:

    enum direction { up, down, left = 10, right }; // 0, 1, 10, 11

`typedef` lets you assign your own name to a built-in or derived data type.  

    typedef int Counter;
    Counter j, n;  // equivalent to `int j, n;`

    typedef Number *NumberObject;
    NumberObject n1, n2;  // equivalent to `Number *n1, *n2;`

    typedef enum { east, west, south, north } Direction;
    Direction d1, d2;

The bitwise operators of the C language:

* `&` bitwise AND
* `|` bitwise inclusive-OR
* `^` bitwise exclusive-OR
* `~` ones complement
* `<<` left shift
* `>>` right shift

Categories and Protocols
========================

Categories let you extend existing classes with new methods without overriding
them.  It's useful for organizing methods or extending a class whose
implementation you don't have access to:

    #import "ClassName.h"

    @interface ClassName (CategoryName)
    - (void) methodDeclarations;
    @end

    @implementation ClassName (CategoryName)
    - (void) methodDefinitions;
    @end

A **category** can override a previously defined method but it's considered poor
practice.  If a method is defined in more than one category, the language does
not specify which method will be used.  Adding methods will affect all
subclasses as well.

A **protocol** is a list of methods shared among classes.  It's like an
"interface" in Java.  Some methods are required, others are optional.  If a
class implements all of the required methods it is said to **conform** or
"adopt" the protocol.

To define a protocol:

    @protocol ProtocolName
    - (void) methodDeclarations;
    @optional
    - (void) optionalMethodDeclarations;
    @end

To adopt a protocol, place its name inside angle brackets:

    @interface ClassName : NSObject <ProtocolName, Protocol2>
    @interface ClassName (CategoryName) <ProtocolName>

Some handy ways to use protocols:

    id <ProtocolName> object;  // compiler makes sure object conforms
    NSObject * <ProtocolName> object2;
    [object conformsToProtocol:@protocol(ProtocolName)];
    [object respondsToSelector:@selector(methodName:)];

A common convention is the use of **informal protocols** which are just
categories that lists methods without implementing them.  It's really just a
group fo methods under a name to help with documentation or modularization:

    @interface NSOBject (NScomparisonMethods)
    - (BOOL) isEqualTo:(id)object;
    // ...
    @end

The Preprocessor
================

The C preprocessor makes it easier to develop, read, modify, and port to
different platforms.  The preprocessor is part of the compilation step.
Preprocessor statements start with `#`.

`#define` assigns symbolic names to program constants and expressions:

    #define TRUE 1
    int gameOver = TRUE;  // equivalent to `int gameOver = 1`

It's common to put define statements in header files so it can be used in more
than one source file.  It's convention to use all capital letters for
definitions or prefix with the letter `k` as in `kMaximumValues`.

The define expression doesn't have to be valid as long as it's valid in the
context that it's used:

    #define AND &&
    #define OR ||
    if (x > 0 AND x < 10 OR y > 0)

If the expression uses multiple lines, use the backslash character `\` to do
a continuation.  Definitions can also take arguments:

    #define IS_LEAP_YEAR(y) y % 4 == 0 && y % 100 != 0 \
                            || y % 400 == 0

Definitions are often called **macros**.  Some interesting bugs can appear from
them if you're not too careful:

    #define SQUARE(x) x * x
    SQUARE(x) // x * x
    SQUARE(x + 1) // x + 1 * x + 1
    #define CORRECTSQUARE(x) (x) * (x)

The `#import` statement lets you collect definitions in a separate file and
include them in the current source file.  It's just like C's `#include` except
it includes guards so you can't include the same file twice.

    // in metric.h
    #define INCHES_PER_CENTIMETER 0.394
    #define CENTIMETERS_PER_INCH (1 / INCHES_PER_CENTIMETER - CENTIMETER)

    // in any other file
    #import "metric.h"
    INCHES_PER_CENTIMETER == 0.394;

Placing the filename in double quotes means the preprocessor will look locally
for the file.  Placing it in angle brackets means it's a special system header
file and will look in your system directories.  Importing lets you centralize
your definitions.

**Conditional compilation** can be used to compile software to run on different
platforms:

* `#ifdef`
* `#endif`
* `#else`
* `#ifndef`

    #ifdef IPAD
    #  define kImageFile @"barnHD.png"
    #else
    #  define kImageFile @"barn.png"
    #endif

To define `IPAD`:

    #define IPAD 1
    // or even just
    #define IPAD

Or you can define it with an option to the compiler:

    gcc -framework Foundation -D IPAD program.m

You can do this in Xcode's build settings.  This is especially useful to set
a `DEBUG` flag for development.

There's a special `defined` operator that lets you perform the same task:

    // if DEBUG is set to non-zero value
    #if defined(DEBUG) && DEBUG

Sometimes you'll want to undefine a macro:

    #undef IPAD

Underlying C Language Features
==============================

Kochan suggests we learn these features on a need-to-know basis since most of
them go against OOP.  They can also interfere with the Foundation Framework.
On the other hand, it's always good to know the underlying language.

**Arrays** are provided and items can be accessed via the `[]` operator:

    Fraction *fracts[100];
    fracts[0];
    fracts[2] = [fracts[0] add:fracts[1]];

    int integers[5] = {0, 1, 2, 3, 4};
    int integers2[5] = {0}; // all zeroes
    int integers3[] = {0}; // array of size 1 automatically

You can make multi-dimensional arrays just like any other language.

If you put a **null character** `'\0'` at the end of a character array, you
get a **character string**:

    char word[] = {'H', 'e', 'l', 'l', 'o', '\0'};
    NSLog(@"%s", word);

`NSLog` is an example of a C function.  They're implemented like this:

    returntype functionname(argtype argname) {
      // function body
      return value;
    }

If your function requires no arguments, use the type `void`.  Otherwise, the
compiler will no warn you when the function is called with an argument list:

    void printMessage(void) {
      NSLog(@"Hello World!");
    }

If the return type of the function is omitted, it defaults to `int`.

You can declare the function with its return type, name,  and arguments so
anyone importing your source file can use it.  This is known as a
**prototype declaration**:

    float absoluteValue(float);
    float absoluteValue(float x); // arg name optional
    void NSLog(NSString *format, ...) // varargs declaration

A good strategy is to declare all your functions and place those in a separate
header file.  Functions are external by default, use the `static` keyword to
limit their scope.  A static function can only be called in the same file that
contains the function's definition.

**Blocks** are a recent feature added by Apple and is not part of the ANSI C
standard:

    void printMessage(void) {
      NSLog(@"Hello World!");
    }
    ^(void) {
      NSLog(@"Hello World!");
    }

A block starts with a caret `^`.  To assign the previous block to a variable:

    void (^printMessage)(void) = ^(void) {
      NSLog(@"Hello World!");
    }
    printMessage();

Here's a block with a return value:

    int (^gcd)(int, int) = ^(int u, int v) {
      int temp;
      while(v != 0) {
        temp = u % v;
        u = v;
        v = temp;
      }
      return u;
    }

A block has access to the variables on the stack.  Its values are whatever the
value is at the block definition time.  These values are readonly, you'll get
a compilation error if you try to change it.  Variables can be defined with a
`_ _ block` modifier to be writeable for blocks:

    _ _ block int foo = 10;

You can use **structures** to group together variables:

    struct date {
      int month;
      int day;
      int year;
    }

    struct date today;
    today.day = 21;
    today.month = 12;
    today.year = 2011;
    NSLog(@"%i/%i/%.2i", today.month, today.day, today.year);

    struct date tomorrow = {12, 22, 2011};

You can use `typedef` to to conveniently shorten your struct type:

    typedef struct {
      int month;
      int day;
      int year;
    } date;
    date today = {12, 21, 2011};

You can also declare and define a struct at the same time:

    struct {
      int x;
      int y;
    } origin = {0, 0};

You can use bit expressions to pack informations in an int, or you can use
**bit fields** in a struct:

    struct packedStruct {
      unsigned int f1:1;    // occupy 1 bit
      unsigned int f2:1;
      unsigned int f3:1;
      unsigned int type:4;  // occupy 4 bits
      unsigned int index:9; // occupy 9 bits
    } packedData;
    packedData.type = n; // only low-order 4 bits are stored

Objective-C makes heavy use of pointers just like C:

    int count = 10;
    int *intPtr = &count;
    int x = *intPtr;
    x == 10; // true

    int count = 10, x, *intPtr; // combining declarations

You'll often use pointers with structures so you don't have to pass entire
structures of data around:

    struct date *datePtr;
    datePtr = &today;
    (*datePtr).day = 21;  // structure member operator has higher precedence
    datePtr->day = 21;    // shortcut structure pointer -> operator

Pointers to arrays are defined as normal pointers:

    Fraction **fractsPtr; // pointer to array of fractions
    int *valuesPtr; // pointer to array of ints

    valuesPtr = values;     // now it points to first value of values array
    valuesPtr = &values[0]; // equivalent of top

    *valuesPtr;               // equivalent to values[0]
    *(valuesPtr + i);         // equivalent to values[i]
    *(valuesPtr + i) = value; // equivalent to values[i] = value

`valuesPtr` is just a memory address that you can increase or decrease:

    valuesPtr += 1;
    valuesPtr > &values[99];  // passed 99th element yet?

Pass arrays to functions using pointers:

    int max(int *array, int arraySize)

Pointers to character strings may also be more convenient:

    void copyString(char to[], char from[]) {
      for(int i = 0; from[i] != '\0'; ++i) {
        to[i] = from[i];
      }
      to[i] = '\0';
    }
    void copyString2(char *to, char*from) {
      for(; *from != '\0'; ++from, ++to) {
        *to = *from;
      }
      *to = '\0';
    }

You can also have pointers to functions.  To declare a pointer to function that
returns an int and takes no arguments:

    int (*fnPtr) (void);

Functions can be assigned and called without any special operators:

    fnPtr = lookup;  // int lookup(void)
    fnPtr();

**Unions** are a construct to store data types in the same storage space:

    union mixed {
      char c;
      float f;
      int i;
    };
    union mixed x;  // x can have either 'c', 'f', or 'i' fields
    union mixed y = {.f = 123.4;};
 
The three fields coexist in the same place in memory, so you can't store all
fields at once.

**Compound literals** is a type name specified in parenthesis followed by an
initializer expression:

    today = (sturct date) {.month = 7, .day = 2, .year = 2011};

The **goto** statement branches code to a specified label.  Don't abuse it.

    goto out_of_data;
    out_of_data: NSLog(@"Unexpected end of data.");

A solitary semicolon wherever a normal statement would go is called a
**null statement**.  You'll find it frequently in for loops:

    int i = 0;
    for(; i < 100; i++)

Never make assumptions about the size of a data type.  Use the `sizeof` operator
to determine allocated memory size on different systems:

    sizeof(int);     // on macbook air it's `4` as in 4 bytes or 32 bits
    int x[100];
    sizeof(x);       // 400, 100 x 4 bytes
    sizeof(myFract); // 4, size of pointer

Objective-C uses the C language underneath.  To shed some light on how things
work in the Objective-C runtime:

* instance variables are stored in structures
* one member of the structure is `isa` ("is a"), which identifies the class the
  object belongs to
* an object variable is really just a pointer
* methods are functions and message expressions are function calls.  The
  Objective-C compiler creates unique names for all your methods.
* the `id` type is a generic pointer.  Since objects carry an `isa` pointer,
  the runtime will always know what class it belongs to.

Introduction to the Foundation Framework
========================================

Numbers, Strings, and Collections
=================================

Working with Files
==================

Memory Management
=================

Copying Objects
===============

Archiving
=========

Introduction to Cocoa and Cocoa Touch
=====================================

Writing iOS Applications
========================
