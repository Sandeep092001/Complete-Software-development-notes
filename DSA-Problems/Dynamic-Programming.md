# Recursion and Dynamic Programming Reasoning

## From brute force to DP

Start by defining a state that contains everything needed to make the remaining
decision. Write the recurrence and base case. If the recursion revisits the same
state, memoize it; if dependency order is clear, convert to bottom-up DP.

```text
brute-force choices
       ↓ identify repeated state
memoized recursion
       ↓ order dependencies
tabulation / space optimization
```

## Non-increasing absolute-difference subsequence

For a subsequence whose adjacent absolute differences must be non-increasing, a
state based only on the current index is insufficient. Future validity also
depends on the last selected value/index and the previously allowed difference.

A conceptual state can be:

```text
dp[index][lastDifference]
```

or a map per ending value/difference, depending on value bounds. The important
interview insight is recognizing that “last difference” is part of the state.
Brute force explores take/skip choices; memoization removes repeated subproblems;
constraints determine whether dense arrays or sparse maps are feasible.

## Interview checklist

1. Is the answer over contiguous elements or a subsequence?
2. What information from the past affects future choices?
3. Are subproblems repeated?
4. What are state count and transition cost?
5. Can values/differences be bounded for an array state?
6. Can memory be reduced without losing dependencies?

## Common traps

- Memoizing with an incomplete state produces fast but incorrect code.
- Calling recursion “DP” without overlapping subproblems misses the reason.
- Ignoring constraints can create an impossible O(n²D) solution.
- Greedy choices need proof; local smallest/largest difference may block a
  better future subsequence.

## Quick revision

- State must summarize all future-relevant history.
- Recurrence expresses choices and transitions.
- Memoization removes repeated recursion.
- Complexity = states × transitions per state.
- Optimize only after correctness and constraints are clear.
