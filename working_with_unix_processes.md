# Working With Unix Processes

An introduction to Unix Processes using Ruby by Jesse Storimer.

# Primer

This book is a walkthrough that uses the Spyglass server, which was created specifically for this
book. We'll be making system calls. In a Unix system, there's two parts: userland and the kernel.

The kernel sits atop the hardware. It's the middleman between any interactions with the hardware.
This includes: writing/reading from filesystem, sending data over network, allocating memory, or
playing audio. Programs can't have direct access to the kernel (which has too much power), it must
use system calls.

System calls connect the kernel to userland. Userland is where your programs run. You can do a lot
without system calls: math, string operations, control flow.

When a system call is in the format `name(2)`, it means you can look it up in section 2 of the
man pages. Section 1 is general commands, 2 is system calls, 3 is C library functions, and 4 is
special files. Use `man 2 name` to access section 2. Leaving out the integer will default to section
one.

Processes are the atoms or building blocks of Unix. Any code executed happens inside a process.
When you launch `ruby` from the command line, a new process is created. When your code finishes,
the process exits.

# Processes Have IDs

Every process has a unique process identifier or `pid`. In ruby, you can get the current pid via
`Process.pid` (aliased to `$$`).

Use `ps -p <pid>` to get a full picture of the process. It's commonly used in log files or other
programs that references processes.

`Process.pid` maps to `getpid(2)`.

# Processes Have Parents

Every process has a parent process, identified by the parent process identifier or `ppid`. Access
it via `Process.ppid`. This can be important for daemon processes or dealing with forking processes.

`Process.ppid` maps to `getppid(2)`.

# Processes Have File Descriptors

In Unix, everything is a file. Devices, sockets, pipes, files, and processes are treated like files.
This book will use the word resource to talk about files in a general sense, and the word file for
the classic definition of file on filesystem.

Processes are assigned a file descriptor number. Use `IO#fileno` to access it:

```rb
passwd = File.open("/etc/passwd")
puts passwd.fileno
```

File descriptor numbers are re-used. If you close a resource and open up a new one, it may have
the same number. These numbers only keep track of open resources.

Every Unix process comes with three open resources: `STDIN`, `STDOUT`, and `STDERR`. Standard
input reads input from keyboard devices or pipes. Standard out and standard error lets you write
out to monitors, files, or printers.

```rb
[STDIN, STDOUT, STDERR].map(&:fileno) # => [0, 1, 2]
```

Ruby's IO class maps to system calls of the same name: `open(2)`, `close(2)`, `read(2)`, `write(2)`,
`pipe(2)`, `fsync(2)`, and `stat(2)`.

# Processes Have Resource Limits

There are resource limits imposed on a process by the kernel.

```rb
p Process.getrlimit(:NOFILE) # max number of allowed file descriptors: [soft limit, hard limit]
```

You can set the limits with `Process.setrlimit(:NOFILE, soft_limit, hard_limit)`

The `Errno::EMFILE` exception will be raised when exceeding the soft limit.

`#getrlimit` and `#setrlimit` map to `getrlimit(2)` and `setrlimit(2)`.

# Processes Have an Environment

Environment variables are key value pairs that hold data for a process. Every process inherits
its parent's environment variables.

```sh
$ MESSAGE="wing it" ruby -e "puts ENV['MESSAGE']"
```

Or in reverse:

```rb
ENV["MESSAGE"] = "wing it"
system "echo $MESSAGE"
```

Environment variables are used as a generic way to accept input in a command line program.

Documentation for this chapter: `setenv(3)`, `getenv(3)`, and `environ(7)`.

# Processes Have Arguments

Every process has access to an argument array called `ARGV`, short for argument vector.

```rb
# in argv.rb
p ARGV
```

Running `ruby argv.rb foo bar -va` will print `["foo", "bar", "-va"]`.

`optparse` is part of the standard library and is useful for parsing `ARGV`.

# Processes Have Names

Every process has a name. For example, `irb` will use the name "irb". Names can be changed at
runtime. They can be used as a method of communication.

In Ruby, the name for the current process is in `$PROGRAM_NAME`. This can be assigned to also. It's
also aliased to `$0`.

# Processes Have Exit Codes

When a process ends, it uses a numeric code (0-255) to denote if it exits successfully or with
an error. An exit code of 0 is successful. Anything else is an error.

Use `Kernel#exit` to exit a Ruby program. It exits with default zero code. Pass an integer to exit
with a different exit code. Ruby has a `Kernel#at_exit` callback which will be called:

```rb
at_exit { puts "Last!" }
```

`Kernel#exit!` defaults to exit code 1 and will not run the `at_exit` hook. `Kernel#abort` exits with
code one by default. Pass a string to print to standard error. `Kernel#raise` is similar to abort,
but it may be caught with an error handler.

# Processes Can Fork

A parent process can fork a child process. The child process inherits all of the parent's memory
by making a new copy. Use `Process.ppid` to get the parent process ID. Forking is fast, so forking
a single large process may be faster than spinning up a new one.

```rb
if fork
  puts "entered the if block"
else
  puts "entered the else block"
end
```

Will actually output both lines. This is because `fork` returns twice, once for the parent and once
for the child. It returns the `pid` of the child process, so the parent will enter the if block.
It returns `nil` for the forked process, so the child will enter the else block.

You can also pass a block to `fork` which will only be executed in the child process. It exits when
it's done executing, so any code following it will only be executed in the parent process.

`Kernel#fork` maps to `fork(2)`.

# Orphaned Processes

Nothing happens to a child process when the parent dies. The child process continues to live on.

Daemon processes are long running processes that are intentionally orphaned and are meant to stay
running forever.

You can use Unix signals to communicate with processes that are not attached to a terminal session.

# Processes Are Friendly

Unix employs copy-on-write semantics (CoW). The parent/child process shares same physical data in
memory until one of them needs to modify it, at which point the memory is copied over.

# Processes Can Wait

`Process.wait` is a blocking call, it waits until one of its child process exits. It returns the
pid of that child process. You can use it for "babysitting", having a parent process stick around
until its child exits.

`Process.wait2` returns 2 values (pid, status). The status gives you access to the exit code, an
instance of Process::Status.

`Process.waitpid` and `Process.waitpid2` will let you wait for a specific process to exit. It takes
the process id as input.

Internally, the kernel uses queue to prevent race conditions. The parent will always receive
information in the correct order.

`Process.wait` and cousins map to `waitpid(2)`.

# Zombie Processes

A zombie process has finished execution but still has an entry in the process table to report to
its parent process.

If you're not going to wait for a child process to exit, then you need to detach the child process.
You don't want to create zombie processes.

```rb
pid = fork do
  # ...
end
Process.detach(pid)
```

`Process.detach` spawns a new thread to wait for the child process to exit, so the kernel doesn't
hang on any status information we don't need.

Using `ps`, processes with `z` or `Z+` mean it's a zombie.

# Processes Can Get Signals

Process signals are a way for processes to handle events or communicate.

```rb
3.times do
  fork do
    sleep 3
  end
end

dead_processes = 0
trap(:CHLD) do # run whenever one of this process's child exits
  puts Process.wait
  dead_processes += 1
  exit if dead_processes == 3
end
```

Signal delivery is unreliable. If your code is handling a CHLD signal while another child process
dies, you may or may not receive a second CHLD signal. To properly handle it, you must call
`Process.wait` in a loop and look for as many dead child processes as are available. Use the
`Process::WNOHANG` flag to keep waiting from blocking:

```rb
Process.wait(-1, Process::WNOHANG)
```

Signals are asynchronous communication. When a process receives a signal, it can: ignore it, perform
a specified action, perform a default action.

In one ruby session, run a script that does: `puts(Process.pid); sleep`.

In a second ruby session, run a script that does: `Process.kill(:INT, <pid of first session>)`.

INT is short for INTERRUPT. The default for this action is to interrupt whatever is happening and
exit immediately.

Some signals actions:

* Term - terminate immediately
* Core - terminate immediately and dump core
* Ign - ignore signal
* Stop - process will stop/pause
* Cont - process wil resume/unpause

Use `Kernel#trap` to handle signals and `Process.kill` to send signals. Note that `trap` will
overwrite existing handlers. Use this pattern to extend existing ones:

```rb
trap(:INT) { puts "this is the first handler" }
old_handler = trap(:INT) {
  old_handler.call
  puts "this is the second handler"
  exit
}
```

This only works with Ruby code handlers, not with default system handlers.

`Process.kill` maps to `kill(2)` and `Kernel#trap` maps go `sigaction(2)`. Also read `signal(7)`.

# Processes Can Communicate

Processes can communicate with each other via IPC or Inter-Process Communication. Commonly used
methods include pipes and socket pairs.

A pipe is uni-directional. One process will write, the other will read:

```rb
reader, writer = IO.pipe # => [#<IO:fd 5>, #<IO:fd 6>]

fork do
  reader.close

  10.times do
    writer.puts "Another one bites the dust"
  end
end

writer.close
while message = reader.gets
  $stdout.puts message
end
```

Note that `gets` and `puts` uses newlines as delimiters, whereas read/write would use EOF.

It's also possible to communicate via messages with Unix sockets.

```rb
require "socket"

child_socket, parent_socket = Socket.pair(:UNIX, :DGRAM, 0)
maxlen = 1000

fork do
  parent_socket.close
  4.times do
    instruction = child_socket.recv(maxlen)
    child_socket.send("#{instruction} accomplished!", 0)
  end
end
child_socket.close

2.times do
  parent_socket.send("Heavy lifting", 0)
end
2.times do
  parent_socket.send("Feather lifting", 0)
end
4.times do
  $stdout.puts parent_socket.recv(maxlen)
end
```

# Daemon Processes

Daemon processes run in the background. Common examples include: web servers, database servers.
The very first process on the system is a daemon process. To daemonize, simply call `Process.daemon`.

Under the hoods, it does:

```rb
exit if fork 
Process.setsid
exit if fork
Dir.chdir "/"
STDIN.reopen "/dev/null"
STDOUT.reopen "/dev/null", "a"
STDERR.reopen "/dev/null", "a"
```

First it exits the parent process, spawning a new process. The orphaned process will have a ppid of
`1`. Calling `Process.setsid` means the process becomes a session leader, it becomes the group leader,
and it has no controlling terminal. Then it spawns a new orphaned process again. Finally, it changes
current directory to be the root and redirects all standard streams to be ignored.

# Spawning Terminal Processes

`execve(2)` lets you replace the current process with a different one. It transforms the current
process. `fork` + `exec` is a powerful combo. Use `fork` to create a new process, then `exec` to
transform it. Fork lets you retain the old process.

You can execute other processes with `exec`, `Kernel#system`, or the backtick. `Kernel#system` returns
the exit code via true/false. The backtick collects STDOUT and returns it as a String.

`Process.spawn` is a non-blocking call. `IO.popen` does fork+exec under the hoods, but using
Unix pipes. `Open3.popen3` lets you access STDIN, STDOUT, and STDERR of the spawned process.
