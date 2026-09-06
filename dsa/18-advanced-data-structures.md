# 18. Advanced Data Structures — Complete Guide

These structures become useful when ordinary arrays, hash maps, heaps, and prefix sums cannot satisfy the required update/query complexity.

The correct order is:

```text
Understand required operations
        ↓
Derive required complexity
        ↓
Choose smallest suitable data structure
```

Do not use a segment tree simply because it is advanced.

---

# 1. Ordered Set and Map

C++ `set` and `map` are typically balanced search trees.

```cpp
set<int> s;
map<int, int> mp;
```

Operations:

**TC:** O(log n) for search, insert, erase.

Advantages:

- sorted iteration
- predecessor/successor
- `lower_bound`
- `upper_bound`

Use these when ordering matters. Use hashing when only expected constant-time membership is needed.

---

# 2. Fenwick Tree / Binary Indexed Tree

A Fenwick Tree supports:

- point update
- prefix sum query

both in O(log n).

A range sum is:

```text
sum(l,r) = prefix(r) - prefix(l-1)
```

### Core idea
Each internal position stores the sum of a carefully chosen range determined by its lowest set bit.

For index `i`:

```text
parent update index = i + (i & -i)
query index         = i - (i & -i)
```

### C++

```cpp
class Fenwick {
    int n;
    vector<long long> bit;

public:
    Fenwick(int n) : n(n), bit(n + 1, 0) {}

    void add(int idx, long long delta) {
        for (++idx; idx <= n; idx += idx & -idx)
            bit[idx] += delta;
    }

    long long sumPrefix(int idx) const {
        long long ans = 0;
        for (++idx; idx > 0; idx -= idx & -idx)
            ans += bit[idx];
        return ans;
    }

    long long rangeSum(int l, int r) const {
        if (l > r) return 0;
        return sumPrefix(r) - (l ? sumPrefix(l - 1) : 0);
    }
};
```

**Update:** O(log n)  
**Query:** O(log n)  
**Space:** O(n)

### When to use it
Dynamic point updates + prefix/range sums.

---

# 3. Fenwick Tree for Inversion Count

Process elements from right to left.

Coordinate-compress values because values may be huge.

For current value `x`, query how many already-seen values are smaller than `x`. That count contributes inversions.

**TC:** O(n log n)  
**SC:** O(n)

This is a powerful combination:

```text
coordinate compression + Fenwick tree
```

---

# 4. Segment Tree

A segment tree stores aggregate information for intervals.

It supports:

- build
- point update
- range query

in O(log n) per operation after O(n) build.

Typical aggregates:

- sum
- minimum
- maximum
- gcd
- custom associative operations

### Why O(log n) query?
A range can be decomposed into O(log n) relevant tree segments for common segment-tree query structures.

**Build:** O(n)  
**Query:** O(log n)  
**Point update:** O(log n)  
**Space:** O(n)

---

# 5. Segment Tree Node Meaning

This is the most important design step.

For a sum tree:

```text
node = sum of its interval
```

For a minimum tree:

```text
node = minimum of its interval
```

The merge operation must correctly combine child information.

If the operation is associative, segment trees are especially natural.

---

# 6. Lazy Propagation

Suppose you need:

```text
range update
+
range query
```

Updating every element individually can be O(n).

Lazy propagation postpones updates to descendants until those descendants are actually needed.

The lazy value means:

> “This entire segment has an update that has not yet been pushed to its children.”

For supported range-add/range-sum structures:

**Update:** O(log n)  
**Query:** O(log n)  
**Space:** O(n)

### Interview warning
Never say “lazy propagation makes everything O(log n).” It depends on the operation and on correctly defining how lazy updates combine.

---

# 7. Segment Tree vs Fenwick Tree

| Requirement | Fenwick | Segment Tree |
|---|---|---|
| Point update | Yes | Yes |
| Prefix sum | Yes | Yes |
| Range sum | Yes | Yes |
| Range min/max | Limited | Yes |
| Complex range aggregate | No/general harder | Yes |
| Range lazy updates | No | Yes |
| Memory | O(n) | O(n) |
| Simplicity | Higher | Lower |

If Fenwick is sufficient, it is usually the simpler implementation.

---

# 8. Sparse Table

For a **static** array, sparse tables answer idempotent range queries such as:

- minimum
- maximum
- gcd

After preprocessing:

**Build:** O(n log n)  
**Query:** O(1) for suitable idempotent operations  
**Space:** O(n log n)

### Why static?
Updating an element would invalidate many precomputed intervals, so sparse tables are designed for immutable data.

---

# 9. Coordinate Compression

Values may be huge:

```text
10, 10^9, 500000000
```

but only their relative ordering matters.

Sort unique values and replace each with its rank:

```text
10 → 0
500000000 → 1
10^9 → 2
```

**TC:** O(n log n)  
**SC:** O(n)

Common with Fenwick trees and segment trees.

---

# 10. DSU with Metadata

DSU can store more than connectivity.

Per component, maintain:

- size
- sum
- minimum/maximum
- parity information
- custom component metadata

When two roots merge, combine metadata according to the problem.

**Amortized operation:** O(alpha(n))  
**Space:** O(n)

---

# 11. DSU with Parity

Some problems ask whether relationships imply two groups or whether constraints are contradictory.

A parity bit can store whether a node has the same/opposite relation to its representative.

This supports advanced bipartite-consistency and constraint problems.

The key idea is that DSU can represent **relative relationships**, not only connected components.

---

# 12. Ordered Statistics

Sometimes the problem asks:

```text
insert value
remove value
find kth smallest
```

A normal BST does not know subtree sizes automatically.

Augment each node with:

```text
subtree size
```

Then order statistics can be answered in O(log n) in a balanced tree.

This is a standard advanced-data-structure design technique: store exactly the additional information needed to answer future queries quickly.

---

# 13. Range Query Design

Before selecting a structure, classify operations.

```text
Static + range minimum → sparse table
Point update + range sum → Fenwick
Point update + general range aggregate → segment tree
Range update + range query → lazy segment tree
Dynamic connectivity → DSU
Ordered predecessor/successor → set/map
```

This table is worth memorizing only after understanding why each mapping exists.

---

# 14. Important OA / Interview Questions

## Beginner

1. Ordered set operations.
2. Ordered map operations.
3. Coordinate compression.
4. Fenwick prefix sum.
5. Fenwick point update.
6. Dynamic range sum.
7. Segment tree build/query.

## Intermediate

8. Range minimum query.
9. Range maximum query.
10. Inversion count using Fenwick.
11. Count smaller elements after self.
12. Dynamic frequency queries.
13. Range query with point updates.
14. Static RMQ with sparse table.

## Advanced

15. Lazy segment tree.
16. Range add + range sum.
17. Range assign + range query.
18. DSU with metadata.
19. DSU parity.
20. Offline dynamic connectivity.
21. Order-statistics tree concept.
22. Persistent segment tree concept.
23. Persistent Trie concept.
24. 2D Fenwick tree.
25. 2D segment tree concepts.

---

# 15. Frequently Asked Interview Questions

### Q1. Fenwick vs segment tree?
Fenwick is simpler and excellent for prefix/range sums with point updates. Segment trees support more general range aggregates and can support lazy range updates.

### Q2. Why is Fenwick O(log n)?
Each update/query repeatedly moves by the lowest set bit, changing the index in a logarithmic number of steps.

### Q3. Why is build-heap O(n) but segment-tree build O(n)?
Both exploit bottom-up aggregation. Every segment-tree node combines two child summaries exactly once, and there are O(n) nodes.

### Q4. What is lazy propagation?
A technique that stores a pending range update at a segment node instead of immediately visiting every descendant.

### Q5. When should you use sparse table?
When the array is static and queries are numerous, especially for idempotent operations such as min/max/gcd.

### Q6. Why coordinate-compress?
To convert a huge sparse value domain into compact indices while preserving the ordering information needed by the algorithm.

### Q7. Why not always use segment tree?
It is more complex and often unnecessary. Choose the smallest structure that satisfies the required operations.

### Q8. Why augment a tree?
Additional metadata such as subtree size can turn expensive queries into logarithmic operations.

### Q9. What is DSU good at?
Maintaining connectivity under repeated component merges.

---

# 16. Complexity Summary

| Structure | Build | Query | Update | Space |
|---|---:|---:|---:|---:|
| `set/map` | — | O(log n) | O(log n) | O(n) |
| Fenwick | O(n) / O(n log n) | O(log n) | O(log n) | O(n) |
| Segment tree | O(n) | O(log n) | O(log n) | O(n) |
| Lazy segment tree | O(n) | O(log n) | O(log n) | O(n) |
| Sparse table | O(n log n) | O(1) suitable RMQ | Static | O(n log n) |
| DSU | O(n) | O(alpha(n)) | O(alpha(n)) | O(n) |

> **Core lesson:** Advanced data structures are not about using complicated tools. They are about matching an operation pattern to the minimum structure that can support it within the required constraints.
