# Imposter's Handbook

Notes for [Imposter's Handbook](https://bigmachine.io/products/the-imposters-handbook/).

# Computation

## Natural Computation

Bernoulli's Weak Law of Large Numbers - the more you observe the results of a set of seemingly
random events, the more the results will converge on some type of relationship or truth. Flip a coin
100 times, you'll have random results. Flip it many more times, it'll get close to fifty-fifty
distribution.

## What is Computation?

How do you compute the square root of 9447838939? Before calculators, way back in 200BC, people
wrote down mathematical calculation results in books called mathematical tables. These were prone
to errors, since they were assembled by human calculators.

Charles Babbage wanted to remove the human aspect, and in the 1820s created the Difference Engine.
It was a mechanical computer run by a steam engine. Using pulleys and gears, it computed simple
mathematical functions.

Inspired by Jacquard Loom's programmable loom, Babbage went on to create the Analytical Engine.
Looms used pins moving up and down to direct patterns in textiles. The Analytical Engine would do
something similar, except the end result would be punching holes into cards. The machine had memory,
could do conditional branching, loops, and work with variables and constants. Babbage was still
focused on mathematical calculations.

Ada Lovelace created the first algorithm for the Analytical Engine to compute Bernoulli numbers. She
recognized the potential for the machine to do more than simple calculations.

In the 1939, Alan Turing built the Bombe which helped compute daily settings for the Enigma encryption
device. This led to Tommy Flowers designing the Colossus, which led to the Colossus Mark 1, which
eventually led to the ENIAC -- the first electronic computer.

# Complexity

Complexity Theory deals with how difficult a given problem is.

## Simple Solutions And P Time

We measure complexity of an algorithm via time, as a scale compared to the inputs.

```
T = 2n
```

where `T` is the overall process time and `n` represents the number of inputs.

Mathematicians like to describe simple problems in a class of complexity called `P`. They would say
a problem is "in P", which means it's a simple problem. Things like sorting and searching arrays
can be solved in P.

## Hard Problems (Exp)

What about problems whose time is squared (or more)?

```
T = n^2
```

This is still considered in P time because the equation above is still a polynomial equation.

On the other hand, if the input is part of the exponent itself:

```
T = 2^n
```

A mathematician would call this an "exponentially complex" problem or that it's solvable in Exp time.

## All Solvable Problems (R)

`R` represents all the problems that are solvable in finite time. Any problems that we can solve
is classified in R. Note that `R` is a superset of `EXP` which is a superset of `P`.

## Infinitely Complex Problems Beyond R

There are problems that lie beyond R. The Halting Problem is a famous one, introduced by Alan Turing.
You wrote a program and want to know if it will stop/halt at some point, either by returning a value
or crashing. It's impossible to write a generalized program that determines if any other program
will halt.

This class of problem is classified as NP-Hard.

## Determinism, Nondeterminism, and Magical Guesses

In a deterministic system, we execute a routine and get a result. Based on that result, we execute
another routine and so-on until we arrive at a final answer.

In a non-deterministic system, we execute a routine and get a result. From that point we might
execute one of a series of next steps -- but we don't know which next step from the series will
be executed. It's decided at the time. We keep doing this until we get to our final answer.
Nondeterminism uses a series of random chance to figure out the answer.

A nondeterministic  algorithm can exhibit different behaviors on different runs, as opposed to the
deterministic algorithm which will always exhibit the same behavior.

A problem is classified as NP (nondeterministic polynomial time) if it is:

* solvable in exponential time
* verifiable in polynomial time
* also solvable in polynomial time by nondeterministic methods

## Does P = NP?

If a nondeterministic algorithm is ever developed to solve NP problems, then all problems in NP
become solvable in P time and it's said that P=NP. If an algorithm can never exist, P!=NP. We can't
make either claim right now.

## Reductions and NP

Certain problems can be reduced to be solvable. An optimization problem can be reduced to a decision
problem. For example: "what are the optimal pubs for optimal group happiness?". Instead of finding
the optimal solution, we can simply iterate through each pub and ask the group if they would be happy.

NP-Hard problems can be reduced to other problems in NP, but are not NP themselves.

NP-Complete problems are decision problems classifiable in NP. NP-Complete are both NP-Hard and NP.

The Halting Problem is beyond R -- solvable only with an infinite amount of time. It can be reduced
to a simple decision problem: will this program halt?

## NP-Complete and Decisions

The main goal of turning a complex problem into a decision problem is for verification.

The Boolean Satisfiability Problem (or SAT): determining if an interpretation exists that satisfies
a given Boolean formula. Can set set the variables of a boolean formula to values of TRUE/FALSE
and have the formula evaluate to TRUE?

At its core, all programs can be reduced to a large decision tree -- or a variation of SAT.

Most of what we do on a daily basis is deal with problems in NP.

## Classic NP-Complete Problems

In Richard Karp's paper, Reducibility Among Combinatorial Problems, showed that you can reduce
many NP problems to SAT in polynomial time. Some of these classic problems include:

* Knapsack - given a set of items, each with a weight and value, determine the number of each item
  to include in a collection so that the total weight is less than or equal to the given limit; and
  the total value is as large as possible.
* Bin Packing - variation of Knapsack, objects of different volumes must be packed into a finite
  number of bins or containers each of volume V in a way that minimizes the number of bins used.
* Traveling Salesman - given a list of cities and distances between each pair of cities, what is the
  shortest possible route that visits each city exactly once and returns to the origin city?

## Approximations and Laziness

Some solutions to these problems have algorithms that solve them in P time. These are approximations,
they're useful if you can tolerate the inexact answers.

# Lambda Calculus

## The Basics

Alonzo Church introduced Lambda Calculus in the 1930s. It's a simple notation for functions and
application. Functions and arguments are on par with one another.

A lambda is an anonymous function that can be thought of as a value. There are no numbers or types,
only functions. A function can be treated as a value.

## Some Rules

* there are only functions, nothing else (no data types, strings, numbers, etc...)
* you can substitute functions using variables
* you can reduce on function to another through application
* you can combine multiple terms to create a higher order function called a combinator

## Anatomy

```
λx.x
```

The Greek lambda denotes a lambda function. The first `x` is the argument to the function. The
final `x` is the body, it's segregated from the argument with a `.`. The equivalent in ES6 is:

```js
(x => x)
```

## Function Application

We can apply a value to the identity function (or I Combinator) above like this:

```
λx.x (y)
```

Which means "substitute all occurrences of x with y" and is called function application. In
JavaScript, this is:

```js
(x => x)(y)
```

Consider this function and application: `λx.y (z)`

Translated to JavaScript:

```js
let z = 3; // for clarity
function thing(x){return y}(z);
```

## Bound and Free Variables

In the above function, there's two variables: `x` and `y`. The `x` is a bound variable, which is
defined in the function. The `y` is a free variable.

## Substitution and Reduction

```
λx.x 3
λx[x:=3]
3
```

This is a substitution and reduction. `x[x:=3]` means replace all bound `x` with 3.

## Applying Multiple Values

Functions only have an arity of 1. You'll need to use currying to break a function with multiple
arguments into chained functions with an arity of 1:

```
λx.λy.t
```

The outer function takes an argument `x` but returns the function `λy.t`. It's similar to doing
something like this in JavaScript:

```js
(x => y => x + y)(2)(3);  // returns 5
```

## Order of Operations

The use of parentheses effects the order of operations. See how the below reduces:

```
λx.(λy.y x)
λx.(y[y:=x])
λx.x
```

But without the parenthesis, you'll get a different result:

```
λx.λy.y x
(λy.y)[x:=x]
λy.y
```

## Church Encoding

In early 1930s, Alonzo Church tried to represent constructs like conditional branches, loops, and
higher order functions for computation. He created representations for values and operations.

The Church encoding lets us use booleans, numbers, conditionals, loops, and combinators.

## Booleans

Represent `true` and `false` in Lambda Calculus like:

```
λx.λy.x
λx.λy.y
```

The JavaScript equivalent would be:

```js
let True = (x => y => x);
let False = (x => y => y);
True(true)(false) // returns first value, true
False(true)(false) // returns second value, false
```

The if, then, else conditional statement can be represented like:

```
λx.λy.λz.x y z
```

The JavaScript equivalent would be:

```js
let If = (x => y => z => x(y)(z));
If(True)("TRUE")("oops.."); // TRUE
If(False)("oops")("FALSE"); // FALSE
```

## Numbers

```
λf.λx.x = 0
λf.λx.f (x) = 1
λf.λx.f (f(x)) = 2
λf.λx.f (f(f(x))) = 3
```

The first line returns the identity function, it's the equivalent of: `f(y) = (f(x) = x)`. It
represents zero because there is no reduction being applied.

The second line is the application of the identity function, it represents one. Subsequent lines
accumulate.

The JavaScript equivalent:

```js
let calculate = f => f(x => x + 1)(0);
let zero = f => x => x;
let one = f => x => f(x);
let two = f => x => f(f(x));
let three = f => x => f(f(f(x)));
calculate(zero) // 0
calculate(one) // 1
calculate(two) // 2
calculate(three) // 3
```

## Combinators

A combinator is a high-order function that uses only function application and earlier defined
combinators to define a result from its arguments. Only lambda expressions and bound variables.

The Omega Combinator can be used to mimic recursion in a programming language:

```
λx.x (λx.x)
x[x:= λx.x]
λx.x

λx.xx (λx.xx)
x[x:=λx.xx]
λx.xx λx.xx
```

The Y Combinator is a recursive, fixed-point combinator that can be used for list iteration:

```
yf = f(yf) forall f
```

In Lambda Calculus:

```
Y=λf.(λx.f(xx))(λx.f(xx))
```

# Machinery

## Probability and The Theory of Forms

Around 400BC, Plato said the world we see/experience is only a partial representation of its true
form, or an abstraction. The real world is hidden from us, beyond our ability to fully perceive it.

The natural world appears to be random events, but a mathematician will take large amounts of these
events and see them converge to an apparent truth. In 1713, Jacob Bernoulli came up with the Law
of Large Numbers to prove this. If you flip a coin enough times, the distribution will come closer to
50/50.

## Markov Chains

Flipping a coin is a single event, where each event is independent of the result of the previous.
Does the law of large numbers still hold true for series of events, dependent on the previous?

Andrey Markov proved this to be true in the early 1900s. The Markov chain describes a set of states
with transition rules, as probabilities. States don't allow for a loop back.

## Finite State Machine

Sometimes called Finite State Automata. Similar to a Markov chain, it's usually represented as a
flowchart of states and transitions. Instead of probabilities for transitions, Finite State Machines
have actions that could include conditions. It's common to use input symbols for transitions too,
for example reading a string of characters where each character represents one input.

The FSM is an abstract, mathematical model of computation that either accepts or rejects. It can be
deterministic of non-deterministic. A non-deterministic machine can transition to the next state as
a result of random choice. If you run a non-deterministic program several times, it could produce
different results even with the same input.

FSM cannot store state outside the current state. There is no persistence or RAM. We can't calculate
digits of pi, perform map/reduce operations. But we can parse Regular Expressions.

## Pushdown Machine

A Pushdown Machine, or Pushdown Automata, is a FSM with a stack data structure that can be used to
store data. State transitions are decided by: input symbol, current state, and stack symbol.

The stack adds a lot of computing power. You can now sum numbers and do other math operations. But
there are limitations. For example, if the stack only have five spaces but we have ten states, you'll
run out of memory.

## Turing's Machine

In the mid 1930s, Alan Turing published his paper On Computable Numbers. He introduced a machine
with a read/write head and some tape. The tape has cells, each cell holds a simple symbol, and you
can move the tape under the head so the machine could read/write.

A machine can take another machine as its input, similar to functions taking functions as arguments.
You can write a machine to describe the number 4 (M4) and the number 6 (M6), then write a machine
that takes those two as inputs to multiply (MX). You can even write a machine that accepts the numbers
and the instructions of MX (a more general universal machine).

> If an algorithm is computable, a Turing Machine can compute it.

Was said by Turing. Similar to Church:

> All total functions are computable.

This became known as the Church-Turing Conjecture.

The Turing Machine has four parts:

1. set of symbols, defined in an alphabet or language the machine can understand (usually 0s and 1s)
2. infinitely long "tape" which contains a single row of symbols that can be anything
3. read/write head that reads from the tape and writes back to it
4. rules for reading/writing from the tape

If you create an instruction set capable of running on a Turing machine, it's "Turing Complete".
All that's needed is: conditional branching, loops, variables/memory.

## The Von Neumann Machine

Initially, computers were very specific devices. The hardware was created specifically to multiply
numbers. Another machine was made specifically to generate prime numbers. Turing and von Neumann
wanted to fix this. They decided the instructions could be part of the tape. This is what we call a
program today.

## ENIAC and EDVAC

In 1940s, JP Eckert and John Mauchly designed the ENIAC. It was Turing Complete and programmable,
but required programmers to physically move wires around. In 1943, they improved the design by
storing the instruction set as a program stored next to the data itself. This machine was called
the EDVAC.

Von Neumann took interest in the ENIAC and EDVAC. He wrote a paper about the project, detailing
the idea of a stored-program machine. But he left out Eckert and Mauchly's names on the paper. It
was soft published by a colleague, so stored programs are now attributed to Von Neumann (incorrectly).

## Von Neumann Architecture

Von Neumann's machine looks familiar, with parts like: CPU, Memory, input and output. Von Neumann
abstracted specific computers, so that they could be written as code. Machines now run within
machines. For example, a calculator runs in a browser which runs on an operating system.

# Big O

## A Super Simple First Step: O(1)

## Iterations and Order(n)

## The Not So Good Approach: O(n^2)

## Refining to Order(log n)

## Quick Logarithm Review

## Rethinking O(n^2) with O(n*log n)

## A Quick Break for Math Geeks

## Thinking in Big-O

## Space Complexity vs Time Complexity

# Data Structures

## Arrays

## Stack

## Queue

## Linked List

## Hash Table

## Dictionary

## Trees

## Binary Tree

## Heap

## Binary Search Tree

## Digital Tree (or Trie)

## Graphs

## So What?

# Algorithms: Simple

## Bubble Sort

## Merge Sort

## Quicksort

## Selection Sort

## Heap Sort

## Binary Search

## Graph Traversal

## Depth First Search

## Breadth First Search

# Algorithms: Advanced

## Dynamic Programming

## Origins

## Fibonacci

## Greedy Algorithms

## Bellman Ford

## Djikstra

# Compilation

## How a Compiler Works

## Lexical Analysis

## Parsing

## Semantic Analysis

## Lexical Scoping

## Optimization

## Code Gen

## LLVM

## GCC

## Garbage Collection

## Tracing

## Referencing Counting

## Compile-time

# Software Design Patterns

## Constructor

## Factory

## Builder

## Method Chaining

## Singleton

## Adapter

## Bridge

## Composite

## Decorator

## Facade

## Flyweight

## Command

## Mediator

## Observer

## State

## Strategy

## In The Real World

# Software Design Principles

## Coupling and cohesion

## Separation of Concerns

## YAGNI and DRY

## Tell, Don't Ask

## Law of Demeter (or: Principle of Least Knowledge)

## Dependency Injection

## Interface-based Programming

## Inversion of Control

# Functional Programming

## Immutability

## Formalizing Data with Structs

## Transforming Data

## Side Effects and Purity

## Currying

## Functors and Monads

# Databases

## First Normal Form (1NF): Atomic Values

## Second Normal Form (2NF): Columns Depend on a Single Primary Key

## Third Normal Form (3NF): Non-keys Describe The Key And Nothing Else

## OLAP And OLTP

## Extraction, Transformation, and Loading (ETL)

## Data Marts and Warehouses

## Analyzing a Data Mart

## Using an OLAP Cube

## Distributed Database Systems

## A Shift in Thinking

## CAP Theorem

## An Alternative to ACID: BASE

## Sharding

## Replication

## Big Data

## In The Real World

# Testing

## The Nuts and Bolts of TDD

## Just Start

## The Happy Path

## The Sad Path

## BDD

## Features, Scenarios, Expectations

## Given, When, Then

# Essential Unix

## Shell Scripts

## Make

## CRON
