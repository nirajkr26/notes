# 13. Greedy Algorithms — Complete Interview Guide

A greedy algorithm makes the best-looking local decision and never revisits it. This sounds simple, but the difficult part is proving that the local decision is globally safe.

The most important interview skill is not memorizing greedy solutions. It is learning to recognize when an **exchange argument, staying-ahead argument, or structural property** proves that greedy is optimal.

---

# 1. Greedy vs Brute Force

Suppose a problem has many choices at every step.

Brute force explores all choices.

Greedy says:

```text
At the current state, choose the option that appears best.
Never reconsider it.
```

Greedy is valid only if the problem has the right structure.

### Warning
“Choosing the largest value now seems good” is not a proof.

---

# 2. How to Prove Greedy

## Exchange argument
Show that any optimal solution can be transformed to include the greedy choice without making it worse.

## Staying ahead
Show that after every step, greedy's partial solution is at least as good as any competing partial solution.

## Cut property
For MST problems, the cheapest edge crossing a suitable cut can safely belong to some MST.

Learning these proof styles is more valuable than memorizing individual tricks.

---

# 3. Activity Selection

Given activities with start/end times, select the maximum number of non-overlapping activities.

### Greedy choice
Choose the activity that finishes earliest.

### Why?
An earlier finishing activity leaves at least as much remaining time as any activity that finishes later. By an exchange argument, an optimal solution can use the earliest-finishing compatible activity without reducing the number of activities that can follow.

### Complexity
Sort by finish time: **O(n log n)**.  
Greedy scan: **O(n)**.  
Auxiliary space: O(1) excluding sorting/output depending on implementation.

This is one of the canonical greedy proofs.

---

# 4. Fractional Knapsack

Each item has value and weight, and fractions are allowed.

Sort by:

```text
value / weight
```

Take the highest ratio first.

### Why does this work?
If a lower-ratio item occupies capacity while a higher-ratio item is available, exchanging capacity toward the higher-ratio item cannot reduce total value.

**TC:** O(n log n)  
**SC:** O(1) auxiliary depending on representation.

### Critical distinction
The same greedy method does **not** solve 0/1 knapsack because an item cannot be split. In 0/1 knapsack, taking a locally better ratio can block a globally better combination.

---

# 5. Assign Cookies

Each child has a greed factor and each cookie has a size.

Sort both. Match the smallest cookie that can satisfy the current least-greedy child.

**TC:** O(n log n + m log m)  
**SC:** O(1) auxiliary.

The greedy idea is to avoid wasting a large cookie on a child that can be satisfied by a smaller one.

---

# 6. Jump Game

At each index, track the farthest position reachable so far.

If the current index exceeds that farthest position, the rest is unreachable.

```cpp
bool canJump(const vector<int>& nums) {
    int farthest = 0;

    for (int i = 0; i < (int)nums.size(); ++i) {
        if (i > farthest) return false;
        farthest = max(farthest, i + nums[i]);
    }
    return true;
}
```

**TC:** O(n)  
**SC:** O(1)

### Invariant
After processing index `i`, `farthest` is the maximum index reachable using positions up to `i`.

---

# 7. Jump Game II

Find minimum jumps to reach the last index.

Treat the reachable positions as layers:

```text
current jump range
        ↓
explore all positions in range
        ↓
choose farthest next range
```

Track:

```text
currentEnd
farthest
jumps
```

When `i == currentEnd`, one jump is required and the next boundary becomes `farthest`.

**TC:** O(n)  
**SC:** O(1)

This is essentially a greedy version of BFS over intervals of reachable indices.

---

# 8. Gas Station

Given gas available and travel cost between stations, determine a valid starting station.

If total gas is less than total cost, no solution exists.

During a scan, maintain `tank`. If it becomes negative at station `i`, none of the stations between the current start and `i` can be a valid start, so restart from `i+1`.

**TC:** O(n)  
**SC:** O(1)

### Why can all intermediate starts be rejected?
The accumulated deficit from the current start through `i` means each intermediate start inherits a suffix of a route that already fails before reaching a feasible completion.

---

# 9. Non-Overlapping Intervals

Remove the minimum number of intervals so the remaining intervals do not overlap.

Equivalent to keeping the maximum number of compatible intervals.

Sort by end time and greedily keep the earliest-finishing compatible interval.

**TC:** O(n log n)  
**SC:** O(1) auxiliary depending on sorting.

This is activity selection in another form.

---

# 10. Minimum Arrows to Burst Balloons

Each balloon is an interval. One arrow can burst every balloon whose interval contains the arrow position.

Sort by ending coordinate. Shoot at the earliest possible end of the first remaining balloon. Every overlapping balloon is also burst.

**TC:** O(n log n)  
**SC:** O(1) auxiliary excluding sort implementation.

### Pattern
Interval problems frequently become greedy after sorting endpoints.

---

# 11. Partition Labels

Partition a string so each character appears in at most one partition.

Record the last occurrence of every character. Scan from left to right while maintaining the farthest last occurrence required by the current partition.

When current index reaches that boundary, the partition is complete.

**TC:** O(n)  
**SC:** O(alphabet)

### Why is cutting safe?
If any character in the current partition appears later, the boundary must extend to its last occurrence. Once every character's last occurrence lies inside the current boundary, no character can cross the cut.

---

# 12. Candy

Each child must receive at least one candy, and a child with a higher rating than an adjacent child must receive more.

A two-pass greedy solution handles both directions:

1. Left-to-right ensures higher-than-left gets more.
2. Right-to-left ensures higher-than-right gets more.

Take the maximum requirement from both passes.

**TC:** O(n)  
**SC:** O(n)

This is an important example where one greedy direction is insufficient and combining two local constraints solves the problem.

---

# 13. Minimum Platforms / Meeting Rooms

Sort arrival and departure times. Use two pointers.

If the next arrival occurs before the earliest departure, another platform is needed. Otherwise a platform becomes free.

**TC:** O(n log n)  
**SC:** O(n) if separate arrays are created.

This is greedy event processing rather than explicit interval objects.

---

# 14. Huffman Coding

Repeatedly combine the two least frequent symbols.

A min-heap makes this efficient.

**TC:** O(n log n)  
**SC:** O(n)

The greedy proof follows the exchange/optimal-prefix-code structure of Huffman coding.

This is an important theoretical greedy algorithm even if it appears less frequently in coding platforms.

---

# 15. MST Greedy Connection

Kruskal and Prim are greedy algorithms.

### Kruskal
Take the cheapest edge that does not create a cycle.

### Prim
Take the cheapest edge crossing from the current tree to an outside vertex.

Their correctness follows from the **cut property** of minimum spanning trees.

This is why graph algorithms and greedy should not be studied as isolated topics.

---

# 16. When Greedy Fails

Consider 0/1 knapsack.

Choosing the item with maximum value/weight ratio may prevent the optimal combination because items cannot be fractionally split.

Use DP when future decisions depend on combinations of previous choices and no greedy exchange proof exists.

### Diagnostic question
Ask:

> Can I prove that an optimal solution can always be transformed to make my greedy choice first?

If not, be suspicious.

---

# 17. Greedy + Sorting Pattern

Many greedy problems follow:

```text
Identify what should be optimized
        ↓
Find the ordering that makes the choice safe
        ↓
Sort
        ↓
Make one irreversible choice at a time
        ↓
Prove exchange/stays-ahead property
```

The sort key is part of the algorithm, not merely an implementation detail.

---

# 18. OA / Interview Problem Bank

## Beginner

1. Assign Cookies.
2. Activity Selection.
3. Fractional Knapsack.
4. Jump Game.
5. Minimum coins for canonical coin systems.
6. Lemonade Change.
7. Buy/Sell scheduling basics.

## Intermediate

8. Jump Game II.
9. Gas Station.
10. Partition Labels.
11. Non-overlapping Intervals.
12. Minimum Arrows.
13. Meeting Rooms.
14. Candy.
15. Queue reconstruction by height.
16. Merge triplets.
17. Hand of Straights.

## Advanced

18. Huffman Coding.
19. Job Sequencing with Deadlines.
20. Minimum number of refueling stops.
21. IPO / maximize capital.
22. Weighted interval scheduling comparison with DP.
23. MST via Kruskal/Prim.
24. Greedy graph problems.
25. Proof-based scheduling problems.

---

# 19. Frequently Asked Interview Questions

### Q1. What is greedy?
An algorithm that repeatedly makes a locally optimal choice without revisiting earlier choices.

### Q2. Why isn't greedy always correct?
A local choice may eliminate combinations required for the global optimum.

### Q3. How do you prove a greedy algorithm?
Use an exchange argument, staying-ahead argument, or structural property such as the MST cut property.

### Q4. Why does activity selection choose earliest finish?
It leaves the maximum possible remaining time for future activities, and any optimal solution can exchange its first activity with the earliest-finishing compatible activity without reducing the number of activities.

### Q5. Why does fractional knapsack use value/weight?
Because capacity can be divided arbitrarily, so every unit of capacity should be assigned to the highest-value-per-unit-weight item.

### Q6. Why does that fail for 0/1 knapsack?
Items cannot be split, so the highest ratio item may block a combination with greater total value.

### Q7. Why does Jump Game need only farthest reach?
Only reachability matters. Among all positions already reachable, a larger reachable boundary dominates a smaller one.

### Q8. How do you recognize interval greedy?
Sort intervals by an endpoint—often finish/end—and ask whether an earliest-finishing choice leaves the most future room.

### Q9. Greedy or DP?
If a local decision can be proven safe, greedy may work. If many alternative choices produce overlapping states and no safe irreversible choice exists, DP is usually more appropriate.

### Q10. Is every two-pointer algorithm greedy?
No. Two pointers is a movement technique; greedy is a correctness strategy based on irreversible local choices.

---

# 20. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Activity selection | O(n log n) | O(1) auxiliary |
| Fractional knapsack | O(n log n) | O(1) auxiliary |
| Assign cookies | O(n log n + m log m) | O(1) auxiliary |
| Jump Game | O(n) | O(1) |
| Jump Game II | O(n) | O(1) |
| Gas Station | O(n) | O(1) |
| Partition Labels | O(n) | O(k) |
| Non-overlapping intervals | O(n log n) | O(1) auxiliary |
| Minimum arrows | O(n log n) | O(1) auxiliary |
| Candy | O(n) | O(n) |
| Huffman | O(n log n) | O(n) |

> **Core lesson:** Greedy is not “choose what looks best.” It is “choose what can be proven safe to commit to.”
