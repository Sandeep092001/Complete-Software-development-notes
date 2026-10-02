# Java Concurrency

## Race conditions and thread safety

A race condition occurs when correctness depends on unpredictable interleaving
of threads. `count++` is not one indivisible operation: it reads, increments,
then writes. Two threads can read the same old value and lose one update.

Local variables are normally confined to one invocation, but objects referenced
by locals may still be shared. The real question is whether mutable state is
reachable by multiple threads.

Spring singleton beans serve many requests concurrently. They should normally
be stateless. Mutable instance fields require an explicit concurrency design.

## `synchronized`

Entering a synchronized region acquires a monitor. It provides mutual exclusion
and a happens-before visibility relationship between unlock and later lock of
the same monitor.

```java
public synchronized void transfer(Account from, Account to, long amount) {
    // compound invariant protected inside this JVM
}
```

Lock the full invariant, use one consistent lock, and avoid slow remote calls
while holding it. Synchronizing different monitor objects does not coordinate
the operations.

## `AtomicInteger` and CAS

`incrementAndGet()` atomically increments and returns the new value. Atomic
classes commonly use compare-and-set (CAS): update only if the current value is
still the expected value. On contention, the implementation retries with the
new observed value.

```text
read expected → calculate update → CAS(expected, update)
                         └─ failure → read again and retry
```

Atomics are excellent for a single counter or reference. They do not
automatically protect invariants spanning several variables or a check followed
by an unrelated side effect. Use a lock, database operation, or redesigned
state transition for compound behavior.

## JVM-local versus distributed coordination

`synchronized` protects threads that share the same JVM monitor. If two requests
reach two application instances, each instance owns a different lock.

```text
Request A → Server 1 → lock in JVM 1
Request B → Server 2 → lock in JVM 2
```

Both can enter simultaneously. For a shared database invariant, prefer a unique
constraint, atomic conditional update, optimistic/pessimistic database locking,
or—when truly necessary—a correctly designed distributed lock.

## Failure scenarios and traps

- `volatile int count; count++` still loses updates; visibility is not
  atomicity.
- Replacing two related integers with two `AtomicInteger`s does not make their
  combined invariant atomic.
- CAS can waste CPU under heavy contention and has subtleties such as ABA for
  some algorithms.
- A thread-safe collection does not make a series of calls a transaction.
- A JVM lock disappears if the process crashes and cannot coordinate replicas.

## Concise interview answer

I first identify shared mutable state and the invariant. For one independent
counter, an atomic type is suitable. For a compound invariant inside one JVM, I
use synchronization or a lock with a small critical section. For multiple
instances, I move coordination to the shared consistency boundary, usually the
database, rather than relying on `synchronized`.

## Quick revision

- Race = outcome depends on interleaving.
- `synchronized` gives mutual exclusion and visibility in one JVM.
- CAS retries when another thread changed the expected value.
- Atomics handle individual atomic state, not arbitrary transactions.
- Stateless singleton beans are safest.
- Distributed invariants need distributed/shared coordination.

## Related questions

1. Why is `volatile` insufficient for increment?
2. When is `LongAdder` preferable to `AtomicLong`?
3. How would you prevent two servers from selling the last ticket?
4. What state in a singleton service would be unsafe?
