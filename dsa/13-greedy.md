# 13. Greedy Algorithms

A greedy algorithm repeatedly makes the best local choice. The hard part is proving that the choice can be made safely.

## How to prove greedy

Common proof styles:

- **Exchange argument:** transform an optimal solution to use the greedy choice without worsening it.
- **Stays-ahead:** show greedy's partial solution is never worse than another solution's partial solution.
- **Cut property:** useful for MST algorithms.

## Beginner

### Activity selection
Sort activities by finishing time and always take the earliest finishing compatible activity.

**TC:** O(n log n) due to sorting; **SC:** O(1) auxiliary depending on sorting/output.

### Assign cookies
Sort both arrays and greedily match the smallest sufficient cookie.

**TC:** O(n log n + m log m), **SC:** O(1) auxiliary.

## Intermediate

### Fractional knapsack
Sort by value/weight and take the highest ratio first. This works because fractions are allowed.

**TC:** O(n log n)  **SC:** O(1) auxiliary.

### Jump Game
Track the farthest reachable index. If current index exceeds it, failure is unavoidable.

**TC:** O(n)  **SC:** O(1)

### Gas Station
If the running fuel becomes negative after starting at `start`, none of the positions before the failure can be a valid start; move start past the failure.

**TC:** O(n)  **SC:** O(1)

## Important questions

1. Activity selection — **O(n log n), O(1)** auxiliary.
2. Fractional knapsack — **O(n log n), O(1)** auxiliary.
3. Jump Game — **O(n), O(1)**.
4. Jump Game II — greedy farthest boundary, **O(n), O(1)**.
5. Gas Station — **O(n), O(1)**.
6. Partition Labels — last occurrence + greedy boundary, **O(n), O(k)**.
7. Merge triplets — greedy filtering, **O(n), O(1)** auxiliary.
8. Non-overlapping intervals — sort by end and keep maximum compatible set, **O(n log n), O(1)** auxiliary.
9. Minimum arrows to burst balloons — interval-end greedy, **O(n log n), O(1)** auxiliary.
10. Candy — two directional greedy passes, **O(n), O(n)**.

## Greedy vs DP

If a local choice can destroy future possibilities and no exchange/cut proof exists, greedy is suspicious. When decisions interact across many future states, DP is often the correct model.

## Pitfall

Never justify greedy with “it looks optimal.” State the invariant and give a proof.