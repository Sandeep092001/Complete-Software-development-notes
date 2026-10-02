# Entity Lifecycle and `merge()`

## Why `merge()` exists

A detached object may contain state that should be copied into a managed entity.
`EntityManager.merge(detached)` returns a managed instance containing merged
state. The passed object does not become managed.

```java
Job detached = obtainDetachedJob();
Job managed = entityManager.merge(detached);

managed.setSalary(3_000_000);  // tracked
detached.setSalary(1);         // not automatically tracked
```

Always use the returned reference if subsequent changes must be tracked.

## What may happen internally

JPA locates or creates a managed instance, copies state from the supplied object,
and returns the managed instance. SQL timing depends on flush. Cascades determine
whether associated entity state is merged.

## Interview traps

- `merge()` does not reattach the same Java object.
- Ignoring the return value and continuing to change the detached object loses
  those later changes.
- Blindly merging a web request into an entity can overwrite fields the client
  was not allowed to control.
- Merge is not a concurrency strategy; optimistic version checks may still fail.
- Large detached graphs plus broad cascade merge can cause unexpected queries
  and updates.

## Safer update style

For service updates, load the managed entity inside the transaction, authorize
the operation, and copy only allowed fields from the request DTO. This makes the
write set explicit and works naturally with dirty checking.

## Concise interview answer

`merge()` copies detached state into a managed instance and returns that managed
instance. The input remains detached. Therefore I use the returned reference—or
prefer loading the managed entity and applying an explicit patch—to avoid lost
or unauthorized changes.

## Quick revision

- Managed objects are tracked; detached objects are not.
- `merge()` returns a managed copy.
- The original reference remains detached.
- Cascades control graph merging.
- Merge does not eliminate optimistic conflicts.
