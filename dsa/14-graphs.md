# 14. Graphs

Graphs model entities and relationships. A graph has vertices `V` and edges `E`; it may be directed/undirected and weighted/unweighted.

## 14.1 Representations

### Adjacency list
Best general-purpose representation for sparse graphs.

**Space:** O(V+E). Traversal: O(V+E).

### Adjacency matrix
O(1) edge existence but O(V²) memory.

## Beginner: BFS and DFS

DFS explores deeply; BFS explores by distance/level.

For adjacency lists:

- DFS: **O(V+E)** time, **O(V)** visited/stack space.
- BFS: **O(V+E)** time, **O(V)** visited/queue space.

### Connected components
Run DFS/BFS from every unvisited vertex.

**TC:** O(V+E)  **SC:** O(V)

### Cycle detection
Undirected graph: DFS parent tracking or DSU. Directed graph: DFS recursion-state/three-color method.

**TC:** O(V+E)  **SC:** O(V)

## Intermediate: shortest paths

### Unweighted graph
BFS gives minimum number of edges.

**TC:** O(V+E)  **SC:** O(V)

### 0-1 BFS
For edge weights only 0 or 1, use a deque.

**TC:** O(V+E)  **SC:** O(V)

### Dijkstra
For non-negative edge weights, repeatedly finalize the vertex with smallest tentative distance. With a binary heap:

**TC:** O((V+E) log V), **SC:** O(V+E) for graph + O(V) auxiliary.

Dijkstra is invalid when negative edges exist.

### Bellman-Ford
Relax every edge `V-1` times; a further relaxation indicates a reachable negative cycle.

**TC:** O(VE)  **SC:** O(V)

### Floyd-Warshall
All-pairs shortest paths using DP over intermediate vertices.

**TC:** O(V³)  **SC:** O(V²)

## MST

### Kruskal
Sort edges and add an edge if it connects two different DSU components.

**TC:** O(E log E), **SC:** O(V) auxiliary.

### Prim
Grow one tree using the cheapest edge crossing the current cut.

Heap version: **O(E log V)** with adjacency lists.

## DAGs and topological sort

Topological order exists exactly for directed acyclic graphs. Kahn's algorithm uses indegrees + queue; DFS uses finish times.

**TC:** O(V+E)  **SC:** O(V)

### Course Schedule
Model prerequisites as directed edges and detect whether a topological ordering exists.

**TC:** O(V+E)  **SC:** O(V)

## DSU / Union-Find

Maintain disjoint sets with path compression and union by size/rank. Amortized operation cost is `O(alpha(n))`, effectively constant.

**Space:** O(n).

## Important questions

1. Number of islands — **O(RC), O(RC)** worst-case visited/recursion.
2. Clone graph — **O(V+E), O(V)**.
3. Course schedule — **O(V+E), O(V)**.
4. Bipartite graph — BFS/DFS coloring, **O(V+E), O(V)**.
5. Rotting oranges — multi-source BFS, **O(RC), O(RC)**.
6. Word ladder — BFS, complexity depends on word length/dictionary representation; standard optimized approach is near O(N·L²).
7. Network delay time — Dijkstra, **O((V+E)logV)**.
8. Cheapest flights within K stops — bounded-state shortest-path/DP approaches; **O(KE)** typical.
9. Redundant connection — DSU, **O(E alpha(V))**.
10. Number of provinces — DFS/DSU, **O(V²)** for matrix input.
11. Accounts merge — DSU/graph + hashing, typically **O(N log N)** due to sorting names/edges.
12. Critical connections / bridges — Tarjan low-link, **O(V+E), O(V)**.

## Recognition

- “Minimum edges” unweighted → BFS.
- Non-negative weights → Dijkstra.
- Negative weights → Bellman-Ford/other specialized method.
- Connect everything as cheaply as possible → MST.
- Dependencies → topological sort.
- Dynamic connectivity → DSU.
- All-pairs shortest paths with moderate V → Floyd-Warshall.

## Pitfalls

Clarify directed vs undirected, weighted vs unweighted, negative edges, disconnected graphs, self-loops, parallel edges, and whether paths must be simple.