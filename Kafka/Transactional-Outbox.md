# Dual Writes and the Transactional Outbox

## The dual-write problem

This is not atomic across MySQL and Kafka:

```java
@Transactional
public void completePayment(...) {
    savePaymentSuccess();
    kafkaTemplate.send("payments", event);
}
```

Possible failures:

- database commits, Kafka publish fails → event is missing
- Kafka accepts the event, database rolls back → event describes state that did
  not commit

Ordinary Spring database `@Transactional` cannot roll back Kafka as if both were
one local resource.

## Transactional outbox

Write the business change and an outbox row in the same database transaction:

```sql
BEGIN;
UPDATE payment SET status = 'SUCCESS' WHERE id = 42;
INSERT INTO outbox(event_id, type, payload, status)
VALUES ('evt-42', 'PaymentCompleted', '{...}', 'NEW');
COMMIT;
```

A separate publisher polls the table or uses change-data capture and publishes
the durable event to Kafka. If Kafka is unavailable, the row remains and can be
retried.

## Remaining duplicate window

The publisher can send successfully and crash before marking the row `SENT`.
After restart it sends again. Therefore outbox gives reliable at-least-once
publication, not automatic duplicate elimination. Consumers use the stable
`event_id` and idempotent processing.

## Operational design

- index pending rows and claim work safely across publisher instances
- use bounded retry/backoff and surface poisoned records
- retain correlation, aggregate ID, type, schema version, creation time
- monitor oldest unpublished age and backlog, not only row count
- archive/delete old sent rows safely
- preserve required ordering, often by aggregate key

## Exactly-once nuance

Kafka producer transactions can coordinate Kafka records and Kafka offsets.
They do not magically enlist an external MySQL transaction. Outbox bridges the
local DB commit to eventual Kafka publication; consumer idempotency closes the
duplicate-effect gap.

## Interview traps

- Marking the outbox row sent before publishing can lose the event.
- Marking it afterward creates a duplicate window; this is intentional and must
  be handled.
- Publishing directly from a request after commit without durable retry still
  loses events.
- Outbox payloads need schema evolution and sensitive-data discipline.

## Concise interview answer

I atomically save business state and an outbox event in one database transaction.
A separate retriable publisher sends it to Kafka. Because a crash after send but
before marking sent can duplicate publication, every event has a stable ID and
consumers are idempotent. I monitor backlog age and provide poison-event recovery.

## Quick revision

- Database plus Kafka is a dual write.
- Business row and outbox row commit together.
- Publisher retries from durable state.
- Duplicate publication remains possible.
- Stable event IDs plus idempotent consumers protect effects.
