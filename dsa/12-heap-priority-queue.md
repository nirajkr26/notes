# 12. Heap & Priority Queue — Complete Interview Guide

A heap is a complete binary tree satisfying a heap-order property.

### Min-heap
Parent <= children.

### Max-heap
Parent >= children.

A heap is **not a sorted array**. Only the root is guaranteed to be the global minimum/maximum.

---

# 1. Array Representation

For zero-based index `i`:

```text
parent = (i-1)/2
left   = 2*i+1
right  = 2*i+2
```

Completeness means the tree is filled level by level, making the array representation compact.

---

# 2. C++ Priority Queue

Max-heap:

```cpp
priority_queue<int> pq;
```

Min-heap:

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

Operations:

| Operation | TC |
|---|---:|
| `top` | O(1) |
| `push` | O(log n) |
| `pop` | O(log n) |
| `empty` | O(1) |
| `size` | O(1) |

---

# 3. Why Is Push O(log n)?

A newly inserted value starts at the bottom. It may need to move upward through the height of the heap.

A complete binary tree has height O(log n), so sift-up is O(log n).

---

# 4. Why Is Pop O(log n)?

Remove the root and move the last element to the root. Then repeatedly swap it downward with the appropriate child until heap order is restored.

Again, at most one root-to-leaf path is traversed.

**TC:** O(log n)

---

# 5. Build Heap in O(n)

A common misconception is that inserting all n elements costs O(n log n), so build-heap must also be O(n log n).

Bottom-up heap construction is O(n) because most nodes are near the leaves and have very small sift-down distances.

This is a classic interview complexity question.

---

# 6. Kth Largest Element

### Heap solution
Maintain a min-heap of size `k`.

For each value:

1. push it
2. if heap size > k, pop the smallest

The heap then contains the k largest values, and its root is the kth largest.

**TC:** O(n log k)  
**SC:** O(k)

### Why min-heap for kth largest?
We need to remove the smallest among the current top-k candidates.

For kth smallest, use the symmetric max-heap.

---

# 7. Top K Frequent Elements

First compute frequencies with a hash map. Then keep the k highest-frequency elements in a min-heap.

**TC:** O(n log k) expected after counting  
**SC:** O(n)

Bucket sorting can achieve linear expected/structured complexity when frequency range is convenient.

---

# 8. K Closest Points

Compute distance without square root:

```text
distance² = x² + y²
```

Keep a max-heap of size `k`. The farthest among the retained candidates is removed when a closer point arrives.

**TC:** O(n log k)  
**SC:** O(k)

### Interview lesson
For “keep best k”:

- If best means smallest → often max-heap of size k.
- If best means largest → often min-heap of size k.

---

# 9. Merge K Sorted Arrays

Put the first element of every array into a min-heap along with its array/index information.

Repeatedly extract the smallest and push the next element from that same array.

If total elements are `N` and there are `k` arrays:

**TC:** O(N log k)  
**SC:** O(k)

This is the same idea as merging K linked lists.

---

# 10. Median From Data Stream — Two Heaps

Maintain two halves:

```text
max-heap = lower half
min-heap = upper half
```

Invariant:

```text
size difference <= 1
all lower-half values <= all upper-half values
```

If sizes are equal, median is the average of both roots. Otherwise median is the root of the larger heap.

**Insertion:** O(log n)  
**Median:** O(1)  
**SC:** O(n)

### Why two heaps?
They provide direct access to the largest value of the lower half and smallest value of the upper half.

---

# 11. Reorganize String

Given character frequencies, repeatedly choose the most frequent character that is different from the previously placed character.

A max-heap gives access to the highest remaining frequency.

A useful pattern is:

```text
extract best
use it
keep previous aside
extract next best
restore previous
```

**TC:** O(n log k) where k is distinct character count  
**SC:** O(k)

---

# 12. Task Scheduler

If tasks have cooldown constraints, frequency determines which tasks create the schedule bottleneck.

A max-heap can repeatedly choose the currently most frequent available task, while a queue tracks cooldown expiration.

The exact complexity depends on the task model, but the key pattern is:

```text
priority queue for best available work
+ queue/time structure for temporarily unavailable work
```

---

# 13. Meeting Rooms II

Sort intervals by start time and keep current meeting end times in a min-heap.

For each meeting:

- if earliest room end <= current start, reuse it
- otherwise allocate a new room
- push the new end

**TC:** O(n log n)  
**SC:** O(n)

---

# 14. Smallest Range Covering K Lists

Put one element from each list into a min-heap. The current range is:

```text
[maxValue - minValue]
```

Advance the list containing the minimum value, because otherwise the minimum cannot increase.

**TC:** O(N log k)  
**SC:** O(k)

This combines a heap with a two-boundary range invariant.

---

# 15. Heap vs Sorting

If you need every element ordered:

```text
sort → O(n log n)
```

If you need only top k:

```text
heap → O(n log k)
```

If the data arrives continuously:

```text
heap → natural streaming solution
```

If only one kth element is required:

```text
quickselect → O(n) average
```

Choosing the structure based on the required operation is more important than memorizing one implementation.

---

# 16. Custom Heap Comparator

Priority queues can store pairs/structs.

For example:

```cpp
using P = pair<int, int>;
priority_queue<P, vector<P>, greater<P>> pq;
```

Pairs compare lexicographically by default, which is useful for `(distance,node)` in Dijkstra.

For complex objects, provide a comparator that represents the priority clearly.

---

# 17. Important OA / Interview Problem Bank

## Beginner

1. Implement min-heap.
2. Implement max-heap.
3. Heapify an array.
4. Build heap.
5. Kth largest.
6. Kth smallest.
7. Priority queue scheduling.

## Intermediate

8. Top K frequent.
9. K closest points.
10. Merge K sorted arrays.
11. Merge K sorted lists.
12. Meeting Rooms II.
13. Reorganize String.
14. Task Scheduler.
15. Last Stone Weight.
16. Find K pairs with smallest sums.

## Advanced

17. Median from data stream.
18. Smallest range covering K lists.
19. Sliding-window median.
20. IPO / maximize capital.
21. Hire K workers under cost constraints.
22. Kth smallest element in sorted matrix.
23. Dijkstra with priority queue.
24. Heap-based streaming algorithms.

---

# 18. Frequently Asked Questions

### Q1. Is a heap sorted?
No. Only the root is guaranteed to be the minimum/maximum. Children and deeper nodes are only constrained relative to their ancestors.

### Q2. Why is build-heap O(n)?
Bottom-up heapify performs little work on nodes near the leaves, and the total weighted work across all heights is linear.

### Q3. Why use min-heap for kth largest?
Because among the current k largest candidates, we need fast access to the smallest one so it can be removed when a better candidate arrives.

### Q4. Heap or sorting for top K?
A heap is often O(n log k), while sorting is O(n log n). For small k, the heap can be substantially better.

### Q5. Why use two heaps for streaming median?
One heap stores the lower half and the other the upper half, exposing the two middle values directly.

### Q6. Why does Dijkstra use a min-heap?
We need to repeatedly process the vertex with the smallest tentative distance.

### Q7. Why can arbitrary heap search be O(n)?
Heap order does not fully sort unrelated branches, so a target may be anywhere.

---

# 19. Complexity Summary

| Operation/problem | TC | SC |
|---|---:|---:|
| Top | O(1) | O(1) |
| Push | O(log n) | O(1) |
| Pop | O(log n) | O(1) |
| Build heap | O(n) | O(1) auxiliary |
| Kth largest | O(n log k) | O(k) |
| Top K frequent | O(n log k) | O(n) |
| K closest | O(n log k) | O(k) |
| Merge K lists | O(N log k) | O(k) |
| Median stream insert | O(log n) | O(n) |
| Meeting Rooms II | O(n log n) | O(n) |
| Smallest range | O(N log k) | O(k) |

> **Core lesson:** A heap is a machine for repeatedly asking “what is the best currently available candidate?” Once you recognize that question, many scheduling, top-K, streaming, and shortest-path problems become natural.
