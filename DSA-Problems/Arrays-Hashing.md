# Arrays and Hashing Patterns

## Two Sum

For each value `x`, look for `target - x` among previously seen values. Store
value → index in a map.

```java
Map<Integer, Integer> seen = new HashMap<>();
for (int i = 0; i < nums.length; i++) {
    int need = target - nums[i];
    if (seen.containsKey(need)) return new int[]{seen.get(need), i};
    seen.put(nums[i], i);
}
```

Time O(n), space O(n). Check before insertion when one element cannot be reused.

## Contains Duplicate

Insert values into a `HashSet`; if `add` returns false, the value already
exists. Time O(n) average, space O(n). Sorting reduces auxiliary structure needs
but costs O(n log n) and may mutate input.

## First Unique Character

First count frequencies, then scan in original order and return the first index
with frequency one. A frequency array can replace a map when the character set
is small and fixed.

## Single Number with XOR

If every number appears twice except one, XOR all values:

- `x ^ x = 0`
- `x ^ 0 = x`
- XOR is associative and commutative

Time O(n), extra space O(1). The technique depends on the exact duplicate-count
constraint; do not apply it blindly when values appear three times.

## Pattern selection

- Membership/duplicate detection → `HashSet`.
- Need associated index/count/state → `HashMap`.
- Small bounded alphabet → array may be simpler and faster.
- Need ordered smallest/largest repeatedly → tree or heap, not plain hash map.

## Interview traps

- State whether expected O(1) hash operations are average case.
- Handle duplicates, negatives, overflow, empty inputs, and whether input may be
  modified.
- Explain why a key/value representation contains exactly the information the
  algorithm needs.

## Quick revision

- Two Sum: complement lookup.
- Duplicate: set insertion result.
- First unique: count, then preserve-order scan.
- Paired cancellation: XOR under exact constraints.
