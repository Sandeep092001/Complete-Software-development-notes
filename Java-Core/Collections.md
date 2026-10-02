# Java Collections and Object Contracts

## `==` versus `equals()`

For primitives, `==` compares values. For references, `==` asks whether two
variables point to the exact same object. `equals()` asks whether two objects
are logically equal, according to the class implementation.

```java
String a = new String("java");
String b = new String("java");

a == b;       // false: different objects
a.equals(b);  // true: same logical text
```

Do not use string-pool behavior as proof that `==` compares string content.

## The `equals()` / `hashCode()` contract

Equal objects must produce the same hash code. Unequal objects may still
collide. If `equals()` is overridden, `hashCode()` must be overridden using the
same equality-defining fields.

A hash-based collection first uses the hash to find a bucket, then uses equality
to distinguish candidates. A broken contract can make a logically equal key
unfindable.

Avoid mutating fields involved in equality while an object is a `HashMap` key or
`HashSet` member. The object can remain in its old bucket while its new hash
points lookup elsewhere.

## Immutability

An immutable class has no observable state changes after construction:

- make the class `final`, or prevent unsafe subclassing
- make fields `private final`
- initialize all state in the constructor
- expose no setters
- defensively copy mutable inputs and outputs
- ensure referenced objects are immutable or safely copied

```java
public final class EmployeeSnapshot {
    private final String id;
    private final List<String> skills;

    public EmployeeSnapshot(String id, List<String> skills) {
        this.id = id;
        this.skills = List.copyOf(skills);
    }

    public List<String> skills() {
        return skills; // unmodifiable snapshot
    }
}
```

`final` prevents reassignment of a reference; it does not make the referenced
object immutable.

## `HashMap` internals

`HashMap` computes a spread hash, maps it to a bucket, and resolves collisions
by comparing keys. Modern Java can turn a heavily collided bucket into a
balanced tree after implementation-specific thresholds are met. Average lookup
is O(1); pathological collision behavior is worse.

It allows one `null` key and multiple `null` values and is not thread-safe.
Resizing is expensive, so capacity planning can matter for large maps.

## Choosing a collection

| Need | Good starting choice | Why |
|---|---|---|
| Fast key lookup | `HashMap` | Average O(1) lookup |
| Insertion/access order | `LinkedHashMap` | Hash table plus linked order |
| Sorted keys | `TreeMap` | O(log n), comparator/natural order |
| Unique sorted values | `TreeSet` | Tree-backed set |
| Concurrent atomic key operation | `ConcurrentHashMap` | Thread-safe compound APIs |
| Repeated smallest/largest item | `PriorityQueue` | O(log n) insertion/removal |

`ConcurrentHashMap.putIfAbsent()` is atomic. `containsKey()` followed by
`put()` is a check-then-act race. `ConcurrentHashMap` does not permit `null`
keys or values because `null` would make concurrent absence ambiguous.

Java's `PriorityQueue` is a min-heap by default. Use a reversed comparator for
a max-heap. Iterating a priority queue does not guarantee sorted order; only
the head is guaranteed.

## Interview traps

- `TreeMap` uniqueness is governed by comparator result, so a comparator
  inconsistent with `equals()` can surprise callers.
- Thread-safe individual operations do not make an arbitrary multi-step
  workflow atomic.
- `LinkedHashMap` can implement an LRU policy with access order and
  `removeEldestEntry`, but concurrent use still needs coordination.

## Concise interview answer

Choose by required semantics first—ordering, uniqueness, lookup, concurrency,
and priority—then consider complexity. For hash collections, implement stable
`equals()` and `hashCode()` together. For concurrent compound operations, use
atomic collection APIs such as `computeIfAbsent` or `putIfAbsent`.

## Quick revision

- `==`: identity for references; `equals()`: logical equality.
- Equal objects must share a hash code.
- Mutable keys can break hash lookup.
- `HashMap` is unordered and not thread-safe.
- `PriorityQueue` exposes the highest-priority head, not sorted iteration.

## Related questions

1. What happens if every key has the same hash code?
2. Why does a mutable map key cause lookup failure?
3. When would you choose `TreeMap` over sorting a `HashMap` result?
4. Why is `putIfAbsent` safer than check-then-put?
