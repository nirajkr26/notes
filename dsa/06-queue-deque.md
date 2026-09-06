# 06. Queue & Deque — From FIFO to BFS and Monotonic Windows

A queue follows **FIFO: First In, First Out**. A deque (double-ended queue) supports insertion and deletion at both ends.

Queues are fundamental to BFS, scheduling, buffering, producer-consumer systems, and level-order processing. Deques become especially powerful for sliding-window maximum/minimum problems.

---

# 1. Queue Fundamentals

```cpp
queue<int> q;
q.push(10);
q.push(20);
cout << q.front();
q.pop();
```

Core operations:

| Operation | Complexity |
|---|---:|
| `push` | O(1) |
| `pop` | O(1) |
| `front` | O(1) |
| `back` | O(1) |
| `empty` | O(1) |

**Space:** O(n)

Never call `front()` or `pop()` on an empty queue.

---

# 2. Implement a Queue Using an Array

A naive implementation that shifts all elements after every dequeue is O(n) per operation.

Instead maintain a front index and reuse storage with a circular queue.

### Circular queue idea

For capacity `C`, the next index is:

```text
(nextIndex) = (index + 1) % C
```

This prevents wasted slots after dequeues.

**Enqueue:** O(1)  
**Dequeue:** O(1)  
**Space:** O(C)

---

# 3. Queue Using Two Stacks

Use one stack for incoming elements and another for outgoing elements.

```text
input:  newest elements
output: oldest elements
```

When output is empty, move all input elements into output. Reversal converts LIFO order into FIFO order.

Each element crosses from input to output at most once.

**Amortized TC:** O(1) per operation  
**Worst single transfer:** O(n)  
**SC:** O(n)

---

# 4. Deque Fundamentals

```cpp
deque<int> dq;
dq.push_back(10);
dq.push_front(5);
dq.pop_back();
dq.pop_front();
```

Both ends support O(1) insertion/removal.

Use a deque when the algorithm needs to maintain candidates from both ends.

---

# 5. BFS — Why Queue?

BFS processes vertices in increasing distance from the source.

For an unweighted graph:

```text
source distance = 0
neighbors       = 1
next layer       = 2
...
```

The queue guarantees that nodes discovered at distance `d` are processed before nodes at distance `d+1`.

### C++ template

```cpp
vector<int> bfs(int source, const vector<vector<int>>& graph) {
    vector<int> dist(graph.size(), -1);
    queue<int> q;

    dist[source] = 0;
    q.push(source);

    while (!q.empty()) {
        int u = q.front();
        q.pop();

        for (int v : graph[u]) {
            if (dist[v] != -1) continue;
            dist[v] = dist[u] + 1;
            q.push(v);
        }
    }
    return dist;
}
```

**TC:** O(V+E)  
**SC:** O(V)

### Why mark visited when enqueueing?
If you wait until dequeueing, the same node may be inserted many times by different neighbors. Marking at discovery ensures one queue insertion per vertex.

---

# 6. Level Order Traversal

Trees are a natural queue application.

At each iteration, capture `q.size()` and process exactly that many nodes. Those nodes form one level.

**TC:** O(n)  
**SC:** O(w), where `w` is maximum width.

Variants:

- zigzag level order
- reverse level order
- right-side view
- average of levels
- minimum depth

---

# 7. Multi-Source BFS

Sometimes there are many starting points rather than one.

Example: Rotten Oranges.

Put **all initially rotten oranges** into the queue with distance/time `0`. BFS then expands simultaneously from all sources.

This computes minimum distance from the nearest source.

**TC:** O(RC) for an `R × C` grid  
**SC:** O(RC) worst case

### Recognition
Words like:

- spread
- infection
- fire
- simultaneous sources
- nearest special cell
- minimum minutes

often suggest multi-source BFS.

---

# 8. Sliding Window Maximum — Monotonic Deque

Given:

```text
[1,3,-1,-3,5,3,6,7], k=3
```

find the maximum of every window.

A heap gives O(n log k). A monotonic deque gives O(n).

### Deque invariant
Store indices such that their values are decreasing:

```text
a[d[0]] >= a[d[1]] >= a[d[2]] ...
```

The front is always the maximum.

For each index `i`:

1. Remove indices outside the window.
2. Remove from the back while their values are <= `a[i]`; they can never beat the new value.
3. Push `i`.
4. Front gives the window maximum.

```cpp
vector<int> maxSlidingWindow(const vector<int>& a, int k) {
    deque<int> dq;
    vector<int> ans;

    for (int i = 0; i < (int)a.size(); ++i) {
        while (!dq.empty() && dq.front() <= i - k)
            dq.pop_front();

        while (!dq.empty() && a[dq.back()] <= a[i])
            dq.pop_back();

        dq.push_back(i);

        if (i >= k - 1)
            ans.push_back(a[dq.front()]);
    }
    return ans;
}
```

**TC:** O(n) amortized  
**SC:** O(k)

### Why O(n)?
Every index enters the deque once and leaves once.

---

# 9. Sliding Window Minimum

Use exactly the same pattern but maintain increasing values instead of decreasing values.

The front then stores the minimum.

**TC:** O(n)  
**SC:** O(k)

---

# 10. First Non-Repeating Character in a Stream

Maintain:

```text
frequency map/array
queue of candidate characters
```

When a character arrives:

1. increment its frequency
2. push it into the queue
3. remove queue-front characters while their frequency > 1
4. front is the first non-repeating character

**TC:** O(n) amortized  
**SC:** O(k)

---

# 11. Shortest Path in an Unweighted Graph

BFS is optimal when every edge has equal cost.

Why?

The first time a vertex is reached, it is reached through the smallest number of edges because BFS explores layers in increasing distance.

**TC:** O(V+E)  
**SC:** O(V)

Do not use ordinary BFS for arbitrary positive weighted edges; use an appropriate weighted shortest-path algorithm.

---

# 12. 0-1 BFS

If every edge weight is either `0` or `1`, use a deque.

When relaxing:

- weight `0` → push front
- weight `1` → push back

This keeps smaller tentative distances near the front.

**TC:** O(V+E)  
**SC:** O(V)

This is an important bridge between BFS and Dijkstra.

---

# 13. Important OA / Interview Problems

## Beginner

1. Implement queue using array.
2. Implement circular queue.
3. Queue using two stacks.
4. Generate binary numbers from 1 to N.
5. Reverse first K elements of a queue.
6. Level-order traversal.
7. First non-repeating character in a stream.

## Intermediate

8. Binary tree level order.
9. Zigzag level order.
10. Rotten Oranges.
11. Shortest path in unweighted graph.
12. Flood fill.
13. Number of islands using BFS.
14. 0-1 BFS.
15. Sliding Window Maximum.
16. Sliding Window Minimum.

## Advanced

17. Shortest path in binary matrix.
18. Walls and Gates.
19. Open the Lock.
20. Word Ladder.
21. Minimum Knight Moves.
22. Multi-source distance transform.
23. Constrained shortest path with state.
24. Monotonic deque optimization for DP.

---

# 14. Frequently Asked Interview Questions

### Q1. Queue vs stack?
Queue is FIFO; stack is LIFO. Queue is natural for level/distance processing, while stack is natural for nested/reversal behavior.

### Q2. Why is BFS shortest path in an unweighted graph?
BFS explores vertices in increasing number of edges from the source, so first discovery gives the minimum edge count.

### Q3. Why mark BFS nodes when pushing them?
To prevent duplicate insertions and ensure each vertex is queued once.

### Q4. Why does sliding-window maximum use a deque instead of a queue?
We need to remove both expired indices from the front and dominated candidates from the back.

### Q5. Why can dominated deque elements be removed permanently?
If a newer element is at least as large, it will remain in future windows longer and therefore always be a better maximum candidate.

### Q6. Why does 0-1 BFS use a deque?
Zero-cost transitions should be processed before one-cost transitions, and the deque provides exactly the required front/back ordering.

### Q7. What is multi-source BFS?
Initialize BFS with all sources simultaneously. Distances then represent distance to the nearest source.

### Q8. When should you use a queue in a tree?
For level-by-level processing, minimum-depth searches, and breadth-first views.

---

# 15. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Queue operation | O(1) | O(1) |
| Circular queue | O(1) | O(C) |
| Queue via two stacks | O(1) amortized | O(n) |
| BFS | O(V+E) | O(V) |
| Tree level order | O(n) | O(w) |
| Multi-source BFS | O(V+E) / O(RC) | O(V) / O(RC) |
| 0-1 BFS | O(V+E) | O(V) |
| Sliding max | O(n) | O(k) |
| First non-repeating stream | O(n) amortized | O(k) |

> **Core lesson:** A queue processes the future in the order it becomes reachable; a monotonic deque goes further by remembering only candidates that can still affect the answer.
