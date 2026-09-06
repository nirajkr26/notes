# 12. Heap & Priority Queue

A binary heap is a complete binary tree represented efficiently in an array.

For zero-based indexing: `parent=(i-1)/2`, `left=2*i+1`, `right=2*i+2`.

## Complexity

| Operation | Complexity |
|---|---:|
| Top | O(1) |
| Push | O(log n) |
| Pop | O(log n) |
| Build heap | O(n) |
| Search arbitrary value | O(n) |

## Beginner

Understand min-heap/max-heap and implement sift-up/sift-down.

C++ `priority_queue<int>` is a max-heap by default; `priority_queue<int, vector<int>, greater<int>>` is a min-heap.

## Intermediate

### Kth largest
Maintain a min-heap of size `k`; when size exceeds `k`, pop the smallest. The root is the kth largest.

**TC:** O(n log k)  **SC:** O(k)

### Top K frequent
Count frequencies, then keep the k largest frequencies in a heap.

**TC:** O(n log k) after counting; **SC:** O(n+k).

### Merge K sorted lists
Push the first node from each list into a min-heap and repeatedly extract the smallest, inserting its next node.

**TC:** O(N log k), **SC:** O(k).

### Two heaps for median
Maintain a max-heap for the lower half and min-heap for the upper half. Keep sizes balanced.

Insertion: **O(log n)**, median: **O(1)**, space: **O(n)**.

## Important questions

1. Kth largest/smallest — **O(n log k), O(k)**.
2. Top K frequent — **O(n log k), O(n)**.
3. K closest points — **O(n log k), O(k)** with bounded heap.
4. Merge K sorted arrays/lists — **O(N log k), O(k)**.
5. Find median from data stream — **O(log n)** insertion, **O(1)** query, **O(n)** space.
6. Task scheduler — heap + counts; complexity depends on task model.
7. Reorganize string — max-heap by frequency, generally **O(n log k)**.
8. Meeting rooms II — sort starts/ends or use min-heap, **O(n log n), O(n)**.
9. Smallest range covering K lists — heap, **O(N log k), O(k)**.

## Advanced

Know heap construction, decrease/increase key concepts, indexed heaps, and when a heap is preferable to sorting: especially streaming data or when only top `k` elements matter.

## Pitfalls

A heap is not fully sorted. Only the root is guaranteed to be the global minimum/maximum. Arbitrary search is O(n).