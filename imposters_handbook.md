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

```js
const nums = [1,2,3,4,5];
const firstNumber = nums[0];
```

How complex was the lookup operation above? We had five inputs, but only required one operation to
look up the zero index. Even if the index or inputs change, an array look up will always be `O(1)`.

## Iterations and Order(n)

```js
const nums = [1,2,3,4,5];
let sum = 0;
for(let num of nums) {
  sum += num;
}
```

How many operations do we have per input? We iterate through each number and run an addition operation
on each one. This algorithm is linear scaling, or `O(n)`.

This algorithm could be simplified using Carl Friedrich Gauss's sum of series formula:

```
S = n(n+1)/2
```

Instead of performing an operation on each element in the array, we can just lookup the last number
(or get the size) and plug it into the formula. That's a static 3 operations, no matter the size
of the Array. But instead of `O(3)` we always refer to these static complexity to simply `O(1)`.

## The Not So Good Approach: O(n^2)

Let's try to determine if an Array has duplicates or not:

```js
const hasDuplicates = function(nums) {
  for (let i=0; i<nums.length; i++) {
    const thisNum = nums[i];
    for (let j=0; j<nums.length; j++) {
      if (j !== i) {
        const otherNum = nums[j];
        if (otherNum === thisNum) return true;
      }
    }
  }
  return false;
}
```

This algorithm iterates over the array exponentially (via the nested loop). This type of complexity
is `O(n^2)` or "Order n squared". While this is the simplest solution, we can improve performance wise.

## Refining to Order(log n)

Assumption 1: is the input sorted? For illustration purposes, let's say yes.

Assumption 2: can we use more storage than just the given array? Let's say yes.

Given these two assumptions, you can use a binary search. This type of algorithm is called divide
and conquer. A binary search works by dividing the input in two, discarding the half which does not
contain your search term, and then repeating until your search term is found.

## Quick Logarithm Review

Logarithms make working with exponents easier.

```
x ^ 3 = 8
```

Here it's easy to solve for `x`. What about:

```
2 ^ x = 512
```

This can be rewritten as a logarithm: `log2(512) = x` or "log base 2 of 512 equals x". The default
base is 10, which is written out like `log(100) = x` "or how many times do you multiply 10 by itself
to arrive at 100? The answer is 2.

For the binary search, we're splitting the input in half, which is log base 2. The complexity for
the binary search algorithm is `O(log n)`.

## Rethinking O(n^2) with O(n*log n)

Instead of doing a linear search for each item, we can simply do a binary search. We'll still need
to iterate through each input though:

```js
for (let num of nums) {
  if(searchFor(nums, num)) {
    return true;
  }
}
return false;
```

This algorithm is `O(n*log n)` since the nested operation is `O(log n)`.

What if we're searching for a guaranteed duplicate in a range of numbers? For example, the input
will always be 1 to N, where one of the numbers are duplicated. In this case, we can use the Gauss
formula to get the sum of the series. Then get the actual sum (an `O(n)` operation). Then simply
subtract the non-duplicate sum from the actual sum. This algorithm is `O(n)`.

## A Quick Break for Math Geeks

Check out the [Numberphile](https://www.youtube.com/user/numberphile) YouTube channel for interesting
math problems.

## Thinking in Big-O

Some patterns we see between data structures and Big-O:

* Array and Hash access is always `O(1)`
* List iterations are `O(n)`
* Nested loops of the same collection are at least `O(n^2)`
* Divide and conquer algorithms are `O(log n)`
* Iterations that use divide and conquer are `O(n*log n)`
* If a nested loop has to be added per input, it's `O(n!)` (find another algorithm)

## Space Complexity vs Time Complexity

We've only been dealing with time complexity, but space complexity can be measured in the same terms.
How much does space usage scale according to input?

# Data Structures

## Arrays

Non-dynamic arrays require a size upfront. Arrays are allocated in adjacent blocks on memory when
created. If you need to add more items than capacity allows, a resizing operation occurs that's
expensive. Languages like JavaScript support dynamically sized Arrays.

Arrays are a simple data structure, with quick `O(1)` access when you know which index you need.

## Stack

With stacks: you can't access items randomly using an index, you can only add/retrieve items from
the top, and there are three operations (push/pop/peek).

The nature of a stack is Last In, First Out or LIFO.

Stacks work perfectly with algorithms that require: reversing, traversing a graph/tree, checking an
opening/closing structure (such as balanced parentheses).

## Queue

A queue is like an Array with some additional rules: can't access values randomly using an index,
you can only add values in one end and retrieve them from the other, have two operations (enqueue
and dequeue).

The nature of a queue is First In, First Out or FIFO.

A single queue can be implemented with two stacks. The enqueue operation would be a push to stack A.
The dequeue operation: pop off of stack B, if stack B is already empty -- pop off of stack A and onto
B until A is empty first.

## Linked List

There are two types of linked lists: singly and doubly linked. A singly linked list is made up of
nodes in memory. A node has: value you want to store and a pointer to the next node. A pointer to
null means it's the last node, or tail. The first node is called the head.

A doubly linked list node includes an additional pointer to the previous node.

The size of a linked list is dynamic, since adding another node is as simple as updating a few
pointers. You can even insert values in the middle of the list. Same for deleting.

The downside is that you must always traverse the list to get the item you want, which is an `O(n)`
operation.

## Hash Table

A hash table stores data using a computed index, the result of which is an index. This computation
is called the hash function. Data is then stored at this index location in the table.

Hash table reads/writes are `O(1)`, since you simply run the hash function on the key to look it up.
In the real-world, you may get duplicate keys called collisions. This happens when the data size is
large, then the read/write time can be reduced to `O(n/k)` where k is the size of the hash table or
n is the size of the data set.

Hash tables can implement "open addressing". It resolves a collision by finding the next available
slot and dropping it there. Look ups may require scanning after hashing. Hash tables can also use
"separate chaining", which solves collisions by using a linked list in each slot.

## Dictionary

Dictionaries are hash tables with unique keys for accessing values. The key is guaranteed to be
unique. Dictionaries provide `O(1)` access to any element. Dictionaries can be a bit larger and use
more space.

Dictionaries in a language may be implemented as hash tables under the hood.

## Trees

Trees are a graph data structure with a hierarchical structure. Trees have a single root node, with
children descending from it. The computer's file system is an example tree.

## Binary Tree

A binary tree is a specialized tree where each node can have 0, 1, or 2 children nodes. Each node
can only have one parent (except the root node which has zero).

Each level of tree represents a logarithmic value. The root is `log2(0) = 1`, which has 1 node. The
next level is `log2(1) = 2`, which has 2 nodes. The next is `log2(2) = 4`, which has 4 nodes.

## Heap

A heap is an inverted tree that stores data according to a heap property. Every child node belongs
to a parent node that has a greater priority (or value) for a max heap. The inverse is true for
a min heap.

They're useful for comparative operations, such as "I need all people in this heap to be age greater
than 23".

Heaps are used in data storage, graphing algorithms, priority queues, and sorting algorithms. The
primary advantage is performance, as they're fast for searching since they are pre-sorted.

## Binary Search Tree

A binary search tree is a heap that's organized based on the values of the nodes, but there is also
a left-to-right priority. The rules are:

1. all child nodes in the tree to the right of a root node must be greater than the current node
2. all child nodes in the tree to the left must be less than the current node
3. a node can only have two children

It becomes very easy to search for an item. The disadvantage is that insertion/deletion can be time
consuming when the tree grows. Re-ordering may be necessary.

## Digital Tree (or Trie)

The term comes from retrieval, but is pronounced "try". It can outperform a binary search tree or
hash table. Tries let you know if a term exists in a body of text.

The advantages of tries are speed and space. Finding a word in a trie is `O(m)` where m is the
length of the word you're trying to find.

Common prefixes are reused so space usage is kept to a minimum. This is how auto completion usually
works.

## Graphs

Graphs are a set of values related in a pair-wise fashion. Nodes are called a "vertex" which are
connected by an "edge".

Graphs can be directed (if edges flow in one direction) or undirected. They can also be weighted
or unweighted. When vertices are connected by edges in a circular fashion, it's called a cycle.
If a graph doesn't have a cycle, it's called a directed acyclic graph. All trees and linked lists
have been directed acyclic graphs.

# Algorithms: Simple

## Bubble Sort

The bubble sort iterates through a list, comparing pairs of values. If the left side is larger
than the right side, it swaps. Otherwise, it keeps the order the same. By the end of the first
iteration, the largest number will be the last item of the list. Then we repeat this swapping again
until the next largest number will be the second-to-last item. Repeat until the list is sorted.

The complexity for this sort is `O(n^2)`.

## Merge Sort

One of the most efficient ways to sort a list and will typically perform well. Time complexity of
`O(n*log n)`.

Merge Sort splits all elements in a list down to smaller, two element lists which can be sorted
by one pass. Then it recursively merges the smaller lists back into a larger list, ordering as you
go.

This is faster than the Bubble Sort because it divides and conquers the list. When merging two
sorted lists, you'll only need to compare the left-most number since you know it's the smallest.

## Quicksort

Quicksort is a divide and conquer algorithm that uses pivoting to break the list into smaller chunks.
In the worst case, quicksort is `O(n^2)` when the pivot is the smallest or largest in the list. In
the best case, it's `O(n*log n)`. The algorithm works like this:

1. start with a set of elements
2. pick a pivot (last element by convention, but can be random)
3. partition list so all items less than pivot value is to the left, all items greater than pivot
   value are moved to right partition
4. when you're done partitioning, the pivot will be in its final position
5. pick another pivot at random, and repeat steps 3-5 until all pivots are in their final position

We can select our pivot more intelligently. This requires an initial step to calculate the median
value, then make that the pivot. The performance will usually beat the merge sort because sorting
happens in place (less space complexity).

## Selection Sort

Selection sort is a simple algorithm. Scan a list of items for the smallest element, then swap that
element for the first item in the list. Continue until all remaining elements are sorted.

Complexity of this sort is `O(n^2)` at worst (when list is pre-sorted in the reverse order you want)
to `O(1)` swaps when the list is already sorted. Average performance is `O(n^2)` comparisons and
`O(n)` swaps.

## Heap Sort

It's similar to a selection sort, but uses a heap to do so. A Heap sort is `O(n*log n)`.

## Binary Search

This is a search algorithm for pre-sorted lists (or a lookup in a binary tree). It's a divide and
conquer algorithm:

1. split the list in two, right down the middle
2. if the rightmost number (also the largest) on the left list is less than our number, remove that
   partition completely
3. otherwise remove the right partition
4. repeat the split and removal until you find your value

## Graph Traversal

We've been traversing lists so far, but how to we search throughout a graph? You can use recursion
or iteration. While recursion can be simpler, it allocates memory onto the stack and can result
in crashing with a stack overflow error.

## Depth First Search

A depth first search algorithm will go as deep as possible into a graph until it hits a leaf node.
Once it hits a leaf, it backtracks back up and to the right until the tree traversal is done.

## Breadth First Search

A breadth first search algorithm will search nodes in order of their levels. So the root node is
first, followed by all of the root node's children, followed by all the root node's grandchildren,
and so on.

# Algorithms: Advanced

## Dynamic Programming

Dynamic programming solves optimization problems by dividing it into sub-problems and using
memoization. The problem must be: an optimization problem, dividable into sub-problems, have an
optimal substructure, reducible to P time through memoization.

## Fibonacci

The Fibonacci sequence is a series of numbers where the next number is the sum of the two preceding
numbers: 1, 1, 2, 3, 5, 8, etc...

If you divide each successive number by itself (5/3 or 8/5) you converge on the number phi, which
is the golden ratio.

To print the Fibonacci sequence up to a given position:

```js
const calculateFibAt = (n) => {
  if (n < 2) {
    return n;
  } else {
    return calculateFibAt(n-2) + calculateFibAt(n-1);
  }
}
for (var i = 0; i <= 10; i++) {
  console.log(calculateFibAt(i));
}
```

The time complexity for this algorithm is `O(2^n)`, an exponentially complex solution. This can be
sped up with some memoization:

```js
var fibFaster = function(n) {
  var sequence = [0,1];
  for (var i=2; i<=n; i++) {
    sequence.push(sequence[i-2] + sequence[i-1]);
  }
  return sequence;
}
console.log(fibFaster(10));
```

The time complexity here is `O(n)`. The space complexity is also `O(n)`.

## Greedy Algorithms

Greedy algorithms follow a heuristic of making the locally optimal choice at each stage with the
hope of finding a global optimum.

## Bellman Ford

Given a graph with weighted wedges, how do you find the shortest path between two vertices?

The Bellman Ford algorithm can do that. It can accommodate negative edges, which Djikstra cannot.
It's a dynamic programming algorithm that uses memoization. To use Bellman Ford:

1. we're going to measure the cost of going from node S to E, getting the lowest cost to each nodes
   A-D along the way
2. make a key/value data structure, the key being the node and the value being the cost
3. set initial costs/values to infinity (S to S should be set to zero)
4. traverse through the graph, memoizing the lowest cost into our key/value table as we go
5. repeat traversal through every possible path (nodes can be visited more than once)

The complexity for this algorithm is `O(n^2)`.

## Djikstra

Dijkstra doesn't work with negative edge weights. It also only visits each vertex once. The next
vertex is chosen with these rules:

* it must not have been visited previously
* it has the smallest cost of the remaining unvisited vertices

# Compilation

## How a Compiler Works

A compiler is a program that reads code you write and generates something that the runtime engine
can execute. The steps of compilation are: lexical analysis, parsing, semantic analysis,
optimization, code generation.

## Lexical Analysis

This step analyzes the code that's passed to the compiler and splits it into tokens. When you read
a sentence, you tokenize it into words by splitting them via whitespace and punctuation. Each token
is then labelled:

```js
if (x == 10) { console.log("Hello") }
```

The lexer will generate pseudo code tokens:

```
{keyword, "if"}
{paren, }
{variable, "x"}
{operator, "=="}
{number, "10"}
{left-brace, }
```

## Parsing

The parser takes over and applies the tokens to the formalized grammar. It turns tokens into
language structures.

```js
if (
  x == 10 // relation -> predicate
) {
  console.log("Hello") // call -> then statement
}
/* if then */
```

## Semantic Analysis

The compiler tries to figure out what you're trying to do.

```js
var x = 12;
var squareIt = function(x) {
  return x * x;
}
y = squareIt(x);
console.log(y);
```

The compiler figures out: what will y evaluate to?

## Lexical Scoping

The compiler figures out the scope of variables.

```js
if (1 === 1) {
  var x = 12;
}
console.log(x); // 12
```

Languages have different rules for lexical scoping. In JavaScript, variable can be hoisted up to
the function block they're declared in.

## Optimization

This is typically the longest step. Compiler optimization produces faster and more efficient code.
Some common optimizations include renaming variables to be shorter or changing expressions and
statements to their optimized forms or replacing variables with literals.

Another example is, if a compiler sees the expression: `number * 0` -- it just replaces it with zero.

## Code Gen

Some compilers will generate an intermediate language output. Java, for example, will generate
instructions for its virtual machine which will later compile it down to machine-level code.
C# does something similar. When it's first run, the virtual machine will compile it down to native
code. Subsequent runs will use that native code, skipping compilation. This is called JIT or Just
In Time compilation.

Dynamic languages like Ruby, JavaScript, Python use an interpreter. Ruby reads a file, does all the
compilation steps above, then executes as needed. This is not as fast as native code.

## LLVM

LLVM, low level virtual machine, is a collection of modular/reusable compiler toolchain technologies.
You can use LLVM to build your own compiler. CLANG is an LLVM-based compiler for C.

## GCC

GCC, GNU Compiler Collection, is another compiler toolchain that provides compilers for C, C++,
Java, Go, and more. The main differences are licensing (GCC uses GPL and LLVM uses MIT/BSD) and
sometimes speed.

## Garbage Collection

Garbage collection cleans up the heap in memory. It's used in high level languages like Java
or JavaScript. Some languages require manual garbage collection, like C.

Garbage collection requires overhead and can slow things down. It's undecidable - like the Halting
Problem, we don't know if a process will complete so we cannot know if we can clear all memory.

## Tracing

Tracing is the most widely used method of garbage collection. When you create an object on the
heap, there's a pointer to it that exists on the stack. The garbage collector takes a look at root
objects and their references, tracing those through the stack and heap. Objects that aren't traceable
will be deallocated.

## Referencing Counting

A count of references are made for each object. When the count goes to zero, the object is deallocated.
This is how Objective-C's ARC works.

## Compile-time Garbage Collection

The XCode compiler analyzes code written and decides the memory use upfront. It uses strong, weak,
and unowned object references. So garbage collection is done at compile-time, freeing up the overhead
of runtime.

# Software Design Patterns

Design Patterns are a controversial topic. From one perspective: we waste precious time on worthless
trivia. From another, we act disciplined and build for the future.

## Constructor

Creates an instance of a class using the language's built-in constructor.

```js
var Thing = function() {
  // body
}
var thing = new Thing();
```

## Factory

When instantiating an object gets complex and we want to abstract it to provide a friendlier
interface. For example, if a Customer constructor takes many arguments: id, name, email, status,
order list, etc... You might create a `fromDefaults()` class method that creates a customer. You can
pull that class method out into another class, whose sole job is to create customers:

```
public class CustomerFactory {
  public Customer fromDefaults() {
    var customer = new Customer(...);
    // ...
    return customer;
  }

  public Customer fromExisting(...) { /* ... */ }
}
```

You can even use the Abstract Factory Pattern to return different types, inherited from Customer.

## Builder

With extremely complex constructors, you may want to use a Builder. Builders are a specialized
factory, that uses method calls to create an object. This works great with immutable objects, since
a builder can just accumulate attributes and return an immutable object at the end:

```
var naiveBuilder = new NaiveStringBuilder();
naiveBuilder.append("this");
naiveBuilder.append("could be");
naiveBuilder.append("very long");
naiveBuilder.append("and blow up");
naiveBuilder.append("your program...");
var result = naiveBuilder.toString();
```

## Method Chaining

A method can return `self` or `this` so that the caller can continue chaining calls. This works well
with the builder pattern above:

```
class NaiveStringBuilder {
  public NaiveStringBuilder append(String str) {
    // ...
    return this;
  }
}

naiveBuilder.append("this")
  .("could be")
  .("very long")
  .toString();
```

## Singleton

A Singleton class only allows one instance of itself. Here's a naive implementation that doesn't
take into account threading:

```
class SingleThing {
  private static SingleThing _instance;

  protected SingleThing () { } // disallow calling constructor directly

  public static SingleThing instance() {
    if (_instance == null) {
      _instance = new SingleThing();
    }
    return _instance;
  }
]
```

## Adapter

Adapters make one interface work with another. ORMs are adapters, which abstract a query interface
to work with different database engines. For example, you'll use ActiveRecord in Ruby as the
interface in your application. But ActiveRecord will connect with an adapter under the hood, switching
adapters depending on if you use PostgreSQL or SQLite3.

## Bridge

Say you have a class hierarchy of shapes. `Shape` is your abstract class and you have two concrete
classes: `Circle` and `Square`. Now you want to add colors, but to add that the number of concrete
classes will grow exponentially: `RedCircle`, `RedSquare`, `BlueCircle`, `BlueSquare`.

The Bridge pattern solves this by switching inheritance to object composition. Instead create a
separate `Color` abstract class with `Blue` and `Red` concrete classes. Then update `Shape` to contain
a color (object composition). Now you can keep your original Circle/Square concrete classes.

We're building a "bridge" between Shape and Color.

The GoF pattern says we're building a bridge between abstraction and implementation (this does NOT
refer to abstract vs concrete classes). The abstraction class is the clean interface which delegates
to the implementation for complex work. For example: the GUI is the abstraction and the operating
system API is the implementation layer. With a bridge, you can have several GUIs and several OS APIs
without exponentially creating new classes.

## Composite

Compose objects into tree structures then work with these structures as if they were individual
objects.

For example, your program deals with packaging products into boxes. A `Box` contains one or more
`Product`s OR other `Box`es. Eventually, everything unwraps to a product.

In this case, the root node could be the outermost box. All leaf nodes are products. In-between
are other boxes. The top-level box can calculate its price by asking for the price of all of its
children. A product simply returns its price. A box recursively asks for its contents' prices. The
box/product should have a common interface.

The Composite pattern lets you run a behavior recursively over all components of the tree.

## Decorator

This pattern adds behavior to an object by wrapping it, think of it as Russian Dolls. For example,
you have a `Notification` core class. But you wrap it (by extending it) to create `SMSNotification`
or `FacebookNotification` or `SlackNotification`. Then you can wrap it further with more specialized
notifications.

Inheritance is a good way to wrap core objects, but you can also wrap via composition. A decorator
simply responds to the same interface as its wrapped object.

Decorators can also wrap objects at runtime. You can structure your business logic into layers and
compose new objects by decorating previous ones.

## Facade

Facades hide implementation details so clients don't have to think about it.

Imagine you're using a sophisticated API, dealing with many complex objects. But your application
has one specific task. You can hide that logic behind a Facade, and only expose a single method
on a single class to your application.

## Flyweight

Flyweight lets you fit more objects into memory by sharing common parts of state between multiple
objects.

Objects have intrinsic state (lives within the object, other objects can only read but not change it).
The rest is extrinsic state (other objects can modify). Intrinsic state can be re-used among multiple
objects, especially because they don't often change.

## Command

This pattern formalizes requests from one API to the next. It turns a request into a stand-alone
object that contains all necessary information.

For example, your GUI application initially had action code implemented in UI classes. When you
click the "Save Button" it executes the save action. But then you added a MenuItem that has the same
action. And then a keyboard shortcut.

You can extract the action into a `SaveCommand` class. Then refactor the UI classes to use that class
when triggered. The next step would be to have multiple commands implement the same interface.

## Mediator

The Mediator pattern reduces chaotic dependencies between many objects. Objects collaborate only
through a mediator object.

For example, in a GUI some form objects may want to communicate with each other. If you click one
button, another one should be disabled. Or if you type in a field, a checkbox will automatically
change state.

Instead of having the form objects communicate directly with each other, send a message to a
mediator. Perhaps the parent dialog all the form elements are on.

Aircraft pilots don't talk to each other directly when deciding who gets to land first. They talk to
the control tower.

## Observer

This pattern facilitates event based programming. Multiple objects can subscribe to events that
are happening and be notified when they occur. A callback can be executed on notification.

## State

The State pattern lets an object change its behavior when its internal state changes. It's closely
related to the Finite-State Machine.

For example, a `Document` can only be in one of several states at any given time: `Draft`, `Published`,
or `Archived`. Often times, you'll have internal logic that does conditional branching on the context
(or state). Instead, you can create a hierarchy of class where each concrete class is based on one
of the state: `DraftDocument`, `PublishedDocument`, `ArchivedDocument`.

## Strategy

The Strategy pattern lets you define a family of algorithms and put them each in a separate class,
but have their objects be interchangeable.

For example, a family of algorithms might be how to travel. Exact implementations can be `BikeTravel`,
`WalkTravel`, `BusTravel`, or `FlightTravel`. These are all interchangeable by the client.

# Software Design Principles

## Coupling and cohesion

Cohesion refers to how related are the functions of each module or class. Do the ideas bond together
logically? For example, is your user-related code in the `User` class?

Coupling is kind of the opposite. When two objects are coupled, they're too dependent on each other.
For example, if a `Membership` instance always creates a `User` instance, they're said to be
tightly coupled.

You want high cohesion with low coupling. Classes/modules should isolate ideas and try not to rely
on each other too much.

## Separation of Concerns

Slice up aspects of your application so they don't overlap. They can be sliced up by horizontal
concerns: user interface, database, business logic. Or even abstract ideas: authentication, logging,
cryptography.

Ruby on Rails famously suggested the Model View Controller approach as a separation of concerns,
which decoupled data access and business logic from HTML.

## YAGNI and DRY

YAGNI - you ain't gonna need it. Developers can prematurely optimize, in terms of building features
that they probably won't need. Don't fall into this trap. Wait until it's necessary to build out
a feature.

DRY - don't repeat yourself. Try to have a single source of truth for logic and data.

## Tell, Don't Ask

OOP design works well if you follow the principle of "Tell, Don't Ask". When using another object,
the caller should tell the object what to do instead of asking. An example anti-pattern:

```rb
class Membership
  def deactivate
    if user.is_active?
      user.active = false
      user.save!
    elsif user.is_suspended?
      user.suspend = false
      user.save!
    end
  end
end
```

In the case above, we're constantly asking the `User` instance about its status and then performing
action based on that. Instead, all of this logic should simply be moved to the `User` class, the
`Membership` class should tell the user what to do.

## Law of Demeter (or: Principle of Least Knowledge)

You shouldn't have to reach through one object to get to another. An example anti-pattern:

```rb
db.user.membership.deactivate!
```

The chain of calls violate the Law of Demeter. You punch a hole through abstraction and introduce
extremely tight coupling.

## Dependency Injection

Send dependencies that a class needs through its constructor. This loosens dependencies and inverts
control, so the caller has more say into which concrete classes to use.

```rb
class Membership
  def initialize
    @user = User.new
  end
end # can be re-written as
class Membership
  def initialize(user)
    @user = user
  end
end
```

## Interface-based Programming

Instead of relying purely on a class type, we can do type checking based on an object's interface.
In Java and C#, you can describe an object's behavior with `Interface`. In Swift and Elixir, this
is done via a `Protocol`. This is similar to dynamic duck typing, where it doesn't matter if the
object isn't a type of Duck -- as long as it quacks.

## Inversion of Control

Dependency Injection is a type of inversion of control. IoC just means giving more power to the
caller instead of the implementer. The caller is responsible for creating and injecting all of the
dependencies that the class will use.

# Functional Programming

## Immutability

Immutability is a core feature of functional languages. Functions cannot change state. They will
always return the same value given the same input.

In the case of data structures, a function will create a new object with updated state. For example:

```elixir
friend = %{name: "Clara"}
friend = Map.put(friend, :name, "Mike")
IO.inspect friend #%{name: "Mike"}
```

In this case, an entire new map is returned by `Map.put`. This isn't horrible for memory because
Elixir uses a pointer under the covers. Idiomatic Elixir also includes pipes:

```elixir
%{name: "Clara"} |> Map.put(:name, "Mike") |> IO.inspect
```

## Formalizing Data with Structs

Structs provide a foundation for data, similar to classes in object oriented languages:

```elixir
defmodule Friend do
  defstruct name: "Clara", age: 0
end
defmodule Immutability do
  def change_name(friend, new_name) do
    Map.put(friend, :name, new_name)
  end
  def get_friend do
    %Friend{}
  end
end
Immutability.get_friend |> IO.inspect
```

## Transforming Data

Transforming data using idiomatic functional programming works very well. Some rules:

1. transform data by passing data through a set of functions
2. smaller functions are better
3. treat functions the same as values (eg you can pass callback functions as arguments)

This works really well with Ruby's enumerable methods:

```ruby
list_of_numbers
  .map { |n| n + 1 }
  .select { |n| n % 2 == 0 }
  .reduce(0) { |a,b| a + b }
```

## Side Effects and Purity

The more side effects a function has (working with database or stdout), the less pure your function
is considered. Purity refers to the level of interaction any code has with the outside world.

Functional programmers like purity. It's easier to test pure function calls without side effects.

## Currying

Currying is the act of using smaller, single arity functions in a chain rather than a larger function
with multiple/complex arguments.

```js
const dateNight = (who, what, where) => {
  return `Out with ${who} having fun ${what} at ${where}`;
};
// can be curried as
const nightOut = who => what => where => {
  return `Out with ${who} having fun ${what} at ${where}`;
};
nightOut("wife")("dancing")("club 9");
```

Currying is similar to partial application of functions.

# Databases

## First Normal Form (1NF): Atomic Values

1NF says that values in a record need to be atomic and not composed of embedded arrays.

## Second Normal Form (2NF): Columns Depend on a Single Primary Key

2NF means we need to identify columns that uniquely define the data in our table. For example,
the primary key of a customers table may be their email address.

## Third Normal Form (3NF): Non-keys Describe The Key And Nothing Else

3NF says every non-primary field must describe the primary key field. No field should exist in the
table that does not describe the primary key field. For example, an orders table that includes
email addresses are bad -- instead it should have a `customer_id` column that references a row in
the customers table.

## OLAP And OLTP

For most applications, OLTP is appropriate. OLTP stands for Online Transaction Processing and is
based on performance -- many reads/writes/deletes.

OLAP stands for Online Analytical Processing. These systems change little over time, apart from
nightly/weekly loads. These power data warehouses and support data mining.

## Extraction, Transformation, and Loading (ETL)

Filing taxes is a form of ETL that people do every year: pull all their financial data from their
banks, savings, investments, etc... and compile it into a single place. They go through it and
verify it reconciles.

ETL systems range from simple to complex. You can use a set of scripts to perform tasks and load
up data in your data warehouse.

## Data Marts and Warehouses

A data warehouse is like a filing cabinet in your home, where you store all financial information:
statements, tax documents, receipts. You try to keep it organized. When you need information, you
pull it out and load it up into something your accountant can understand, like a spreadsheet.
The spreadsheet is the data mart -- a focused/targeted place that can answer questions.

## A Shift in Thinking

Data storage design always kept two things in mind: storage capacity and memory/processing speed.
NoSQL systems are getting popular because they are easier (technically and economically) to build
distributed databases with. They can utilize multiple smaller servers, scaling horizontally.

SQL servers require ACID compliance, which is a problem in distributed systems. ACID means:

* Atomic - single transaction happens or doesn't, no partial transactions
* Consistent - entire database is aware of a change in data whenever transaction is completed. The
  state of the database will change completely, never partially
* Isolated - one transaction will not affect another if they happen at the same time
* Durable - when a transaction concludes, nothing can change the data back unless the another
  transaction occurs. It's written to disk and persists even if system fails.

## CAP Theorem

Distributed processing can only provide two of the three guarantees at any given time:

* Consistency - state of database will change with each transaction
* Availability - distributed system will respond in some way to a request
* Partition Tolerance - distributed system relies on a network of some sort to function. If part of
  that network goes down, the system should still operate.

## An Alternative to ACID: BASE

BASE - basically available, soft state, eventually consistent.

ACID is about data paranoia: protect it at all costs. Make sure it's written to disk. BASE is
the opposite: strength in numbers, more machines, spread the risk.

BASE systems can scale massively by adding more nodes.

## Sharding

Sharding means distributing load across multiple machines. There are two ways to do this:

* let the machine decide how - modern databases can use primary keys to divide data into equal chunks
* we decide how - if your system lends itself to a natural way to segregate, for example divide by
  region or country. Sharding logically might make more sense

## Replication

Replicate the data across nodes in your cluster to have a guarantee it's as accurate as possible.
If you have data centers across the world, make sure to replicate the data across each region.

# Testing

## The Nuts and Bolts of TDD

Steps for test driven development:

1. think about what you need to create
2. write a simple test to get yourself started
3. run the test and watch it fail
4. write code to make the test pass
5. repeat steps 1-4, constantly refactoring what you've written

## The Happy Path

The initial test is usually what people call the happy path: it passes when everything works as
we expect it to work. This tests the basic functionality of your implementation.

## The Sad Path

The sad path tries to blow up the happy path. It's what happens when you do X. It's a test that
gets written after the initial happy path implementation is done.

When you read your code, take into consideration how you can break it. Then write tests to break it.

## BDD

Behavior Driven Design works like TDD, but you focus on the behavior of the application instead.
It's a subtle shift. Instead of testing our code, we write down how our application responds.

Some downsides to TDD:

* The tests are bound to the design of your class. If you change your design, you have to change
  your test and code.
* The focus is on engineering instead of application experience
* Testing proliferates, where you try to get as much code coverage as possible

With BDD, you write tests that your application will have to deal with under specific circumstances.

## Features, Scenarios, Expectations (& Given, When, Then)

Using a different syntax of features, scenarios, and expectations helps you focus more on your
application behavior. It provides: readability, ubiquity, and focus.
