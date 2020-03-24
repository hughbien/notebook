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

Crystal is strongly type. 

# Organizing Code in Methods and Procs

# Using Classes and Structs

# Working with Modules

# Managing Projects

# Advanced Features

# Using Web Frameworks and Shards
