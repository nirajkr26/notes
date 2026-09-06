# 08. Two Pointers & Sliding Window

These patterns turn many O(n²) contiguous/pair problems into O(n).

## 8.1 Two pointers

Use two indices whose movement is justified by a monotonic property.

### Pair sum in sorted array
If the sum is too small, move `l`; if too large, move `r`.

**TC:** O(n)  **SC:** O(1)

### Remove duplicates from sorted array
Keep a write pointer for the next unique value.

**TC:** O(n)  **SC:** O(1)

### 3Sum
Sort, fix one index, then use two pointers. Skip duplicates at each level.

**TC:** O(n²)  **SC:** O(1) auxiliary excluding output.

## 8.2 Fixed sliding window

Maintain the state of exactly `k` consecutive elements. Add the new right element and remove the element leaving on the left.

Example: maximum sum of a window of size `k`.

**TC:** O(n)  **SC:** O(1)

## 8.3 Variable sliding window

Use when expanding/shrinking the window preserves a useful condition.

Canonical shape:

```cpp
int l = 0;
for (int r = 0; r < n; ++r) {
    add(a[r]);
    while (!valid()) remove(a[l++]);
    answer = max(answer, r - l + 1);
}
```

The technique requires a condition that can be maintained as the left pointer moves. For sum constraints, the common shrink-while-too-large approach generally requires non-negative numbers.

## Important questions

1. Two Sum II — **O(n), O(1)**.
2. 3Sum — **O(n²), O(1) auxiliary**.
3. Container With Most Water — **O(n), O(1)**; move the shorter side because the taller side cannot improve the current width.
4. Trapping Rain Water — **O(n), O(1)** with two pointers.
5. Longest substring without repeating characters — **O(n) expected, O(k)**.
6. Longest substring with at most K distinct characters — **O(n) expected, O(k)**.
7. Minimum window substring — **O(n) expected with fixed alphabet/hash operations, O(k)**.
8. Longest repeating character replacement — maintain counts and maximum frequency; **O(n), O(k)**.
9. Permutation in string — fixed-size frequency window, **O(n), O(k)**.
10. Minimum size subarray sum — sliding window when values are positive, **O(n), O(1)**.
11. Sliding Window Maximum — monotonic deque, **O(n), O(k)**.

## Recognition checklist

- Pair/triplet in sorted data → two pointers.
- Contiguous range → consider sliding window/prefix sum.
- Exact window size → fixed window.
- “Longest/shortest valid substring/subarray” → variable window is a prime candidate.
- Negative values can invalidate sum-based shrink logic; consider prefix sums/hashing instead.

## Pitfalls

Define exactly what the window contains. Update state when adding/removing. Check whether duplicates matter. For minimum windows, record the answer only after the window becomes valid; for maximum windows, often shrink until valid and then record.