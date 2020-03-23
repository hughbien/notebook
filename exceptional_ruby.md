# Exceptional Ruby

Notes for [Exceptional Ruby](https://exceptionalruby.com) by Avdi Grimm.

The two goals for this book:

1. Have a more complete understanding of Ruby exception handling
2. Have patterns/idioms to implement robust failure handling strategies

# What is a Failure?

Exception - occurrence of abnormal condition during execution

Failure - inability of software element to satisfy its purpose

Error - presence in software element not satisfying its specification

Methods have a contract with their callers. A method has failed when it fails to fulfill this contract.
A contract states "given the following inputs, I promise to return certain outputs and/or cause
certain side-effects". The caller ensures the methods preconditions (inputs). The method ensures its
postconditions (outputs and side-effects). The method also ensures the invariant of its object (state
of the object).

Failures may have many reasons. A robust program needs a plan for handling exceptional conditions.

# Life-Cycle of an Exception

Ruby uses `raise` or `fail` to raise exceptions. They're synonyms. Raising seems to be more popular
now in Ruby codebases.

```ruby
raise [EXCEPTION_CLASS], [MESSAGE], [BACKTRACE]
```

These are all equivalent:

```ruby
raise
raise RuntimeError

raise "Doh!"
raise RuntimeError, "Doh!"

raise ArgumentError, "Doh!"
raise ArgumentError.new("Doh!")
```

The backtrace argument is useful for methods like `#assert`, which usually spits out backtrace for
debugging purposes:

```ruby
raise RuntimeError, "Doh!", caller
```

Backtraces in Ruby are an array of strings, each in the format: `<FILE>:<LINE>:<CONTEXT>`. For
example: `prog.rb:2:in 'foo'`

`#raise` and `#fail` are both Ruby methods on `Kernel`. It does four things:

1. call `#exception` to get the exception object
2. set the backtrace
3. set the global exception variable `$!`
4. throws the exception object up the call stack, either to a `rescue` or `ensure`

Explicit returns from ensure blocks will override the raised exception. Avoid using them.

Rescue without an argument will work with all exception types except: `NoMemoryError`, `LoadError`,
`NotImplementedError`, `SignalException`, `Interrupt`, `ScriptError`. It will not rescue bare
`Exception`. It will rescue any `StandardError` and its descendants.

These are equivalent:

```ruby
rescue
rescue StandardError

rescue IOError, SystemCallError
exceptions = [IOError, SystemCallError]; rescue *exceptions
```

You can define a custom matcher as a rescue-er:

```ruby
def starts_with_a = Object.new
def starts_with_a.===(e)
  /^A/ =~ e.name
end

begin
  raise ArgumentError, "Bad Argument"
rescue starts_with_a => e
  puts "#{e} starts with a; ignored"
end
```

Rescue can also be used as a statement modifier:

```ruby
f = open("nonesuch.txt") rescue nil
file_or_exception = open("nonesuch.txt") rescue $!
```

Exceptions can be re-raised within a rescue block:

```ruby
begin
  raise ArgumentError, "Bad Argument"
rescue
  # do something
  raise
end
```

Uncaught exceptions are handled by Ruby, which will print the stack trace and terminate the program.
In non-main threads, the thread terminates and the exception is re-raised when another thread joins.

# Responding to Failures

*Failure flags and benign values* can be used, such as returning nil:

```ruby
def save
  # ...
rescue
  nil
end

begin
  resosne = HTTP.get_response(url)
  JSON.parse(response.body)
rescue Net::HTTPError
  {"stock_quote" => "<Unavailable>"}
end
```

*Reporting failures to the console* by printing to stderr is a common technique:

```ruby
$stderr.puts "Something bad happened..."
warn "Something bad happened..."  # output of warn can be temporarily silenced with -W0 ruby flag
```

You can track down warnings by overriding it:

```ruby
if $DEBUG
  module Kernel
    def warn(message)
      raise message
    end
  end
end
```

*Implement remote failure reporting* via a central log server, an email, or a third party exception
reporting service.

*Use bulkheads* which are metal partitions on ships to divide them into watertight compartments.
One error in a single compartment won't bring down the entire ship. It's a good idea to put them in
external services and processes. Use them in your system like:

```ruby
begin
  SomeExternalService.some_request
rescue Exception => error
  logger.error "Exception intercepted calling SomeExternalService"
  logger.error error.message
  logger.error error.backtrace.join("\n")
end
```

*Use the Circuit Breaker pattern*. It's a mechanism that controls operations. It has three states:
closed which allows subsystems to operate normally. A counter tracks number of failures. When threshold
is exceeded, it enters the open state -- subsystem is not permitted to operate. After a timeout or
human intervenes, it enters the half-open state where the subsystem is in probation and a single
failure will send it back into the open state.

*Exit the program.* Call exit with a non-zero value to exit with an error code. These are equivalent:

```ruby
exit 1
raise SystemExit.new(1)

warn "Uh oh"; exit(1)
abort "Uh oh"

```

# Alternatives to Exceptions

*Sideband data* is a secondary channel of communication for reporting meta information about the
status of a process. The simplest sideband is using multiple return values or a struct:

```ruby
return [result, status]
return OpenStruct.new(result: 42, status: status)
```

You could also use an output parameter. For example, a transcript that captures additional info:

```ruby
def make_user_accounts(host, transcript=StringIO.new)
  transcript.puts "Making user accounts..."
  # ...
end

def install_packages(host, transcript=StringIO.new)
  transcript.puts "Installing packages..."
  # ...
end
```

Try a *caller-supplied fallback strategy* to inject a failure policy into a process:

```ruby
def make_user_accounts(host, failure_policy=method(:raise))
  # ...
rescue => error
  failure_plicy.call(error)
end
```

The final solution is to represent the process as an object and give the object an attribute for
collecting status data:

```ruby
class Provisionment
  attr_reader :problems
  def initialize
    @problems = []
  end
  def perform
    # ...
    @problems << "Failure downloading key file..."
  end
end
```

# Your Failure Handling Strategy

Exceptions shouldn't be expected. They should be used only for exceptional situations. Ask yourself,
will this code still run if I remove all exception handling? If the answer is no, maybe the exceptions
are being used in non-exceptional cases.

Use `throw` for expected cases:

```ruby
throw :halt
# ...
catch :halt do
  # do something ...
end
```

What constitutes an exceptional case though? It depends. Sometimes it's best to let the caller
determine by using the caller supplied fallback strategy. Some questions to ask yourself:

1. Is the situation truly unexpected? Maybe you can just change the program or user experience,
   such as looping and asking for a Y/N reply instead of exiting on bad input.
2. Am I prepared to end the program?
3. Can I punt the decision up the call chain?
4. Am I throwing away valuable diagnostics?
5. Would continuing result in a less informative exception?

*Isolate exception handling code.* Programs that use exceptions as part of their normal processing
suffer from all the readability and maintainability of spaghetti code. Here's a bad example:

```ruby
begin
  try_something
  rescue
    begin
      try_something_else
    rescue
      # handle failure
    end
  end
end
```

Instead, refactor to clearly separate main logic from error handling:

```ruby
def foo
  # mainline logic goes here
rescue
  # failure logic goes here
end
```

This sometimes means you'll have to refactor your code to break out smaller methods for the implicit
begin/end block. Grimm calls these methods contingency methods.

Should a library attempt to recover when something goes wrong? Not usually, but it's useful to
leave information as clean and harmless a state as possible. A method's *exception safety* describes
how it will behave in the presence of exceptions:

* weak guarantee - if exception is raised, object will be left in consistent state
* strong guarantee - if exception is raised, object will be rolled back to its beginning state
* nothrow guarantee - no exceptions will be raised, if one is it will be handled internally
* no guarantee

Note that consistency just means the object can operate without crashing or exhibiting undefined
behavior. It doesn't mean the object is valid -- its business rules are met.

It's usually good to *namespace your own exceptions*. A good way to do this in Ruby is to inherit
from StandardError:

```ruby
module MyLibrary
  class Error < StandardError; end
end
```

Use `MyLibrary::Error` when raising and rescuing now, to avoid rescuing exceptions outside the scope
of your library. You can even document this to your users so they can scope rescues to your library.

Going one step further, you can *tag exceptions*. This is similar to namespacing, except users of
your library can also rescue the original exception type:

```ruby
def tag_errors
  yield
rescue Exception => error
  error.extend(MyLibrary::Error)
  raise
end

# now if an IOError is raised, both lines will work:
rescue IOError
rescue MyLibrary::Error
```

*With a no-raise API, you completely delegate decisions on how to handle failures to client code.*
For example, the Typhoeus library always responds a Typhoeus::Response object for HTTP requests.
Instead of raising an exception for 4xx or 5xx status codes, you can check the response object:

```ruby
if response.success?
  # ...
elsif response.timed_out?
  # ...
elsif response.code == 0
  # ...
else
  # ...
end
```

There are three classes of exceptions:

1. User Error - user did something invalid or not allowed, they can fix the problem
2. Logic Error - error in the system, typically let the user know the problem is being looked at
3. Transient Error - something is over capacity or offline, let user know they can try again later
