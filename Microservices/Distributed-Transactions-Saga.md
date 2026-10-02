# Distributed Transactions and Saga

## The problem

Order, Inventory, and Payment own independent databases. Rolling back the Order
database cannot undo an Inventory commit or an external payment charge. A local
transaction ends at its resource boundary.

## Saga

A Saga is a sequence of local transactions. When a later step cannot complete,
the workflow runs compensating actions for earlier committed work.

```text
Create Order → Reserve Inventory → Charge Payment
                                    │ failure
                                    ▼
                    Release Inventory → Cancel Order
```

Compensation is a business action, not time travel. A refund is a new transaction
and may itself fail, so it needs retry, idempotency, status tracking, and manual
recovery paths.

## Orchestration versus choreography

An orchestrator explicitly commands steps and records workflow state, making the
flow easier to understand and operate. Choreography has services react to events,
reducing central control but making long workflows and unintended coupling
harder to trace. Choose based on complexity and ownership rather than fashion.

## Failure classification

- Transient technical failure: retry with bounded backoff when safe.
- Permanent business failure: do not retry forever; compensate or reject.
- Unknown outcome: query/reconcile using stable operation IDs.
- Poison message/manual case: dead-letter plus operational workflow.

Persist Saga state so process restarts do not forget progress. Make every command
and compensation idempotent and include correlation IDs for tracing.

## Interview traps

- Saga does not provide isolation equivalent to one serializable transaction;
  intermediate states may be visible.
- Compensation may be impossible or only approximate.
- Publishing an event after a local commit creates a dual-write gap unless an
  outbox or equivalent is used.
- Infinite retries can block the workflow and overload a dependency.

## Concise interview answer

For independent service databases, I model the workflow as durable local
transactions with idempotent compensations. I persist Saga state, distinguish
transient from business failures, publish reliably through outbox, and provide
reconciliation/manual handling for outcomes that cannot be automatically fixed.

## Quick revision

- Local rollback cannot undo another service's commit.
- Saga = local steps plus compensation.
- Compensation is a new fallible business operation.
- Persist state and make steps idempotent.
- Expect eventual consistency and intermediate visibility.
