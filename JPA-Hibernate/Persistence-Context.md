# JPA Persistence Context and Dirty Checking

## Mental model

An `EntityManager` interacts with a persistence context: a unit of managed
entity identity and change tracking. Within one context, loading the same entity
type and ID normally returns the same managed Java instance.

```java
@Transactional
public void reviseSalary(long jobId, int salary) {
    Job job = entityManager.find(Job.class, jobId);
    job.setSalary(salary);
    // no save() required for this managed entity
}
```

At flush, Hibernate compares managed state (or uses enhanced tracking) and
generates an `UPDATE`. Commit normally triggers a flush, but flush and commit are
not synonyms: flush synchronizes SQL with the database inside the transaction;
commit makes the transaction durable/visible according to database semantics.

## Entity states

- **Transient**: new object, not associated with a persistence context.
- **Managed**: tracked by the active context; changes can be dirty-checked.
- **Detached**: has identity but is no longer tracked by that context.
- **Removed**: managed and scheduled for deletion.

## First-level cache

The persistence context is also the mandatory first-level cache. Repeated finds
of the same entity ID in one context can avoid another select and preserve
identity. It is scoped to the context/transactional unit, not a shared
application cache.

Queries may still execute even when entities are managed, and bulk update/delete
statements can leave managed state stale because they bypass normal entity
tracking.

## Failure scenarios and traps

- Modifying a detached entity does not trigger dirty checking.
- `save()` is not universally required after modifying a managed entity.
- Flush can happen before commit, for example before a query under AUTO flush.
- A transaction can flush SQL and later roll back; seeing SQL in logs does not
  prove commit.
- Keeping a context open too long increases memory use and stale-state risk.

## Concise interview answer

The persistence context is JPA's unit of identity and change tracking. A loaded
entity is managed; changes are detected and flushed, often at commit, without an
explicit update call. Its first-level cache guarantees one managed instance per
entity identity within that context.

## Quick revision

- Managed entities are dirty-checked.
- Flush sends SQL; commit finalizes the transaction.
- First-level cache belongs to the persistence context.
- Same ID in one context normally means the same instance.
- Detached changes are not automatically persisted.
