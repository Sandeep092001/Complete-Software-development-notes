# Idempotency

## Payment retry problem

The server may successfully charge a payment but the response can be lost. The
client cannot tell “failed” from “succeeded but response lost,” so it retries.
Without idempotency, the retry may charge again.

The client sends a stable key for the same logical operation:

```text
Idempotency-Key: order-123-payment
```

The Payment Service persists the key under a unique constraint together with
request identity/status/result. A duplicate request returns or resumes the
existing operation rather than creating a second charge.

## Concurrent duplicate requests

A read-then-insert check is race-prone. Two requests can both observe absence.
The unique database constraint is the final arbiter. Also bind the key to a
request fingerprint: reusing the same key with a different amount should be
rejected, not treated as the original request.

## State handling

- `SUCCESS`: return the stored successful result.
- `IN_PROGRESS`: wait, poll, or return a consistent in-progress response.
- retryable failure: define whether processing can resume.
- permanent failure: return the recorded failure according to API policy.

Retention must exceed the client's legitimate retry window.

## Consumer idempotency

For at-least-once messages, store a stable event ID in an inbox/deduplication
table with a unique constraint, in the same local transaction as the business
update. An in-memory set is neither durable nor shared across replicas.

## Interview traps

- Idempotency is about one logical operation, not all requests with the same
  endpoint.
- A random new key on every retry defeats deduplication.
- Checking first without a uniqueness constraint races.
- Marking a key complete before the business action can lose work; marking it in
  a separate transaction leaves a duplicate window.
- Provider calls must propagate a suitable idempotency key too.

## Concise interview answer

I require a client-generated stable key, enforce it with a unique database
constraint, store the request fingerprint and durable result, and make key
creation plus local state changes atomic. Concurrent duplicates then converge
on one business operation and receive the stored outcome.

## Quick revision

- Same logical retry → same key.
- Unique constraint handles concurrency.
- Persist result/status and request fingerprint.
- Define in-progress and retention behavior.
- Consumers use durable event IDs and atomic inbox processing.
