# 05. Stack

A stack follows LIFO. In C++, `stack<T>` gives O(1) top, push and pop.

## Beginner

Core operations: `push`, `pop`, `top`, `empty`. Understand stack using arrays/vectors before relying on STL.

**All basic operations:** O(1).

## Intermediate: matching and simulation

### Valid Parentheses
Push opening brackets; every closing bracket must match the current top.

**TC:** O(n)  **SC:** O(n)

### Min Stack
Store either `(value, currentMin)` or encode differences to achieve O(1) minimum.

Pair approach: **TC O(1)** per operation, **SC O(n)**.

## Monotonic stack

Maintain elements in increasing or decreasing order so dominated elements can be removed permanently.

### Next Greater Element
Scan from right to left. Pop values that cannot be the next greater value; the remaining top is the answer.

**TC:** O(n) amortized  **SC:** O(n)

### Daily Temperatures
Use a decreasing stack of indices. Each index is pushed and popped at most once.

**TC:** O(n)  **SC:** O(n)

### Largest Rectangle in Histogram
Maintain increasing bar indices. When a smaller bar appears, finalize rectangles whose height is no longer extendable.

**TC:** O(n)  **SC:** O(n)

## Important questions

1. Implement stack using array/linked list — **O(1)** operations.
2. Queue using two stacks — **O(1) amortized** per operation, **O(n)** space.
3. Valid parentheses — **O(n), O(n)**.
4. Min stack — **O(1)** each, **O(n)** space.
5. Next greater element — **O(n), O(n)**.
6. Stock span — **O(n), O(n)**.
7. Daily temperatures — **O(n), O(n)**.
8. Largest rectangle histogram — **O(n), O(n)**.
9. Maximal rectangle in binary matrix — **O(rows × cols), O(cols)** auxiliary beyond the matrix.
10. Remove K digits — monotonic increasing stack, **O(n), O(n)**.

## Pitfalls

Always ask whether an element can be permanently discarded. That is the key reason monotonic-stack solutions become linear.