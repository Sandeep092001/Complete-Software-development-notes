# JPA Locking and Concurrency

## Optimistic locking

Add a version field:

```java
@Version
private long version;
```

An update includes the previously read version in its condition and increments
the version. If another transaction changed the row, zero rows match and JPA
raises an optimistic-lock failure. The application should abort or retry the
whole business operation carefully.

Best fit: conflicts are uncommon and holding database locks during user/business
work would be expensive.

## Pessimistic locking

A pessimistic write lock maps conceptually to `SELECT ... FOR UPDATE`. Competing
transactions wait or fail according to lock timeout/deadlock behavior. It is
useful when conflicts are likely and work is short, but reduces concurrency and
can create lock queues or deadlocks.

## `@Transactional` is not enough

Two transactions can both read the same old value and then overwrite each other
under common isolation levels. A transaction makes each workflow atomic; it does
not automatically serialize workflows.

## Choosing

- Low contention: optimistic versioning.
- High contention with short critical sections: pessimistic locking may fit.
- Simple state acquisition/counter invariant: atomic conditional SQL is often
  clearer and faster.
- Cross-instance Java locks: insufficient; enforce at shared storage.

## Quick revision

- `@Version` detects stale writes.
- Retry the operation, not merely the final SQL, after an optimistic conflict.
- Pessimistic locks block and can deadlock.
- Transaction boundary and concurrency strategy are separate decisions.
