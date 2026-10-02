# Complete Software Development Notes

Interview-focused revision notes for backend/SDE-2 preparation. These notes
capture concepts already covered in the Daily Interview Prep sessions, with the
failure modes, corrections, and traps that matter in interviews.

## Quick navigation

### Java

- [Collections and object contracts](Java-Core/Collections.md)
- [Concurrency](Java-Core/Concurrency.md)
- [JVM memory: heap and stack](Java-Core/JVM-Memory.md)

### Spring Boot

- [IoC, dependency injection, and beans](Spring-Boot/IOC-DI-Beans.md)
- [Bean lifecycle](Spring-Boot/Bean-Lifecycle.md)
- [Exception handling](Spring-Boot/Exception-Handling.md)
- [Bean validation](Spring-Boot/Validation.md)
- [Transaction boundaries](Spring-Boot/Transactions.md)

### Persistence and databases

- [Persistence context and dirty checking](JPA-Hibernate/Persistence-Context.md)
- [Entity lifecycle and `merge()`](JPA-Hibernate/Entity-Lifecycle-Merge.md)
- [Lazy loading and N+1](JPA-Hibernate/Lazy-Loading-NPlusOne.md)
- [JPA locking and concurrency](JPA-Hibernate/Locking-Concurrency.md)
- [Transactions and isolation](Databases/Transactions-Isolation.md)
- [Database concurrency and locking](Databases/Concurrency-Locking.md)

### Distributed backend

- [Microservices fundamentals](Microservices/Fundamentals.md)
- [Resilience patterns](Microservices/Resilience-Patterns.md)
- [Idempotency](Microservices/Idempotency.md)
- [Distributed transactions and Saga](Microservices/Distributed-Transactions-Saga.md)
- [Kafka fundamentals](Kafka/Fundamentals.md)
- [Partitions and consumer groups](Kafka/Partitions-Consumer-Groups.md)
- [Offsets and delivery semantics](Kafka/Offsets-Delivery-Semantics.md)
- [Transactional outbox](Kafka/Transactional-Outbox.md)

### DSA patterns

- [Arrays and hashing](DSA-Problems/Arrays-Hashing.md)
- [Sliding window](DSA-Problems/Sliding-Window.md)
- [Prefix sum](DSA-Problems/Prefix-Sum.md)
- [Heaps and Top-K](DSA-Problems/Heaps.md)
- [Dynamic programming reasoning](DSA-Problems/Dynamic-Programming.md)

## How to revise

1. Read the quick-revision bullets.
2. Answer the related questions aloud without looking.
3. Explain the failure window and trade-off, not only the definition.
4. Revisit the example and change one assumption: more traffic, another server,
   a crash, or a concurrent request.

## Future session rule

At the end of each completed prep session, add the learned material to the most
specific existing note, or create a focused new note. Record corrected answers,
interview traps, and unresolved questions. Commit the session as one coherent
change.
