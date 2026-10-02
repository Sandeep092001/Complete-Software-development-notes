# Prefix Sum Patterns

## Core idea

Let prefix `P[i]` be the sum before index `i`. Then the sum of `[left, right]` is
`P[right + 1] - P[left]`. Precomputation gives O(1) range-sum queries.

## Subarray Sum Equals K

At the current prefix sum `sum`, a previous prefix `sum - k` creates a subarray
whose sum is `k`. Store frequency, not just existence, because several previous
prefixes can contribute.

```java
Map<Long, Integer> frequency = new HashMap<>();
frequency.put(0L, 1);
long sum = 0;
int answer = 0;

for (int value : nums) {
    sum += value;
    answer += frequency.getOrDefault(sum - k, 0);
    frequency.merge(sum, 1, Integer::sum);
}
```

Initialize prefix zero with frequency one so a valid subarray beginning at index
zero is counted. Use `long` if cumulative sums may overflow `int`.

## Why this handles negatives

Unlike a sum-based sliding window, prefix-difference lookup does not require the
sum to change monotonically as pointers move. It works with negative, zero, and
positive values.

## Interview traps

- Storing only the latest prefix index is wrong when counting all subarrays.
- Update the answer before adding the current prefix when zero-length ranges are
  not allowed.
- Prefix sum solves contiguous ranges, not arbitrary subsequences.

## Quick revision

- Range sum = difference of prefixes.
- Need previous prefix `current - k`.
- Store frequencies for counting.
- Seed `{0: 1}`.
- Prefer `long` for large cumulative values.
