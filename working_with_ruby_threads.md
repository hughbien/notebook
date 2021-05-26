# Working With Ruby Threads

A guide to Ruby threads by Jesse Storimer.

Your code must be written to take advantage of multiple CPU cores using some concurrency. Threads
offer concurrency with a lower cost than spawning processes.

# You're Always in a Thread

There's always at least one thread: the main thread.

```rb
Thread.main
Thread.current == Thread.main  # => true
Thread.new { Thread.current == Thread.main }  # => false
```

When the main thread exits, all other threads are immediately terminated and the Ruby process exits.

# Threads of Execution

Threads have a shared address space, with shared memory (variables) and AST (compiled source code).
The code below has a race condition:

```rb
require "thread"

class FileUploader
  def initialize(files)
    @files = files
  end

  def upload
    threads = []
    @files.each do |(filename, file_data)|
      threads << Thread.new {
        status = upload_to_s3(filename file_data)
        results << status
      }
    end
    threads.each(&:join)
  end

  def results
    @results ||= Queue.new
  end

  def upload_to_s3(filename file)
    # uploads to s3
  end
end

FileUploader.new(
  "boots.png" => "*pretend png data*",
  "shirts.png" => "*pretend png data*"
).upload
```

The race condition happens at `@results ||= Queue.new`. It's entirely possible that the thread
scheduler schedules two concurrent threads to call `results << status` at near the same time. Which
may cause `results` two be assigned to two different Queues. In which case, one result will be lost.

# Lifecycle of a Thread

The `Thread` constant is already loaded by default. `require "thread"` brings in utility classes
like `Queue`.

Spawning a thread happens with `Thread.new { ... }` or `Thread.fork { ... }`. You can also pass in
variables:

```rb
Thread.start(1,2) { |x,y| x + y }
```

Use `Thread#join` to wait for it to finish.

```rb
thread = Thread.new { sleep(3) }
thread.join # the current thread will sleep until spawned thread exits
puts "You'll have to wait 3 seconds to see this."
```

If one thread raises an unhandled exception, it terminates the thread where the exception was raised,
but it won't affect other threads. If it's joined, the current thread will re-raise the exception.

`Thread#value` is similar to join, but it returns the last expression from the block of code the
thread executed.

`Thread#status` is useful for checking the status of another thread. Use `Thread.current.status`
to check status on current thread. Possible values:

* `"run"` - currently running
* `"sleep"` - currently sleeping, blocked waiting for a mutex, or waiting on IO
* `false` - finished executing their block of code, or were successfully killed
* `nil` - raised an unhandled exception
* `"aborting"` - currently running, yet dying

`Thread.stop` puts current thread to sleep and tells thread scheduler to schedule some other thread.
It remains sleeping until `Thread#wakeup` is called.

`Thread.pass` is similar to stop, but it doesn't put the thread to sleep. It does ask scheduler to
schedule another thread.

Avoid `Thread#raise` and `Thread#kill`. They don't properly respect ensure blocks.

# Concurrent != Parallel

Single-threaded codes runs serially.

Multi-threaded code running on a single CPU runs concurrently. Multiple threads can interleave with
each other. But the single CPU cannot execute two threads at the same time.

Multi-threaded code running on a multi-core CPU can run in parallel. Multiple threads can execute
at the same time. It's up to the thread scheduler (not you).

# The GIL and MRI

MRI allows concurrent execution of Ruby code but prevents parallel execution. JRuby and Rubinius
do not have a GIL and allow parallel execution.

The Global Interpreter Lock is a global lock around execution of Ruby code. There is one GIL per
instance of MRI process. If a process spawns multiple threads, they all share the same GIL. If any
thread wants to execute Ruby code, it will acquire the lock.

Blocking IO does not count as Ruby code. If a thread is waiting on IO, it releases the GIL so another
thread can execute Ruby code.

The GIL exists:

1. to protect MRI internals from race conditions
2. to facilitate the C extension API, which acquires the GIL just like corresponding Ruby code
3. to reduce likelihood of race conditions in your Ruby code

# Real Parallel Threading with JRuby and Rubinius

JRuby and Rubinius don't have a GIL. They both allow parallel execution of Ruby code. Other
implementations solve the GIL problems:

1. protecting internals from race conditions - JRuby/Rubinius uses fine-grained locks in their internals
2. facilitate the C extension API - JRuby doesn't support C extensions. Rubinius opts to help gem
   authors fix thread-safety issues
3. reduce race conditions in Ruby code - no answer to this one, authors are expected to be aware
   of thread safety issues

# How Many Threads Are Too Many?

The only way to be sure is to measure and compare. Try with one thread per CPU core, then 5 threads
per CPU core, try more and compare. Some heuristics to follow:

* try as many threads as possible, eg 10k -- usually not what you want as threads still have
  some overhead to manage them
* usually you want to spawn as many threads as CPU cores, but if there's IO-bound code you'll want
  more threads than CPU cores
* use `Benchmark.bm` to test your hypothesis
* CPU-bound code will max out at same number of threads as CPU cores
* your application will most likely be IO-bound in some places and CPU-bound in others

Rails is a prime example of IO and CPU bounded code. Communicating with the database, client, external
services are a lot of chances of IO-bound. Rendering HTML templates, transforming data to JSON are
examples of CPU-bounded code. The only surefire answer is the measure.

# Thread Safety

Thread-safe means your code can run in a multi-threaded context and your underlying data (in memory)
will be safe. When it's not thread-safe, the underlying data becomes incorrect.

There's a "check-then-set" race condition, which happens when code first checks a condition before
it changes some value:

```rb
Thread.new do
  if order.pending?
    order.collect_payment
  end
end
```

The problem here is that the status might change between the conditional check and the call to
collect payment, in a multi-threaded environment. The code needs to be synchronized to function
correctly.

Very few things are thread-safe by default. Even single operators in Ruby may not be single, atomic
operations in the underlying virtual machine. The same is true for `+=`, `||=`, or `Array`/`Hash`
operations. **Any concurrent modifications to the same object are not thread-safe.**

# Protecting Data with Mutexes

A mutex, short for mutual exclusion, provides a lock to synchronize access to critical portions
of code. If you wrap code in a mutex, you guarantee no two threads can enter that section at the
same time.

```rb
mutex = Mutex.new
Thread.new do
  mutex.lock
  if order.pending?
    order.collect_payment
  end
  mutex.unlock
end
```

The mutex above fixes the race condition. You can also use `Mutex#synchronize` and pass in a block:

```rb
mutex.synchronize do
  if order.pending?
    order.collect_payment
  end
end
```

Note that the local variable `mutex` needs to be a shared instance between the threads. Until the
owning thread unlocks the mutex, no other thread can lock it. This is guaranteed by the system
call to `pthread_mutex_init(3)`.

Mutexes carry an implicit memory barrier. When one mutex is locked, a memory barrier provides proper
memory visibility. If one thread holds a mutex to write a value, other threads can lock the same
mutex to read it and they will see the correct, most recent value.

There is a tradeoff. Mutexes inhibit parallelism. Make sure to only include critical sections of code
in the mutex. For example, don't put a network call in the `Mutex#synchronize` if it's not necessary,
which will slow down parallelism for no added benefit. **Put as little code in your critical sections
as possible**.

Deadlocks happen when multiple threads are waiting for the same resources, blocking each other and
causing an infinite stalemate. For example, Thread A needs Mutex A and Mutex B. Thread B needs Mutex
B and Mutex A. If they run concurrently and Thread A locks Mutex A, while Thread B locks Mutex B --
both threads enter a deadlock and neither can proceed.

This can be resolved with `Mutex#try_lock`, which attempts to acquire a lock but will not wait
if the mutex isn't available. Instead it will return false. You might be tempted to keep attempting
to lock until it passes, which should happen eventually due to the non-deterministic nature of threads.
But you might enter a livelock, where a system stops proceeding but gets stuck in a loop where two
threads continually attempt to lock the same mutexes.

Another alternative is to use a Mutex hierarchy. From the previous example, if both Thread A and B
try to acquire Mutex A and B in the same order -- the deadlock will never occur.

# Signaling Threads with Condition Variables

`ConditionVariable` can be used to signal one or more threads when an event happens. A thread can
wait (sleep) until being signaled.

```rb
require "thread"
require "net/http"

mutex = Mutex.new
condvar = ConditionVariable.new
results = Array.new

Thread.new do
  10.times do
    response = Net::HTTP.get_response("dynamic.xkcd.com", "/random/comic/")
    random_comic_url = response["Location"]

    mutex.synchronize do
      results << random_comic_url
      condvar.signal # signal the ConditionVariable
    end
  end
end

comics_received = 0
until comics_received >= 10
  mutex.synchronize do
    while results.empty?
      condvar.wait(mutex) # sleep until signaled
    end
    url = results.shift
    puts "You should check out #{url}"
  end
  comics_received += 1
end
```

In the example above, the main thread sleeps until it's signaled by the `ConditionVariable`. If
no threads are waiting, `ConditionVariable#signal` is a no-op. `#wait` needs to receive a locked
mutex. It unlocks that mutex on initial call to `#wait` and will re-lock it whenever the `condvar`
is signaled. The `while` check is a fail-safe in case other threads remove the results.

`ConditionVariable#signal` will wake up exactly one thread. `ConditionVariable#broadcast` will wake
up all threads currently waiting.

# Thread-Safe Data Structures

`Queue` is a thread-safe data structure that's included on `require "thread"`.

Let's implement our own thread-safe data structure, a blocking queue that blocks the current thread
on `#pop` until there's something in it:

```rb
q = BlockingQueue.new
q.push "thing"
q.pop # => "thing"
```

The implementation:

```rb
class BlockingQueue
  def initialize
    @storage = Array.new
    @mutex = Mutex.new
    @condvar = ConditionVariable.new
  end

  def push(item)
    @mutex.synchronize do
      @storage.push(item)
      @condvar.signal
    end
  end

  def pop
    @mutex.synchronize do
      while @storage.empty?
        @condvar.wait(@mutex)
      end
      @storage.shift
    end
  end
end
```

For thread-safe Array or Hash, Storimer recommends the rubygem <https://github.com/headius/thread_safe>.

Immutable data structures are inherently thread-safe.

# Writing Thread-Safe Code

Some tips to write thread-safe code:

* idiomatic Ruby code is most often thread-safe Ruby code.
* avoid mutating global objects, which are implicitly shared between all threads
* if there is one shared instance (aka singleton), it counts as global
* modifying the AST at runtime is almost always a bad idea, threads will have inconsistent code
* create more objects rather than sharing one (try thread locals)
* use resource pool pattern
* avoid lazy loading
* prefer thread-safe data structures over mutexes

For thread locals:

```rb
# instead of
$redis = Redis.new
# use
Thread.current[:redis] = Redis.new # instantiates a new Redis for every spawned thread
```

# Thread-Safety on Rails

Some tips for thread-safe Ruby on Rails:

* make sure your gem dependencies are thread-safe
* the request is the boundary, don't share objects between requests (eg `User.current`)

# Wrap Your Threads in an Abstraction

From Robert Martin's Clean Code: **one level of abstraction per function** is a good principle
to follow. Mutexes/threads are a low level concern. Don't mix high level code with it. For example,
the thread-safe file uploader could extract out:

```rb
module Enumerable
  def concurrent_each
    threads = []
    each do |element|
      threads << Thread.new {
        yield element
      }
    end
    threads.each(&:join)
  end
end
```

Consider using Celluloid and the actor model. This abstracts threads/mutexes, so you can simply write
a class which `include Celluloid` and then call `Klass.new.async.method`.

# Closing

The safest path to concurrency:

1. don't do it
2. if you must do it, don't share data across threads
3. if you must share data across threads, don't share mutable data
4. if you must share mutable data across threads, synchronize access to that data
