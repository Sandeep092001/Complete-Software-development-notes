# Database Concurrency and Locking

## Lost update

Two requests read the same state, calculate independently, and both write. The
later write overwrites the earlier result. Merely wrapping both in transactions
does not necessarily detect this.

## Atomic conditional update

Push the condition and transition into one SQL statement:

```sql
UPDATE account
SET status = 'ACTIVE'
WHERE id = 123
  AND status = 'FREE';
```

Affected rows are part of the result:

- `1`: this request acquired the state.
- `0`: the precondition was false; another request may have won.

This avoids a separate vulnerable check-then-update window and is often ideal
for a simple state machine or inventory decrement with a guarded predicate.

## Optimistic versus pessimistic strategy

Optimistic locking detects collisions at write time using a version and works
well when contention is low. Pessimistic locking reserves the row during the
transaction and can help under high contention, but increases waits and
deadlock risk.

For ticket booking, also enforce a unique database constraint on the seat/event
ownership. Application checks improve messages; the database constraint is the
final race-safe guard. Very hot inventory may require queueing, reservation
expiry, partitioning, or a deliberately serialized allocator.

## Deadlocks and operational concerns

Transactions can deadlock when they acquire locks in inconsistent order. Keep
transactions short, acquire resources in a consistent order, index predicates
so locks are precise, configure timeouts, and retry deadlock victims only when
the operation is safe/idempotent.

## Interview traps

- `synchronized` cannot protect rows across multiple app replicas.
- “Use Redis lock” is not automatically safer than a database constraint.
- Retrying a transaction without idempotency can repeat external side effects.
- A pessimistic lock held across a remote call can exhaust the database pool.

## Concise interview answer

I define the invariant first. For a simple transition, I prefer a conditional
update plus affected-row check and a database constraint. For general low-conflict
editing I use optimistic versioning; for short, high-conflict critical sections
I consider pessimistic row locking and plan for timeouts/deadlocks.

## Quick revision

- Transactions alone do not eliminate lost updates.
- Constraints are the final shared guard.
- Conditional SQL atomically checks and changes state.
- Optimistic locking detects; pessimistic locking blocks.
- Keep locked transactions short.
