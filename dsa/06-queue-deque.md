# 06. Queue & Deque

A queue is FIFO; a deque supports insertion/removal at both ends.

## Fundamentals

`queue<T>`: `push`, `pop`, `front`, `back` are O(1).
`deque<T>`: operations at either end are O(1).

## Circular queue

Use an array plus front/size (or front/rear modulo capacity) so removed slots are reused.

**Enqueue/dequeue:** O(1), **SC:** O(capacity).

## BFS foundation

A queue is the natural structure for level-order traversal and unweighted shortest paths.

**BFS graph TC:** O(V+E), **SC:** O(V).

## Monotonic deque

A deque can maintain a sliding-window maximum/minimum by removing indices that are both older than the window or dominated by the new value.

### Sliding Window Maximum
Maintain decreasing values by index. The front is always the maximum.

**TC:** O(n) amortized  **SC:** O(k)

## Important questions

1. Implement queue using array — **O(1)** amortized/normal operations.
2. Implement queue using two stacks — **O(1)** amortized, **O(n)** space.
3. First non-repeating character in a stream — queue + frequency, **O(n), O(n)**.
4. Generate binary numbers from 1 to N — queue, **O(n), O(n)**.
5. Rotten Oranges — multi-source BFS, **O(RC), O(RC)**.
6. Sliding Window Maximum — monotonic deque, **O(n), O(k)**.
7. Shortest path in unweighted graph — BFS, **O(V+E), O(V)**.

## Recognition

Use a queue when work must be processed in arrival order or by distance/level. Use a deque when a window needs efficient access to an extreme while both ends must be updated.