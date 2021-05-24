# Working With TCP Sockets

An introduction to TCP Sockets using Ruby By Jesse Storimer. Main focus will be Berkeley Sockets API.
TCP stands for Transmission Control Protocol. It's used on top of IP to ensure reliable transmission
of packets.

# Part 1: Introduction to Primitives of Socket Programming

## Your First Socket

```rb
require "socket"

socket = Socket.new(Socket::AF_INET, Socket::SOCK_STREAM)
```

`INET` is short for internet and refers to IPv4 family of protocols.

When two sockets want to communicate, they use IP addresses to route messages to specific hosts.

Systems also have a loopback interface, which is entirely virtual. Data sent to the loopback
interface is received on the same interface, constrained to local host.

Whereas IP address is the "phone number" of TCP, ports would be extension numbers. Without ports,
a host could only support one socket at a time. HTTP uses port 80 by default. FTP uses port 21
by default. See <https://www.iana.org> for a list.

## Establishing Connections

**Socket that listens is a server. Socket that initiates connection is a client.**

## Server Lifecycle

The lifecycle looks like:

1. create
2. bind
3. listen
4. accept
5. close

```rb
require "socket"

socket = Socket.new(Socket::AF_INET, Socket::SOCK_STREAM)  # create
addr = Socket.pack_sockaddr_in(4481, "0.0.0.0")            # c struct to hold address for listening
socket.bind(addr)                                          # bind to it
socket.listen(128)                                         # listen for incoming connections
connection, _client_addr = server.accept                   # accept a connection
connection.close                                           # close connection
```

Ports 1-65,535 can be used. But don't use 0-1024, they're reserved for system use. Don't use ports
49,000-65,535 range. They're ephemeral for temporary purposes. Use anything from 1025-48,999. Check
the IANA list to make sure it doesn't conflict.

If you bind to 127.0.0.1 your socket will only listen to loopback interface. Only connections to
localhost or 127.0.0.1 will be routed to your server socket. If you want to listen to all interfaces,
use 0.0.0.0. Most of the time, this is what you want.

The number passed to `#listen` represents max number of pending connections your socket will
tolerate. List of pending connections is the **listen queue**. `Socket::SOMAXCONN` is the maximum
your system will allow. On the author's Mac, it's 128.

The call to `#accept` blocks until a connection arrives. Give it one using netcat:

```sh
$ echo ohai | nc localhost 4481
```

`#accept` pops the next pending connection off the listen queue. If none are available, it waits
until one is pushed onto it.

In Unix, everything is treated as a file. So sockets are files. The connection object returned will
have two addresses: `local_address` and `remote_address`. The combination of these four properties
must be unique for each TCP connection: local-host, local-port, remote-host, remote-port. You cannot
have two simultaneous connections to a remote host if the local ports and remote ports are identical.

A production server will want to continually listen for incoming connections instead of exiting on
a single connection:

```rb
loop do
  connection, _client_addr = server.accept
  connection.close
end
```

Even though file descriptors close automatically on program exit, it's a good idea to still do it
to help with resource usage. It's also possible to close only reads/writes via `#close_write` or
`#close_read`.

A shorthand for creating a server:

```rb
server = TCPServer.new(4481)
Socket.accept_loop(server) do |connection|
  connection.close
end
```

Or even:

```rb
Socket.tcp_server_loop(4481) do |connection|
  connection.close
end
```

System calls in this chapter: `bind`, `listen`, `accept`, `local_address` or `getsockname`,
`remote_address` or `getpeername`, `close`, `close_write`/`close_read` or `shutdown`.

## Client Lifecycle

Steps in the client lifecycle:

1. create
2. bind - explicit call is rare, usually client automatically binds to a random port from the
   ephemeral range. Clients don't need to be accessible from a known port.
3. connect
4. close

```rb
require "socket"

socket = Socket.new(:INET, :STREAM)
remote_addr = Socket.pack_sockaddr_in(80, "google.com")
socket.connect(remote_addr)
```

A shorthand:

```rb
socket = TCPSocket.new("google.com", 80)
```

Or even:

```rb
Socket.tcp("google.com", 80) do |connection|
  connection.write "GET / HTTP /1.1 \r\n"
  connection.close
end
```

System calls in this chapter: `bind`, `connect`.

## Exchanging Data

TCP is a stream based protocol. It sends packets over the network. From the perspective of your
application code, a TCP connection provides an ordered stream of communication with no beginning
or end. Just a stream.

Some pseudo code:

```rb
# sending data over stream, one at a time
data = ["a", "b", "c"]
data.each { |c| write_to_connection(c) }

# receiving data in one operation
result = read_from_connection # => ["a", "b", "c"]
```

Reading will read all data sent. There are no message boundaries. Order is preserved.

## Sockets can Read

```rb
Socket.tcp_server_loop(4481) do |connection|
  puts connection.read # simplest way to read data from connection
  connection.close
end
```

Use netcat to send a message:

```sh
$ echo gekko | nc localhost 4481
```

Ruby's socket class uses the IO API, which shares common methods like: `read`, `write`, `flush`, etc.
The underlying calls are system calls. Everything is a file.

```sh
$ tail -f /var/log/system.log | nc localhost 4481
```

The above command will never print data, because it never sends an EOF (end-of-file). `#read` will
continue blocking until the client finishes sending data.

```rb
one_kb = 1024
Socket.tcp_server_loop(4481) do |connection|
  while data = connection.read(one_kb) do
    puts data
  end
  connection.close
end
```

Or better yet, use `#readpartial` and pass a maximum length. It reads up to that length, but will
not block waiting for more. It raises an `EOFError` when encountering EOF though.

```rb
one_kb = 1024
Socket.tcp_server_loop(4481) do |connection|
  begin
    while data = connection.readpartial(1024*100) do
      puts data
    end
  rescue EOFError
  end
  connection.close
end
```

System calls from this chapter: `read`.

## Sockets can Write

Use `#write`:

```rb
Socket.tcp_server_loop(4481) do |connection|
  connection.write("Welcome!")
  connection.close
end
```

## Buffering

When `write` returns successfully, it just means the data has been handed off to the OS kernel. The
OS may send it out immediately or it may combine it with other day for efficiency. There are read
buffers too. Most projects use 16KB as their read buffers.

## Our First Client/Server

We're going to write a NoSQL server called CloudHash. The server:

```rb
module CloudHash
  class Server
    def initialize(port)
      @server = TCPServer.new(port)
      puts "Listening on port #{@server.local_address.ip_port}"
      @storage = {}
    end

    def start
      Socket.accept_loop(@server) do |connection|
        handle(connection)
        connection.close
      end
    end

    def handle(connection)
      request = connection.read
      connection.write(process(request))
    end

    def process(request)
      command, key, value = request.split
      case command.upcase
      when "GET"
        @storage[key]
      when "SET"
        @storage[key] = value
      end
    end
  end
end

server = CloudHash::Server.new(4481)
server.start
```

Here's the client:

```rb
module CloudHash
  class Client
    class << self
      attr_accessor :host, :port
    end

    def self.get(key)
      request "GET #{key}"
    end

    def self.set(key, value)
      request "SET #{key} #{value}"
    end

    def self.request(string)
      @client = TCPSocket.new(host, port)
      @client.write(string)
      @client.close_write
      @client.read # read until EOF to get response
    end
  end
end

CloudHash::Client.host = "localhost"
CloudHash::Client.port = 4481
puts CloudHash::Client.set "prez", "obama"
puts CloudHash::Client.get "prez"
puts CloudHash::Client.get "vp"
```

These are the basics of TCP sockets, but it does not set a good example. A new connection is created
for each client request, but we could design the server to re-use connections. Also we could change
the server to handle concurrency, so a single slow client wouldn't slow down all requests.

# Part 2: Advanced Topics in Socket Programming

## Socket Options

You can retrieve socket options:

```rb
socket = TCPSocket.new("google.com", 80)
opt = socket.getsockopt(Socket::SOL_SOCKET, Socket::SO_TYPE)
opt.int == Socket::SOCK_STREAM # => true
opt.int == Socket::SOCK_DGRAM # => false
```

The `SO_TYPE` option lets you know what type of socket it is.

The `SO_REUSE_ADDR` option tells the kernel it's ok for another socket to bind to the same local
address as the server, if it's currently in the TCP `TIME_WAIT` state. When you close a socket, its
pending data is not discarded in case the kernel is buffering and might need to re-send it.

## Non-blocking IO

`read_nonblock` requires a maximum number of bytes to read as an argument.

```rb
Socket.tcp_server_loop(4481) do |connection|
  loop do
    begin
      puts connection.read_nonblock(4096)
    rescue Errno::EAGAIN
      IO.select([connection]) # block until connection becomes readable, instead of spamming read
      retry
    rescue EOFError
      break
    end
  end
  connection.close
end
```

`write_nonblock` can return a partial write, whereas `write` will always take care of writing all
data that you send it.

```rb
client = TCPSocket.new("localhost", 4481)
payload = "Lorem Ipsum" * 10_000
written = client.write_nonblock(payload) # writes as much as it can, returns number of bytes written
written < payload.size # => true
```

Use `IO.select` to tell us when a socket is writable:

```rb
begin
  loop do
    bytes = client.write_nonblock(payload)
    break if bytes >= payload.size
    payload.slice!(0, bytes)
    IO.select(nil, [client])
  end
rescue Errno::EAGAIN
  IO.select(nil, [client])
  retry
end
```

There are also `accept_nonblock` and `connect_nonblock` methods.

## Multiplexing Connections

This means working with multiple active sockets at the same time, using non-blocking IO.

```rb
connections = [<TCPSocket>, <TCPSocket>, ...]
loop do
  connections.each do |conn|
    begin
      data = conn.read_nonblock(4096) # read in non-blocking way, process, then try next
      process(data)
    rescue Errno::EAGAIN
    end
  end
end
```

We'll want to make the loop less busy though, and wait for connections to be ready with `IO.select`:

```rb
loop do
  ready = IO.select(connections)
  readable_connections = ready[0]
  readable_connections.each do |conn|
    data = conn.readpartial(4096)
    process(data)
  end
end
```

`IO.select` returns an array of arrays, the first being connections ready for reading and the second
being ready for writing. Its arguments follow this same pattern. It's a synchronous call. It also
takes a fourth argument, a timeout value in seconds, which prevents it from blocking indefinitely.

`select(2)` is the oldest and least performing of multiplexing. There are alternatives like `poll(2)`,
`epoll(2)`, and `kqueue(2)`.

## Nagle's Algorithm

After writing to a socket, there are 3 outcomes: it sends it all immediately if there's enough space
in the buffer, it sends immediately without requiring buffer, it puts it in local buffer.

This algorithm guards against sending too many tiny TCP packets. If a protocol ensures messages are
large enough like HTTP, this algorithm will have no effect.

To disable it, use the `TCP_NODELAY` option:

```rb
server.setsockopt(Socket::IPPROTO_TCP, Socket::TCP_NODELAY, 1)
```

## Framing Messages

Re-using connections across multiple messages is like the keep-alive feature of HTTP. It can save
a lot of resources. To do that, you'll need to define a way to frame messages without EOFs. You
can use newlines:

```rb
def handle(connection)
  loop do
    request = connection.gets
    break if request == "exit"
    connection.puts process(request)
  end
end
```

You'll need to be certain that the client/server runs on the same operating system, since different
OS use different newline characters.

Besides newlines, you can also use content length. Your server can read a pre-defined length for
the message size, then only read the remaining size after that.

## Timeouts

`IO.select` lets you implement timeout.

```rb
require "socket"
require "timeout"

timeout = 5 # seconds
Socket.tcp_server_loop(4481) do |connection|
  begin
    connection.read_nonblock(4096)
  rescue Errno::EAGAIN
    if IO.select([connection], nil, nil, timeout)
      retry # socket is readable
    else
      raise Timeout::Error
    end
  end
  connection.close
end
```

## DNS Lookup

A slow DNS server can block your entire Ruby process. The GIL understands blocking IO. If a thread
does blocking IO, MRI will release the GIL and let another thread continue execution. But it's less
forgiving for C extensions. When a C extension API is used, the GIL blocks other code from executing.
If a C extension is blocking on IO, all other threads will be blocked. Ruby uses a C extension for
DNS lookups by default, hence the block.

The `resolv` library provides a pure Ruby replacement for DNS lookups.

```rb
require "resolv"
require "resolv-replace"
```

## SSL Sockets

SSL sockets lets you upgrade plain insecure sockets to secure SSL sockets. A single socket can't do
both SSL and non-SSL communication.

```rb
require "socket"
require "openssl"

def main
  server = TCPServer.new(4481)
  ctx = OpenSSL::SSL::SSLContext.new
  ctx.cert, ctx.key = create_self_signed_cert( # taken from webrick/ssl
    1024,
    [["CN", "localhost"]],
    "Generated by Ruby/OpenSSL"
  )
  ctx.verify_mode = OpenSSL::SSL::VERIFY_PEER

  ssl_server = OpenSSL::SSL::SSLServer.new(server, ctx)
  connection = ssl_server.accept
  connection.write("Bah now")
  connection.close
end
```

Sending a non-ssl message will raise an `OpenSSL::SSL::SSLError`. Your client needs to implement SSL.

```rb
socket = TCPSocket.new("0.0.0.0", 4481)
ssl_socket = OpenSSL::SSL::SSLSocket.new(socket)
ssl_socket.connect
ssl_socket.read
```

## Urgent Data

`Socket#send` is a specialized version of `#write`. `write` is generalized to be used with any IO
object, the `send` method is specialized to work with just sockets. It has a second argument for
flags. To send urgent data:

```rb
socket.send "!", Socket::MSG_OOB  # out-of-band
```

The receive needs to handle this:

```rb
  urgent_data = connection.recv(1, Socket::MSG_OOB)
  data = connection.readpartial(1024)
```

# Part 3: Real-World Scenarios, Applying Concurrency to Network Programs, Architecture Patterns

## Network Architecture Patterns

How should concurrency be handled? How should errors? How to handle slow clients? How to make most
efficient use of resources?

The example project the author uses is a subset of FTP. You can create a server without creating
a client, since FTP clients already exist.

```rb
module FTP
  class CommandHandler
    CRLF = "\r\n"

    attr_reader :connection

    def initialize(connection)
      @connection = connection
    end

    def pwd
      @pwd || Dir.pwd
    end

    def handle(data)
      cmd = data[0..3].strip.upcase
      options = data[4..-1].strip

      case cmd
      when "USER"
        "230 Logged in anonymously" # accept any username anonymously
      when "SYST"
        "215 UNIX Working With FTP" # what's your name?
      when "CWD"
        if File.directory?(options)
          @pwd = options
          "250 directory changed to #{pwd}"
        else
          "550 directory not found"
        end
      when "PWD"
        "257 \"#{pwd}\" is the current directory"
      when "PORT"
        parts = options.split(",")
        ip_address = parts[0..3].join(".")
        port = Integer(parts[4]) * 256 + Integer(parts[5])
        @data_socket = TCPSocket.new(ip_address, port)
        "200 Active connection established (#{port})"
      when "RETR"
        file = File.open(File.join(pwd, options), "r")
        connection.respond("125 Data transfer starting #{file.size} bytes")
        bytes = IO.copy_stream(file, @data_socket)
        @data_socket.close
        "226 Closing data connection, sent #{bytes} bytes"
      when "LIST"
        connection.respond("125 Opening data connection for file list")
        result = Dir.entries(pwd).join(CRLF)
        @data_socket.write(result)
        @data_socket.close
        "226 Closing data connection, sent #{result.size} bytes"
      when "QUIT"
        "221 Ciao"
      else
        "502 Don't know how to respond to #{cmd}"
      end
    end
  end
end
```

## Serial

In a serial architecture, all client connections are handled serially. There's no concurrency.

1. client connects
2. client/server exchange requests/responses
3. client disconnects
4. back to step #1

```rb
class Serial
  def initialize(port = 21)
    @control_socket = TCPServer.new(port)
    trap(:INT) { exit }
  end

  def gets
    @client.gets(CRLF)
  end

  def respond(message)
    @client.write(message)
    @client.write(CRLF)
  end

  def run
    loop do
      @client = @control_socket.accept
      respond "220 OHAI"
      handler = CommandHandler.new(self)
      loop do
        request = gets
        if request
          respond handler.handle(request)
        else
          @client.close
          break
        end
      end
    end
  end
end
```

This server does not accept connections while processing, so there can be no concurrency. The
greatest advantage is its simplicity. No locks, shared state, etc.

## Process Per Connection

This architecture reuses the above, but after accepting a connection the server will fork a child
process whose sole purpose is to handle the new connection. The child process handles the connection,
then exits when done.

1. connection comes in to server
2. main server accepts connection
3. forks new child process which is an exact copy of server process
4. child process handles connection in parallel, while server process goes back to step #1

```rb
def run
  loop do
    @client = @control_socket.accept
    pid = fork do
      respond "220 OHAI"
      handler = CommandHandler.new(self)
      # ... same loop using handler ...
    end
    Process.detach(pid)
  end
end
```

After a process exits, it isn't fully cleaned up until its parent detaches. Process per connection
is simple and uses little cognitive overhead. Downside is there's no upper bound on number of child
processes. This works for small number of clients, but spawning dozens or hundreds of processes will
cause your system to fail over. `fork` is only supported on Unix systems, not supported on Windows
or JRuby.

## Thread Per Connection

Similar to forking a process, but spawn a thread instead. Threads are cheaper to spawn. They exist
in the same process, share memory, and can spawn faster. Downside is additional complexity with
synchronizing with mutexes and locks. Processes don't need to do this because each process has its
own copy of everything. Because of the GIL (Global Interpreter Lock), MRI prevents threads from
running with true parallelism. It's pretty smart though, and will execute when a thread is blocking
on IO.

```rb
Connection = Struct.new(:client) do
  CRLF = "\r\n"
  
  def gets
    client.gets(CRLF)
  end

  def respond(message)
    client.write(message)
    client.write(CRLF)
  end

  def close
    client.close
  end
end

def run
  Thread.abort_on_exception = true
  loop do
    conn = Connection.new(@control_socket.accept)
    Thread.new do
      conn.respond "220 OHAI"
      # ... rest of code is the same ...
    end
  end
end
```

Note that we need to extract Connection so that each thread has its own `conn` instance.

The pros of this system are: threads are lighter on resources, there can be more threads, it's
relatively simple. The cons: MRI GIL, system can still be overwhelmed if there are too many threads.

## Preforking

Rather than forking a child per incoming connection, the server forks processes when it boots up
before any connection arrives:

1. main server process creates a listening socket
2. main server process forks a horde of child processes
3. each child process accepts connections on the shared socket and handles them independently
4. main server process keeps eye on child processes

The main server process doesn't accept connections on it. We don't have to worry about load balancing
or synchronizing connections, because the kernel handles it. The kernel makes sure only one copy of
the socket will accept any particular connection.

```rb
CONCURRENCY = 4

def run
  child_pids = []
  CONCURRENCY.times { child_pids << spawn_child }
  trap(:INT) {
    child_pids.each do |cpid|
      begin
        Process.kill(:INT, cpid)
      rescue Errno::ESRCH
      end
    end
    exit
  }
  loop do
    pid = Process.wait
    $stderr.puts "Process #{pid} quit unexpectedly"
    child_pids.delete(pid)
    child_pids << spawn_child
  end
end

def spawn_child
  fork do
    # ... handler loop here ...
  end
end
```

Pros: since we pre-fork, too many connections won't overwhelm the system. Since processes are
separate, a crash won't interfere with a different process. Cons: this uses more memory than
threads.

## Thread Pool

This pattern works like preforking for processes, but instead uses threads. The flow/code is the
same, except substitute `Thread.new` for `fork`. You'll also use `ThreadGroup`.

```rb
def run
  Thread.abort_on_exception = true
  threads = ThreadGroup.new
  CONCURRENCY.times { threads.add(spawn_thread) }
  sleep
end

def spawn_thread
  Thread.new do
    # ... same handler loop here ...
  end
end
```

## Evented (Reactor)

Based on the Reactor pattern <http://en.wikipedia.org/wiki/Reactor_pattern>, it's single-threaded
and single-process but can achieve concurrency on par with other patterns. A central multiplexer
monitors active connections and dispatches upon triggering.

1. server monitors listening socket for incoming connections
2. upon receiving new connection, it adds to list of sockets to monitor
3. server monitors active connections as well as listening socket
4. upon notification of active connection being readable, server reads chunk of data from connection
   and dispatches relevant callback
5. upon notification of active connection still readable, server reads another chunk and dispatches
   callback again
6. server receives new connection, adds to list of sockets to monitor
7. server is notified that first connection is ready for writing, so response is written

All of this happens in a single thread. Server is able to accept a new connection while first
connection was still in the middle of read/write flow. The server is splitting each operation into
small chunks so various events pertaining to multiple connections can be interleaved.

```rb
module FTP
  class Evented
    CHUNK_SIZE = 1024 * 16
    
    class Connection
      CRLF = "\r\n"
      attr_reader :client

      def initialize(io)
        @client = io
        @request, @response = "", ""
        @handler = CommandHandler.new(self)

        respond "220 OHAI"
        on_writable
      end

      def on_data(data)
        @request << data
        if @request.end_with?(CRLF)
          respond @handler.handle(@request) # request completed
          @request = ""
        end
      end

      def respond(message)
        @response << message + CRLF
        on_writable # write what can be written immediately
      end

      def on_writable
        bytes = client.write_nonblock(@response)
        @response.slice!(0, bytes)
      end

      def monitor_for_reading?
        true
      end

      def monitor_for_writing?
        !(@response.empty?)
      end
    end

    def initialize(port = 21)
      @control_socket = TCPServer.new(port)
      trap(:INT) { exit }
    end

    def run
      @handles = {}

      loop do
        to_read = @handles.values.select(&:monitor_for_reading?).map(&:client)
        to_write = @handles.values.select(&:monitor_for_writing?).map(&:client)

        readables, writables = IO.select(to_read + [@control_socket], to_write)
        readables.each do |socket|
          if socket == @control_socket
            io = @control_socket.accept
            connection = Connection.new(io)
            @handles[io.fileno] = connection
          else
            connection = @handles[socket.fileno]
            begin
              data = socket.read_nonblock(CHUNK_SIZE)
              connection.on_data(data)
            rescue Errno::EAGAIN
            rescue EOFError
              @handles.delete(socket.fileno)
            end
          end
        end

        writables.each do |socket|
          connection = @handles[socket.fileno]
          connection.on_writable
        end
      end
    end
  end
end

server = FTP::Evented.new(4481)
server.run
```

This pattern has a reputation of handling high levels of concurrency. Other patterns are limited by
processes/threads. The disadvantage is the programming model that's forced upon you. You must never
block the Reactor.

## Hybrids

Nginx can serve 1 million concurrent requests on a single server. It does this using a preforking
pattern, but each forked process uses the evented pattern. Forking takes maximum advantage of
multiple cores and server resources.

Puma leans heavily on threads and works well for Ruby implementations without a GIL. It uses a
thread pool. But it also monitors new requests and processes those using an evented reactor.

EventMachine uses the evented pattern at its core, but it also provides a thread pool for deferring
long-running or blocking operations that would otherwise slow down the reactor.
