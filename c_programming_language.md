C Programming Language
======================

A concise introduction to the C programming language by Brian W. Kernighan and
Dennis M. Richie.  The book is written for a reader without any programming
experience, but these notes assume prior experience.

A Tutorial Introduction
=======================

Kernighan and Ritchie start off with a "Hello World" program:

    #include <stdio.h>
    main() {
      printf("hello, world\n");
    }

To actually compile and run it:

    % cc hello.c
    % ./a.out

A C program consists of **functions**, **statements**, and **variables**.  This
example uses the special `main` function where program execution starts.
Every program needs this function.  The `#include` statement tells the compiler
to include information about standard input/output.

`"hello, world\n"` is called a **character string** or **string constant**.
It's a sequence of characters.

This next program prints a Fahrenheit-Celsius conversion table:

    #include <stdio.h>
    /* print Fahrenheit-Celsius table
       for fahr = 0, 20, ..., 300 */
    main() {
      int fahr, celsius;
      int lower, upper, step;

      lower = 0;    /* lower limit of temp scale */
      upper = 300;  /* upper limit */
      step = 20;    /* step size */

      fahr = lower;
      while (fahr <= upper) {
        celsius = 5 * (fahr-32) / 9;
        printf("%d\t%d\n", fahr, celsius);
        fahr = fahr + step;
      }
    }

Text between `/*` and `*/` are comments and can include newlines.

The first two lines of the main function are **declarations** that announces
variables.  The type `int` means the variables are integers.  The range and size
of `int`s and `float`s depend on the machine you're using.  Some other types:

* `char` single byte character
* `short` short integer
* `long` long integer
* `double` double-precision floating point

There's also **arrays**, **structures**, and **unions** which are data
structures made up of basic types or **pointers** to them.

To get a more accurate table, the authors switched the program to use floats
instead of ints.  A decimal point is also used to indicate constants are floats
and not ints:

    celsius = (5.0/9.0) * (fahr-32.0);

C will implicitly convert an integer to floating point if the operation involves
another floating point.  However, it doesn't hurt to be explicit with decimal
points.

Formatting for the `printf` function:

* `%d` print a decimal integer
* `%6d` print a decimal integer, at least 6 characters wide
* `%f` print a floating point
* `%6f` print a floating point, at least 6 characters wide
* `%.2f` print a floating point, 2 characters after decimal point
* `%6.2f` print a floating point, at least 6 characters wide and 2 after decimal

The program can be shortened using a `for` statement instead of the `while`:

    for (fahr = 0; fahr <= 350; fahr = fahr + 20)
      printf("%3d %6.1f\n", fahr, (5.0/9.0)*(fahr-32));

It's bad practice to bury "magic numbers".  We deal with this by defining a
**symbolic name** or **symbolic constant**:

    #define name replacement text

Any occurrence of the "name" will be replaced by the "replacement text".

    #define LOWER 0
    #define UPPER 300
    #define STEP 20
    // ...
    for (fahr = LOWER; fahr <= UPPER; fahr = fahr + STEP)
      printf("%3d %6.1f\n", fahr, (5.0/9.0)*(fahr-32));

At its simplest, input/output functions use `getchar()` to get a character from
the keyboard and `putchar(c)` to print a character to the string.  `getchar`
returns a single character typed from the keyboard.  You can use this to write
a program to copy files:

    int c = getchar();
    while (c != EOF) {
      putchar(c);
      c = getchar();
    }

Notice that `getchar` returns the distinctive EOF value for "end of file".  We
also use the `int` type instead of `char` because EOF might be too big of a
value to store into a `char`.

Arrays in C are declared like this:

    int ndigit[10];  // an array of 10 integers

Subscripts start with zero, so `ndigit[0]` accesses the first element.

`if/else` statements are formatted like:

    if (condition1)
      statement1
    else if (condition2)
      statement2
    else
      statement3

Multi-line statements can be surrounded with brackets.  The `else if` and `else`
are both optional.

Let's write a `power` function that computes `x^y`:

    int power(int m, int n);

    // main function goes here

    int power(int base, int n) {
      int p = 1;
      for (int i = 1; i <= n; ++i)
        p = p * base;
      return p;
    }

First, we declared our function at the top.  This announces it like a variable
so we can use it without the compiler complaining.  It's called a **function
prototype**.  Parameter names don't need to match, in fact they're optional.
But they're still good for documentation.

A function definition has the following format:

    return-type function-name(parameter declarations, if any) {
      declarations
      statements
    }

**Parameters** are variables named in the parenthesized list of a function,
sometimes called **formal arguments** or **actual arguments**.  These parameters
are passed by value - they're local copies.

The most common use for arrays in C are character arrays or strings.  They're
terminated with a null byte or `\0`:

    h e l l o \n \0
    0 1 2 3 4  5  6

The `%s` format for `printf` expects this null byte to be there and uses it
for formatting strings.  When arrays are parameters in functions, they're passed
by reference.  If you modify it, you modify the original variable.

So far, we've used **local variables** which exist when the function is called
and disappears when the function exits.  They're also called **automatic
variables**.  These must be explicitly set or else they will contain garbage.

C also supports **external variables**, globally accessible by any functions.
These retain their values even when functions exit.  They must be defined
outside a function and declared inside each function that wants to access it
(either implicitly or explicitly).

    #include <stdio.h>
    int global_counter;

    main() {
      extern int global_counter;
      // ...
    }

The keyword `extern` is used to declare it as external to the function.  The
prefix may be omitted if the variable was declared in the same file before
usage.

Types, Operators, and Expressions
=================================

Variable names are made of letters, digits, or hyphens.  The first character
must be a letter.

The data types available are `char`, `int`, `float`, and `double`.  There
are also `short`, `long`, `signed`, and `unsigned` qualifiers.  See `<limits.h>`
or `<float.h>` for actual sizes and limits.

    short int sh; // int is optional
    long int counter;

You've seen integer constants before, they're just numbers.  Float constants use
a decimal point or exponent (`1e-2`).  Constants can be suffixed with `u` or `U`
for unsigned.  They can also be suffixed with `l` or `L` for long.  For example:
`123ul`.

A leading zero specifies octal, a leading `0x` specifies hexadecimal.

A **character constant** is just an integer written as one character in single
quotes like `'x'`.  It's stored as a numerical value according to the machine's
character set.

String constants are concatenated at compile time:

    "hello, " "world" == "hello, world"

**Enumeration constants** are lists of constant integers:

    enum boolean { NO, YES };

The first element has value 0, following elements are incremented by 1.  They
can receive explicit values:

    enum escapes { NEWLINE = '\n', TAB = '\t' };

We've already covered variable declaration.  You can also initialize variables
at the time you declare it.  Automatic variables without explicit initialization
have undefined values.  External and static variables are initialized to zero
by default.

The binary arithmetic operators are: `+`, `-`, `*`, `/`, and `%`.  The logical
operators are: `>`, `>=`, `<`, `<=`, `==`, `!=`, `&&`, and `||`.

There are also unary increment and decrement operators: `++` and `--`.

C sometimes does implicit type conversions from narrower to wider types, like
ints to floats.  chars may be used in arithmetic expressions, since they're
just narrow ints.  Logic expressions may use integers, true just means non-zero.

Longer integers are converted to shorter ones by dropping excess high-order
bits.

Explicit type conversion can be coerced with a unary **type cast**:

    (type name) expression

C provides operators for bit manipulation: `&` AND, `|` OR, `^` exclusive OR, 
`<<` left shift, `>>` right shift, `~` one's complement.

AND is often used to mask off bits, while OR is used to turn bits on.  The shift
operators will shift bits by a given number of positions.  `x << 2` shifts x by
two positions.  New bits are zero.

The **assignment operator** is an abbreviated way to combine binary operators
with an assignment.  `i = i + 2` becomes `i += 2` and works with most binary
operators.

Conditional expressions may also use the **ternary operator** `?:`:

    expr1 ? expr2 : expr3

Control Flow
============

An **expression** becomes a **statement** when it's followed by a semicolon.
The semicolon is a statement terminator.  Braces are used to group declarations
and statements together into a compound statement or **block**, syntactically
equivalent to a single statement.

We've already seen the if-else statement in earlier examples.  Conditional
expressions evaluate any non-zero values as true.

    if (condition)
      statements
    else if (condition)
      statements
    else
      statements

There's also the **switch** statement:

    switch (expression) {
      case const-expr: statements
      case const-expr: statements
      default: statements
    }

Constant expressions need to be an integer value (remember that characters are
integer values also).

C supports while loops:

    while (expression)
      statement
 
And for loops:

    for (expr; expr; expr)
      statement

The first expression is the initializer, the second is the condition to stop,
and the third is the step expression.  Each are optional.

There's also the **do-while loop**:

    do
      statement
    while (expression)

It's valuable when the statement needs to be executed at least once.  The
keyword **break** allows you to exit early from any loops or switch statement.
The keyword **continue** exits the current block, but continues to the next
iteration of the loop.

For completeness, the authors mention the **goto statement**.  It's not normally
used in practice, but is provided:

    for ( ... )
      for ( ... ) {
        if (disaster)
          goto error:
      }
    ...
    error:
      /* clean up the mess */

The label follows the same naming convention as variables and has the scope of a
function.

Functions and Program Structure
===============================

Functions break a large program into smaller chunks.  A function needs to be
defined or declared as a prototype before being used in a file.  We've already
seen what a function definition looks like:

    return-type function-name(argument declarations) {
      declarations and statements
    }

Many parts are optional, here's the minimal function:

    dummy() {}

The **return statement** is used to return a value from a function to its
caller:

    return expression;

There's an implicit conversion to the return type if needed.  Parentheses around
the expression are optional.

Compiling multiple source files will create object files.  If you need to, you
can recompile a single file to produce an executable, just pass along the
generated object files:

    % cc main.c getline.c strindex.c
    % cc main.c getline.o strindex.o
    % ./a.out

The default return type of a function is an integer.  If it's something else, it
needs to be explicitly declared.  Function declarations can even be made local
to another function:

    main() {
      double atof(char []);
    }

Functions are considered external, you cannot have a function local to another
one.  Variables are external if it's defined outside of any function.

The **scope** of a variable is the part of the program in which it may be
used.  The scope of an external variable or function is from the point it's
declared (or defined) to the end of the file being compiled.  If it's being used
before it's defined, an `extern` declaration is required.

    extern int sp;
    extern double val[];

The lines above declare the variables for usage, but they do not reserve any
storage space for them.  There should be only one definition among all files,
other files contain the `extern` declaration to access it.

A **header file** uses the file suffix `.h` and usually contains declarations
for functions or variables for central access.  For example, a `calc.h` header
file might look like:

    #define NUMBER '0'

    void push(double);
    double pop(void);
    int getop(char []);
    int getch(void);
    void ungetch(int);

Whereas the implementation file will look like:

    #include <stdio.h>
    #include <ctype.h>
    #include "calc.h"

    int getop() {
      ...
    }

The **static** declaration limits the scope of the variable to the source file
it's in during compilation.  External static means it's external so multiple
functions can access it, but it's still limited in scope to a single source
file.  It can be applied to functions also.

    static char buf[BUFSIZE];
    static int bufp = 0;
    static ing getch(void) { ... }

It can also be applied to local variables within a function.  Unlike automatics,
they remain in existence after the end of the function.

The **register** declaration tells the compiler that the variable is in heavy
use and should consider storing it in the CPU register:

    register int x;

The scope of a variable can be limited by any curly brackets, such as following
if statements (not just functions).  You can override a variable name when the
program name enters a new scope:

    int x;
    f(double x) { ... }

Automatic and register variables have garbage values for implicit initialization.
External and static variables are initialized to zero.  You can initialize an
array using curly brackets:

    int days[] = {31, 28, 31, 30};

C functions may be used recursively (calling itself).  Be sure to declare the
function so it can call itself.

The C preprocessor is an initial step in compilation.  We've used this before
with `#include` and `#define`.  For file inclusion, use quotes to include a
relative file and use angle brackets for system libraries. 

Macros can be defined with arguments:

    #define max(A, B) ((A) > B) ? (A) : (B))

They can be undefined with `#undef`.  If a parameter name is preceded by a
`#` in the replacement text, the preprocessor will use a quoted string.

Some other useful preprocessor statements are `#if`, `#elif`, `#else`, and
`#endif`.  This can be used for conditional inclusion:

    #if !defined(HDR)
    #define HDR
    #endif

Pointers and Arrays
===================

Structures
==========

Input and Output
================

The UNIX System Interface
=========================
