# 03. Sorting

Sorting is both an algorithm topic and a problem-solving tool. Many difficult problems become simple after ordering the input.

## 3.1 Complexity overview

| Algorithm | Best | Average | Worst | Extra space | Stable |
|---|---:|---:|---:|---:|---|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) average stack | Usually no |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Can be |
| Radix | O(d(n+k)) | O(d(n+k)) | O(d(n+k)) | O(n+k) | Can be |

In C++, `std::sort` is the default practical choice and has O(n log n) worst-case complexity under its standard introspective sorting strategy.

## 3.2 Beginner

### Selection sort
Repeatedly select the smallest remaining element and put it at the next position.

**TC:** O(n²)  **SC:** O(1)

### Insertion sort
Maintain a sorted prefix and insert the next element into its correct position.

Best case is O(n) when already sorted; worst case O(n²).

**SC:** O(1), stable.

## 3.3 Intermediate: merge sort

Divide the array, recursively sort each half, then merge two sorted halves.

**TC:** O(n log n) in all cases. **SC:** O(n) auxiliary.

The merge step is also the foundation of inversion counting.

### Count inversions
An inversion is `(i,j)` with `i < j` and `a[i] > a[j]`. During merge, when a right-half element is selected before remaining left-half elements, all those left elements form inversions.

**TC:** O(n log n)  **SC:** O(n)

## 3.4 Quick sort

Choose a pivot and partition into values smaller/larger than it. Average complexity is O(n log n), but poor pivot choices can cause O(n²).

Randomization or a robust pivot strategy reduces the likelihood of bad cases.

## 3.5 Non-comparison sorting

Counting sort is useful when the value range `k` is small relative to `n`.

**TC:** O(n+k)  **SC:** O(k)

Radix sort processes digits from least significant to most significant using a stable digit sort.

## 3.6 Important questions

### Q1. Sort an array of 0s, 1s and 2s
Dutch National Flag: maintain `[0..low-1] = 0`, `[low..mid-1] = 1`, `[high+1..n-1] = 2`.

**TC:** O(n)  **SC:** O(1)

### Q2. Merge two sorted arrays
Two pointers consume the smaller front element.

**TC:** O(n+m)  **SC:** O(n+m) if a new result is required.

### Q3. Merge intervals
Sort by start and merge overlaps.

**TC:** O(n log n)  **SC:** O(n) output.

### Q4. Meeting rooms
Sort intervals by start and track the earliest finishing meeting using a min-heap.

**TC:** O(n log n)  **SC:** O(n)

### Q5. Kth largest element
Use a heap for clarity or quickselect for average linear time.

Heap: **O(n log k)** with size `k`, **SC:** O(k).

Quickselect: **O(n)** average, **O(n²)** worst-case, typically O(1) auxiliary apart from recursion.

### Q6. Largest number
Sort numbers using comparator `a+b > b+a`.

**TC:** O(n log n × L), where `L` reflects comparison string length. **SC:** O(n) depending on representation.

## 3.7 When sorting is the key insight

If a problem asks for closest pairs, intervals, duplicates, ordering by a custom relation, or a greedy choice based on endpoints, try sorting first.

## 3.8 Pitfalls

- Comparator must define a consistent ordering.
- `std::sort` on integers is numeric, unlike JavaScript's default sort.
- Do not claim O(1) space when the output itself requires O(n).
- For inversion count, use a wide integer type because the number of inversions can be O(n²).