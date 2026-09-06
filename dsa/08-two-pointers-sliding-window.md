# 08. Two Pointers & Sliding Window — Complete Pattern Guide

These patterns are designed to remove unnecessary repeated work from pair and contiguous-range problems. They are among the most common O(n) optimizations in online assessments.

---

# 1. Two Pointers

Two pointers means maintaining two positions and moving them according to a proven rule.

Common forms:

1. Left/right pointers from opposite ends.
2. Slow/fast pointers moving in the same direction.
3. Read/write pointers.
4. Two pointers representing two sequences.

The key question is not “Can I use two pointers?” but:

> **What guarantees that moving this pointer cannot remove a valid better answer?**

---

# 2. Pair Sum in Sorted Array

Given sorted:

```text
[1,2,4,6,8,9]
```

and target `10`.

Start:

```text
l = 1
r = 9
```

If sum is too small, increasing `l` is the only useful direction. If sum is too large, decreasing `r` is the only useful direction.

```cpp
pair<int,int> twoSumSorted(const vector<int>& a, int target) {
    int l = 0, r = (int)a.size() - 1;

    while (l < r) {
        long long sum = (long long)a[l] + a[r];
        if (sum == target) return {l, r};
        if (sum < target) ++l;
        else --r;
    }
    return {-1, -1};
}
```

**TC:** O(n)  
**SC:** O(1)

### Why is each movement safe?
If `a[l]+a[r] < target`, every value `<= a[r]` paired with `a[l]` is also too small, so `l` must move. The symmetric argument holds when the sum is too large.

---

# 3. Remove Duplicates from Sorted Array

Because the array is sorted, equal values are adjacent.

Maintain `write`, the position where the next unique value should be written.

**TC:** O(n)  
**SC:** O(1)

This is a classic read/write pointer problem.

---

# 4. Container With Most Water

For lines at `l` and `r`:

```text
area = min(height[l], height[r]) * (r-l)
```

Start with the widest container.

Suppose `height[l] < height[r]`. Moving `r` inward reduces width while the right side was already taller than the limiting left side. Therefore keeping `l` cannot produce a better area; move `l`.

**TC:** O(n)  
**SC:** O(1)

### Interview proof
The shorter side is the bottleneck. Moving the taller side cannot increase the limiting height, while width decreases. So the taller side can be safely discarded.

---

# 5. 3Sum

Find unique triples whose sum is zero.

### Brute force
O(n³).

### Optimized
Sort, fix one index, then solve two-sum with two pointers.

**TC:** O(n²)  
**SC:** O(1) auxiliary excluding output

### Duplicate handling
Skip duplicate fixed elements. After finding a pair, skip duplicate pointer values.

This is one of the most important examples of:

```text
sorting + two pointers + duplicate control
```

---

# 6. Trapping Rain Water with Two Pointers

Water at index `i` is limited by the shorter of the maximum walls on either side.

Maintain:

```text
leftMax
rightMax
```

If `leftMax <= rightMax`, the left side can be finalized because the right side already has a boundary at least as high as `leftMax`.

Otherwise finalize the right side.

**TC:** O(n)  
**SC:** O(1)

### Important interview explanation
Do not say “move the smaller pointer.” Explain that the smaller known boundary determines the water level that can safely be computed.

---

# 7. What Is a Sliding Window?

A sliding window represents a contiguous section:

```text
[l ... r]
```

As `r` moves right, new data enters. As `l` moves right, old data leaves.

The purpose is to avoid recomputing every subarray from scratch.

There are two major types:

1. Fixed-size window.
2. Variable-size window.

---

# 8. Fixed-Size Window

Problem: maximum sum of exactly `k` consecutive elements.

### Brute force
Compute every window sum from scratch: O(nk).

### Optimization
Maintain one running sum:

```text
newSum = oldSum + entering - leaving
```

```cpp
long long maxWindowSum(const vector<int>& a, int k) {
    if (k <= 0 || k > (int)a.size()) return 0;

    long long window = 0;
    for (int i = 0; i < k; ++i) window += a[i];

    long long best = window;

    for (int i = k; i < (int)a.size(); ++i) {
        window += a[i] - a[i - k];
        best = max(best, window);
    }
    return best;
}
```

**TC:** O(n)  
**SC:** O(1)

---

# 9. Variable Sliding Window

Typical form:

```cpp
int l = 0;
for (int r = 0; r < n; ++r) {
    add(a[r]);

    while (!valid()) {
        remove(a[l]);
        ++l;
    }

    answer = max(answer, r - l + 1);
}
```

The crucial requirement is that once the window becomes invalid, moving `l` forward eventually restores validity without needing to move it backward.

---

# 10. Longest Substring Without Repeating Characters

Maintain a window containing unique characters.

When character `c` repeats inside the current window, move `l` after its previous occurrence.

Using last-seen positions can skip multiple removals.

**TC:** O(n) expected  
**SC:** O(k), where `k` is alphabet size / distinct characters tracked.

### Common bug
Do not move `l` backwards. Use:

```text
l = max(l, last[c] + 1)
```

---

# 11. Longest Substring with At Most K Distinct Characters

Maintain a frequency map and the number of distinct characters.

Expand right. When distinct count exceeds `k`, remove from the left until valid again.

**TC:** O(n) expected  
**SC:** O(k) distinct-state storage

### Why O(n)?
Both pointers only move forward. Each character enters and leaves the window at most once.

---

# 12. Minimum Window Substring

Find the smallest substring containing all required characters with required frequencies.

Maintain:

```text
required frequency
current frequency
number of requirements currently satisfied
```

Expand `r` until valid. Then shrink `l` as much as possible while remaining valid.

**TC:** O(n) expected for hash operations / O(n) for bounded alphabet  
**SC:** O(k)

### Important distinction
For maximum-length problems, you usually shrink only until valid. For minimum-length problems, once valid you try to shrink aggressively and record the best window before invalidating it.

---

# 13. Longest Repeating Character Replacement

Given a string and at most `k` replacements, find the longest substring that can be made all one character.

For a window of length `L`, if the most frequent character occurs `maxFreq` times, replacements required are:

```text
L - maxFreq
```

The window is valid when:

```text
L - maxFreq <= k
```

**TC:** O(n)  
**SC:** O(alphabet)

### Why can maxFreq remain stale?
For this particular maximum-length algorithm, a stale upper bound can make the algorithm temporarily consider a window valid, but it does not cause the final maximum length to become incorrect because the window length only grows under that bound. This is a subtle interview discussion point.

---

# 14. Permutation in String

A permutation of a pattern has exactly the same character-frequency vector.

Use a fixed-size window equal to pattern length and compare frequency state.

**TC:** O(n × alphabet) with direct comparison, or O(n) with a maintained match count  
**SC:** O(alphabet)

---

# 15. Minimum Size Subarray Sum

If all numbers are positive, use a variable sliding window.

Expand until sum >= target. Then shrink from the left while still valid.

**TC:** O(n)  
**SC:** O(1)

### Why positivity matters
With negative numbers, removing an element may increase the sum and adding an element may decrease it. The monotonic property required by this sliding window disappears.

For negative values, consider prefix sums + ordered/hash-based techniques instead.

---

# 16. Fixed Window vs Prefix Sum

Use fixed sliding window when windows have a fixed length and state can be updated incrementally.

Use prefix sum when arbitrary range sums are needed.

Example:

```text
Need every window of length k → sliding window
Need sum(l,r) for arbitrary queries → prefix sum
```

---

# 17. Two Pointers vs Sliding Window

They overlap, but are not identical.

### Two pointers
Usually focuses on relationships between two positions, often from opposite ends.

Examples:

- 2Sum sorted
- 3Sum
- container with most water
- partitioning

### Sliding window
The pointers define a contiguous active range.

Examples:

- longest substring
- minimum window
- fixed-size maximum
- at most K distinct

---

# 18. Important OA / Interview Questions

## Beginner

1. Pair sum in sorted array.
2. Remove duplicates.
3. Move zeroes.
4. Reverse string.
5. Fixed-size maximum sum.
6. Maximum consecutive ones.
7. Merge two sorted arrays.

## Intermediate

8. Two Sum II.
9. 3Sum.
10. Container With Most Water.
11. Longest substring without repeats.
12. At most K distinct characters.
13. Minimum size subarray sum.
14. Permutation in string.
15. Longest repeating replacement.
16. Fruit Into Baskets.
17. Sort colors.

## Advanced

18. Trapping Rain Water.
19. Minimum Window Substring.
20. 4Sum.
21. Subarrays with K distinct integers.
22. Longest subarray with at most K zeros.
23. Sliding window median concept.
24. Count subarrays satisfying a monotonic condition.
25. Two-pointer problems on linked structures.

---

# 19. Frequently Asked Interview Questions

### Q1. When can two pointers reduce O(n²) to O(n)?
When pointer movement is monotonic and every movement permanently eliminates candidates that cannot produce a valid/better answer.

### Q2. Why does two-pointer 2Sum require sorted order?
The direction of pointer movement depends on whether the current sum is too small or too large. Without order, that information is unavailable.

### Q3. Why can a nested while loop still be O(n)?
Because the left pointer never moves backward. Across the whole algorithm it advances at most n times.

### Q4. When is sliding window invalid?
When the window condition cannot be maintained monotonically as the left pointer moves. Negative numbers are a classic reason for sum-based windows to fail.

### Q5. Why does the minimum-window algorithm shrink after becoming valid?
The goal is minimum length, so every valid window should be reduced as much as possible before recording the candidate.

### Q6. Why does maximum-length sliding window usually record after restoring validity?
Once the window is valid, its current length is a candidate. Expanding further may find a larger valid window.

### Q7. Why store frequencies?
For constraints involving duplicates, distinct counts, or required multiplicities, the window must know how many times each value occurs.

### Q8. Why are indices sometimes stored instead of values?
Indices provide expiration information and allow exact window membership checks.

---

# 20. Complexity Summary

| Pattern | TC | SC |
|---|---:|---:|
| Sorted 2Sum | O(n) | O(1) |
| 3Sum | O(n²) | O(1) auxiliary |
| Container | O(n) | O(1) |
| Trapping Rain Water | O(n) | O(1) |
| Fixed window | O(n) | O(1) |
| Unique substring | O(n) expected | O(k) |
| K distinct | O(n) expected | O(k) |
| Minimum window | O(n) expected | O(k) |
| Character replacement | O(n) | O(k) |
| Positive sum window | O(n) | O(1) |
| Sliding maximum | O(n) | O(k) |

> **Core lesson:** Two pointers and sliding windows work because they turn repeated examination into monotonic movement. If you cannot explain why a pointer can safely move forward, you have not yet proved the algorithm.
