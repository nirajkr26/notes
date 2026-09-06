# 18. Advanced Data Structures

Study this after trees, heaps, graphs, and DP. These structures are useful when basic arrays/maps are not fast enough for dynamic range or order queries.

## 18.1 Fenwick Tree / Binary Indexed Tree

Supports point updates and prefix sums in O(log n). A range sum is the difference of two prefix sums.

**Update:** O(log n)  **Query:** O(log n)  **Space:** O(n)

### Recognition
Dynamic point updates + prefix/range sums, especially when only additive aggregation is needed.

## 18.2 Segment Tree

A segment tree stores aggregate information for intervals. It supports point updates and range queries in O(log n). Lazy propagation supports many range updates in O(log n) each for compatible operations.

**Build:** O(n)  **Query:** O(log n)  **Point update:** O(log n)  **Space:** O(n)

With lazy range updates, explain exactly what the lazy value means and when it is pushed.

## 18.3 Sparse Table

Preprocess a static array for idempotent range queries such as min/max/gcd.

**Build:** O(n log n), **query:** O(1) for idempotent operations, **space:** O(n log n).

No efficient general update support; it is for static data.

## 18.4 Ordered set/map

C++ `set` and `map` are typically balanced trees.

Search/insert/delete: **O(log n)**. They maintain sorted order and support predecessor/successor via iterators.

## 18.5 DSU advanced usage

Path compression + union by size/rank gives amortized O(alpha(n)) operations. Add component size, parity, or other metadata when the problem requires it.

## Important questions

1. Dynamic range sum — Fenwick/segment tree, **O(log n)** per update/query.
2. Range minimum with updates — segment tree, **O(log n)**.
3. Range add + range sum — lazy segment tree, **O(log n)** per operation.
4. Inversion count — Fenwick + coordinate compression, **O(n log n)**.
5. Count smaller elements after self — Fenwick/segment tree + compression, **O(n log n)**.
6. Offline connectivity — DSU, near-linear.
7. Kruskal MST — **O(E log E)**.
8. Range minimum on static array — sparse table, **O(1)** query after O(n log n) preprocessing.

## When to use what

| Requirement | Structure |
|---|---|
| Exact membership | Hash set |
| Sorted membership/order | Ordered set/map |
| Top/bottom repeatedly | Heap |
| Prefix sum + point update | Fenwick |
| General range aggregate + updates | Segment tree |
| Static idempotent range query | Sparse table |
| Dynamic connectivity | DSU |

## Pitfalls

Advanced structures are unnecessary if sorting, prefix sums, a heap, or a hash map already meets constraints. First establish the required operation complexity, then choose the smallest structure that satisfies it.