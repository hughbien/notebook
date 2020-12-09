# A Philosophy of Software Design

Notes on A Philosophy of Software Design by John Ousterhout.

# Nature of Complexity

Complexity in software is anything that makes it hard to understand or modify.

Examples: how a piece of code works, high effort to implement small improvement, unclear what needs
to be changed, difficult to fix bug without introducing another.

In terms of cost/benefit: complex systems require large work for small improvements. Simple systems
can make large improvements with less effort.

Isolating complexity to where it will never be seen is almost as good as eliminating it.

Complexity is more apparent to readers than writers. If you write simple code, but readers think
it is complex, then it is complex.

Symptoms of complexity:

* change amplification - do simple changes require code modifications in many different places?
* cognitive load - how much does a developer need to know to complete a task?
* unknown unknowns - is it obvious what knowledge is necessary to carry out a task?

Most important design goal is for the system to be obvious.

Complexity is caused by dependencies and obscurity. Dependencies exist when a code change cannot
be made in isolation.

Complexity is incremental that accumulates in small chunks, over time.

# Working Code Isn't Enough

Good design requires a strategic mindset instead of a tactical mindset. With tactical programming,
your focus is to get something working as quickly as possible. With strategic programming, working
code isn't enough. The focus shifts to the long-term design of the system.

# Modules Should Be Deep

Modules take the form of classes, subsystems, services, and more. Modules work together by calling
each other's functions or methods. Modules have two parts: interface and implementation.

Prefer deep modules (simple interface but complex implementation) over shallow modules (complex
interface but simple implementation).

The formal part of an interface can be enforced by the programming language: method signatures,
class usage, etc... The informal part cannot be enforced, such as side effect behaviors. Try to
move as much as possible to be formal.

Abstracts make things easier for us to work and reason. It's a simplified view of an entity that
omits unimportant details. False abstractions omit important details, which will make things harder
to use.

Example deep module includes file I/O on Unix systems. There are only five basic calls: `open`,
`read`, `write`, `lseek`, `close`. There's a ton of implementation details handled by these calls:
file representation, efficient access, directory storage, permissions, file access, etc...

Conventional wisdom is that classes should be small, not deep. The author calls this *classitis*.
One example of this is Java's IO library, which requires knowledge of three different classes just
to read serialized objects.

While choice is good, interfaces should be designed to make the common case as simple as possible.

# Information Hiding (and Leakage)

Information hiding is a technique for achieving deep modules. It involves encapsulating knowledge
in a module, so it's a part of the implementation but not the interface. Hidden information includes
data structures, algorithms, lower level details (such as page size), or higher level concepts (such
as assumptions on requirements).

Leakage occurs where modules share a design decision, so that one change requires changes in multiple
modules.

# General Purpose Modules are Deeper

General purpose modules implement features that address a broad range, even including potential
future use cases. The special purpose approach means you can focus on today's needs, incrementally
adding as more needs arrive.

Make classes somewhat general purpose. Find the sweet spot. Functionality should reflect current
needs, but the interface should be general enough to support multiple uses for the future.

Ask yourself:

* what is the simplest interface that covers all my current needs?
* how many situations will this method be used?
* is the API easy to use for my current needs?

# Different Layer, Different Abstraction

Software is made up of layers, higher layers using lower layers. If a system contains adjacent
layers with similar abstractions, it's a red flag that suggests class decomposition.

Pass-through methods do little except invoke another method, whose signature is similar. They make
classes shallower. They suggest an overlap of responsibility between classes. Possible solutions:
expose lower level class directly to callers, redistribute responsibilities between classes, or
merge the classes.

Decorator classes tend to be shallow and often contain many pass-through methods. While it's useful,
you should consider: can you add functionality directly?

Pass-through variables are another red flag, which is a variable passed down through a long chain
of methods. Eg `method1(..., ptArg, ...)` calls `method2(..., ptArg)` calls `method3(ptArg)` and
so on. `ptArg` may only be needed by the last method, but gets passed through all methods. They force
intermediate methods to be aware but those methods don't use them. Possible solutions: see if there's
already an object shared between topmost/bottommost methods, store information in global variable,
introduce a `context` object that stores application's global state. There is one context object per
instance of the system.

# Pull Complexity Downards

Complexity should be handled internally, within the module, as opposed to leaking it out to the
module's users. It's more important to have a simple interface than a simple implementation.

Example: text editor software was implemented in two ways, one interface used lines as parameters.
The other interface used ranges. The implementation for lines was easier, but end users suffered
and had to create their own complex systems to wrap it.

Counter example: configuration parameters move complexity upwards instead of down, exposing them
to the user. When you use them, see if you can set a reasonable default automatically.

# Better Together or Better Apart?

Given two pieces of functionality, should they be implemented together in the same place or separated?

Subdividing into smaller components will simplify each individual component. But it also adds
complexity:

* complexity from number of components, which makes it harder to keep track of everything
* additional code to manage, for example usage may now need to require managing multiple objects
  instead of just one
* separation, developers must jump across files to understand how to use your module. If components
  are truly independent, then this is good.

Clues that two components are related:

* they share information
* they are always used together (eg `FileInputStream` and `BufferedInputStream`)
* they overlap conceptually

Separate general purpose and special purpose code. Special purpose code associated with a general
purpose mechanism should go to a different module. Lower layers tend to be more general purpose,
with upper layers being more special purpose.

Length by itself is not a good reason for splitting up a method. Splitting introduces more
interfaces and complexity. It makes it harder to read if the code is related. Long methods aren't
always bad. **Each method should do one thing and do it completely**.

# Define Errors Out of Existence

Exceptions contribute disproportionately to complexity. Reduce the number of places where exceptions
must be handled.

Classes with many exceptions have complex interfaces, they are shallower than classes with fewer
exceptions. Exceptions are part of the interface.

Define errors out of existence. In the `Tcl` language, the `unset` command will delete a variable.
If it tries to delete an non-existing variable, it throws an error. Instead it should ensure that
a variable doesn't exist. So if the variable doesn't exist, it still works.

Exception masking detects exceptions and handles it at a low level in the system, so that higher
levels can do its job. For example, TCP packets can be dropped for any reason, they are just resent
in their implementation.

Exception aggregation handles many exceptions in a single location. For example, a web server usually
handles all of its exceptions in a single place and return a preset status code: 40x, 500s, etc...

# Design it Twice

Consider multiple options for each major design decision. Try approaches that are radically different
from each other. It's a better way to learn. Consider: the simpler interface, general vs special
purpose, implementation complexity.

# Why Write Comments? The Four Excuses

The process of writing comments will improve a system's design. The four excuses not to:

1. good code is self-documenting - design information exists that can't be conveyed through code
   alone. What about a high-level description? Or rationale behind a design decision? Or pre and
   post-conditions?
2. I don't have time to write comments - investment in time will pay off
3. comments get out of date and become misleading
4. comments are worthless, why bother

# Comments Should Describe Things that Aren't Obvious from the Code

Comments are important for abstractions. They provider simpler, higher level view. Developers should
understand a module without having to read any code other than its externally visible declarations.

Most comments fall into the categories:

* interface - precedes declaration of module (class, data structure, function, method)
* data structure member - such as instance variable or static variable for class
* implementation comment - inside block of code, describing how the code works
* cross module comment - describing dependencies across module boundaries

Don't repeat your code in comments. Example: `if is_unlocked(ptr_copy) # check if obj is unlocked`

Use different words in the comments than in your code. It provides additional context. Example:

```rb
# The amount of blank space to leave on the left and
# right sides of each line of text, in pixels.
TEXT_HORIZONTAL_PADDING = 4
```

Comments augment code by providing information at a different level of detail. They add precision
by clarifying code. Fill in missing details like: what are the units of this variable? Are the
boundary conditions inclusive/exclusive? If null is permitted, what does it imply? Whose responsibility
is it to free/close a resource? Are there certain properties that must always be true (invariants)?

Higher level comments enhance intuition. They omit details and help readers understand the overall
intent of the code.

Separate interface from implementation comments. Interface comments provide information that a user
must know to use the class or method. Implementation comments describe details on what the module
does (or why design decisions were made).

Interface comment parts: overall description of method, describe each argument and return value,
describe any side effects, describe any exception, describe any preconditions/invariants/postconditions.

# Choosing Names

Good names are a form of documentation. Bad names increases complexity, creates ambiguities and
misunderstandings. Bad names cause bugs.

Names should create an image for the reader. Names should be precise. For example `getCount()` is
too generic. Count of what? `result` is another example. The name should provide information about
what the result is: `mergedLine` or `totalChars`.

There are exceptions. It's okay to use generic names for loop iteration variables or any blocks that
only span a few lines.

Use names consistently. Use the common name for the given purpose, don't use the common name for
anything else, make sure the purpose is narrow enough that al variables with the name have the same
behavior. For example: always use `index` for loops.

# Write The Comments First

Beset time to write comments is at the beginning of the process. Example process:

1. start new class by writing class interface comment
2. next, write interface comments and signatures for most public methods (leaving bodies empty)
3. iterate on these until basic structure feels right
4. write declarations/comments for most important instance variables
5. fill in implementation (writing implementation comments as needed)

# Modifying Existing Code

Systems are constantly evolving. When modifying existing code, stay strategic. Working code isn't
enough. You must resist the temptation to make a quick fix.

Maintain comments. The best way to do this is to keep comments close to the code. Comments belong
in the code, not the commit log. Check the diffs to make sure comments stay in sync.

# Consistency

Consistency reduces complexity by making behavior more obvious. Examples:

* names
* coding style
* interfaces
* design patterns
* invariants

Ensure consistency via documentation or automated builds that flag inconsistencies.

# Code Should be Obvious

Obscurity occurs when important information about a system is not obvious. Obvious code can be
read quickly, without much effort. The reader's first guess about a system should be correct.

Things that make code more obvious: white-space, comments.

Things that make code less obvious: event-driven programming, generic containers (define a specialized
class for your purposes instead), different types for declaration/allocation (eg using an `ArrayList`
but declaring the variable as `List`), code that violates reader expectations.

# Software Trends

Some popular software trends:

* Object-oriented programming and inheritance
* Agile development
* Unit tests
* Test driven development
* Design patterns
* Getters and setters

# Designing for Performance

Simplicity improves a system's design and usually makes it faster too.

Run micro-benchmarks to learn which operations are expensive in your system. Once you get a sense
for what is expensive/cheap, use that information to choose cheap operations whenever possible.

If you must introduce complexity for efficiency, try to hide the complexity so it doesn't affect
any interfaces.

Measure before modifying. Intuitions about performance is unreliable. Before making the change,
measure the existing behavior.
