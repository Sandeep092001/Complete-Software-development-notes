# Sliding Window Patterns

## When sliding window works

A window technique is useful for contiguous ranges when state can be updated as
the right boundary expands and the left boundary shrinks. A fixed window has a
known length; a variable window maintains an invariant.

## Longest substring without repeating characters

Maintain a left boundary and each character's latest index. On a repeated
character inside the active window, move `left` to one past its previous index.
Never move `left` backward.

```java
Map<Character, Integer> last = new HashMap<>();
int left = 0, best = 0;
for (int right = 0; right < s.length(); right++) {
    char c = s.charAt(right);
    if (last.containsKey(c)) left = Math.max(left, last.get(c) + 1);
    last.put(c, right);
    best = Math.max(best, right - left + 1);
}
```

Time O(n), space O(character set).

## At most K distinct

Store character/value counts. Expand right; while distinct count exceeds K,
decrement the left value and remove it when its count reaches zero. Record the
largest valid length.

Exactly K distinct is often:

```text
count(at most K) - count(at most K - 1)
```

## Minimum window substring

Track required counts and how many requirements are satisfied. Expand until the
window is valid, then shrink while valid to minimize it. Distinguish number of
distinct required characters from total character count.

## Positive-sum window limitation

For positive numbers, if a running sum is too large, moving left can only reduce
it, enabling a monotonic window strategy. Negative numbers break that property;
prefix sums plus a map or another technique may be required.

## Interview traps

- Sliding window is not valid merely because the problem mentions a subarray.
- Removing a key only when its count becomes zero is essential for distinct
  counts.
- Move pointers in a way that proves each index is processed O(1) times.
- Clarify substring/subarray (contiguous) versus subsequence (not necessarily).

## Quick revision

- Define the invariant before writing pointer logic.
- Expand right, repair by moving left.
- Counts support duplicate-aware windows.
- Positive monotonicity enables sum windows; negatives may invalidate them.
