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

* performance - measures how fast the system processes a single transaction
* throughput - describes number of transactions system can process in a given time span
* scalability - describes how throughput changes under varying loads, adding capacity to the system
* capacity - maximum throughput a system can sustain while maintaining an acceptable response time

In every system, exactly one constraint determines the system's capacity. The limiting factor or
bottleneck.

## Capacity Antipatterns

Resource Pool Contention - a bottleneck arises when more threads require one of the resources than
are available. Connection pools will start to block the requesting thread. During regular peak loads,
there should be no contention for resources. Size resource pools to the request thread pool. Watch
for the blocked threads anti-pattern.

AJAX Overkill - applications are sending too many AJAX requests to load a single page. Avoid needless
requests (eg throttle autocomplete polling requests). Respect your session architecture so requests
include a session ID. Minimize size of replies (eg just send back JSON data instead of formatted
HTML). Increase the size of your web tier.

Overstaying Sessions - expunge sessions at the earliest opportunity. Find the average and standard
deviation of time between page requests. Keep sessions light, use keys instead of whole objects.

Wasted Space in HTML - bandwidth and CPU are not cheap. The larger the page, the more resources
it takes up. Omit needless characters. Remove whitespace. Use CSS layout instead of HTML tables
and spacer images.

Reload Button - make your site so fast that users don't ever want to hit the reload button.

Handcrafted SQL - developer crafted SQL tends to be unpredictable. Minimize handcrafted SQL. If you
can't get around it, check with a DBA. Verify gains against real production data.

Database Eutrophication - a slow buildup of sludge in your database. Use indexes and partitioning
to deal with it. Purge unnecessary old data. Keep reports out of production.

Integration Point Latency - expose yourself to latency as seldom as possible.

Cookie Monsters - don't shoot yourself in the foot with HTTP cookies. Browsers keep cookies around
until their expiration date. Cookies were meant to send small chunks of data. Cookies are sent between
all user's requests/responses. Serve small cookies and expire them when they're no longer necessary.

## Capacity Patterns

Pool Connections - pooling eliminates connection setup time. Pool sizing properly is vital. Undersized
leads to pool contention, oversized causes excess stress on the servers. Monitor calls to connection
pools to see how long your threads are waiting.

Use Caching Carefully - monitor hit rates to verify the cache has performance gains. Avoid caching
things that are cheap to generate. Precomputing results can eliminate the need for caching. Be aware
of stale data. Limit cache sizes and build a flush mechanism.

Precompute Content - if sections of your site have infrequent content changes, it might be best to
precompute the HTML fragments. Factor the cost of generating content out of individual requests
and into the deployment process. Or have different triggers for precomputation.

Tune the Garbage Collector - user patterns make a huge difference, tune the garbage collector
in production.

# General Design Issues

## Networking

Multihomed Servers - a server with more than one IP address. It exists on several networks
simultaneously. This improves security and performance.

Virtual IP Addresses - a cluster server is an application that acts like a controller for other
applications. Suppose an application goes down. The cluster server will notice the lack of regular
heartbeat and can start up the application on a secondary server. It can take over the virtual
IP address assigned to the clustered network interface. A Virtual IP address is just an IP
address that can be moved from one NIC to another as needed.

## Security

Principle of Least Privileged - mandates that a process should have the lowest level of privilege
needed to accomplish its task. Don't run it as root.

Configured Passwords - passwords are the Achilles heel of application security. Text files are
vulnerable. Passwords to production databases should be kept separate from any other configuration
file. Use password vaulting, which keeps passwords in encrypted files.

## Availability

Gathering Availability Requirements - without considering cost, the desire us unlimited. We need
to balance the cost of increasing availability with the revenue loss of downtime.

Documenting Availability Requirements - "The system shall be available 99.9% of the time" is too
vague. What is the system? What counts as available? Define each variable. How often will the
monitoring device execute its synthetic transaction? What is the maximum acceptable response time?
What response indicates a success or failure? How many locations? Will data be recorded?

Load Balancing - building systems for horizontal scaling implies load balancing. Use DNS round
robins, reverse proxies, hardware load balancers.

Clustering - load balancing doesn't require collaboration between separate servers. Clusters can be
used for load balancing (active/active). They can also be used for redundancy in case of failure
(active/passive) meaning one server handles all the load until it fails, then a passive one takes
over.

## Administration

Does QA Match Production? - keep them separated. Test for zero, one, or many cases. Buy the equipment
to match environments as closely as possible.

Configuration Files - keep production configuration properties separate from basic wiring/plumbing
of the application. They should be separate files. Keep config files in version control. Name
properties after their function, not just their nature (eg `authenticationServer` instead of
`hostname`).

Start-up and Shutdown - build a clean start-up sequence and ensure components are started in the
right order. The sequence must start completely and successfully before the application can accept
work.

Administrative Interfaces - create an admin UI for your internal team. Don't force them to do things
manually, through SSH tunnels.

# Operations

## Transparency

Status Dashboard - keep a status dashboard that makes certain answers to common questions visible.
For example: how many orders did we take yesterday? How does this compare to same day last year?
How much disk space did we consume? Use existing software to keep track of lower level metrics:
memory usage, garbage collection, worker threads, db connection pools, traffic statistics. The
application itself can have its own metrics: business transactions, number of users, circuit breakers,
integration points. Use green/yellow/red to signal trouble.

Logging - configure logs to appropriate levels. Above all else, log files are human readable. Messages
should include an identifier that can be used to trace the transaction.

Monitoring Systems - most run agents on hosts being observed, these agents run periodic observations.
They may also alert for critical issues. Expose traffic indicators, resource pool health, db health,
integration point health, and cache health.

Operations Database - logging/monitoring is good for exposing immediate behavior. Operations database
serves time-oriented views of the system. It accumulates status and metrics from all servers and
feeds to make up the extended system.

Supporting Processes - if a reporting system sends out a report to dozens of people, but they all
setup an email rule to ignore it, it's not useful. You still need good processes. What should be
done for a critical alert? Or for a soft report? Setup standard operating procedures to handle
critical outages so you know what to do.

## Adaptation

Adaptation Over Time - systems should change to fit its solution space better over time. Each new
release is motivated by either new features or fixing defects.

Adaptable Software Design - design software to be adaptable. Use dependency injection, object design,
XP coding practices, agile databases.

Releases Shouldn't Hurt - make sure deployments don't cost too much. Aside from testing, you can
also work on: configuration management, documentation, marketing communications, deployment, and
support. Have zero downtime deployments.
