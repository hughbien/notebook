# Release It

Notes on [Release It!](https://pragprog.com/titles/mnee2/release-it-second-edition/).

# Stability

## Introducing Stability

* transaction - abstract unit of work processed by the system. In an e-commerce site, a transaction
  might be "Customer Places Order". Transactions are the reason the system exists.
* mixed workload - combination of different transaction types by a system
* system - complete set of hardware/applications/services required to process transactions
* stability - resilient system that keeps processing transactions, even when there are impulses or
  stresses or component failures disrupting normal processing
* impulse - rapid shock to the system
* stress - force applied to system over an extended period
* strain - change in shape of the system after stress is applied
* failure modes - when cracks in the system spread and damage the system
* crumple zones - create safe failure modes that contain damage and protect the rest of the system
* chain of failure - when one small failures leads to another, which leads to another

## Stability Antipatterns

Integration Points - software systems don't exist on their own anymore. They usually integrate with
other software. Every integration point will fail, you need to be prepared for that failure. Use
packet sniffers and network diagnostics tools to help. Be aware of cascading failures. Use defensive
programming patterns like: circuit breaker, timeouts, decoupling middleware, and handshaking.

Chain Reactions - when node after node fails, usually due to a resource leak or load-related crash.
A chain reaction in one layer can lead to a cascading failure in another layer. When these occur,
hunt for resource leaks or obscure timing bugs. Defend with bulkheads and circuit breakers.

Cascading Failures - when a crack in one layer triggers a crack in the calling layer. Stop cracks
from jumping the gap. Scrutinize resource pools. Defend with timeouts and circuit breakers.

Users - traffic, expensive resources to serve, unwanted users, flooding sessions, malicious users
will all test stability. Minimize the memory consumption of users. Hire people to test your system,
users do weird and random things. Make your system easy to patch and keep up to date with best
security practices. Run stress tests.

Blocked Threads - can happen anytime you check resources out of a connection pool, deal with caches
or object registries, or make calls to external systems. It leads to chain reactions and cascading
failures. Scrutinize resource pools. Use safe primitives. Don't roll your own producer/consumer
queue. Defend with timeouts.

Attacks of Self-Denial - any situation in which the system conspires against itself. Keep communication
open, educate your own organization. Protect shared resources. Expect rapid redistribution of any
cool or valuable offer.

Scaling Effects - examine production versus QA environments to spot scaling effects. Some patterns
work fine in small environments but fail in production sizes. Watch out for point-to-point
communication. Watch out for shared resources.

Unbalanced Capacities - a special case of scaling effects, on side of a relationship scales up more
than the other side. Stress both sides of the interface.

Slow Responses - slow responses trigger cascading failures. They cause more traffic as users refresh
the page. When the average response time exceeds a threshold, consider returning an immediate
error response instead. Hunt for memory leaks or resource contention.

SLA Inversion - when a system must meet a high availability SLA, but that system depends on a system
of lower availability. Don't make empty promises. Examine every dependency. Decouple your SLAs.

Unbounded Result Sets - development and test data sets are too small to showcase this issue. Test
using production-sized data sets. Don't rely on data producers, even if you think a query will only
ever return a handful of results. Think "zero", "one", or "lots". Put limits into application level
protocols.

## Stability Patterns

Timeouts - network and service calls should have a timeout threshold to provide fault isolation.
Apply to integration points, blocked threads, and slow responses. Consider delayed retries via
a queued operation.

Circuit Breaker - if a service fails multiple times, once it reaches a threshold all future calls
should just return the error response without even attempting the call. After a certain amount of
time passes (and a monitoring alert), the circuit breaker can start making regular calls again. This
pattern works well for integration points. Popping a circuit breaking always indicates a serious problem,
so make sure it's reported/recorded.

Bulkheads - bulkheads on ships are metal partitions that can seal compartments. So a single
penetration might fill up one part, but other parts will be completely sealed off and protected.
A software service can have the same pattern, for example, using dedicated server pools for particular
frontend services.

Steady State - it's best to keep people off production systems to the greatest extend possible. Avoid
human intervention. Purge data with application logic instead. Limit the amount of memory a cache
can consume. Configure log rotation.

Fail Fast - slow failure responses are the worst. Inform callers quickly, don't make them wait
for an error message. Verify integration points early. Use a circuit breaker. Do basic user input
validation before touching resources.

Handshaking - servers can protect themselves by throttling its own workload, rejecting incoming
work instead of becoming a victim to it. Servers can provider a "health check" query for use by
clients. Clients can make a health check before making a request (perhaps when un-breaking a circuit),
which provides a decent handshake between clients/servers.

Test Harness - consider using real services for your integration tests, so that actual network calls
are made. The test harness can be designed like an application server; it can have pluggable behavior
for the tests. A good test harness lets you simulate all sorts of messy, real-world failure modes.
You can stress test the caller.

Decoupling Middleware - message-oriented middleware decouples the endpoints in both space and time.
Requesting systems don't sit around waiting for a response. This kind of middleware cannot produce
cascading failures. Use message systems and queues to stabilize your architecture.

# Capacity

## Introducing Capacity

## Capacity Antipatterns

## Capacity Patterns

# General Design Issues

## Networking

## Security

## Availability

## Administration

# Operations

## Transparency

## Adaptation
