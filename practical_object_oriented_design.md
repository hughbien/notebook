# Practical Object Oriented Design

Notes on [Practical Object Oriented Design](https://www.poodr.com).

# Object-Oriented Design

If a codebase succeeds, it will live long enough where the biggest problem will be dealing with
change. Design is arranging code efficiently to handle change.

Good design requires learning theories and applying theory appropriately. Learn the rules of OOD,
apply them, but also realize theory != practice. Theory can't be applied 100% of the time. The
real world involves change, confusion, and uncertainty.

Some principles that will be covered later:

* SOLID - single responsibility, open-closed (open for extension but closed for modification --
  eg inherit instead of modifying classes), Liskov substitution (you should be able to use subclassed
  instances without altering correctness), interface segregation (many client-specific interfaces
  is better than one generic interface), dependency inversion (depend upon abstractions instead
  of concretions).
* DRY - don't repeat yourself, every piece of knowledge has a single/unambiguous/authoritative
  representation within a system
* Law of Demeter - given object should assume as little as possible about anything else

# Designing Classes with a Single Responsibility

Organize code to be TRUE:

* transparent - consequences of change should be obvious, even in distance code
* reasonable - cost of change is proportional to its benefits
* usable - existing code should be usable in new/unexpected contexts
* exemplary - code should encourage those who change it to perpetuate these qualities

When everything in a class is related to a central purpose, it is **highly cohesive** or it has
a single responsibility.

Depend on behavior, not data. The author recommends hiding instance variables (use getter methods).
This way any unintended changes can be made at the method level. The author recommends hiding
data structures. For example:

```rb
def diameters
  data.collect { |cell| cell[0] + (cell[1]*2) }
end
```

The above is bad, because `data` is a complicated data structure -- a 2d array with implicit properties
set at certain indices. It's better to be explicit and use an Array of Structs.

Methods should also have a single responsibility. Having multiple, smaller, single responsibility
methods will: expose hidden qualities, avoid need for comments, encourage reuse, are easy to refactor
out to another class.

After extracting out behavior to smaller methods, the scope of your class will be more apparent.
You can decide if you should split your single class into multiple ones.

# Managing Dependencies

Inject dependencies to create loosely coupled objects. Isolate dependencies to allow for adaptation.
Depend on abstractions (instead of concrete objects) to decrease the chance of facing difficult changes.

An object depends on another if, when one changes, the other is forced to change too. Some degree
of dependency is inevitable, since objects must collaborate. A class should know just enough to do
its one job.

One destructive kind of dependency is when one object knows another, who knows something, who knows
something else, and so on. It violates the Law of Demeter and introduced tight coupling.

**Inject Dependencies** - removes dependencies on a class name. In the example below, the `gear_inches`
method has a dependency on the `Wheel` class.

```rb
class Gear
  attr_reader :chainring, :cog, :rim, :tire
  def initialize(chainring, cog, rim, tire)
    # ... set instance variables ...
  end

  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end
  # ...
end
```

But it's the diameter method that's important, not the class name. What if we want to work with
cylinders or something else in the future. Instead, we could use dependency injection:

```rb
class Gear
  attr_reader :chainring, :cog, :wheel
  def initialize(chainring, cog, wheel)
    # ...
  end

  def gear_inches
    ratio * wheel.diameter
  end
end
Gear.new(52, 11, Wheel.new(26, 1.5)).gear_inches
```

**Isolate dependencies** - when you can't remove a dependency, isolate them within your class. Think
of each dependency as bacterium, they should each be quarantined.

Isolate instance creation code. If `Wheel` cannot be injected as above, instead of setting it as
an instance variable, expose it as an isolated public method:

```rb
def initialize(chainring, cog, rim, tire)
  # ...
  @wheel = Wheel.new(rim, tire)
end

def gear_inches
  ratio * wheel.diameter
end

# ... instead, extract wheel to its own isolated method
def wheel
  @wheel ||= Wheel.new(rim, tire)
end
```

Isolate vulnerable external messages. If `gear_inches` was a bit more complicated, you might want
to extract `wheel.diameter` to its own method:

```rb
def gear_inches
  # ... complicated calculation ...
  foo = ratio * diameter
  # ... more calculations ...
end

def diameter
  wheel.diameter
end
```

**Remove argument order dependencies**. Instead of parameters requiring a specific order, consider
passing in a hash. Use `Hash#fetch` to set defaults, which will allow passing in `nil` or `false`
values.

If you cannot change the method signature, consider creating a decorator that wraps it. If it's
for instantiating classes, this pattern is known as a Factory.

**Choose dependency direction**. In the examples above, `Gear` was dependent on `Wheel`. But it could
have easily been reversed. Or they could have been dependent on each other. You control the dependency
direction. Some general rules to guide the direction better:

* some classes are more likely than other to change in requirements
* concrete classes are more likely to change than abstract classes
* changing a class that has many dependents will result in widespread consequences

A simple heuristic: *depend on things that change less often than you do*.

# Creating Flexible Interfaces

If you plot your application as a directed graph (classes as nodes, messages as edges), ideally you'll
see a tree pattern flowing in one direction.

Not great design:

    A<-->B<-->C
    ^----+----^
    |    |
    +--->D<-->E<-->F<-->G
    |    ^----^----^----^
    |    |
    +--->H

Better design:

    A
    +-->B-->C
    |   +-->D
    |
    +-->E-->F-->G
            +-->H

The public interface of a class: reveals its primary responsibility, will not change on a whim,
are safe for others to depend on, are thoroughly documented in its tests. The private interface:
handles implementation details, are not expected to be sent to others, can change for any reason
whatsoever, are unsafe for others to depend on, may not even be referenced in tests.

The public interface should be stable.

**Ask for "What" instead of telling "How"**. Given a `Trip` and `Mechanic` class, where a trip may
have many `Bicycle`s, inside the trip the class may ask the mechanic how to prepare bicycles by:

```
for each bicycle:
  mechanic.clean_bicycle(bike)
  mechanic.pump_tires(bike)
  mechanic.lube_chain(bike)
  mechanic.check_brakes(bike)
```

But it's better to leave the implementation details to the mechanic:

```
for each bicycle:
   mechanic.prepare_bicycle(bike)
```

**Seek context independence**. Trip has a single responsibility but it expects a context. Preparing
a trip always requires preparing bicycles. An object's context isn't its primary objective, but the
object will expect a few things from its surroundings (similar to test case setup).

Instead of iterating through bicycles, asking each one to be prepared, can we just pass the `Trip`
instance to `Mechanic`: `mechanic.prepare_trip(self)`.

Rules of thumb for creating interfaces:

* create explicit interfaces - public methods that handle what instead of how, that will be stable,
  that take a hash as an options parameter.
* honor the public interface of others - do your best to only use the public interface of other classes
* use caution if you're depending on private interfaces
* minimize context

If you find yourself violating the Law of Demeter, it's usually a clue that there are objects whose
public interfaces are lacking.

# Reducing Costs with Duck Typing

Some code smells that hint for duck typing are:

* case statements that switch on a class
* `kind_of?` and `is_a?`
* `responds_to?`

When you see code that switches on a class:

```rb
case preparer
when Mechanic
  preparer.prepare_bicycles(bicycles)
when TripCoordinator
  preparer.buy_food(customers)
when Driver
  preparer.gas_up(vehicle)
  preparer.fill_water_tank(vehicle)
end
```

It's usually a good opportunity to use polymorphism:

```rb
preparer.prepare_trip(self)
# ...
class Mechanic
  def prepare_trip(trip)
    prepare_bicycles(trip.bicycles)
  end
end
class TripCoordinator
  def prepare_trip(trip)
    buy_food(trip.customers)
  end
end
class Driver
  def prepare_trip(trip)
    gas_up(trip.vehicle)
    fill_water_tank(vehicle)
  end
end
```

# Acquiring Behavior Through Inheritance

Use inheritance to share behavior via the template pattern. Use it for "is a" relationships.
Composition should be used for "has a" relationships. Use abstract classes.

```rb
class Bicycle
  attr_reader :size, :chain, :tire_size

  def initialize(args = {})
    @size = args[:size]
    @chain = args[:chain] || default_chain
    @tire_size = args[:tire_size] || default_tire_size
  end

  def default_chain
    '10-speed'
  end

  def default_tire_size
    raise NotImplementedError, "This #{self.class} cannot respond to: "
  end
end

class RoadBike < Bicycle
  # ...
  def default_tire_size
    '23'
  end
end

class MountainBike < Bicycle
  # ...
  def default_tire_size
    '2.1'
  end
end
```

Use `super` to manage coupling between superclasses and subclasses:

```rb
class Bicycle
  def spares
    { tire_size: tire_size, chain: chain }
  end
end
class MountainBike
  def spares
    super.merge({ rear_shock: rear_shock })
  end
end
```

**Decouple subclasses using hook messages**. Hook messages exist solely to provide subclasses a
place to contribute information.

```rb
class Bicycle
  def initialize(args = {})
    @size = args[:size]
    @chain = args[:chain] || default_chain
    @tire_size = args[:tire_size] || default_tire_size

    post_initialize(args)
  end

  def post_initialize(args)
    nil
  end
end

class RoadBike < Bicycle
  def post_initialize(args)
    @tape_color = args[:tape_color]
  end
end
```

This change reduces `RoadBike`'s coupling to its superclass. It doesn't need to know when `post_initialize`
is called, or need to know `Bike`'s initialize details. The same technique can be used for the
`spares` method too.

# Sharing Role Behavior with Modules

When objects that play a common role need to share behavior, use modules. Code defined in a module
can be added to any object.

The `Trip` class above had an implicit `Preparable` role. With duck typing, a class only needs to
implement the `prepare_trip` method to be considered preparable. The `Preprarer` in this case is
the `Mechanic`. Ruby supports mixin modules, so these roles can be made explicit.

**Let objects speak for themselves**. The `StringUtils.empty?(str)` method lets you check if a string
is empty. But this is redundant, since strings are objects, we should just use `String#empty?`.

```rb
module Schedulable
  def schedule
    @schedule ||= ::Schedule.new
  end

  def schedulable?(start_date, end_date)
    !scheduled?(start_date - lead_days, end_date)
  end

  def scheduled?(start_date, end_date)
    schedule.scheduled?(self, start_date, end_date)
  end

  # includers may override
  def lead_days
    0
  end
end

class Bicycle
  include Schedulable

  def lead_days
    1
  end
end
```

Since mixins behave similar to inherited classes, we can use the template pattern to fill in more
specific implementation.

There are two anti-patterns that indicates code might benefit from inheritance. If a variable like
`type` or `category` exists to determine which message to send to `self`. Or if the sending object
checks the class of the receiving object to determine which message to send.

Insist on an abstraction. All code in an abstract class should apply to every class that inherits
from it.

Subclasses must honor the contract of superclasses. An instance of a subclass should always have
correct behavior when a program asks for the superclass.

Preemptively decouple classes by avoiding code that requires its inheritors to send `super`. Use
hook messages. Requiring code to send `super` adds an additional dependency.

Create shallow hierarchies. Every hierarchy has depth and breadth. The simplest are shallow/narrow,
followed by shallow/wide, then deep/narrow, finally deep/wide.

# Combining Objects with Composition

Composition involves combining distinct parts into a complex whole. Whereas inheritance deals with
"kind of" relationships, composition deals with "has a" relationships.

For example, a `Bicycle` has `Parts`.

```rb
class Bicycle
  attr_reader :size, :parts

  def initialize(args = {})
    @size = args[:size]
    @parts = args[:parts]
  end

  def spares
    parts.spares
  end
end

class Parts
  attr_reader :chain, :tire_size

  def initialize(args = {})
    @chain = args[:chain] || default_chain
    @tire_size = args[:tire_size] || default_tire_size
    post_initialize(args)
  end

  def spares
    { tire_size: tire_size, chain: chain }.merge(local_spares)
  end

  def default_tire_size
    raise NotImplementedError
  end

  def post_initialize(args)
    nil
  end

  def local_spares
    {}
  end

  def default_chain
    '10-speed'
  end
end

class RoadBikeParts < Parts
  attr_reader :tape_color

  def post_initialize(args)
    @tape_color = args[:tape_color]
  end

  def local_spares
    {tape_color: tape_color}
  end

  def default_tire_size
    '23'
  end
end
```

`Parts` can be further refactored, to have individual `Part` instances. After extracting out `Part`,
we can switch the implementation of `Parts` to inherit from `Array` with some extra behavior. Or
simply implement `Enumrable`.

Composition has a natural tendency to create many small objects, each being straightforward with
a single responsibility. Unfortunately, a composed object relies on many parts. Each individual
part needs to be transparent.

> Inheritance is best suited to adding functionality to existing classes when you will use
> most of the old code and add relatively small amounts of new code.
> - Erich Gamma, Richard Helm, Ralph Johnson, and John Vilssides; GoF Patterns

> Use composition when the behavior is more than the sum of its parts.
> - Grady Booch, Object-Oriented Analysis and Design

# Designing Cost-Effective Tests

Tests give you the confidence to refactor constantly. Efficient tests prove the altered code is
correct without raising costs.

The true benefits of testing is to reduce costs by reducing bugs, providing documentation, letting
you defer design decisions, supports abstractions, and exposes design flaws.

Write tests first, before implementation. It forces good design.

Use the mainstream testing libraries/frameworks. For Ruby, MiniTest or RSpec is recommended.

Make assertions about the public interface.

Isolate the object under test. You can do this by injecting dependencies.

What about testing private methods? You can ignore them, since tests are redundant. The public
interface should be tested and that interface uses the private methods. Private methods are also
unstable. Testing can also mislead others to use those private methods. But it's a rule-of-thumb. 
Be biased against writing them but don't be dogmatic.

When testing roles, consider extracting test cases into a module. For example:

```rb
module PreparerInterfaceTest
  def test_implements_the_preparer_interface
    assert_respond_to(@object, :prepare_trip)
  end
end

class MechanicTest < MiniTest::Unit::TestCase
  include PreparerInterfaceTest

  def setup
    @mechanic = @object = Mechanic.new
  end
end
```

Shared tests in a module can also be used to test inherited classes.
