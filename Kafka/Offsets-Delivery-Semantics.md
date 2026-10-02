# Kafka Offsets and Delivery Semantics

## Offsets

An offset is a record position within one partition. A consumer group's committed
offset records where that group should resume. Reading does not delete the
record.

## At-most-once

Commit before processing:

```text
poll → commit → process
```

A crash after commit but before the side effect can lose the business operation.
The record is processed zero or one times from the application's perspective.

## At-least-once

Process before commit:

```text
poll → process → commit
```

If the side effect succeeds and the consumer crashes before the commit, Kafka
redelivers from the last committed position. This avoids loss under the assumed
model but permits duplicates. An idempotent consumer is required.

## Exactly-once boundary

Kafka transactions can atomically write Kafka output records and consumed
offsets inside Kafka when correctly configured. They do not make an arbitrary
MySQL update, email, or payment-provider call atomic with Kafka.

Exactly-once business effect is commonly approximated/achieved using
at-least-once delivery plus stable IDs, unique constraints, atomic local inbox
processing, and idempotent downstream APIs.

## Interview traps

- An offset is not a business deduplication key.
- Auto-commit can acknowledge work before it is safely complete if used
  carelessly.
- “Kafka supports exactly once” is incomplete without naming sources, sinks,
  transaction boundary, and configuration.
- Reprocessing is expected behavior, not necessarily a broker bug.

## Concise interview answer

Offsets track group progress per partition. Committing before work risks loss;
committing after work risks replay. I generally choose at-least-once and make the
business operation idempotent. Kafka exactly-once applies to Kafka transaction
boundaries, not automatically to external systems.

## Quick revision

- Record retention is independent of consumption.
- Commit controls resume position.
- At-most-once: loss possible.
- At-least-once: duplicates possible.
- End-to-end guarantees depend on external side effects.
