# Functional Core, Imperative Shell

Notes on [Functional Core, Imperative Shell](https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell).

## Summary

**Functional Core** - purely functional code that's immutable. It serves as the core of the
application. It never calls the imperative code. It's easier to understand and easier to test.
It contains most of the logic and data manipulation. It serves as the bulk of the program.

**Imperative Shell** - surrounds the functional core. It manipulates stdin/stdout, the database,
the network, and handles user input. All based on values produced by the functional core. It handles
the state of the world and the state of the program.

Using this pattern leads to easy-to-understand core code that's naturally isolated. The shell code
often has fewer conditionals and more dependencies. The functional code has more conditionals/logic
but very few dependencies. It makes reasoning about program's state easier over time. It makes testing
easier. Unit tests for the functional code fit well with less mocks. Integration tests for the
imperative code works well due to more dependencies.

A codebase following this pattern could use the 80/20 rule, where 80% of the code is written in
a functional style. 20% would be stateful, written in the imperative style and utilizes the functional
core. But there's a clean separate between the two. And it's uni-directional.

## In Practice: Backend

What can serve as the functional core: business logic and service classes, utility functions,
formatters, presenters, serializers, and helpers.

What can serve as the imperative shell: database calls, network calls.

## In Practice: Frontend

What can serve as the functional core: pure function ui components, formatter helpers, business logic
helpers, utility functions.

What can serve as the imperative shell: hooks, stores, network calls, user input handlers, stateful
components which wrap the pure function components.
