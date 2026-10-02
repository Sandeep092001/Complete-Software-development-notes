# Spring Bean Lifecycle

## Simplified lifecycle

```text
instantiate bean
      ↓
inject dependencies
      ↓
aware callbacks / post-processors
      ↓
@PostConstruct
      ↓
bean is ready for use
      ↓
container shutdown
      ↓
@PreDestroy
```

The precise lifecycle includes BeanPostProcessors and possible proxy creation,
but the sequence above is the useful first interview model.

Use `@PostConstruct` for initialization that requires injected dependencies.
Avoid long remote operations there because they delay application startup and
can make readiness difficult. Use `@PreDestroy` to release bean-owned resources
during an orderly container shutdown.

## Critical correction

`@PreDestroy` is a Spring/Jakarta lifecycle callback associated with bean
destruction or application-context shutdown. It is not triggered merely because
an arbitrary Java object becomes unreachable, and it is not a garbage-collector
callback. A forced process kill may also prevent graceful shutdown callbacks.

## Proxy nuance

Features such as transactions and AOP are commonly applied through a proxy
around a bean. Lifecycle and method-interception behavior should not be confused:
constructors initialize the target; a proxied method call activates advice only
when it passes through the proxy.

## Quick revision

- Constructor creates the object; DI supplies dependencies.
- `@PostConstruct` runs after injection.
- `@PreDestroy` runs on managed shutdown, not normal GC eligibility.
- Graceful callbacks are not guaranteed after abrupt termination.
- Spring post-processors can wrap beans with proxies.
