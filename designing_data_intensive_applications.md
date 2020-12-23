# Designing Data Intensive Applications

Notes on [Designing Data Intensive Applications](https://dataintensive.net) by Martin Kleppmann.

# Part 1: Foundations of Data Sytems

## Reliable, Scalable, and Maintainable Applications

Functional Requirements - what should the application do, such as allowing data to be stored,
retrieved, searched, or processed.

Non-functional Requirements - general properties like security, reliability, compliance, scalability,
compatibility, and maintainability.

Reliability - making systems work correctly even when faults occur. Faults can be from hardware,
software (bugs), humans. Fault-tolerance techniques can hide faults from end users.

Scalability - having strategies for keeping performance good even as load increases. Have a way to
measure load/scalability quantitatively. A scalable system can add processing capacity to remain
reliable under high load.

Maintainability - making life easy for the engineering/operations team who work on the system. Done
via good abstractions to reduce complexity, making the system easier to modify/adapt, have good
visibility into the system's health, have effective ways to manage health.

## Data Models and Query Languages

Hierarchical Model - how data was represented, historically, as one big tree.

Relational Model - the Hierarchical Model wasn't good for representing many-to-many relationships,
so this model was created to solve that problem. It represents data in tables (relations), where
a row from one table can reference a row in another table.

Document Databases - targets use cases where data comes in self-contained documents and relationships
between one document and another are rare. Typically doesn't enforce a schema.

Graph Databases - targets use cases where anything is potentially related to everything. Typically
doesn't enforce a schema.

Query Language or Framework - comes with each data model, and is the API used to fetch data from
the database. Examples: SQL, MapReduce, MongoDB's aggregation pipeline, Cypher, SPARQL, Datalog

Sequence Similarity Search (GenBank) - researchers working on genome data needed to search for
very long DNA strings, matching it against a large database of similar strings. Nothing worked well
for them, so they created their own database in GenBank.

Full-text Search - information retrieval is a large specialist subject. Search indexes are covered
later on in the book.

## Storage and Retrieval

OLTP - optimized for transaction processing. These systems are typically user-facing and may see
a huge volume of requests. Application queries tend to touch only a small number of records, using
a key, and the storage engine uses an index for the query. Disk seek time is usually the bottleneck.

OLAP - optimized for analytics processing. These are Data Warehouses used by business analysts instead
of end users. They handle lower volume of queries, but each query is typically demanding. Disk bandwidth
is the bottleneck here. Queries scanning large number of rows means indexes are less relevant; it's
more important to encode data very compactly to minimize how much is read from disk. Column-oriented
storage achieves this.

Log-structured - school of thought for OLTP, only permits appending to files and deleting obsolete
files. Never updates a file that has been written. LevelDB, Cassandra, HBase, Lucene are examples.
They turn random-access writes into sequential writes on disk, which enables higher throughput due to
new high performant hard drives and SSDs.

Update-in-place - school of thought for OLTP, treats disk as a set of fixed-size pages that can be
overwritten. B-trees are mainly used for the data structure, used in all major relational databases.

## Encoding and Evolution

Encoding - programs typically work with data in memory. Often times, the data is either written to
or read from a file (or over a network). There needs to be some kind of translation between those
two representations. Translating data to a byte sequence is called encoding (also known as
serialization or marshalling). The reverse is called decoding (parsing, deserialization, unmarshalling).

Rolling Upgrades - where new versions of a service is gradually deployed to a few nodes at a time,
rather than deploying all nodes simultaneously. This allows releases to have zero downtime, encouraging
more frequent/smaller deploys, making deploys less risky. Backwards/forwards compatible encoding
is important for this.

Evolvability - the ease of making changes to an application. Rolling upgrades, easier releases,
and backwards compatible encoding are all properties that are beneficial to evolvability.

Programming Language-Specific Encodings - APIs from programming languages that serialize their data
structures from memory to bytes (for disk or network). These are usually restricted to the single
programming language and fail to provide forward/backward compatibility, so they're not commonly used.

Textual Formats - JSON, XML, CSV are widespread and can be very compatible depending on usage. They
have optional schema languages. These formats are vague about datatypes, so you have to be careful
with numbers and binary strings.

Binary Schema-Driven Formats - Thrift, Protocol Buffers, Avro allow compact/efficient encoding with
clearly defined forward/backward compatibility semantics. They provide useful documentation and
code generation. But data needs to be decoded before it can be read by a human.

Writing to Database - a dataflow where data is encoded and persisted onto the disk. The database
does the encoding, the process reading from it (application) decodes it.

RPC and REST APIs - client encodes a request, server decodes the request and encodes a response,
client finally decodes the response.

Asynchronous Message Passing - where nodes communicate by sending each other encoded messages that
are decoded by the recipient.

# Part 2: Distributed Data

Reasons you might want to distribute a database across multiple machines: scalability (spreading
load across multiple machines to handle data volume, high read/write load), fault tolerance and
high availability (application can still work even if one machine goes down), latency (for users
around the world -- having servers in several locations will speed things up).

## Replication

Replication - means keeping a copy of the same data on multiple machines connected via a network.
This keeps data geographically close to users (reduce latency), allows system to continue working
even if some parts fail (increases availability), and scales out number of machines to serve read
queries (increase read throughput).

Common Problems Setting Up Replication - requires carefully thinking about concurrency. Requires
dealing with unavailable nodes and network interruptions. Remember to deal with silent data
corruption.

High Availability - keeping the system running, even if one or more machines go down.

Disconnected Operation - allowing application to continue working when there is a network
interruption.

Latency - placing data geographically closer to users, so users can interact with it faster.

Scalability - being able to handle a higher volume of reads than a single machine can handle, by
performing reads on replicas.

Single-Leader Replication - clients send all writes to the leader node, which sends stream of data
change events to followers/replicas. Reads performed on any replica, but reads on followers can be
stale. Popular because it's easy to understand and there is no conflict resolution to worry about.

Multi-Leader Replication - clients send each write to one of several leader nodes, any can accept
writes. Leaders send streams of data change events to each other and to any follower nodes. More
robust in presence of faulty nodes, network interruptions, and latency spikes. Harder to reason about
and providing only very weak consistency guarantees.

Leaderless Replication - clients send each write to several nodes, and read from several nodes in
parallel to detect and correct nodes with stale data.

Synchronous Replication - a strategy for Multi-Leader Replication. Wait for write to be replicated
to all replicas before telling user that the write was successful. You lose the advantage of
allowing each replica to accept writes independently, so you might as well use Single-Leader.

Asynchronous Replication - a strategy for Multi-Leader Replication. Can be fast when system is
running smoothly, but if leader fails and you promote an asynchronously updated follower to be a new
leader, recently committed data may be lost.

Read-after-write Consistency - consistency model for deciding how an application should behave
under replication lag. Users always see data that they submitted themselves.

Monotonic Reads - another consistency model. After users see data at one point in time, they
shouldn't later see the data from some earlier point in time.

Consistent Prefix Reads - users should see data in a state that makes causal sense.

## Partitioning

Partitioning is necessary when you have so much data that storing/processing on a single machine is
no longer feasible. Partitioning spreads data and query load evenly across machines. This requires
choosing a partitioning scheme and rebalancing as necessary.

Key Range Partitioning - where keys are sorted and a partition owns all the keys within thresholds.
It has the advantage of efficient range queries, but there's risk of hot spots if often accesses
keys that are close together in the sorted order. Partitions are typically rebalanced dynamically
by splitting the range into two subranges when a partition gets too big.

Hash Partitioning - hash function is applied to each key and a partition owns a range of hashes.
Destroys the ordering of keys, making range queries inefficient, but distributes load more evenly.
Usually you want to create a fixed number of partitions in advice, assigning several partitions to
each node, and to move entire partitions from one node to another when nodes are added/removed.

Secondary index also needs to be partitioned. There are two methods:

Document-Partitioned Indexes (local indexes) - secondary indexes stored in same partition as the
primary key and value. This means only a single partition needs to be updated on write, but a read
of the secondary index requires a scatter/gather across all partitions.

Term-Partitioned Indexes (global indexes) - where secondary indexes are partitioned separately,
using indexed values. Entry in the secondary index may include records from all partitions from
primary key. On write, partitions of secondary index need to be updated. But read can be served
from single partition.

Routing Queries to Partitions - range from simple partition-aware load balancing to sophisticated
parallel query execution engines.

## Transactions

Transactions - grouping several reads and writes together into a logical unit. Either every single
operation is successful, or they all fail together. This is an abstraction layer that reduces a
huge number of potential problems into a single transaction abort, which the application can handle
specifically. A success is called a *commit*. On failure, a transaction can *abort* or *rollback*.
It can safely try after failure. You don't need to worry about partial failures.

Errors Without Transactions - processes crashing, network interruptions, power outages, etc... will
cause data inconsistencies without transactions. It becomes much more difficult to interact with the
database, instead of a simple transaction abort/rollback/retry.

ACID - Atomicity, Consistency, Isolation, Durability. Atomic means something cannot be broken down
into smaller parts. Consistency means you have invariants about your data. Isolation means concurrently
executing transactions are isolated from each other to prevent race conditions. Durability means once
a transaction is committed successfully, any data written is not forgotten even if there is a hardware
fault or database crash.

Transaction Isolation - if two transactions touch different data, they can be run in parallel because
there are no dependencies. Race conditions occur when one transaction depends on another, or if two
transactions try to simultaneously modify the same data. Isolation makes your life easier by letting
you pretend no concurrency is happening.

Serializable Isolation - the database guarantees that transactions have the same effect as if they
were run in serial, without any concurrency. This level of isolation has a higher cost, so some
databases will use a weaker level of isolation. Weaker isolation protects against some concurrency
issues, but not all.

Read Committed Isolation - guarantees two things: when reading from the database you'll only see
data that has been committed (no dirty reads) and when writing you'll only overwrite data that has
been committed (no dirty writes).

Snapshot Isolation (or Repeatable Read) - each transaction reads from a consistent snapshot of the
database -- the transaction sees all the data was committed at the start of the transaction. Even if
data is changed by another transaction, each transaction only sees the old data from that particular
point in time.

Dirty Reads (race condition) - reading partially written data means that data is in an inconsistent
state. If a transaction rolls back, any partially written data may also need to be rolled back.

Dirty Writes (race condition) - dirty writes cause race conditions such as simultaneous buyers to
the same item, eg a single physical car for two buyers. It does not prevent the race condition between
two counter increments.

Read Skew (Non-Repeatable Reads, race condition) - client sees different parts of database at
different parts in time. Prevented with snapshot isolation, which lets a transaction read from a
consistent snapshot at one point in time. Usually implemented with multi-version concurrency
control.

Lost Updates (race condition) - two clients concurrently perform a read-modify-write cycle. One
overwrites the other's write without incorporating its changes, so data is lost. Some snapshot
isolation implementations prevents this, others require a manual lock (SELECT FOR UPDATE).

Write Skew (race condition) - transaction reads, makes a decision based on read, writes new decision
to database. But by the time write is made, the premise of the decision is no longer true. Only
serializable isolation prevents this anomaly.

Phantom Reads (race condition) - transaction reads objects that match a search condition. Another
client writes something that affects the search results. Snapshot isolation prevents straightforward
phantom reads, but phantoms in context of write skew require index-range locks.

Weak vs Serializable Isolation - weak isolation levels protect against some of these bugs, but
leaves you manually handling the others with patterns like explicit locking. Serializable isolation
protects you against all of these bugs. Some ways to implement serializable isolation are below.

Literally Executing Transactions in Serial Order - if each transaction has very fast execution and
throughput is low, this is a simple and effective option.

Two-Phase Locking (2PL) - standard implementation for serializable transactions, sometimes avoided
due to its performance. Normally, readers never block writers and writers never block readers. With
2PL, writers block writers and readers. If two transactions try to write concurrently to the same
object, a lock ensures the second writer must wait until the first is done. 2PL is similar, but has
a stronger lock requirement. Several transactions are allowed to concurrently read the same object as
long as nobody is writing to it. If someone wants to write, exclusive access is required.

Serializable Snapshot Isolation (SSI) - new implementation that uses an optimistic approach, allowing
transactions to proceed without blocking. When a transaction wants to commit, it's checked and aborted
if the execution was not serializable.

## The Trouble with Distributed Systems

Problems that can occur in distributed systems:

* Packets and replies over network may be lost or delayed.
* Node's clock may be out of sync with other nodes. Don't rely on system clocks.
* Process may pause at any point in execution or be declared dead by other nodes.

Distributed systems will have nodes that will fail, go slow, or not respond and time out. Partial
failures happen all the time. We want to build tolerance of partial failures, so the system can still
operate as a whole even while missing some parts.

First step is to detect partial failures. Distributed systems rely on timeouts. Even after detecting,
there are still problems: no shared global variable, shared memory, or common knowledge between
machines.

## Consistency and Consensus

Inconsistency - timing issue that occurs in replicated database. Looking at two database nodes at
the same moment in time, you might see two different data values because write requests arrive
on different nodes at different times.

Eventual Consistency - most replicated databases provide this, if you stop writing to the database
and wait, eventually all read requests will return the same value. Inconsistency is temporary. Another
term for it is convergence.

Linearizability - popular consistency model. As soon as one client completes a write, all clients
reading from the database must be able to see the new value. It's a recency guarantee. Goal is to
make replicated data appear as though there were only a single copy, making all operations act on it
atomically. It makes a database behave like a variable in a single-threaded program. The downside is
that it's slow, especially in environments with large network delays. Other names include: atomic
consistency, strong consistency, immediate consistency, external consistency.

Causality - another consistency model. Imposes an ordering on events in a system. Causality provides
a weaker consistency model: some things can be concurrent, so it's a timeline with branching and
merging. It's much less sensitive to network problems.

Consensus - if you solve one of these problems, you'll transform it into a solution for the others.
Achieving consensus means deciding something such that all nodes agree and the decision is
irrevocable. Wide range of problems are reducible to consensus, including the below.

Linearizable Compare-and-Set Registers - register needs to atomically decide whether to set its
value, based on whether its current value equals the parameter given in the operation.

Atomic Transaction Commit - database must decide whether to commit or abort a distributed transaction.

Total Order Broadcast - messaging system must decide on the order in which to deliver messages.

Locks and Leases - when several clients racing to grab a lock/lease, lock decides which one
successfully acquires it.

Membership/Coordination Service - given a failure detector (like timeouts) the system decides which
nodes are alive and which are dead.

Uniqueness Constraint - when several transactions concurrently create conflicting records with the
same key, the constraint decides which one to allow and which to fail.

Handling Single Leader Fails - if a network interruption makes the leader unreachable, there are
three ways to handle the failure. Wait for the leader to recover and block in the meantime. Manually
fail over by getting humans to choose a new leader node and reconfigure the system. Use an algorithm
to choose a new leader.

Outsourced Consensus - implemented by tools like ZooKeeper, which providers outsourced consensus
along with failure detection and membership service.

# Part 3: Derived Data

## Batch Processing

Services (online systems) - service waits for a request/instruction from client to arrive. When
received, service handles as quickly as possible and sends response back. Response time is primary
measure of performance, availability is very important.

Batch Processing Systems (offline systems) - takes large amount of input data, runs a job to process
it, and produces output data. Jobs can take a while (minutes to days), so user shouldn't wait for
job to finish. Batch jobs are scheduled to run periodically. Primary performance measure of batch
processing is throughput.

Stream Processing Systems (near-real-time systems) - between online/offline. It consumes inputs and
produces outputs rather than responding to requests. But it operates on events shortly after it
happens, whereas a batch job operates on a fixed set of input data. Streaming systems have lower
latency and builds upon batch processing.

Unix Tools - tools are built with a design philosophy, each tool does one simple job but are able to
communicate with each other through pipelines. We see this with simple log analysis using tools like
`cat`, `awk`, `sort`, `uniq`, and `head`.

MapReduce - similar to Unix tools, but distributed across potentially thousands of machines. It's
fairly blunt, brute-force, but effective. A single job is like a single Unix process, it takes one
or more inputs and produces one or more outputs. The job does not modify input or have any side
effects. While Unix tools work with `stdin` and `stdout`, MapReduce jobs read/write files on a
distributed filesystem. It uses Mapper and Reducer callback functions.

Partitioning - In MapReduce, mappers are partitioned according to input file blocks. Output of
mappers is repartitioned, sorted, merged into a number of reducer partitions. This brings all related
data together in same place.

Fault Tolerance - MapReduce writes to disk frequently, making it easy to recover from individual
failed task without restarting the entire job. It also slows down executing in the failure-free
case.

Sort-Merge Joins - each input being joined goes through a mapper which extracts the join key. By
partitioning, sorting, and merging, all records with the same key end up going to the same call of
the reducer. Function then outputs the joined records.

Broadcast Hash Joins - one of two join inputs is small, so it's not partitioned and can be entirely
loaded into hash table. You can start a mapper for each partition of the large join table, load the
hash table for the small input into each mapper, then scan over large input one record at a time,
querying hash table for each record.

Partitioned Hash Joins - if two join inputs are partitioned in the same way, then the hash table
approach can be used independently for each partition.

Callback Function Programming Model - jobs don't need to worry about fault-tolerance. Framework
guarantees it, even if it needs to retry tasks several times. It's a side effect of having the
callback model, where as the developer you only need to implement the success case.

Bounded Input Data - input data has a known, fixed size. Because of this, a job knows when it's
done reading the entire input and when a job will eventually complete.

Unbounded Input Data - how do you deal with never-ending streams of data? That's where stream
processing (next chapter) comes in.

## Stream Processing

Stream Processing - similar to batch processing, it's a system that takes in input and runs it
through jobs to produce outputs. Streams are near real-time, operating on events shortly after they
occur. Input is continuous and unbounded. Message brokers and event logs serve as streaming equivalent
of a filesystem.

AMQP/JMS-style Message Broker - assigns individual messages to consumers. Consumers acknowledge
messages when they have been processed. Messages are deleted from broker after acknowledgement.
It's an asynchronous form of RPC. An example is a task queue, order is not important and there's no
need to go back and read old messages after processing.

Log-based Message Broker - assigns messages in a partition to the same consumer node. Delivers messages
in the same order. Parallelism achieved through partitioning. Consumers track progress by checkpointing
offset of last message processed. Broker retains messages on disk, so you can retrieve old messages.

Where Streams Come From - user activity events, sensors providing periodic readings, data feeds
are natural streams. Writes to databases can be thought of as a stream, by capturing the changelog.

Databases as Streams - you can keep derived data systems like search indexes, caches, and analytics
systems completely up to date.

Stream-Stream Joins - both input streams consist of activity events, the join operator searches for
related events that occur within some window of time. For example, two actions taken by the same
user within 30 minutes of each other.

Stream-Table Joins - one input stream consists of activity events, while other is a database
changelog. Changelog keeps local copy of database up to date. Join operator queries database and
outputs an enriched activity event.

Table-Table Joins - both input streams are database changelogs. Every change on one side is joined
with latest change on other side.

Fault Tolerance and Exactly-Once Semantics - need to discard partial output of failed tasks. Stream
is long running and produces output continuously. Finer-grained recovery mechanism can be used via
microbatching, checkpointing, transactions, and idempotent writes.
