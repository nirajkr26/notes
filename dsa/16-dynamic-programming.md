# 16. Dynamic Programming

DP is systematic reuse of overlapping subproblems. The essential skill is defining the **state** and **transition**.

## 16.1 DP recipe

1. Define what `dp[state]` means.
2. Identify choices/transitions.
3. Define base cases.
4. Determine evaluation order.
5. Identify answer state.
6. Optimize memory only after the recurrence is correct.

## Beginner: 1D DP

### Climbing Stairs
`dp[i] = dp[i-1] + dp[i-2]`.

**TC:** O(n)  **SC:** O(n), or O(1) after rolling-state optimization.

### House Robber
At each house: skip it or take it plus the best up to two houses back.

**TC:** O(n)  **SC:** O(1) optimized.

### Min Cost Climbing Stairs
Each step chooses one of the previous reachable states.

**TC:** O(n)  **SC:** O(1) optimized.

## Intermediate: knapsack family

### 0/1 Knapsack
Each item is either taken once or skipped.

`dp[c] = max(dp[c], value + dp[c-weight])`, iterating capacity **backwards** for 1D optimization.

**TC:** O(nW)  **SC:** O(W)

### Unbounded knapsack
Items can be reused; capacity iteration direction changes according to the state semantics.

**TC:** O(nW)  **SC:** O(W)

### Subset Sum
Boolean DP for whether a sum is reachable.

**TC:** O(nS)  **SC:** O(S)

## 16.3 Grid DP

For unique paths, obstacle grids, and minimum path sums, the state is usually the best/count value reaching `(r,c)` from predecessor cells.

Typical **TC:** O(RC), **SC:** O(C) optimized.

## 16.4 String DP

### Longest Common Subsequence
`dp[i][j]` represents the LCS of prefixes. If characters match, extend; otherwise take the better of skipping one character from either side.

**TC:** O(nm)  **SC:** O(nm), reducible to O(min(n,m)) for length only.

### Edit Distance
Transitions correspond to insert, delete, and replace.

**TC:** O(nm)  **SC:** O(nm), reducible for distance only.

## 16.5 Advanced DP patterns

### Longest Increasing Subsequence
Classic DP is O(n²); the tails/binary-search method is O(n log n) for length.

### Interval DP
State is an interval `[l,r]`; split at `k`. Examples: matrix-chain multiplication and burst balloons.

Often **O(n³)** time depending on transitions.

### Tree DP
Each subtree returns states such as best value when a node is selected/not selected.

### Bitmask DP
State contains a subset, typically for `n <= ~20`. Complexity often O(2^n n) or O(2^n n²).

### Digit DP
State tracks position, tightness, and properties such as digit sum/count. Used for counting integers satisfying digit constraints in a range.

## Important questions

1. Climbing Stairs — **O(n), O(1)** optimized.
2. House Robber — **O(n), O(1)**.
3. House Robber II — **O(n), O(1)**.
4. Coin Change — **O(amount × coins), O(amount)**.
5. Coin Change II — **O(amount × coins), O(amount)**.
6. 0/1 Knapsack — **O(nW), O(W)**.
7. Partition Equal Subset Sum — **O(nS), O(S)**.
8. Target Sum — DP over reachable sums, complexity depends on total sum; typical O(nS).
9. Unique Paths — **O(RC), O(C)**.
10. Minimum Path Sum — **O(RC), O(C)**.
11. LCS — **O(nm), O(nm)**.
12. Edit Distance — **O(nm), O(nm)**.
13. LIS — **O(n log n), O(n)**.
14. Longest Palindromic Subsequence — **O(n²), O(n²)** or optimized space.
15. Palindromic substrings — center expansion **O(n²), O(1)**; DP also O(n²).
16. Word Break — **O(n²)** in common formulation with efficient substring/set operations; **O(n)**-ish extra state.
17. Decode Ways — **O(n), O(1)**.
18. Stock DP variants — state by holding/not holding, cooldown, transaction count, or fee.
19. Matrix Chain Multiplication — **O(n³), O(n²)**.
20. Burst Balloons — **O(n³), O(n²)**.
21. Traveling Salesman bitmask DP — **O(n²2^n), O(n2^n)**.

## DP vs recursion vs greedy

- Pure recursion explores repeated states.
- Memoization caches those states.
- Tabulation evaluates states bottom-up.
- Greedy commits to one local choice without retaining alternatives.

If the same state is reached through many paths, DP is a strong candidate.

## Common mistakes

- State does not contain enough information to make future decisions.
- Transition skips a legal choice.
- Wrong iteration order in 0/1 vs unbounded knapsack.
- Confusing subsequence with substring.
- Claiming O(1) space while storing the whole DP table.
- Optimizing memory before proving the recurrence.