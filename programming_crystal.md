# Programming Crystal

Notes on [Programming Crystal](https://pragprog.com/book/crystal/programming-crystal)
by Ivo Balbaert and Simon St. Laurent.

# Diving into Crystal

Crystal comes with:

* a static type system with automatically inferred types
* garbage collection and memory safety
* compilation to machine code through LLVM
* macro system
* full OOP
* generics and method/operator overloading
* scalability and concurrency via fibers

# Crystal Foundations

Variables are assigned with `=`. Crystal can infer the type, but you can also be explicit.

```cr
name = "Diamond"
element = 'C'
hardness = 10

puts typeof(name) # => String
puts typeof(element) # => Char
puts typeof(hardness) # => Int32

name : String = "Diamond"
hardness : Int32
hardness = "20" # => Error... type must be Int32
```

Constants are variable names with all caps, the compiler will complain if you try to re-assign it:

```cr
DIAMOND_HARDNESS = 10
DIAMOND_HARDNESS = 20 # => already initialized
```

Crystal offers the traditional mathematical binary operators: `+, -, *, /, %`. String concatenation
works with `+` or via interpolation: `"Hardness of #{name} is #{hardness}"`.

Methods are called like: `object.message`.

Arrays can only contain the type(s) it's initialized with. If you initialize an empty array, you
must specify the element type.

```cr
minerals = ["alunite", "chromium", "vlasovite"]
typeof(minerals) # => Array(String)

minerals << "wagnerite"
minerals.size # => 4
minerals[0] # => "alunite"
minerals[-1] # => "wagnerite"
minerals[2, 3]
minerals[2..4]
minerals[7] # => Runtime error: Index out of bounds
minerals[7]? # => nil

minerals << 42 # => Error: no overload matches 'Array(String)#<<' with type Int32

precious_minerals = [] # => Error: for empty arrays use '[] of ElementType'
precious_minerals = [] of String
precious_minerals2 = Array(String).new

pseudo_minerals = ["alunite", 'C', 42]
typeof(pseudo_minerals) # => Array(Char | Int32 | String)
```

If you need raw performance, use `StaticArray` instead which is a fixed-size array allocated on the
stack during compilation.

Hashes use `{}` syntax and operate similarly to arrays:

```cr
mohs = {
  "talc" => 1,
  "calcite" => 3,
  "apatite" => 5,
  "corundum" => 9
}
typeof(mohs) # => Hash(String, Int32)

mohs["apatite"] # => 5
mohs["gold"] # => Runtime error
mohs["gold"]? # => nil

mohs['C'] = 4.5 # => Error: no overload matches
mohs = {} # => Error: Syntax error
mohs = {} of String => Int32
mohs = Hash(String, Int32).new
```

Control the flow with `if/unless/else/elsif/case/when/end` keywords:

```cr
hardness = 5.25
output = if 0 < hardness < 5
  puts "softer than apatite"
elsif hardness < 8
  puts "harder than apatite, softer than topaz"
else
  puts "topaz or harder!"
end
output # => "harder than apatite, ..."
```

`if` and `unless` can also be used as a suffix, eg `puts("test") unless condition`

Loop with `times` or `each``:

```cr
5.times do
  puts "test"
end
5.times { puts "test" }
(2..5).each do |i|
  puts "repeat #{i}"
end
```

Ranges are specified with `..` for inclusive or `...` for exclusive.

You can also specify a `loop` for an infinite loop. Use the `break` keyword to stop it. `while` can
be used as a traditional while loop. `until` is the equivalent of `while !condition`.

Methods are defined just like Ruby. Crystal can usually infer the type, but you can be explicit also:

```cr
def double(num)
  num * 2
end

def double(num: Int32)
  num * 2
end

def double(str: String)
  str + " " + str
end
```

Classes are defined and instantiated just like Ruby:

```cr
class Mineral
  def initialize(common_name : String, hardness : Float64)
    @common_name = common_name
    @hardness = hardness
  end
end

mine = Mineral.new("talc", 1.0)
puts typeof(mine) # => Mineral
```

There's a shortcut for assignment in the initialize method and for getters/setters:

```cr
class Mineral
  getter common_name : String
  setter common_name
  getter hardness : Float64

  def initialize(@common_name : String, @hardness : Float64)
  end
end
```

Modules can be mixed in:

```cr
module Hardness
  def data
    {"talc" => 1}
  end
end

class Mineral
  include Hardness
  # extend can be used to add methods to the class instead of the instance
end
```

Crystal's concurrency model is based on:

1. Fibers - lightweight threads created by the spawn method and controlled by Crystal
2. Channels - how fibers communicate with each other

```cr
chan = Channel(String).new
num = 10000
num.times do |i|
  spawn do
    chan.send "fiber #{i}: I like crystals!"
  end
  puts chan.receive
end
```

# Typing Variables and Controlling the Flow

Crystal is strongly typed. You can try to convert between types using `to_*` methods:

```cr
"64.34603985".to_i # Runtime error: Invalid Int32
rate = "64.34603985".to_f
rate.to_i # => 64
```

Int32 is the default integer type, even if you use numbers that will fit into Int8. Crystal offers
integer suffixes of: `i8, i16, i32, i64, u8, u16, u32, u64`.

```cr
arr = [75, 42, 126]
typeof(arr) # => Array(Int32)

arr = [75_i8, 42_i8, 126_i8]
typeof(arr) # => Array(Int8)
```

The following code will fetch numbers less than 256 and put them into an Array:

```cr
arr = [] of Int8
while num = gets # false, nil, and null pointers are considered false
  num = num.strip
  if num == "" || num == "stop"
    break
  end
  arr << num.to_i8
end
p arr
```

Exception handling works just like Ruby, via `begin, rescue, else, ensure, end`:

```cr
begin
  # some dangerous code
rescue ex
  p ex.message
else
  # if exception isn't raised
ensure
  # always executed
end
```

Crystal uses the `ARGV` constant to get arguments from the command line. Elements in this array are
Strings.

Symbols (`:gold`) can be used as identifiers. Every instance of a given symbol is the same object.

Enums exist to group related values:

```cr
enum Direction
  North # value 0
  East  # value 1
  South # value 2
  West  # value 3
end

Direction::South # South
Direction::South.value # => 2
```

Regular expressions use the PCRE syntax via the C library. Patterns are created with the `/pattern`
syntax or `%r(pattern)`. Use `=~` to determine if a string matches, which will returning the starting
position of the match or nil. If parenthesis are in your pattern, `$1, $2, ...` will return the
matched part. Or you can use `String#match` to return a MatchData object.

Other composite types:

* Tuples group related values: `{42, "silver", 'C'}` or `Tuple.new(42, "silver", 'C')`
* NamedTuples are fixed size hashes with keys known at compile time:
  `{name: "Crystal", year: 2017}`
* Sets don't have any order but store unique values: `Set{41, 42, 43}`

Nilable types can be declared like so: `n: Int32 | Nil` or `a : Int32?`. The method `#try` can be
used to use the variable, the block will return nil if no value exists.

For union types, you won't be able to use a method unless it's implemented in both types. If you're
sure a variable stores a specific type, use the `#as` method.

```cr
var1 = rand < 0.5 ? 42 : "Crystal"
typeof(var1) # => (Int32 | String)
var1.abs # => Error: undefined method 'abs' for String
ivar = var1.as(Int32) # => Error, can be either type
if ivar1 = var1.as?(Int32) # => 42 or nil, or var1.is_a?(Number)
  p ivar1.abs
end
```

`case` is a very flexible construct in Crystal. `when` matchers can use types, methods, or literals:

```cr
case var1
when Number
  # ...
when .even?
  # ...
when 2
  # ...
end
```

# Using Classes and Structs

Here's an example Crystal program:

```cr
class Mineral
  getter name : String
  getter hardness : Float64
  getter crystal_struct : String

  def initialize(@name, @hardness, @crystal_struct)
  end
end

def mineral_with_crystal_struct(crstruct, minerals)
  minerals.find { |m| m.crystal_struct == cstruct }
end

def longest_name(minerals)
  minerals.map { |m| m.name }.max_by { |name| name.size }
end

minerals = [
  Mineral.new("gold", 1.0, "cubic"),
  Mineral.new("topaz", 8.0, "orthorombic"),
  Mineral.new("apatite", 5.0, "hexagonal")
]

min = mineral_with_crystal_struct("hexagonal", minerals)
if min # required to check for Nil, otherwise you'll get a compilation error
  puts "#{min.crystal_struct} - #{min.name} - #{min.hardness}"
else
  puts "No mineral found!"
end

puts longest_name(minerals)
```

Types and default values can be assigned in the `#initialize` definition. A property without a type
must have a default value, or the value must be assigned in the method.

```cr
getter name, hardness, crystal_struct

def initialize(@name : String = "unknown",
               @hardness : Float64,
               @crystal_strct : String)
end
```

Generics can be used like:

```cr
class Mineralg(T)
  getter name

  def initialize(@name : T)
  end
end

Mineralg.new("gold")
Mineralg.new(42)
Mineralg(String).new(42) # => Error: no overload matches
````

Getters use `getter`, setters use `setter`. For both use `property`. Class variables use the `@@`
prefix. Classes can have a `finalize` method which is called when it's garbage collected, but it's
a burden on the garbage collector. Only use it to free resources taken by external resources.
You can re-open classes.

Classes are inherited via `<`, eg `class PDFDocument < Document`. Use `super` in a method definition
to call the parent class method. Overridden methods may use different argument types. Crystal also
supports abstract classes:

```cr
abstract class Shape
  abstract def area
  abstract def perim
end

class Rect < Shape
  def initialize(@width : Int32, @height : 32)
  end

  def area
    @width * @height
  end

  def perim
    2 * (@width + @height)
  end
end

s = Shape.new # => abstract classes cannot be instantiated
Rect.new(3, 6).area # => 18
```

If an abstract method isn't implemented, you'll get a compilation error.

Crystal supports virtual classes, for example:

```cr
class Document
end

class PDFDocument < Document
  def print
  end
end

class XMLDocument < Document
  def print
  end
end
```

Document is a virtual class, indicated as `Document+`. It's equivalent to all types that inherit
from Document including itself. It's the equivalent to `Document | PDFDocument | XMLDocument`.
The `print` method cannot be used here, to remove the compilation error just make Document abstract.

Visibility can be restricted using `private` or `protected` prefixing the method definition. Private
methods cannot have a receiving object, so can only be used in the same class or its descendants.
Protected methods can only be used if self is of the same type.

Class methods are prefixed with `self.` like `def self.compare`. Note that the Ruby `class << self`
does not exist in Crystal.

Objects from classes use heap memory and are garbage collected. Structs are allocated in stack
memory and are passed by value.

```cr
struct User
  property name, age

  def initialize(@name : String, @age : Int32)
  end

  def print
    puts "#{age} - #{name}"
  end
end
```

Structs work best for immutable data structures, especially when you have a lot of small ones. They
can only inherit from abstract structs.

Use `crystal tool hierarchy <filename.cr>` to display the entire class hierarchy. Use the `-e`
flag for a specific fragment: `crystal tool hierarchy -e Document <filename.cr>`.

Types can be given a synonym with `alias` eg `alias PInt32 = Pointer(Int32)`

Use `#to_s(io)` for performance. Creating a lot of strings will clog up the heap. Appending many
to an IO object won't create intermediate strings:

```cr
def to_s(io)
  io << name << ", " << hardness
end
io = IO::Memory.new
to_s(io).to_s
```

Define your own exceptions: `class CoolException < Exception`. Rescuing exceptions can use pattern
matching: `rescue ex : CoolException | KeyError`.

Define a list of procs like `@callbacks = [] of ->`. Then call them like `@callbacks.each &.call`.

# Working with Modules

`require` will search for files on the current path. Subsequent requires for a previously required
file will do nothing. `require "./part"` searches for part.cr or part/part.cr. Use ".." for the
parent path. Use `*` for all .cr files in a directory: `require "./dirA/*"`. Double splats work
too.

When not using the dot prefix. Crystal will look for the files in several spots. First is the
standard library (/opt/crystal/src on Linux). The second is the lib folder in the current working
folder.

Use `module` to define namespaces. Top-level methods in modules are prefixed with `.self` just like
class methods.

```cr
module Crystals
  class Rhombic
    def self.print
      puts "Hello"
    end
  end
end

Crystals::Rhomic.new
typeof(Crystals) # => Class
```

If you don't want to write `def self.` repeatedly, you can use `extend self` in the module
definition. It's a common idiom. `include` a module to include all of its definitions in the current
namespace -- also known as mixing in. Modules can include abstract methods, every class that includes
it must have a specific implementation. Modules are prepended into the ancestor list.

Some built-in modules that are useful:

* Comparable: for `<=>` and `==`
* Enumerable: for working with `#each` and iterators

# Managing Projects

Use the Crystal tool chain to manage projects, also called shards.

```sh
crystal init TYPE NAME [DIR]
```

The type can be `app` or `lib`. The directory is optional.

```sh
crystal init app proj1
crystal src/proj1.cr # to run it
crystal build src/proj1.cr # to build a binary file, proj1
./proj1

crystal tool format file.cr # to use recommended coding style
crystal docs # to generate doc/index.html
crystal spec # run tests
crystal spec spec/file_spec.cr

crystal shards # installs dependencies
```

Crystal uses the testing framework `spec`, located in the spec directory.

```cr
describe Mineral do
  it "works" do
    false.should eq(true)
  end
end
```

To use an external library, update the `shard.yml` file and run `crystal shards`. You can see what's
installed with `shards list`.

Use the `--release` flag when you're ready to deploy your code. You'll get a single binary.

# Advanced Features

Macros can be used for metaprogramming. It looks like:

```cr
macro get(*props)
  {% for prop in props %}
    def {{prop}}
      @{{prop}}
    end
  {% end %}
end

class Mineral
  def initialize(@name : String, @hardness : Float64)
  end

  get name, hardness
end
```

There's also {% if %} and {% else %}. Similar to Ruby's method_missing, there's a macro:
`macro method_missing(call); # ...; end`. Other macros invoked at compile time include:

* inherited - when a subclass is defined
* included - when a module is included
* extended - when a module is extended

When a Crystal program starts, a main fiber executes top level code. A Runtime Fiber Scheduler
takes care of executing all fibers in the queue. A non-blocking Event Loop fiber is in charge of
IO. And there's a Garbage Collector fiber to clean up memory.

Fibers communicate via channels:

```cr
ch = Channel(Int32).new

spawn do
  puts "start fiber"
  n = ch.receive # fiber will block if nothing on channel yet
  puts "fiber received #{n}"
end

puts "before send"
ch.send 42
puts "main has send 42"
```

`spawn` can also take a method name along with arguments for that method. When working with concurrent
channels, Crystal has `select` and `when`:

```cr
loop do
  select
  when n1 = ch1.receive
    puts n1
  when f1 = ch2.receive
    puts f1
  when ch3.receive
    break
  end
end
```
