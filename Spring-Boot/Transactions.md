# Spring Transaction Boundaries

## What `@Transactional` provides

Spring usually applies `@Transactional` through a proxy. A call entering the
proxied method opens or joins a transaction, and normal return commits while
configured failures roll it back.

Place the boundary around a complete business use case rather than each tiny
repository call. Keep network calls out of long database transactions when
possible because locks and connections remain occupied.

## What it does not provide

- It does not prevent every race or lost update; isolation and locking matter.
- It does not make a database update and Kafka publish one atomic operation.
- It does not coordinate two independent service databases by normal rollback.
- It does not apply automatically to a self-invocation that bypasses the proxy.

## Rollback trap

By default, Spring rolls back for unchecked exceptions. If code catches a
failure and returns normally, the proxy may commit unless rollback is explicitly
requested or the exception is rethrown. Checked-exception behavior may require
configuration.

## Concise interview answer

`@Transactional` defines a local transaction boundary through Spring's proxy
infrastructure. It gives atomicity for participating resources in that boundary,
but concurrency correctness still depends on isolation/locking, and cross-system
consistency needs patterns such as outbox or Saga.

## Quick revision

- Transactions are commonly proxy-driven.
- Self-invocation can bypass advice.
- Local atomicity is not distributed atomicity.
- Isolation and locks decide concurrent behavior.
- Catching exceptions can change rollback behavior.
