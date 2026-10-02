# Resilience Patterns

## Timeout

A timeout limits how long a caller waits. Without it, slow dependencies can hold
threads, sockets, and connection-pool entries until the caller itself becomes
unavailable. Configure connection and response/read timeouts based on the end-to-
end latency budget.

## Retry, backoff, and jitter

Retry only failures likely to be transient and only when the operation is safe
to repeat. Bound attempts and the total time budget. Exponential backoff spaces
attempts; jitter randomizes them so many clients do not retry simultaneously.

Do not retry permanent business failures such as insufficient funds forever.
Retrying a non-idempotent payment request can double-charge.

## Circuit breaker

- **CLOSED**: calls flow; failures are measured.
- **OPEN**: calls fail fast without reaching the unhealthy dependency.
- **HALF-OPEN**: a limited probe tests recovery.

A breaker protects caller resources and gives a dependency room to recover. It
does not repair the dependency or guarantee a successful fallback.

## Why all three?

```text
Timeout        → bound one attempt
Retry/backoff  → recover from selected transient failures
Circuit breaker→ stop repeated calls during sustained failure
```

Add bulkheads to isolate resource pools, rate limits/backpressure to bound load,
and observability to tune thresholds. Every retry increases downstream load, so
the total retry budget matters across service layers.

## Failure scenario

Payment latency rises from 100 ms to 20 seconds. Without timeouts, Order request
threads accumulate. Immediate retries multiply Payment traffic. With a bounded
timeout, carefully limited jittered retry, and breaker, Order fails quickly or
uses a valid asynchronous fallback rather than exhausting itself.

## Interview traps

- A retry is not a fallback.
- A circuit breaker is not a rate limiter.
- Retrying at every layer causes multiplicative retry storms.
- A fallback returning fake success corrupts business semantics.
- Timeout values copied blindly from defaults may exceed the caller's deadline.

## Concise interview answer

Timeouts bound waiting, retries recover selected transient idempotent operations,
and a circuit breaker prevents persistent failure from exhausting the caller. I
use a shared deadline/retry budget, exponential backoff with jitter, bulkheads,
and metrics rather than stacking unlimited retries.

## Quick revision

- Timeout each remote dependency.
- Retry selectively, with limits, backoff, and jitter.
- Breaker states: closed, open, half-open.
- Protect pools from cascading exhaustion.
- Preserve business truth in fallbacks.
