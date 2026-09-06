# 03. Sorting — From Fundamentals to Advanced Problems

Sorting is not just an algorithm to memorize. It is a **problem-solving transformation**: after ordering the data, relationships become easier to detect and many O(n²) searches become linear two-pointer scans.

---

# 1. What Does Sorting Mean?

Sorting arranges elements according to an ordering relation.

For integers, ascending order means:

```text
x0 <= x1 <= x2 <= ... <= xn-1
```

The ordering may also be custom, such as:

- increasing interval end
- decreasing frequency
- lexicographic order
- `a+b > b+a` for forming the largest number

A correct comparator is therefore an important interview skill.

---

# 2. Why Do We Sort?

Sorting is useful because it can provide:

1. **Binary-searchability.**
2. **Two-pointer movement.**
3. **Easy duplicate detection.**
4. **Greedy ordering.**
5. **Interval merging.**
6. **Nearest/closest-element structure.**
7. **Deterministic organization of records.**

A common interview thought process is:

```text
Can I sort first?
        ↓
Does sorted order reveal a monotonic relationship?
        ↓
Can I now use two pointers / binary search / greedy?
```

---

# 3. Comparison Sorting Lower Bound

For general comparison-based sorting, the decision-tree argument gives a lower bound of Ω(n log n) comparisons in the worst case.

That is why algorithms such as merge sort, heap sort, and well-designed quicksort target O(n log n).

Non-comparison algorithms such as counting/radix sort can beat this when the input has additional structure, such as a small integer range or bounded digit representation.

---

# 4. Bubble Sort — Beginner

Bubble sort repeatedly compares adjacent elements and swaps them when they are in the wrong order.

After one complete pass, the largest remaining element reaches the end.

### Optimized C++

```cpp
void bubbleSort(vector<int>& a) {
    int n = a.size();
    for (int i = 0; i < n; ++i) {
        bool swapped = false;
        for (int j = 0; j + 1 < n - i; ++j) {
            if (a[j] > a[j + 1]) {
                swap(a[j], a[j + 1]);
                swapped = true;
            }
        }
        if (!swapped) break;
    }
}
```

**Best TC:** O(n) when already sorted  
**Average/Worst TC:** O(n²)  
**SC:** O(1)  
**Stable:** Yes

### What it teaches
Bubble sort is rarely the answer in interviews, but it teaches the idea of maintaining a sorted suffix and detecting an already-sorted input.

---

# 5. Selection Sort

Selection sort repeatedly finds the minimum element from the unsorted suffix and places it at the current position.

### C++

```cpp
void selectionSort(vector<int>& a) {
    int n = a.size();
    for (int i = 0; i < n; ++i) {
        int best = i;
        for (int j = i + 1; j < n; ++j) {
            if (a[j] < a[best]) best = j;
        }
        swap(a[i], a[best]);
    }
}
```

**TC:** O(n²) in all cases  
**SC:** O(1)  
**Stable:** Usually no

### Why learn it?
It is a simple example of repeatedly selecting the best remaining element. That idea later appears in heaps and greedy algorithms, where we make the selection efficiently.

---

# 6. Insertion Sort

Insertion sort maintains a sorted prefix.

For each new element, shift larger elements to the right until the correct location is found.

### C++

```cpp
void insertionSort(vector<int>& a) {
    for (int i = 1; i < (int)a.size(); ++i) {
        int x = a[i];
        int j = i - 1;

        while (j >= 0 && a[j] > x) {
            a[j + 1] = a[j];
            --j;
        }
        a[j + 1] = x;
    }
}
```

**Best TC:** O(n)  
**Worst TC:** O(n²)  
**SC:** O(1)  
**Stable:** Yes

### When is insertion sort useful?
Small arrays, nearly sorted arrays, or as a base case inside more sophisticated hybrid sorting algorithms.

---

# 7. Merge Sort — Core Interview Algorithm

Merge sort uses divide and conquer.

### Step 1 — Divide
Split the array into two halves.

### Step 2 — Conquer
Recursively sort both halves.

### Step 3 — Combine
Merge the two sorted halves.

The recurrence is:

```text
T(n) = 2T(n/2) + O(n)
```

which gives O(n log n).

### C++

```cpp
void mergeSort(vector<int>& a) {
    int n = a.size();
    vector<int> temp(n);

    function<void(int, int)> solve = [&](int l, int r) {
        if (r - l <= 1) return;

        int mid = l + (r - l) / 2;
        solve(l, mid);
        solve(mid, r);

        int i = l, j = mid, k = l;
        while (i < mid && j < r) {
            if (a[i] <= a[j]) temp[k++] = a[i++];
            else temp[k++] = a[j++];
        }
        while (i < mid) temp[k++] = a[i++];
        while (j < r) temp[k++] = a[j++];

        for (int p = l; p < r; ++p) a[p] = temp[p];
    };

    solve(0, n);
}
```

**TC:** O(n log n)  
**SC:** O(n) auxiliary + recursion stack

### Why is merge sort stable?
When two equal values are compared, selecting the left value first preserves their original relative order.

---

# 8. Counting Inversions Using Merge Sort

An inversion is a pair:

```text
i < j and a[i] > a[j]
```

Example:

```text
[2, 4, 1, 3, 5]
```

Inversions include `(2,1)`, `(4,1)`, `(4,3)`.

### Key insight
During merge, if `right[j] < left[i]`, then `right[j]` is smaller than **every remaining element** in the left half because the left half is sorted.

Therefore it creates:

```text
mid - i
```

inversions at once.

**TC:** O(n log n)  
**SC:** O(n)

Use `long long` for the count because the number of inversions can be O(n²).

---

# 9. Quick Sort

Quick sort chooses a pivot and partitions the array around it.

Conceptually:

```text
values < pivot | pivot | values >= pivot
```

Then recursively sort the two sides.

### Complexity

**Average TC:** O(n log n)  
**Worst TC:** O(n²)  
**Average recursion SC:** O(log n)  
**Worst recursion SC:** O(n)

The worst case occurs when partitions are extremely unbalanced repeatedly, such as repeatedly choosing the smallest element as pivot for already sorted input with a poor pivot rule.

### Interview question
Why is `std::sort` generally preferred over writing quicksort?

Because the standard library provides a highly optimized hybrid implementation with strong worst-case behavior, while a naive hand-written quicksort can be vulnerable to bad partitions.

---

# 10. Heap Sort

Heap sort builds a heap and repeatedly extracts the maximum/minimum.

**TC:** O(n log n) worst case  
**SC:** O(1) auxiliary  
**Stable:** No

It is useful conceptually because it connects sorting with the heap data structure.

---

# 11. Sorting Algorithm Comparison

| Algorithm | Best | Average | Worst | Extra Space | Stable |
|---|---:|---:|---:|---:|---|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) avg | No |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Can be |

`k` is the value-range size in counting sort.

---

# 12. `std::sort` and Custom Comparators

For integers:

```cpp
sort(a.begin(), a.end());
```

Descending:

```cpp
sort(a.begin(), a.end(), greater<int>());
```

For pairs, structs, or intervals, write a comparator expressing the required ordering.

### Important comparator rule
A comparator should define a consistent strict ordering. Avoid inconsistent conditions such as returning arbitrary results for equal elements.

---

# 13. Dutch National Flag

Sort an array containing only `0`, `1`, and `2` in O(n) time without extra storage.

The three-region invariant is:

```text
[0, low)       → 0
[low, mid)     → 1
[mid, high]    → unknown
(high, n)       → 2
```

### C++

```cpp
void sort012(vector<int>& a) {
    int low = 0, mid = 0, high = (int)a.size() - 1;

    while (mid <= high) {
        if (a[mid] == 0) {
            swap(a[low++], a[mid++]);
        } else if (a[mid] == 1) {
            ++mid;
        } else {
            swap(a[mid], a[high--]);
        }
    }
}
```

**TC:** O(n)  
**SC:** O(1)

### Critical interview detail
After swapping with `high`, do not increment `mid`, because the newly swapped value has not yet been classified.

---

# 14. Merge Two Sorted Arrays

Use two pointers because each array is already ordered.

At every step, the smallest unprocessed element must be at one of the two pointer positions.

**TC:** O(n+m)  
**SC:** O(n+m) for a separate result

If the problem gives enough unused space in the first array, merge from the end to avoid overwriting values.

---

# 15. Merge Intervals

Example:

```text
[1,3] [2,6] [8,10] [9,12]
```

Sort by start.

Then maintain the current merged interval:

```text
current = [start, end]
```

For each next interval:

- if `next.start > current.end`, no overlap
- otherwise extend `current.end`

### C++

```cpp
vector<vector<int>> mergeIntervals(vector<vector<int>> intervals) {
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> ans;

    for (auto interval : intervals) {
        if (ans.empty() || ans.back()[1] < interval[0]) {
            ans.push_back(interval);
        } else {
            ans.back()[1] = max(ans.back()[1], interval[1]);
        }
    }
    return ans;
}
```

**TC:** O(n log n)  
**SC:** O(n) for output

### Variants
Learn the same pattern for:

- Insert Interval
- Non-overlapping Intervals
- Minimum Arrows to Burst Balloons
- Meeting Rooms
- Meeting Rooms II

---

# 16. Meeting Rooms II

Given meeting intervals, find the minimum rooms required.

Two standard approaches:

### Approach A — min-heap
Sort meetings by start. Keep end times in a min-heap. If the earliest ending meeting has already ended, reuse that room.

**TC:** O(n log n)  
**SC:** O(n)

### Approach B — separate starts and ends
Sort all start times and end times. Two pointers count active meetings.

**TC:** O(n log n)  
**SC:** O(n)

The second approach is a useful example of converting interval objects into independent event streams.

---

# 17. Kth Largest Element

There are several solutions.

### Sorting
Sort and take `a[n-k]`.

**TC:** O(n log n)**

### Min-heap of size k
Keep only the largest `k` values.

**TC:** O(n log k)  
**SC:** O(k)

### Quickselect
Partition around a pivot and recurse only into the side containing the target rank.

**Average TC:** O(n)  
**Worst TC:** O(n²)  
**Auxiliary:** typically O(1) plus recursion

### Interview discussion
If the input is a stream, heap is usually more natural. If only one kth-order query is required, quickselect can be attractive. If you need the complete sorted order, sort.

---

# 18. Sort by Frequency

Count frequencies first, then order by frequency.

Possible structures:

- hash map + sorting
- heap
- bucket sort

This teaches a recurring pattern:

```text
raw data → aggregate metadata → sort/heap by metadata
```

---

# 19. Largest Number

Given integers, arrange them to form the largest possible number.

Suppose we compare strings `a` and `b`.

Which should come first?

```text
a+b > b+a
```

For example:

```text
"9" + "34" = "934"
"34" + "9" = "349"
```

So `9` comes before `34`.

**TC:** O(n log n × L) where `L` is comparison string length.  
**SC:** O(n) depending on representation.

### Key lesson
A custom sort comparator can encode a problem-specific ordering that is not ordinary numeric or lexicographic order.

---

# 20. Sort Colors / Partition Problems

When values belong to a small number of categories, ask whether you can maintain regions rather than fully sorting.

Examples:

- 0/1/2 → Dutch National Flag
- negative/non-negative partition
- evens/odds
- elements smaller/larger than pivot

The goal is often O(n) in-place partitioning rather than O(n log n) full sorting.

---

# 21. Counting Sort

Counting sort works when values lie in a manageable range.

For values `0..k`:

1. Count frequency of each value.
2. Reconstruct values in order.

**TC:** O(n+k)  
**SC:** O(k)

### When should you NOT use it?
If values range from `0` to `10^9` but only `10^5` elements exist, allocating `10^9` counters is wasteful. Consider sorting, hashing, or coordinate compression.

---

# 22. Radix Sort

Radix sort processes numbers digit by digit, commonly from least significant digit to most significant digit, using a stable intermediate sort.

If there are `d` digits and each digit has range `k`:

**TC:** O(d(n+k))

It can be linear for fixed-width integers, but it relies on assumptions that comparison sorting does not.

---

# 23. Sorting + Two Pointers

This is one of the most important interview combinations.

### Example: 3Sum

1. Sort.
2. Fix `i`.
3. Set `l=i+1`, `r=n-1`.
4. Compare `a[i]+a[l]+a[r]`.
5. Move `l` or `r` based on the sum.

**TC:** O(n²)  
**SC:** O(1) auxiliary excluding output

### Why sorting matters
Without sorted order, moving a pointer would not tell us whether we are eliminating valid candidates.

---

# 24. Sorting + Greedy

Sorting often exposes the order in which a greedy decision is safe.

Examples:

- Activity selection → earliest finish.
- Interval scheduling → earliest end.
- Fractional knapsack → highest value/weight.
- Minimum arrows → earliest finishing endpoint.
- Job sequencing variants → sort by profit/deadline.

The sorting step is not itself the greedy proof. You still need to explain why the chosen ordering supports optimal decisions.

---

# 25. Important OA / Interview Question Bank

## Beginner

1. Bubble sort.
2. Selection sort.
3. Insertion sort.
4. Implement merge sort.
5. Check whether an array is sorted.
6. Merge two sorted arrays.
7. Sort 0s and 1s.
8. Sort 0s, 1s and 2s.
9. Remove duplicates from sorted array.
10. Find kth smallest/largest after sorting.

## Intermediate

11. Merge intervals.
12. Insert interval.
13. Non-overlapping intervals.
14. Meeting rooms.
15. Meeting rooms II.
16. 3Sum.
17. 4Sum.
18. Kth largest element.
19. Sort characters by frequency.
20. Largest number.
21. Count inversions.
22. Reverse pairs.
23. Minimum difference between pairs.
24. Closest three-sum.
25. Group overlapping intervals.

## Advanced

26. Quickselect.
27. Median of two sorted arrays.
28. Count smaller elements after self.
29. Count range sums.
30. Maximum gap.
31. Wiggle sort.
32. Merge K sorted arrays.
33. External sorting concept.
34. Coordinate compression + Fenwick tree.
35. Sweep-line problems using sorted events.

---

# 26. Frequently Asked Interview Questions

### Q1. Why is merge sort O(n log n)?
There are O(log n) levels because the array is repeatedly halved, and each level performs O(n) total merging work.

### Q2. Why is quicksort O(n²) in the worst case?
If every partition produces sizes `0` and `n-1`, the recurrence becomes `T(n)=T(n-1)+O(n)`, which is O(n²).

### Q3. Why can quicksort be fast in practice?
It has good cache locality, in-place partitioning, and low constant factors, and good pivot selection usually produces balanced partitions.

### Q4. Why is merge sort stable?
Equal elements can be taken from the left half before the right half, preserving their original relative order.

### Q5. What does stable sorting mean?
If two records compare equal under the sorting key, their original relative order remains unchanged.

### Q6. Why can sorting turn O(n²) into O(n)?
Sorting gives order. That order can make pointer movement monotonic, allowing each pointer to move only forward/backward once.

### Q7. When is counting sort better than comparison sorting?
When the value range is sufficiently small relative to the number of elements.

### Q8. Why does counting sort fail for huge sparse ranges?
Its memory and time depend on the range `k`, not just the number of elements.

### Q9. Why does merge sort help count inversions?
The two halves are sorted, so when a right element is smaller than the current left element, it forms inversions with all remaining left elements at once.

### Q10. Why use a heap instead of sorting for top K?
A heap can maintain only the `k` elements that matter, giving O(n log k) instead of sorting all n elements.

### Q11. Why use quickselect for kth element?
Partitioning tells us which side contains the kth element, so we discard the other side instead of recursively sorting everything.

### Q12. What is the difference between stable and unstable sorting?
Stable sorting preserves equal-key relative order; unstable sorting does not guarantee it.

### Q13. Why must custom comparators be consistent?
Sorting algorithms rely on a coherent ordering. An inconsistent comparator can produce undefined or incorrect behavior.

---

# 27. Choosing the Right Sorting Strategy

```text
Need complete ordering?
        ↓
std::sort / merge sort / other full sort

Need only kth element?
        ↓
quickselect / heap

Need top K from large input?
        ↓
heap

Small integer range?
        ↓
counting sort

Fixed-width digits?
        ↓
radix sort

Intervals?
        ↓
sort endpoints → sweep/merge/greedy

Pairs/triples?
        ↓
sort → two pointers
```

---

# 28. Common Mistakes

- Writing a comparator that violates strict ordering.
- Forgetting sorting changes original indices.
- Using O(n log n) when a three-way partition gives O(n).
- Claiming quicksort is always O(n log n).
- Forgetting output space when returning sorted/generated data.
- Counting inversions in `int`.
- Using counting sort when the value range is enormous.
- Forgetting duplicate handling in 3Sum/4Sum.
- Assuming a heap contains globally sorted elements.

---

# 29. Mastery Test

Before moving to Linked Lists, solve these without notes:

1. Implement insertion sort.
2. Implement merge sort.
3. Explain quicksort worst case.
4. Sort 0/1/2 in one pass.
5. Merge intervals.
6. Count inversions.
7. Find kth largest using a heap.
8. Explain quickselect.
9. Solve 3Sum.
10. Explain why sorting enables two pointers.

For every solution, say:

```text
Brute force → bottleneck → optimization → invariant → correctness → TC → SC → edge cases
```

> **Core lesson:** Sorting is valuable not because every problem asks you to sort, but because ordering can reveal structure that was invisible in the original input.
