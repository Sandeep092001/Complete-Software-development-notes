# Database Transactions and Isolation

## Transactions and atomicity

A transaction groups operations into one unit: either all commit or all roll
back. Atomicity prevents partial local updates, but isolation determines what
concurrent transactions can observe and whether anomalies are possible.

## READ COMMITTED

Under READ COMMITTED, each statement sees data committed before that statement.
It prevents dirty reads but permits a non-repeatable read:

```text
T1 reads balance = ₹10,000
T2 updates balance = ₹5,000 and commits
T1 reads again and sees ₹5,000
```

The two reads in T1 need not return the same value because T2 committed between
them.

## Isolation is not business correctness by itself

Even a valid isolated transaction can violate an application invariant if the
algorithm is read-then-write without conflict detection. Use constraints,
conditional updates, versions, or locks based on the invariant.

## Interview traps

- Atomicity and isolation are different ACID properties.
- `@Transactional` does not imply SERIALIZABLE isolation.
- Higher isolation can reduce anomalies but increase blocking/aborts; database
  implementations use different locking/MVCC details.
- Never claim a precise anomaly guarantee without knowing the database and
  isolation implementation.

## Concise interview answer

READ COMMITTED prevents reading uncommitted data, but each statement may see a
newer committed snapshot, so non-repeatable reads are possible. I choose
isolation together with constraints or explicit concurrency control based on the
business invariant and contention.

## Quick revision

- Transaction = atomic local unit of work.
- Isolation governs concurrent visibility.
- READ COMMITTED allows non-repeatable reads.
- Correctness may still require conflict detection or locking.
