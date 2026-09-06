# 02. Binary Search — From Basics to Answer Search

Binary search is one of the most important patterns in online assessments. It is not limited to searching a sorted array. The broader idea is:

> **If the search space has an order and a decision lets us permanently discard one side, binary search may apply.**

There are two major families:

1. **Search for an existing value/position** in an ordered structure.
2. **Search for the minimum or maximum feasible answer** when feasibility is monotonic.

---

# 1. Why Binary Search Is O(log n)

Suppose there are `n` candidates.

After one iteration, at most `n/2` remain.

After two iterations:

```text
n / 2 / 2 = n / 4
```

After `k` iterations:

```text
n / 2^k
```

We stop when only one candidate remains:

```text
n / 2^k <= 1
2^k >= n
k >= log2(n)
```

Therefore the running time is O(log n).

---

# 2. First Decision: What Is the Search Space?

Before coding, explicitly write:

```text
Search space = [L, R]
```

Then define an invariant such as:

> The answer, if it exists, is always inside `[L, R]`.

Every pointer movement must preserve this invariant.

This is the best way to avoid binary-search bugs.

---

# 3. Standard Binary Search

### Problem
Given a sorted array, return any index containing `target`, or `-1`.

### C++

```cpp
int binarySearch(const vector<int>& a, int target) {
    int l = 0;
    int r = (int)a.size() - 1;

    while (l <= r) {
        int mid = l + (r - l) / 2;

        if (a[mid] == target) return mid;
        if (a[mid] < target) l = mid + 1;
        else r = mid - 1;
    }

    return -1;
}
```

**TC:** O(log n)  
**SC:** O(1)

### Why `l + (r-l)/2`?
The mathematically equivalent `(l+r)/2` can overflow when `l` and `r` are large integers.

### `[l,r]` convention
This implementation uses a closed interval. Therefore:

- continue while `l <= r`
- discard left side with `l = mid + 1`
- discard right side with `r = mid - 1`

Do not mix this with a half-open `[l,r)` implementation unless you deliberately change all boundaries.

---

# 4. Lower Bound and Upper Bound

These are more important in interviews than ordinary binary search.

## Lower bound

Find the first position `i` such that:

```text
a[i] >= target
```

Think of the predicate as:

```text
false false false true true true
                  ^ first true
```

### C++

```cpp
int lowerBound(const vector<int>& a, int target) {
    int l = 0, r = (int)a.size();

    while (l < r) {
        int mid = l + (r - l) / 2;
        if (a[mid] < target) l = mid + 1;
        else r = mid;
    }
    return l;
}
```

**TC:** O(log n)  
**SC:** O(1)

Notice that `r = n`, not `n-1`. This is the half-open interval `[l,r)`.

## Upper bound

Find the first position `i` such that:

```text
a[i] > target
```

Replace the condition with `a[mid] <= target`.

### Useful consequences

For sorted array `a`:

```text
first occurrence of x = lower_bound(x)
last occurrence of x  = upper_bound(x) - 1
count of x            = upper_bound(x) - lower_bound(x)
```

These three formulas solve many OA questions instantly.

---

# 5. Search Insert Position

Given a sorted array, find the index where `target` should be inserted to keep the array sorted.

This is exactly `lower_bound(target)`.

**TC:** O(log n)  
**SC:** O(1)

### Interview lesson
When an apparently different problem reduces to a known boundary, do not write a separate algorithm. Recognize the underlying pattern.

---

# 6. First and Last Position of a Target

Run lower-bound-style search twice:

- first index `>= target`
- first index `> target`

Check whether the first position actually contains `target`.

**TC:** O(log n)  
**SC:** O(1)

### Important edge cases

- target absent
- target at index `0`
- target at index `n-1`
- all elements equal
- array of length `1`

---

# 7. Search in Rotated Sorted Array

Example:

```text
[4,5,6,7,0,1,2]
```

The array was sorted and then rotated around a pivot.

At any midpoint, **at least one half is sorted**.

### Algorithm

1. Compute `mid`.
2. If `a[mid] == target`, return.
3. If left half is sorted, check whether target lies within it.
4. Otherwise the right half is sorted; check that side.
5. Discard the impossible half.

### C++

```cpp
int searchRotated(const vector<int>& a, int target) {
    int l = 0, r = (int)a.size() - 1;

    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (a[mid] == target) return mid;

        if (a[l] <= a[mid]) {
            if (a[l] <= target && target < a[mid]) r = mid - 1;
            else l = mid + 1;
        } else {
            if (a[mid] < target && target <= a[r]) l = mid + 1;
            else r = mid - 1;
        }
    }
    return -1;
}
```

**TC:** O(log n) when values are distinct  
**SC:** O(1)

### What changes with duplicates?

For `[1,0,1,1,1]`, it can be impossible to determine which side is sorted when boundary values equal midpoint. In that situation, shrink both boundaries:

```text
l++, r--
```

Worst-case complexity can degrade to O(n).

---

# 8. Find Minimum in Rotated Sorted Array

For distinct elements:

```text
[4,5,6,7,0,1,2]
```

Compare `a[mid]` with `a[r]`.

- If `a[mid] > a[r]`, the minimum is strictly to the right of `mid`.
- Otherwise the minimum is at `mid` or to its left.

### C++

```cpp
int findMin(const vector<int>& a) {
    int l = 0, r = (int)a.size() - 1;

    while (l < r) {
        int mid = l + (r - l) / 2;
        if (a[mid] > a[r]) l = mid + 1;
        else r = mid;
    }
    return a[l];
}
```

**TC:** O(log n) with distinct values  
**SC:** O(1)

---

# 9. Find Peak Element

A peak satisfies:

```text
a[i] > a[i-1] and a[i] > a[i+1]
```

The trick is not to compare against every element. Look at the slope between `mid` and `mid+1`:

- If `a[mid] < a[mid+1]`, a peak must exist to the right.
- Otherwise a peak exists at `mid` or to the left.

This gives logarithmic search.

**TC:** O(log n)  
**SC:** O(1)

---

# 10. Single Element in a Sorted Array

Every element appears twice except one.

Example:

```text
[1,1,2,3,3,4,4]
```

Before the single element, pairs begin at even indices. After it, the pairing pattern shifts.

Force `mid` to be even. Compare `a[mid]` and `a[mid+1]`.

**TC:** O(log n)  
**SC:** O(1)

### Pattern
This is an excellent example of binary search on **index parity**, not value comparison.

---

# 11. Binary Search on Answer

This is the most important advanced idea.

Suppose the problem asks:

> What is the minimum capacity that can ship all packages within `D` days?

You could test every capacity. That is too slow.

Instead ask:

```text
Can capacity X finish the job?
```

The answer is monotonic:

```text
capacity too small → false
capacity works      → true
larger capacities   → true
```

So we search the **first true**.

---

# 12. Generic First-True Template

```cpp
long long firstTrue(long long lo, long long hi) {
    while (lo < hi) {
        long long mid = lo + (hi - lo) / 2;

        if (can(mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}
```

The entire problem becomes:

1. Find valid lower bound `lo`.
2. Find valid upper bound `hi`.
3. Write a correct monotonic `can(x)`.
4. Binary search the boundary.

**TC:** O(log range × cost(`can`))  
**SC:** O(1) auxiliary in the generic pattern.

---

# 13. Koko Eating Bananas

Koko eats at speed `k` bananas/hour.

For pile `p`, hours required are:

```text
ceil(p / k)
```

In integer arithmetic:

```text
(p + k - 1) / k
```

Feasibility is:

```text
sum(hours) <= h
```

### C++

```cpp
int minEatingSpeed(const vector<int>& piles, int h) {
    int lo = 1;
    int hi = *max_element(piles.begin(), piles.end());

    auto can = [&](int speed) {
        long long hours = 0;
        for (int p : piles) {
            hours += (p + speed - 1LL) / speed;
            if (hours > h) return false;
        }
        return true;
    };

    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (can(mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}
```

**TC:** O(n log M), where `M = max(piles)`  
**SC:** O(1)

### How to derive the search bounds

- Minimum speed = `1`.
- Maximum speed = largest pile, because anything faster is unnecessary.

Never blindly use `1e9` as the search space when the input gives a much tighter natural bound.

---

# 14. Capacity to Ship Packages Within D Days

The capacity must be at least the heaviest package and at most the total weight.

For a candidate capacity:

- keep filling the current day
- when the next package does not fit, start a new day
- if required days exceed `D`, candidate is too small

**TC:** O(n log S), where `S = sum(weights)`  
**SC:** O(1)

### Key observation
The feasibility function is monotonic because increasing capacity can never increase the required number of days.

---

# 15. Split Array Largest Sum / Allocate Books

Partition the array into at most `k` contiguous groups while minimizing the largest group sum.

Search the answer `X`:

> Can we partition the array so that every group has sum at most `X`?

Greedily extend the current group until the next element would exceed `X`, then start a new group.

Why greedy works for the feasibility check: once adding the next element exceeds the allowed sum, that group cannot contain it under this candidate limit.

**TC:** O(n log S)  
**SC:** O(1)

---

# 16. Aggressive Cows / Maximize Minimum Distance

Given sorted stall positions, place cows so that the minimum pairwise distance is as large as possible.

Binary-search a candidate distance `d`.

Feasibility:

- place the first cow at the first stall
- greedily place the next cow at the first stall at least `d` away
- count how many can be placed

If at least `k` cows fit, distance `d` is possible.

**TC:** O(n log range) after sorting  
**SC:** O(1) auxiliary

### Important greedy + binary-search combination
The binary search chooses the answer; greedy only answers the yes/no feasibility question.

---

# 17. Median of Two Sorted Arrays

One of the hardest binary-search interview questions.

The key idea is partitioning the two arrays such that the left side contains exactly half the elements and every left element is `<=` every right element.

Binary-search the cut in the smaller array.

For partitions `i` in A and `j` in B:

```text
j = (n + m + 1) / 2 - i
```

Valid partition conditions:

```text
A[i-1] <= B[j]
B[j-1] <= A[i]
```

Once both hold, the median comes from boundary values.

**TC:** O(log(min(n,m)))  
**SC:** O(1)

### Interview expectation
You should be able to explain why binary-searching the smaller array is sufficient and how the partition conditions guarantee global ordering.

---

# 18. Kth Element of Two Sorted Arrays

This is the same partition concept without restricting yourself to the median.

Choose `x` elements from the first array and `k-x` from the second. Binary-search `x` until the partition is valid.

**TC:** O(log(min(n,m)))  
**SC:** O(1)

---

# 19. When Binary Search Does NOT Apply

Do not use binary search simply because there is a numeric answer.

You need an exploitable order/monotonicity.

Example of a bad predicate:

```text
true false true false true
```

There is no single boundary to locate, so ordinary binary search is invalid.

### Ask these questions

1. What is my search space?
2. Can I totally discard one side?
3. Is the predicate monotonic?
4. What invariant do my bounds represent?
5. Are my bounds inclusive or exclusive?

---

# 20. Important Interview Questions

## Beginner

1. Standard binary search.
2. Search insert position.
3. First occurrence.
4. Last occurrence.
5. Count occurrences in sorted array.
6. Lower bound / upper bound.
7. Find floor/ceil in a sorted array.

## Intermediate

8. Search rotated sorted array.
9. Find minimum in rotated sorted array.
10. Find peak element.
11. Single element in sorted array.
12. Search in nearly sorted array.
13. Find square root of an integer.
14. Nth root of an integer.
15. Find a local peak/valley under a monotonic condition.

## Advanced

16. Koko Eating Bananas.
17. Ship packages within D days.
18. Split array largest sum.
19. Allocate minimum pages.
20. Aggressive cows.
21. Painter's partition.
22. Median of two sorted arrays.
23. Kth element of two sorted arrays.
24. Minimize maximum distance / load / time problems.

---

# 21. Integer Square Root

Find the largest integer `x` such that `x*x <= n`.

Binary-search `x` from `0` to `n`.

Use division instead of `mid * mid` if overflow is possible:

```text
mid <= n / mid
```

**TC:** O(log n)  
**SC:** O(1)

This is a classic beginner example of **binary search on answer**.

---

# 22. Common Binary Search Bugs

### Bug 1: Wrong midpoint
Use:

```cpp
mid = l + (r - l) / 2;
```

### Bug 2: Infinite loop
If using a closed interval, make sure the search range shrinks using `mid ± 1`.

### Bug 3: Wrong first-true update
For first true:

```cpp
if (can(mid)) hi = mid;
else lo = mid + 1;
```

For last true, the midpoint and update rules are different.

### Bug 4: Forgetting absence
A lower bound may equal `n`; check before indexing.

### Bug 5: No monotonicity proof
A `can(mid)` function must have a reason why all values on one side have the same truth value.

### Bug 6: Integer overflow
Use `long long` for sums, products, capacities, and answer calculations.

---

# 23. Interview Questions with Short Answers

### Q1. Why is binary search logarithmic?
Each iteration removes roughly half the candidates, so after `k` iterations only `n/2^k` remain.

### Q2. What is the most important binary-search invariant?
The answer remains inside the maintained search interval after every update.

### Q3. Lower bound vs upper bound?
Lower bound finds first `>= x`; upper bound finds first `> x`.

### Q4. Why does binary search on answer work?
Because feasibility changes only once: all values before the optimal boundary are infeasible and all values after it are feasible, or vice versa.

### Q5. Why can rotated-array search still be logarithmic?
At least one side of the midpoint is sorted, which gives enough information to discard the other side.

### Q6. Why do duplicates hurt rotated-array search?
Equal boundary and midpoint values can hide which side is sorted, forcing us to shrink both sides and potentially reducing progress to one element at a time.

### Q7. When should you use `lower_bound` directly?
When the data is sorted and the required result is a boundary position. Understand the implementation before relying on the STL.

### Q8. Why search the smaller array for median of two sorted arrays?
It bounds the number of possible partitions and gives O(log(min(n,m))) complexity.

### Q9. What is binary search on answer plus greedy?
Binary search guesses the answer; greedy verifies whether that guess is feasible.

### Q10. What is the fastest way to detect this pattern?
Look for “minimum possible maximum,” “maximum possible minimum,” “first feasible,” or a yes/no question over a numeric range.

---

# 24. Master Problem-Solving Template

When you suspect binary search, write this before code:

```text
Search space: [L, R]
Predicate: can(x)
Monotonicity: false → true OR true → false
Target boundary: first true / last true
Invariant: answer remains in [L, R]
Complexity: O(log range × predicate cost)
```

That six-line checklist prevents most binary-search mistakes.

> **Core lesson:** Binary search is not about memorizing one loop. It is about proving that a boundary exists and then maintaining an invariant while narrowing toward it.
