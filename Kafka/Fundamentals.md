# Kafka Fundamentals: Why Kafka?

## Why use Kafka?

Kafka provides a durable, high-throughput event log for asynchronous
communication. A producer publishes a business fact once; Payment, Inventory,
Notification, and Analytics consumer groups can process it independently.

Compared with a chain of synchronous REST calls, this reduces temporal coupling
and lets consumers recover from downtime by resuming from retained records. It
also absorbs bursts. The cost is eventual consistency, delivery/ordering design,
schema evolution, lag monitoring, and duplicate handling.

## Core concepts

- **Broker**: Kafka server storing partition replicas.
- **Topic**: named stream of records.
- **Partition**: ordered append-only log and unit of parallelism.
- **Offset**: a record's position inside one partition.
- **Producer**: writes records.
- **Consumer group**: cooperating consumers that divide partitions.

Kafka does not delete a record merely because one consumer reads it. Retention
is controlled by time/size/compaction policy, while each group tracks its own
position.

## Kafka versus REST

Use REST when a caller needs an immediate answer from a known service. Use an
event when the producer can publish a fact and continue while independent
consumers react. Many systems use both.

## Interview traps

- Kafka does not by itself make a workflow exactly once end-to-end.
- Async messaging does not remove failure; it moves failure handling into lag,
  retries, poison events, and reconciliation.
- An event should describe something that happened, not leak one consumer's
  internal model.
- Kafka retention and committed consumer offsets are separate concepts.

## Concise interview answer

Kafka decouples producers from consumers in time and deployment, offers durable
replay and high partitioned throughput, and enables multiple independent
consumers. I use it when eventual asynchronous processing is acceptable and
explicitly design keys, schemas, delivery semantics, idempotency, and lag/error
handling.

## Quick revision

- Topic = logical stream; partition = ordered log.
- Offsets locate records per partition.
- Consumer groups maintain independent progress.
- Kafka improves decoupling, not automatic correctness.
