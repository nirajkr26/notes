# 16. Dynamic Programming — From Recursion to Advanced DP

Dynamic Programming (DP) is one of the most important and most misunderstood DSA topics.

The central idea is simple:

> **Solve a problem by defining states, reusing overlapping subproblems, and storing results so the same state is not solved repeatedly.**

Do not begin DP by memorizing formulas. Begin by learning how to define a state.

---

# 1. When Should You Think About DP?

Typical signals:

- choose/take/skip decisions
- maximize/minimize something
- count number of ways
- reachability
- repeated subproblems
- “best answer up to position i”
- current decision depends on previous decisions

Two common properties:

### Overlapping subproblems
The same smaller state appears repeatedly.

### Optimal substructure
An optimal answer can be constructed from optimal answers to smaller states.

---

# 2. DP Recipe

For every DP problem, explicitly answer:

```text
1. What does dp[state] mean?
2. What choices are available?
3. What transition connects states?
4. What are the base cases?
5. In what order can states be computed?
6. Where is the final answer?
7. Can memory be optimized afterward?
```

If you cannot define `dp[i]` in one precise sentence, you probably are not ready to code.

---

# 3. Recursion → Memoization → Tabulation

Naive recursion:

```text
solve(state)
  ├── solve(smaller state)
  └── solve(smaller state)
```

If the same state repeats, memoize:

```text
if already computed → return cached answer
```

Tabulation reverses the direction:

```text
base states → larger states → answer
```

Both usually have the same state/transition complexity.

---

# 4. Climbing Stairs

At stair `n`, the final move is either:

- from `n-1`
- from `n-2`

Therefore:

```text
dp[n] = dp[n-1] + dp[n-2]
```

Base:

```text
dp[0] = 1
 dp[1] = 1
```

Space can be optimized because only the previous two states are needed.

```cpp
int climbStairs(int n) {
    int prev2 = 1;
    int prev1 = 1;

    for (int i = 2; i <= n; ++i) {
        int cur = prev1 + prev2;
        prev2 = prev1;
        prev1 = cur;
    }
    return prev1;
}
```

**TC:** O(n)  
**SC:** O(1)

---

# 5. House Robber

At house `i`, either:

```text
take current + best through i-2
skip current + best through i-1
```

Transition:

```text
dp[i] = max(dp[i-1], dp[i-2] + nums[i])
```

Only two previous values are required.

**TC:** O(n)  
**SC:** O(1)

### Key lesson
“Take or skip” is one of the most common 1D DP patterns.

---

# 6. House Robber II

Houses form a circle, so first and last cannot both be chosen.

Split into two linear cases:

```text
rob houses [0 ... n-2]
OR
rob houses [1 ... n-1]
```

Take the maximum.

**TC:** O(n)  
**SC:** O(1)

### Important technique
Circular DP often becomes multiple linear DP cases.

---

# 7. Coin Change

Find the minimum number of coins needed to make amount `A`.

Define:

```text
dp[x] = minimum coins required to make amount x
```

For every coin `c`:

```text
dp[x] = min(dp[x], 1 + dp[x-c])
```

Initialize impossible states to a large value.

**TC:** O(A × number_of_coins)  
**SC:** O(A)

### Why is this DP?
Many amounts can be reached through the same smaller amount states.

---

# 8. Coin Change II

Count the number of combinations that make an amount.

The ordering of loops matters because the problem asks for combinations, not permutations.

A common 1D formulation processes each coin first and then capacities increasing, ensuring a coin can be reused while avoiding counting different orderings as different combinations.

**TC:** O(A × C)  
**SC:** O(A)

### Interview trap
Swapping the loop order can change combinations into permutations.

---

# 9. 0/1 Knapsack

Each item can be used at most once.

State:

```text
dp[c] = maximum value achievable with capacity c
```

For each item `(weight, value)`:

```text
dp[c] = max(dp[c], value + dp[c-weight])
```

Iterate capacity **backward**:

```text
c = C ... weight
```

Why backward?
It prevents the same item from being reused in the same iteration.

**TC:** O(nC)  
**SC:** O(C)

---

# 10. Unbounded Knapsack

Items may be used repeatedly.

The transition is similar, but capacity is generally iterated forward in a 1D formulation because the current item may legitimately contribute multiple times.

**TC:** O(nC)  
**SC:** O(C)

### Core interview distinction

```text
0/1 knapsack     → backward capacity
unbounded        → forward capacity
```

Always derive this from state semantics rather than memorizing blindly.

---

# 11. Subset Sum

Determine whether some subset reaches sum `S`.

```text
dp[s] = whether sum s is reachable
```

For each value `x`, update sums backward to prevent reusing the same item.

**TC:** O(nS)  
**SC:** O(S)

---

# 12. Partition Equal Subset Sum

If total sum is odd, impossible.

Otherwise target is:

```text
total / 2
```

Now solve subset sum for that target.

**TC:** O(nS)  
**SC:** O(S)

### Important lesson
Many seemingly different problems reduce to a known DP family after one mathematical transformation.

---

# 13. Unique Paths

For a grid without obstacles:

```text
dp[r][c] = paths from top + paths from left
```

Boundary cells have one possible direction.

**TC:** O(RC)  
**SC:** O(C) optimized

---

# 14. Minimum Path Sum

Replace counting with optimization:

```text
dp[r][c] = grid[r][c] + min(top, left)
```

**TC:** O(RC)  
**SC:** O(C) optimized

### General grid DP
Ask:

> From which previous cells can I reach this state?

Then choose sum/min/max depending on the objective.

---

# 15. Longest Common Subsequence

Given strings A and B:

```text
dp[i][j] = LCS length of prefixes A[0..i-1], B[0..j-1]
```

If characters match:

```text
dp[i][j] = 1 + dp[i-1][j-1]
```

Otherwise:

```text
dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

**TC:** O(nm)  
**SC:** O(nm), or O(min(n,m)) when only length is required.

### Subsequence vs substring
LCS is about subsequences, so characters may be skipped. A substring must be contiguous.

---

# 16. Edit Distance

Allowed operations:

- insert
- delete
- replace

State:

```text
dp[i][j] = minimum operations to transform first i chars into first j chars
```

If characters match, no new operation is required. Otherwise take:

```text
1 + min(insert, delete, replace)
```

**TC:** O(nm)  
**SC:** O(nm), reducible to O(min(n,m)) for distance only.

---

# 17. Longest Increasing Subsequence

### O(n²) DP

```text
dp[i] = LIS length ending at i
```

For each earlier `j < i` with `a[j] < a[i]`:

```text
dp[i] = max(dp[i], dp[j] + 1)
```

**TC:** O(n²)  
**SC:** O(n)

### O(n log n) optimization
Maintain `tails`, where `tails[len-1]` is the smallest possible ending value for an increasing subsequence of that length.

Binary search the first tail `>= x` and replace it.

Important: `tails` itself is not necessarily an actual LIS; its length is the answer.

**TC:** O(n log n)  
**SC:** O(n)

---

# 18. Decode Ways

A digit string can be decoded using one-digit and valid two-digit codes.

State:

```text
dp[i] = number of ways to decode first i characters
```

Transition from one digit if valid and from two digits if valid.

**TC:** O(n)  
**SC:** O(1) optimized

Zero handling is the main edge case.

---

# 19. Stock DP

Stock problems become easier when you explicitly define state by whether you currently hold a stock.

For example:

```text
hold[i]    = best profit after day i while holding
cash[i]    = best profit after day i while not holding
```

Then add dimensions for:

- transaction limit
- cooldown
- transaction fee

This is a reusable **state-machine DP** pattern.

---

# 20. Interval DP

State represents an interval:

```text
dp[l][r]
```

Try every split `k` between `l` and `r`.

Examples:

- Matrix Chain Multiplication.
- Burst Balloons.
- Optimal BST variants.

Typical complexity is O(n³) because there are O(n²) intervals and O(n) split points.

---

# 21. Matrix Chain Multiplication

Given matrices, determine the minimum scalar multiplications needed to multiply them.

Choose the final split `k`:

```text
dp[l][r] = min over k {
    dp[l][k] + dp[k+1][r] + multiplication cost
}
```

**TC:** O(n³)  
**SC:** O(n²)

The important skill is recognizing that the final operation partitions the interval into two independently solved intervals.

---

# 22. Burst Balloons

The trick is to choose the **last** balloon burst in an interval rather than the first. Once the last balloon is chosen, its two sides are independent.

This reversal of viewpoint is a major interval-DP technique.

**TC:** O(n³)  
**SC:** O(n²)

---

# 23. Tree DP

A tree naturally provides independent subtrees.

For each node, return multiple states.

Example: maximum sum of non-adjacent tree nodes:

```text
state[0] = best if current node is skipped
state[1] = best if current node is taken
```

Then combine children appropriately.

**TC:** O(n)  
**SC:** O(h)

---

# 24. Bitmask DP

When `n` is small, represent a subset of items by an integer mask.

For `n` items there are `2^n` masks.

A state might be:

```text
dp[mask][last]
```

meaning the best result after visiting exactly the items in `mask` and ending at `last`.

Common in TSP and subset-assignment problems.

**Typical TC:** O(n² 2^n)  
**SC:** O(n 2^n)

---

# 25. Digit DP

Digit DP counts numbers satisfying digit properties over a range.

Typical state includes:

```text
position
 tight
 started
 additional property
```

For example, count numbers <= N whose digit sum equals S.

This is advanced and usually appears in competitive programming rather than ordinary interviews, but it is important for mastery.

---

# 26. DP Optimization Techniques

After the recurrence is correct, optimize:

### Rolling arrays
If row `i` depends only on row `i-1`, keep two rows.

### Two variables
If a 1D recurrence uses only two previous states, keep two variables.

### State compression
Represent equivalent states compactly.

### Monotonic queue optimization
Some transitions involve sliding-window maximum/minimum and can be optimized with a deque.

### Divide-and-conquer optimization / Knuth optimization
Advanced techniques for specific DP recurrences with additional mathematical properties.

---

# 27. DP vs Greedy

### Greedy
Makes one choice and discards alternatives permanently.

### DP
Keeps enough state to compare alternative choices.

Example:

```text
0/1 knapsack → DP
fractional knapsack → greedy
```

The difference comes from whether the local ratio choice can be proven globally safe.

---

# 28. DP vs Backtracking

Backtracking enumerates choices, often producing many outputs.

DP is useful when different choice paths converge to the same state.

Example:

```text
fib(5)
 ├── fib(4)
 └── fib(3)
```

The state `fib(3)` appears repeatedly. Cache it.

---

# 29. OA / Interview Problem Bank

## Beginner

1. Climbing Stairs.
2. Fibonacci with memoization.
3. House Robber.
4. Min Cost Climbing Stairs.
5. Unique Paths.
6. Minimum Path Sum.
7. Decode Ways.

## Intermediate

8. House Robber II.
9. Coin Change.
10. Coin Change II.
11. 0/1 Knapsack.
12. Unbounded Knapsack.
13. Subset Sum.
14. Partition Equal Subset Sum.
15. Target Sum.
16. LCS.
17. Longest Palindromic Subsequence.
18. Edit Distance.
19. LIS.
20. Word Break.
21. Stock with cooldown/fee.

## Advanced

22. Matrix Chain Multiplication.
23. Burst Balloons.
24. Palindrome Partitioning II.
25. Distinct Subsequences.
26. Regular Expression Matching.
27. Wildcard Matching.
28. Tree DP.
29. Bitmask DP.
30. TSP.
31. Digit DP.
32. DP with monotonic queue.

---

# 30. Frequently Asked Interview Questions

### Q1. What makes a problem DP?
Overlapping subproblems plus a reusable state that captures all information needed for future decisions.

### Q2. Memoization vs tabulation?
Memoization is top-down recursion with caching. Tabulation is bottom-up computation of states.

### Q3. How do you define a DP state?
Describe exactly what information the state represents and make sure that information is sufficient to determine all future transitions.

### Q4. Why is state definition more important than code?
Once the state and transition are correct, implementation is usually mechanical. A wrong state cannot be fixed by clever code.

### Q5. Why optimize memory only afterward?
Memory optimization often hides the original recurrence and makes debugging harder. First prove the full DP.

### Q6. Why does 0/1 knapsack iterate capacity backward?
Backward iteration ensures the current item cannot use a state updated by the same item earlier in the iteration.

### Q7. Why does unbounded knapsack often iterate forward?
Forward iteration allows the current item to contribute repeatedly.

### Q8. How do you know whether DP can be reduced to O(1) space?
Inspect which previous states each transition actually uses. If only a constant number of previous states are required, retain only those.

### Q9. What is interval DP?
DP where each state represents a contiguous interval and transitions choose a split or final operation inside that interval.

### Q10. What is bitmask DP?
DP where an integer bitmask represents a subset of a small universe.

---

# 31. DP Debugging Checklist

```text
1. Define state in one sentence.
2. List every possible decision.
3. Make sure every decision appears in transition.
4. Define smallest/base states.
5. Check impossible states.
6. Verify iteration order.
7. Test n=0/1.
8. Test all-negative / zero cases where relevant.
9. Compare against brute force for tiny inputs.
10. Only then optimize memory.
```

---

# 32. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Climbing Stairs | O(n) | O(1) |
| House Robber | O(n) | O(1) |
| Coin Change | O(A·C) | O(A) |
| 0/1 Knapsack | O(nC) | O(C) |
| Subset Sum | O(nS) | O(S) |
| Grid DP | O(RC) | O(C) |
| LCS | O(nm) | O(nm) / O(min) |
| Edit Distance | O(nm) | O(nm) / O(min) |
| LIS | O(n log n) | O(n) |
| Interval DP | O(n³) typical | O(n²) |
| Bitmask DP | O(n²2^n) typical | O(n2^n) |

> **Core lesson:** DP is the discipline of replacing repeated decision-making with a precise state. If you can explain the state, transition, base case, and why no information is missing, you have solved the hardest part of the problem.
