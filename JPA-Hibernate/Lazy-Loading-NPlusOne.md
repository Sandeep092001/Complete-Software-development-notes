# Lazy Loading and the N+1 Query Problem

## Lazy loading

A lazy association is represented by a proxy or persistent collection and
loaded when accessed. It avoids fetching data that a use case does not need.
If the persistence context is closed before an uninitialized association is
accessed, Hibernate can throw `LazyInitializationException`.

Making everything `EAGER` is not a general fix: it can load large graphs, use
more memory, and still produce inefficient SQL.

## N+1 example

```text
1 query: load 100 departments
100 queries: access employees for each department
= 101 queries
```

The code appears simple, but query count scales with result count. Detect it by
examining SQL/query metrics and testing realistic collection sizes.

## Solutions by use case

- **`JOIN FETCH`**: fetch a required relationship in the same query.
- **`@EntityGraph`**: declare a use-case-specific fetch graph.
- **`@BatchSize` / batch fetching**: initialize several lazy associations per
  follow-up query rather than one at a time.
- **DTO projection**: select only fields/aggregates needed by the response.

If an endpoint needs department ID, name, and employee count, do not load full
Employee entities. Query those columns and `COUNT` into a projection.

## Cartesian explosion

Joining multiple to-many collections can multiply result rows: a department
with 10 employees and 8 projects may yield up to 80 joined rows before ORM
deduplication. Memory and network cost remain even if the root entity list is
deduplicated. Prefer separate targeted queries, batching, or projections.

## Interview traps

- LAZY is a loading intention; accidental access during serialization can still
  trigger queries.
- Open Session in View can hide boundary problems and move queries into response
  rendering.
- `DISTINCT` may deduplicate entities but does not erase the database work of a
  huge joined result.
- Pagination plus collection fetch joins can be problematic because rows and
  root entities do not have a one-to-one relationship.

## Concise interview answer

I keep associations lazy by default and define the fetch plan per use case. For
entity workflows I use a targeted fetch join/entity graph or batching; for
read-only APIs I prefer DTO projections. I verify query count and watch for
multiple-collection Cartesian growth.

## Quick revision

- Lazy access needs an active persistence context.
- N+1 = one root query plus one query per result/association.
- Fetch plans should be use-case-specific.
- Projections avoid over-fetching.
- Multiple collection joins can explode rows.
