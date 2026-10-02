# JVM Memory: Heap and Stack

## Mental model

Each thread has its own stack containing call frames, parameters, local
variables, and intermediate execution state. Objects and arrays are generally
allocated on the shared heap; a local variable may hold a reference to a heap
object.

```text
Thread stack                         Heap
┌─────────────────────┐             ┌──────────────────┐
│ frame: processJob   │             │ Job object       │
│ id = 42             │             │ salary = ...     │
│ jobRef ─────────────┼────────────►│                  │
└─────────────────────┘             └──────────────────┘
```

The JVM may optimize allocation, so this is the language-level interview model,
not a promise about every physical byte.

## Failure modes

- Deep or infinite recursion can exhaust a thread's stack and cause
  `StackOverflowError`.
- Retaining reachable objects can exhaust the heap and cause
  `OutOfMemoryError`.
- An object being unreachable makes it eligible for garbage collection; it does
  not imply immediate collection or invoke Spring's `@PreDestroy`.

## Interview traps

- “Local variable” does not mean the referenced object is thread-safe; the
  reference can escape or point to shared state.
- Heap versus stack is not the same as mutable versus immutable.
- Garbage collection manages heap reachability; it does not close every external
  resource deterministically. Use try-with-resources/lifecycle hooks.

## Quick revision

- Stack is per thread and organized by method frames.
- Heap is shared and holds objects/arrays in the standard model.
- References may live on a stack while objects live on the heap.
- Reachability drives GC eligibility, not Spring bean destruction callbacks.
