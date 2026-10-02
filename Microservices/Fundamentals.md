# Microservices Fundamentals

## Core model

A microservice owns a business capability, deployment lifecycle, and normally
its data. In an e-commerce example:

- Order Service owns order workflow.
- Payment Service owns charges/refunds.
- Inventory Service owns stock/reservations.
- Notification Service owns delivery of messages.
- Analytics Service consumes facts for reporting.

Separate databases reduce schema coupling and independent deployment risk, but
remove the convenience of cross-service joins and one local ACID transaction.

## Synchronous and asynchronous communication

REST is useful when the caller needs an immediate response. It creates temporal
coupling: the dependency must be reachable and fast enough now. Events/queues
allow producers and consumers to operate at different times, absorb bursts, and
add consumers without changing the producer, but introduce eventual consistency,
duplicates, ordering questions, and harder debugging.

## Scaling and failure isolation

Stateless service instances can scale horizontally behind a load balancer.
Local memory and JVM locks are then per instance. Shared state and invariants
must be designed for multiple processes.

Microservices do not automatically isolate failure. A slow Payment Service can
consume Order Service threads, connections, and request time until both fail.
Timeouts, bounded retries, circuit breakers, bulkheads, backpressure, and
asynchronous boundaries create real isolation.

## Interview traps

- One database shared by all services preserves tight coupling.
- Calling five services synchronously makes availability depend on the chain.
- “Event-driven” does not mean exactly-once or ordered everywhere.
- Splitting a monolith without clear ownership creates a distributed monolith.
- Independent deployment has operational costs: observability, versioning,
  testing, deployment, and incident response.

## Concise interview answer

I choose service boundaries around business capabilities and data ownership,
not technical layers. I use synchronous calls for immediate answers and events
for decoupled workflows, then explicitly design timeouts, retries, idempotency,
eventual consistency, and observability.

## Quick revision

- Service boundary = capability plus ownership.
- Separate data enables autonomy but complicates consistency.
- REST creates temporal coupling.
- Async improves decoupling but adds delivery complexity.
- Horizontal scaling invalidates JVM-local coordination assumptions.
