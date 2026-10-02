# Heaps and Top-K

## Heap mental model

A heap efficiently exposes one extreme element. Java's `PriorityQueue` is a
min-heap by default. Offer and poll are O(log n); peek is O(1). It does not keep
all elements in fully sorted iteration order.

## Top K largest

Maintain a min-heap of size K. For every number, add it; if size exceeds K,
remove the smallest. The heap then contains the K largest seen so far.

Time O(n log k), space O(k). This is better than full sorting when K is much
smaller than n and no complete order is needed.

For K smallest, use a max-heap of size K. For frequency problems, first build a
frequency map, then heap entries by frequency.

## Comparator trap

Avoid `b - a` as a reverse comparator because subtraction can overflow. Use
`Comparator.reverseOrder()` or `Integer.compare(b, a)`.

## When not to use a heap

- Need one maximum from a static array → linear scan.
- Need membership → set/map.
- Need all output sorted → sorting may be simpler.
- Small fixed value range → bucket/counting methods may be O(n).

## Quick revision

- Java priority queue defaults to min-heap.
- Top K largest → min-heap of size K.
- Complexity O(n log k), memory O(k).
- Heap iteration is not sorted.
- Choose heap when repeated extreme access matters.
