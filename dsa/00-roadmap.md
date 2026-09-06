# DSA Roadmap — Beginner → Advanced

This folder is designed to be learned **top-to-bottom**. Every chapter uses **C++ only** and follows the same structure: intuition → core concepts → patterns → important interview questions → complexity → pitfalls.

## Learning order

| # | Chapter | Main goal |
|---|---|---|
| 01 | Arrays | Traversal, manipulation, prefix/suffix, Kadane |
| 02 | Binary Search | Search on sorted data and monotonic answers |
| 03 | Sorting | Comparison/non-comparison sorting and sorting-based patterns |
| 04 | Linked List | Pointer manipulation and structural problems |
| 05 | Stack | LIFO, monotonic stack, expression problems |
| 06 | Queue & Deque | FIFO, BFS foundations, monotonic deque |
| 07 | Hashing | Frequency, membership, prefix-state lookup |
| 08 | Two Pointers & Sliding Window | Linear-time subarray/subsequence patterns |
| 09 | Recursion & Backtracking | State-space search and pruning |
| 10 | Trees | DFS/BFS, traversals, tree properties |
| 11 | Binary Search Tree | Ordered trees and predecessor/successor logic |
| 12 | Heap & Priority Queue | Top-K, scheduling, streaming min/max |
| 13 | Greedy | Local-choice proofs and interval/scheduling problems |
| 14 | Graphs | Traversal, shortest paths, MST, DSU, DAGs |
| 15 | Trie | Prefix-based string search |
| 16 | Dynamic Programming | State, transition, memoization, tabulation, optimization |
| 17 | Bit Manipulation | Masks, XOR, shifts, bit DP basics |
| 18 | Advanced Data Structures | Fenwick tree, segment tree, sparse table, advanced DSU |

## How to study each chapter

1. Understand the data structure or invariant without code.
2. Implement the basic operations from memory.
3. Learn the beginner problems before the pattern problems.
4. For every optimization, explicitly identify what the brute force repeats.
5. Memorize **why** a solution is correct, not the exact code.
6. Before coding, state time complexity and auxiliary space.
7. Re-solve the important questions after 1 day, 1 week, and 1 month.

## Universal problem-solving checklist

```text
1. What are the constraints?
2. What is the brute-force complexity?
3. What work is repeated?
4. Is the input sorted or monotonic?
5. Is the answer about a contiguous range?
6. Do I need frequency/membership lookup?
7. Can two pointers remove candidates?
8. Is there an optimal substructure / overlapping subproblem?
9. Is this a graph/tree/state-space problem?
10. What invariant proves the algorithm is correct?
11. What are TC and SC?
12. Which edge cases break the obvious implementation?
```

## Complexity targets

- `O(1)` / `O(log n)`: preferred for single queries when possible.
- `O(n)`: standard target for one-pass array/string problems.
- `O(n log n)`: usually acceptable for large inputs.
- `O(n^2)`: often acceptable only for smaller constraints.
- `O(2^n)` / `O(n!)`: usually for deliberately exponential state-space problems.

> **Rule:** Do not move to advanced topics until the previous chapter's basic patterns feel mechanical.