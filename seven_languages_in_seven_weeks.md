Seven Languages in Seven Weeks
==============================

Bruce A. Tate introduces us to seven different programming languages.  It's
meant to enlighten you with introductions rather than make you an expert.

For each language, Tate will answer:

* What's the typing model?
* What's the programming model?
* How to interact with it?
* What are the decision constructs and core data structures?
* What core features make this language unique?

Ruby
====

Ruby belongs in the family of scripting languages.  It's an interpreted,
dynamically/strong typed, object-oriented language with some functional features.
It's not hugely efficient in terms of execution speed but makes programmers
happier.

Go to [http://www.ruby-lang.org/en/downloads/] to download Ruby.  Start a REPL
by with the `irb` command (stands for interactive ruby).  Here's a quick sample:

    >> puts 'hello, world'
    hello, world
    >> nil
    >> language = 'my Ruby'
    >> 'my Ruby'
    >> puts "hello, #{language}"
    >> 'hello, my Ruby
    >> nil

Every line returns a value.  Every method you define will always return a value.
If nothing is explicitly returned, the last expression will be implicitly
returned.

Methods are defined like:

    def method_name(arg1, arg2)
      # method body
    end

The last line is an implicit return value.  Arrays are initiated like:

    >> animals = ['ions', 'tigers', 'bears']
    >> animals[0]
    => "lions"

Hashes uses labels as keys and are used like:

    >> numbers = {1 => 'one', 2 => 'two', :array => [1,2,3]}
    >> numbers[1]
    => 1
    >> numbers[:array]
    => [1,2,3]

Ruby supports code blocks which are functions without names:

    >> 3.times { puts 'hello world' }
    >> 3.times do
    >>   puts 'hello world'
    >> end

Arguments are passed through vertical bars:

    >> animals.each { |a| puts a }

Everything is an object.  The number `4` is an instance of `Fixnum`, which
inherits from `Integer` which inherits from `Numeric` which is an instance of
`Class` which inherits from `Module` which inherits from `Object`.  Here's
a class:

    class Person
      attr_accessor :first_name, :last_name

      def initialize(first, last)
        @first_name = first
        @last_name = last
      end

      def full_name
        @first_name + ' ' + @last_name
      end
    end

This implicitly inherits from `Object`.  An explicit inherit looks like:

    class ClassName < SuperClassName

Ruby uses modules when inheritance doesn't make sense.  Modules are collections
of functions/constants.

    module ToFile
      def filename
        "object_#{self.object_id}.txt"
      end

      def to_f
        File.open(filename, 'w') {|f| f.write(to_s)}
      end
    end

    class Person
      include ToFile
    end

    p1 = Person.new("John", "Doe")
    p1.filename # => 'object_123.txt'
    p1.to_f     # => saves to_s to file

Some strengths of Ruby:

* duck typing for polymorphic designs
* uniform/consistent object interfaces
* open classes and modules
* great for scripting and web development if scaling is reasonable

Some weaknesses:

* performance (many features hurt the performance)
* concurrency is tough with OOP
* type safety, tools are harder to build for such dynamic typing

Io
==

Prototype language like JavaScript where every object is a clone of another.
Core strengths are embeddable onto small VMs, concurrency, customizable
syntax, and simplicity of syntax.

The interpreter is here: <http://iolanguage.com>

    Io> "Hi ho, Io" print

No syntatic sugar, you're sending a message to the String object.  Create new
objects by cloning existing ones.  We're creating a Vehicle object (not a
class) and assigning a value to the `description` slot:

    Io> Vehicle := Object clone
    Io> Vehicle description := "Something to take you places"
    Io> Vehicle description
    ==> Something to take you places

    Io> Vehicle slotNames
    ==> list("type", "description")

    Io> Vehicle type
    ==> Vehicle

Slots are automatically created if they don't already exist for `:=`.  You can
also use the `=` syntax, which will throw an exception if the slot doesn't
exist.

There are no classes, interfaces, or modules.  There are only objects.  Io
uses convention - if your variable is lowercase than it's an instance.  If it
starts with an uppercase - then it's more of a blueprint:

    Io> ferrari := Vehicle clone
    Io> ferrari slotNames
    ==> list()
    Io> ferrari type
    ==> Car

If a slot name doesn't exist, Io will pass it up the cloned chain.  Instead of
superclass, Io calls it an object's prototype.

Methods are created like:

    Io> method("So, you've come for an argument." println)
    Io> Vehicle drive := method("Vroom" println)
    Io> ferrari drive
    Vroom
    ==> Vroom
    Io> ferrari getSlot("drive")
    ==> method("Vroom" println)

The `proto` slot is useful for debugging.  There's also a master namespace
called `Lobby`.

Some useful objects:

* use the `list` method to create lists which have the slots: `size`, `append`,
  `average`, `sum`, `at`, `pop`, `prepend`, and `isEmpty`.
* use `Map clone` to create hashes which have the slots: `atPut`, `at`,
  `asObject`, `asList`, `keys`, and `size`.
* booleans are represented by `true` and `false` (`and` and `or` for operations)
* to create a singleton just overwrite the `clone` slot to return itself

Loops can be done like:

    loop("getting dizzy..." println)
    ^C
    while(i <= 11, i println; i = i + 1);
    for(i, 1, 11, i println);
    for(i, 1, 11, 2, i println); # optional increment

Prolog
======

Scala
=====

Erlang
======

Clojure
=======

Haskell
=======

Wrap-Up
=======

