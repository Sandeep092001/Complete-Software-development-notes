# Bean Validation

## Deserialization versus validation

Jackson first converts the request body into a Java object. Bean Validation then
checks constraints when the controller parameter is annotated with `@Valid`.
They are separate stages: malformed JSON can fail before constraint validation.

```java
record CreateJobRequest(
        @NotBlank String title,
        @NotNull @Positive Integer salary) {}

@PostMapping("/jobs")
ResponseEntity<?> create(@Valid @RequestBody CreateJobRequest request) {
    return ResponseEntity.ok(service.create(request));
}
```

Prefer `Integer` when “not supplied” must be distinguished from zero. A primitive
`int` defaults to zero during object construction and cannot represent `null`.
Combine `@NotNull` with `@Positive` when both absence and non-positive values are
invalid.

## Validation errors

Invalid method arguments commonly raise `MethodArgumentNotValidException`.
Field errors are available through:

```java
ex.getBindingResult().getFieldErrors()
```

Convert them into a stable response shape, often `Map<String, String>` for one
message per field or `Map<String, List<String>>` when all violations matter.
`BindingResult` can also be accepted next to the validated parameter for local
handling, but a global handler keeps APIs consistent.

## Request validation versus business validation

Annotations are well suited to structural and context-free constraints: null,
length, format, positivity. Rules depending on current data, user permissions,
workflow state, or product policy belong in the service/domain layer.

For example, “salary must be positive” is a DTO constraint. “The maximum salary
for this role in the active compensation policy is ₹X” is a business rule that
may require data and can change independently.

## Interview traps

- `@Valid` does not make database state or cross-record rules valid.
- Deserialization errors and validation errors may require different handlers.
- Primitive defaults can accidentally pass/fail constraints with a misleading
  message.
- Validation at the API boundary does not protect other entry points; enforce
  true invariants in the domain/service and database as appropriate.

## Quick revision

- Deserialize first, validate second.
- `@Valid` triggers nested Bean Validation.
- `MethodArgumentNotValidException` exposes a `BindingResult`.
- Use wrappers when absence matters.
- Keep dynamic business rules in the business layer.
