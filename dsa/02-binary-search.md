# 02. Binary Search

Binary search is not merely “search in a sorted array.” The deeper pattern is **eliminating half of the search space using a monotonic property**.

## 2.1 Core invariant

Maintain a search interval containing every possible answer. At each step, choose `mid`, determine which half cannot contain the answer, and discard it.

```cpp
int binarySearch(const vector<int>& a, int target) {
    int l = 0, r = (int)a.size() - 1;
    while (l <= r) {
        int m = l + (r - l) / 2;
        if (a[m] == target) return m;
        if (a[m] < target) l = m + 1;
        else r = m - 1;
    }
    return -1;
}
```

**TC:** O(log n)  **SC:** O(1)

Always compute `mid` as `l + (r-l)/2` to avoid overflow.

## 2.2 Beginner: first and last occurrence

For `lower_bound`, search for the first position where `a[i] >= target`. For `upper_bound`, search for the first position where `a[i] > target`.

A useful mental model is **first true**:

```text
false false false true true true
                  ^ answer
```

**TC:** O(log n)  **SC:** O(1)

C++ also provides `lower_bound` and `upper_bound`, but understand the manual invariant before using them.

## 2.3 Search space variants

### Search in rotated sorted array

At least one of `[l,mid]` or `[mid,r]` is sorted. Determine which half is sorted and test whether the target lies inside it.

**TC:** O(log n) without problematic duplicates; duplicates can force O(n) in the worst case. **SC:** O(1).

### Find minimum in rotated sorted array

Compare `a[mid]` with `a[r]` to decide which side contains the minimum.

**TC:** O(log n)  **SC:** O(1)

## 2.4 Binary search on answer

This is the most important advanced pattern. If the question is “What is the minimum/maximum value for which a condition becomes possible?”, define a predicate `can(x)`.

Example structure:

```cpp
long long binarySearchAnswer(long long lo, long long hi) {
    while (lo < hi) {
        long long mid = lo + (hi - lo) / 2;
        if (can(mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}
```

**TC:** O(log range × cost of `can`)  **SC:** O(1), excluding predicate storage.

## 2.5 Classic answer-search problems

### Q1. Koko Eating Bananas
`can(speed)` checks whether all piles can be eaten within `h` hours. The predicate is monotonic: if speed `x` works, every larger speed works.

**TC:** O(n log M), where `M` is the largest pile. **SC:** O(1).

### Q2. Capacity to Ship Packages Within D Days
Binary-search capacity. `can(capacity)` greedily counts required days.

**TC:** O(n log(sum(weights)))  **SC:** O(1)

### Q3. Allocate Books / Split Array Largest Sum
Binary-search the maximum allowed segment sum and greedily determine whether the array can be partitioned within the required number of groups.

**TC:** O(n log(sum))  **SC:** O(1)

### Q4. Aggressive Cows / Maximize Minimum Distance
Binary-search the answer distance. Greedily place cows as far apart as possible to test feasibility.

**TC:** O(n log range) after sorting; **SC:** O(1) auxiliary.

## 2.6 Important interview questions

1. Binary search in sorted array — **O(log n), O(1)**.
2. First/last occurrence — **O(log n), O(1)**.
3. Count occurrences — two bounds, **O(log n), O(1)**.
4. Search insert position — **O(log n), O(1)**.
5. Find peak element — **O(log n), O(1)**.
6. Search rotated sorted array — **O(log n)** normally, **O(n)** worst-case with duplicates, **O(1)** space.
7. Find minimum in rotated array — **O(log n), O(1)** when distinct.
8. Single element in sorted array — **O(log n), O(1)** using index parity.
9. Median of two sorted arrays — **O(log(min(n,m)))**, **O(1)** auxiliary.
10. Kth element of two sorted arrays — **O(log(min(n,m)))**, **O(1)** auxiliary.
11. Koko Eating Bananas — **O(n log M), O(1)**.
12. Ship packages within D days — **O(n log S), O(1)**.
13. Split array largest sum — **O(n log S), O(1)**.
14. Aggressive cows — **O(n log range)** after sorting, **O(1)** auxiliary.

## 2.7 How to recognize binary search

Look for:

- Sorted input.
- “First/last position.”
- “Minimum possible maximum.”
- “Maximum possible minimum.”
- A yes/no feasibility question that becomes permanently true or false after some threshold.
- A numeric answer with a manageable search range.

## 2.8 Common mistakes

- Infinite loops from incorrect boundary updates.
- Mixing `[l,r]` and `[l,r)` conventions.
- Overflow in `mid` or answer arithmetic.
- Forgetting duplicates change rotated-array complexity.
- Writing `can(mid)` without proving monotonicity.
- Using binary search on a predicate that is not monotonic.

## 2.9 Master checklist

```text
1. What exactly is the search space?
2. What does the invariant say?
3. What is the predicate?
4. Is the predicate monotonic?
5. Is this first-true or last-true?
6. What happens at lo/hi boundaries?
7. Can arithmetic overflow?
8. What is TC = log(search space) × predicate cost?
```