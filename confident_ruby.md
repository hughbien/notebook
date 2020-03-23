# Confident Ruby

Notes on [Confident Ruby](https://www.confidentruby.com) by Avdi Grimm.

It's a collection of techniques and patterns tied together with a common theme: self confidence.

Code can tell a story. There are usually four parts to a method:

1. collecting input
2. performing work
3. delivering output
4. handling failures

There are also two less common parts: diagnostics and cleanup.

Un-confident code (or timid code) haphazardly mixes the parts of the method. It adds an extra
cognitive burden to the reader. Methods that tell a good story will keep these parts atomic in
distinct stripes, in the order listed above.

# Performing Work

While it's part #2 the order above, it's the most important part. If no work is performed, the
method probably shouldn't exist.

*Send a strong message.* The fundamentals of OOP is sending messages. When a message is sent, we
don't send multiple orders about how to perform a task. Instead, send one single strong message and
trust that the work will be done.

The steps to initially write a method:

1. Identify the messages we want to send (in a language as close to the problem domain as possible)
2. Determine the roles which make sense to receive those messages
3. Bridging the gap between those roles and which objects actually exist in the system

If we're lucky, roles will match perfectly with a set of classes. More likely, clarity of method
will suffer. There might be low level data manipulation code mixed with high level domain language.
Or code will be cluttered with type checks: `@logger && @logger.info("...")`. Confident code should
use duck typing.

# Collecting Input

We mostly think of simple or direct inputs -- parameters to a method. But there are indirect inputs
also -- such as an `initialize` method that initializes a `@start_date = Time.now`. Inputs can also
be fetched from the surrounding system, such as environment variables or I/O.

Our input also determines which objects we have available. We then map those objects into roles that
are necessary to perform work. This can be done with three strategies:

1. Coerce objects into the roles we need them to play
2. Reject unexpected values which cannot play the needed roles
3. Substitute known-good objects for unacceptable inputs

*Guard the borders, not the hinterlands.* Programming defensively in every method is redundant. Most
techniques here are suited for the borders of your code, such as the public API if you're writing
a library.

*Use built-in conversion protocols.* If you want to ensure inputs are a specific core type, such
as making sure you get an Integer, use Ruby's defined conversion protocols like `#to_i` or `#to_str`
or `#to_path` or `#to_ary`.

Ruby's arrays use `#to_int` to convert the array index to an integer. If you wanted to use a special
object as an Array index, simply define that method and it should work like another int. For example:

```ruby
winners = ["Homestar", "King of Town", ...]
Place = Struct.new(:index, :name, :prize)
first = Place.new(0, "first", "Peasant's Quest game")
winners[first] # => TypeError

Place = Struct.new(:index, :name, :prize) do
  def to_int
    index
  end
end
winners[first] # => "Homestar"
```

Another example includes `File.open` which works with any object that responds to `#to_path` returning
the file path as a String. Here's a list of standard conversion methods:

| Method    | Target Class | Type     |
| --------- | ------------ | -------- |
| to_a      | Array        | Explicit |
| to_ary    | Array        | Implicit |
| to_c      | Complex      | Explicit |
| to_enum   | Enumerator   | Explicit |
| to_h      | Hash         | Explicit |
| to_hash   | Hash         | Implicit |
| to_i      | Integer      | Explicit |
| to_int    | Integer      | Implicit |
| to_io     | IO           | Implicit |
| to_open   | IO           | Implicit |
| to_path   | String       | Implicit |
| to_proc   | Proc         | Implicit |
| to_r      | Rational     | Explicit |
| to_regexp | Regexp       | Implicit |
| to_s      | String       | Explicit |
| to_str    | String       | Implicit |
| to_sym    | Symbol       | Implicit |

Explicit conversions occur on classes that are entirely unrelated to the target class. Implicit
happens on classes that are closely related to the target class. For example, converting from a
Time object to a String would be explicit because Time and String are entirely unrelated. But
converting from a Symbol to a String would be implicit.

*Conditionally call conversion methods.* If you want to support multiple input types without forcing
those inputs to understand the protocol. For example: `input.respond_to?(:to_path) ? input.to_path : input`
or `filename.to_str unless filename.is_a?(String)`. This is an exception to the duck typing suggestion.
Instead of asking "are you the kind of object I need?" it says "can you give me the kind of object
I need?"

*Define your own conversion protocols.* For converting arbitrary objects to user-defined types, create
your own well-documented protocol to accept the widest types of inputs. For example, if you have
a `Meter` type consider creating the `#to_meters` method, which can be implemented in the `Feet` class.

*Use built-in conversion functions.* Ruby has built in functions such as `Integer(...)` or `Array(...)`
provided by the `Kernel` module. These include Array, Float, String, Integer, Rational, Complex. Some
other modules provide additional functions, such as Uri and Pathname. They're idempotent and offer
a wider range of input types than `#to_*` methods. For example, Integer uses this strategy:

1. given a numeric value, converts to an Integer or Bignum
2. Convert strings containing integers to decimal, hexidecimal, octal, or binary formats
3. Attempt to use `#to_int` if it exists
4. Falls back on `#to_i`

Calling `Integer()` means convert this to an Integer if there is any sensible way of doing so.

There is no `Hash()` function. The closest thing is `Hash[]`, which given an even numbered list will
produce a hash where the odd members are keys and even members are values.

`Array()` is especially useful to coerce input into an Array. It works on creating an array from a
single element, returning an array if it already is one, or returning an empty array for nil.

*Define your own conversion functions.* Define an idempotent conversion function for incoming
objects. For example, a `Point()` function which yields a Point object. These functions should be
concise and idempotent. It works especially well when combined with your own conversion protocol:

```ruby
module Graphics
  module Conversions
    module_function # makes methods private and singleton

    def Point(*args)
      case args.first
      when Point then args.first
      when Integer then Point.new(*args)
      when String then Point.new(*args.first.split(':').map(&:to_i))
      when ->(arg) { arg.respond_to?(:to_point) }  # case matching uses Proc#===
        args.first.to_point
      when ->(arg) { arg.respond_to?(:to_ary) }
        Point.new(*args.first.to_ary)
      else
        raise TypeError, "Cannot convert #{args.inspect} to Point"
      end
    end
  end
end
```

Then you can use the `Point()` function liberally within your library, especially when working with
input values from external sources.

*Replace "string typing" with classes.* Don't use inputs that are specially formatted Strings. Define
a class instead and lean on polymorphism. Instead of:

```ruby
class TrafficLight
  def change_to(state)
    @state = state
  end

  def signal
    case @state
    when "stop" then turn_on_lamp(:red)
    when "caution" then turn_on_lamp(:yellow)
    when "proceed" then turn_on_lamp(:green)
    end
  end
end
```

Try using a specific class:

```ruby
class TrafficLight
  State = Struct.new(:name, :color) do
    def to_s
      name
    end
  end

  STOP = State.new("stop", :red)
  CAUTION = State.new("caution", :yellow)
  PROCEED = State.new("proceed", :green)

  def change_to(state)
    @state = State(state)
  end

  def signal
    turn_on_lamp(@state.color)
  end

  private

  def State(state)
    case state
    when State then state
    else self.class.const_get(state.to_s.capitalize)
    end
  end
end
```

The first would error if we used the wrong casing or symbols instead. The second is more robust and
accepts a wider options of inputs.

*Wrap input objects with adapters to give them a consistent interface.* Instead of branching on
type checking in the perform work section of your method, wrap your inputs during the collecting
input section. Make it a transparent adapter by using Ruby's `DelegateClass()` to delegate all
unknown methods to the input's class.

*Reject unacceptable values early using preconditions.* If a method cannot convert or adapt an input,
it's better to fail early and obviously. Preconditions serve as a guard and executable documentation.

```ruby
def hire_date=(new_hire_date)
  raise TypeError, "Invalid hire date" unless new_hire_date.is_a?(Date)
  @hire_date = new_hire_date
end
```

An implicit assertion can be done for Hashes requiring a key via `Hash#fetch`. This method will raise
a KeyError if it does not exist. You can customize the error handling by passing your own block or
returning a default value.

*Document assumptions with assertions.* Use assertion failures to improve your understanding of the
data: `transactions.is_a?(Array) or raise TypeError, "transactions is not an Array"`

*Handle special cases with a Guard Clause.* Instead of raising an error, we may just want to exit
out of the method early. Put the return first:

```ruby
def log_reading(reading_or_readings)
  return if @quiet
  # ... work done here ...
end
```

*Handle special cases as objects.* The classic example is of a guest user in a web application.
Instead of having `current_user` set as nil, consider returning a special `GuestUser` type who
may have a special avatar or username. Another example is the Null Object pattern, which usually
no-ops for all methods.

*Substitute a benign, known-good value for nil.* This can act as a placeholder to eliminate tedious
checks in your method. For example, can you use a blank string as a placeholder in a template?

*Bundle arguments that commonly appear together into a new class.* If multiple methods in an object
take the same list of parameters, consider creating a new class. The most common example is if
multiple methods take two parameters: an X coordinate and a Y coordinate -- create a Point class.

*Yield a parameter builder object.* A builder-based interface providers an approachable front-end 
for complex object creation, hiding the complexity by separating the interface from the implementation:

```ruby
require 'delegate'

class PointBuilder < SimpleDelegator
  def initialize(point)
    super(point)
  end

  def fuzzy_radius=(fuzzy_radius)
    __setobj__(FuzzyPoint.new(point, fuzzy_radius))
  end

  def point
    __getobj__
  end
end

class Map
  def draw_point(*point_args)
    builder = PointBuilder.new(Point(*point_args))
    yield(builder) if block_given?
    point = builder.point
    # ... do work ...
  end
end

Map.new.draw_point(7, 9) do |point|
  point.magnitude = 2
  point.name = "home base"
  point.fuzzy_radius = 5
end
```

The biggest advantage of this pattern is that it decouples client code API from the internal structure
of our library.

*Receive policies (in the form of a block/Proc) instead of data.* This works well for methods with
many edge cases:

```ruby
def delete_files(files, &error_policy)
  error_policy ||= -> (file, error) { raise error }
  files.each do |file|
    begin
      File.delete(file)
    rescue => error
      error_policy.call(file, error)
    end
  end
end

def delete_files_with_multiple_policies(files, options={})
  error_policy = options.fetch(:on_error) { ->(file, error) { raise error } }

  files.each do |file|
    begin
      File.delete(file)
    rescue => error
      error_policy.call(file, error)
    end
  end
end
```

This gives the caller options to ignore errors, log errors, or simply raise them.

# Delivering Output

*Return a single type in all cases.* If a method may return a zero, one, or many elements -- consider
always returning an Array instead of `nil`, one item, or an Array. For example:

```ruby
find_words('gnu') # => ["gnu", "gnus"]
find_words('ruby') # => "ruby"
find_words('fnord') # => nil

# Use the Array() function on the output instead
find_words('gnu') # => ["gnu", "gnus"]
find_words('ruby') # => ["ruby"]
find_words('fnord') # => []
```

*Conditionally yield to a block on success rather than returning a value.* This may be more meaningful
than returning a true or false value. For example:

```ruby
def import_purchase(date, title, user_email)
  user = User.find_by_email(user_email)
  if user.purchased_titles.include?(title)
    false
  else
    user.purchases.create(title: title, purchased_at: date)
    true
  end
end

if import_purchase(date, title, user_email)
  send_book_invitation_email(user_email, title)
end
```

Can be written as:

```ruby
def import_purchase(date, title, user_email, &import_callback)
  user = User.find_by_email(user_email)
  unless user.purchased_titles.include?(title)
    purchase = user.purchases.create(title: title, purchased_at: date)
    import_callback.call(user, purchase)
  end
end

import_purchase(date, title, user_email) do |user, purchase|
  send_book_invitation_email(user.email, purchase.title)
end
```

*Represent failure with a benign value or special case object.* Return a default value, such as an
empty string, which will not interfere with normal operation of the caller. Or return a special type,
like a `GuestUser` or `NullObject`.

*Return a status object* which represents the outcome of the method, which can be more nuanced than
a simple true/false. Different ways to return statuses from a method:

* returning true/false for success/failure (doesn't offer enough statuses)
* returning symbols (no way to tell what the error was from outside the implementation/method)
* returning a special class (which can offer multiple statuses and offer more metadata)

```ruby
class ImportStatus
  def self.sucess() new(:success) end
  def self.redundant() new(:redundant) end
  def self.failed(error) new(:failed, error) end

  attr_reader :error

  def initialize(status, error=nil)
    @status = status
    @error = error
  end

  def success?
    @status == :success
  end

  def redundant?
    @status == :redundant
  end

  def failed?
    @status == :failed
  end

  def on_success
    yield if success?
  end

  def on_redundant
    yield if redundant?
  end

  def on_failed
    yield if failed?
  end
end

def import_purchase(date, title, user_email)
  user = User.find_by_email(user_email)
  if user.purchased_titles.include?(title)
    ImportStatus.redundant
  else
    purchase = user.purchases.create(title: title, purchased_at: date)
    ImportStatus.success
  end
rescue => error
  ImportStatus.failed(error)
end
```

You can also yield a status object instead of returning it. This has an added benefit that you can
return nil for no-op guard clauses.

*Use `throw` toe signal an early termination and `catch` to handle it.* For example, a SAX XML parser
can signal that it has all the data it needs and has no reason to continue parsing the document.

# Handling Failure

Begin/rescue/end (BRE) blocks are the pink lawn flamingo of Ruby code. It's a distraction and an
eyesore wherever it turns up, interrupting narrative flow of code. These patterns help push out the
BRE blocks.

*Prefer top-level rescue clause.* Instead of using BRE blocks, simply follow this pattern:

```ruby
def bar
  # happy path goes here
rescue
  # failure scenario goes here
end
```

Often this means breaking out new methods, which is a side benefit.

*Use checked methods for risky operations.* Wrap system or library calls into a method that handles
the possible exception. Encapsulating common lower level errors eliminates duplicated error handling.
For example:

```ruby
def checked_popen(command, mode, error_policy=->{raise})
  IO.popen(command, mode) do |process|
    return yield(process)
  end
rescue Errno::EPIPE
  error_policy.call
end

def filter_through_pipe(command, message)
  checked_popen(command, "w+", ->{message}) do |process|
    process.write(message)
    process.close_write
    process.read
  end
end
```

Consider wrapping system and third party library calls in an Adapter class.

*Use bouncer methods* which check for the error state and raise an exception. Bouncer methods DRY
up common logic and keep higher-level logic free from digressions into low-level error-checking. This
is similar to checked methods above, but instead of rescuing an exception it will execute a conditional
on a status variable, such as `$?` which gets set by `IO.popen`.
