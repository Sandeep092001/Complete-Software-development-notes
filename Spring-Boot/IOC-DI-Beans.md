# Spring IoC, Dependency Injection, and Beans

## IoC and DI

Inversion of Control means application code does not manually construct and
wire the whole object graph. Spring's container discovers configuration,
creates beans, resolves dependencies, and manages lifecycle. Dependency
Injection is the mechanism by which a bean receives its collaborators.

Constructor injection is the default choice because required dependencies are
explicit, can be `final`, and are easy to provide in unit tests.

```java
@Service
public class JobService {
    private final JobRepository repository;

    public JobService(JobRepository repository) {
        this.repository = repository;
    }
}
```

## Stereotypes and layering

- `@Component`: general Spring-managed component.
- `@Service`: business/service-layer intent.
- `@Repository`: persistence intent; also participates in persistence exception
  translation.
- `@RestController`: HTTP endpoint component whose return values are written to
  the response body.

A common direction is Controller → Service → Repository. Controllers translate
HTTP input/output, services enforce use cases and business rules, and
repositories abstract persistence. Spring Data JPA can generate repository
implementations from interfaces.

## Singleton beans and concurrency

Singleton is the default scope: one bean instance per application context, not
one instance globally across every server. Many request threads can call it at
the same time, so service beans should avoid mutable request-specific fields.

## Circular dependencies

If A requires B through its constructor and B requires A, neither can be fully
constructed first. This usually signals muddled responsibilities.

Prefer breaking the cycle by extracting a third responsibility, reversing a
dependency through a better abstraction, or publishing an event. `@Lazy` may
defer resolution but often hides the design problem.

## Interview traps

- DI is not merely “using `@Autowired`”; it is external construction and wiring.
- A singleton bean is not automatically thread-safe.
- Field injection hides requirements and makes isolated tests awkward.
- Component stereotypes express architectural intent; they are not four
  unrelated bean-creation mechanisms.

## Concise interview answer

Spring's IoC container builds and manages the application object graph. I use
constructor injection for mandatory dependencies, keep singleton services
stateless, and maintain Controller → Service → Repository responsibilities.
Circular constructor dependencies generally indicate a design boundary that
should be refactored.

## Quick revision

- IoC owns construction and lifecycle.
- DI supplies collaborators.
- Constructor injection exposes required dependencies.
- Singleton means one per context and concurrent access is possible.
- Circular dependencies are usually a design smell.

## Related questions

1. Why prefer constructor injection to field injection?
2. Is a Spring singleton the same as the GoF singleton pattern?
3. How would you remove an A → B → A dependency?
