# Spring Boot Exception Handling

## Why centralize error handling?

Business/service code should normally throw meaningful exceptions and let them
propagate. Repetitive controller `try/catch` blocks mix transport concerns with
business logic and produce inconsistent error bodies.

`@RestControllerAdvice` provides cross-controller handling.

```java
@RestControllerAdvice
class ApiExceptionHandler {
    @ExceptionHandler(JobNotFoundException.class)
    ResponseEntity<ApiError> handleNotFound(
            JobNotFoundException ex, HttpServletRequest request) {
        ApiError error = new ApiError(
                404, ex.getMessage(), Instant.now(),
                request.getRequestURI(), Map.of());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}
```

Useful error fields include status/code, safe message, timestamp, request path,
correlation ID, and validation errors. Do not expose stack traces or internal
SQL details to clients.

## 400 versus 404

- `400 Bad Request`: malformed syntax, invalid input shape, or rejected request
  values.
- `404 Not Found`: the addressed resource does not exist or is intentionally
  undisclosed.

Map domain failures deliberately; do not turn all exceptions into status 500.
Unexpected exceptions should be logged with diagnostic context while returning
a generic client-safe response.

## Propagation

If a repository/service throws and no layer handles the exception, it unwinds
the call stack to Spring MVC, where a matching `@ExceptionHandler` can translate
it into HTTP. Handling an exception too early can suppress transaction rollback
or destroy useful context.

## Interview traps

- A global handler is not a replacement for validation or correct domain
  exceptions.
- Returning HTTP 200 with an error payload breaks HTTP semantics.
- Catching `Exception` everywhere often hides bugs and may allow invalid work to
  continue.
- Client messages and internal logs have different security needs.

## Concise interview answer

I throw domain-specific exceptions from the business layer and translate them
once in `@RestControllerAdvice` using focused `@ExceptionHandler` methods. This
keeps layers clean and produces a consistent, safe error DTO with the correct
HTTP status.

## Quick revision

- Centralize HTTP exception translation.
- Use domain-specific exceptions.
- Preserve meaningful HTTP semantics.
- Never leak implementation details.
- Log unexpected failures with correlation context.
