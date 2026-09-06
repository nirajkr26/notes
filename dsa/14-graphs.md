# 14. Graphs — From Basics to Advanced Algorithms

Graphs model relationships between entities. They are one of the broadest DSA topics because the same graph can be solved using traversal, shortest paths, topological ordering, minimum spanning trees, DSU, or dynamic programming depending on the question.

---

# 1. Graph Terminology

A graph consists of:

```text
V = vertices / nodes
E = edges / relationships
```

Important types:

- directed / undirected
- weighted / unweighted
- connected / disconnected
- cyclic / acyclic
- simple / multigraph
- sparse / dense

Always identify these properties before choosing an algorithm.

---

# 2. Adjacency List

For sparse graphs, adjacency lists are usually the best general representation.

```cpp
vector<vector<int>> graph(n);
graph[u].push_back(v);
graph[v].push_back(u); // undirected
```

**Space:** O(V+E)

For weighted graphs:

```cpp
vector<vector<pair<int,int>>> graph(n);
```

where pair is `(neighbor, weight)`.

---

# 3. Adjacency Matrix

```cpp
vector<vector<int>> matrix(n, vector<int>(n));
```

Advantages:

- O(1) edge-existence check.

Disadvantage:

- O(V²) memory.

Use it when the graph is dense or the input is already a matrix.

---

# 4. DFS

DFS explores one path as deeply as possible before returning.

```cpp
void dfs(int u, const vector<vector<int>>& g,
         vector<bool>& visited) {
    visited[u] = true;

    for (int v : g[u]) {
        if (!visited[v]) dfs(v, g, visited);
    }
}
```

**TC:** O(V+E)  
**SC:** O(V) visited + recursion stack

For extremely deep graphs, iterative DFS can avoid call-stack overflow.

---

# 5. BFS

BFS explores by distance from a source.

Use a queue and mark nodes when discovered.

**TC:** O(V+E)  
**SC:** O(V)

### Most important recognition

```text
minimum number of edges
unweighted shortest path
level-by-level exploration
nearest source
```

→ BFS.

---

# 6. Connected Components

For every unvisited vertex, start DFS/BFS. Each traversal discovers one connected component.

**TC:** O(V+E)  
**SC:** O(V)

This is one of the most common graph templates.

---

# 7. Number of Islands

A grid can be viewed as a graph where neighboring land cells are connected.

For every unvisited land cell, BFS/DFS marks its entire island.

For an `R × C` grid:

**TC:** O(RC)  
**SC:** O(RC) worst-case recursion/queue

### Important insight
Many grid problems are graph problems in disguise.

---

# 8. Cycle Detection — Undirected Graph

DFS needs to know the parent from which the current vertex was reached.

If we see a visited neighbor that is not the parent, we found a cycle.

**TC:** O(V+E)  
**SC:** O(V)

DSU provides another solution when edges are processed incrementally.

---

# 9. Cycle Detection — Directed Graph

A visited node alone is not enough. A directed edge to an already completed node does not necessarily mean a cycle.

Use three states:

```text
0 = unvisited
1 = currently in recursion stack
2 = completely processed
```

An edge to a state-1 node is a back edge and proves a cycle.

**TC:** O(V+E)  
**SC:** O(V)

---

# 10. Bipartite Graph

A graph is bipartite if vertices can be divided into two groups such that every edge connects different groups.

Color each component with two colors using BFS/DFS. If an edge connects equal colors, it is not bipartite.

**TC:** O(V+E)  
**SC:** O(V)

Equivalent interpretation: an undirected graph is bipartite iff it has no odd cycle.

---

# 11. Topological Sort

A topological ordering is a sequence where for every directed edge `u → v`, `u` appears before `v`.

It exists exactly for DAGs (directed acyclic graphs).

### Kahn's algorithm

1. Compute indegrees.
2. Put all zero-indegree vertices into a queue.
3. Remove one, append it to ordering.
4. Decrease neighbors' indegrees.
5. Newly zero vertices enter the queue.

If fewer than V vertices are processed, a cycle exists.

**TC:** O(V+E)  
**SC:** O(V)

---

# 12. Course Schedule

Prerequisites are directed edges.

The question “Can all courses be completed?” becomes:

> Does the dependency graph contain a cycle?

Use topological sorting. If all courses can be processed, a valid order exists.

**TC:** O(V+E)  
**SC:** O(V)

---

# 13. Unweighted Shortest Path

Every edge has equal cost, usually one.

BFS from source gives minimum edge distance.

```text
distance[source] = 0
neighbor = 1
next layer = 2
```

**TC:** O(V+E)  
**SC:** O(V)

---

# 14. 0-1 BFS

For edge weights only `0` and `1`, use a deque.

- weight 0 → push front
- weight 1 → push back

This maintains nodes in nondecreasing tentative-distance order efficiently.

**TC:** O(V+E)  
**SC:** O(V)

---

# 15. Dijkstra's Algorithm

Use Dijkstra for shortest paths with **non-negative edge weights**.

Maintain `dist[v]`, the best distance currently known.

Repeatedly choose the vertex with smallest tentative distance, then relax outgoing edges.

### C++

```cpp
vector<long long> dijkstra(
    int source,
    const vector<vector<pair<int,int>>>& g) {

    const long long INF = LLONG_MAX / 4;
    vector<long long> dist(g.size(), INF);

    using State = pair<long long, int>;
    priority_queue<State, vector<State>, greater<State>> pq;

    dist[source] = 0;
    pq.push({0, source});

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        if (d != dist[u]) continue;

        for (auto [v, w] : g[u]) {
            if (dist[v] > d + w) {
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

**TC:** O((V+E) log V) with binary heap  
**SC:** O(V+E) including graph; O(V) algorithmic auxiliary

### Why non-negative weights?
Once the smallest tentative distance is selected, Dijkstra assumes no future edge can produce a cheaper route to that finalized vertex. A negative edge can violate that assumption.

---

# 16. Bellman-Ford

Bellman-Ford supports negative edge weights and can detect reachable negative cycles.

Relax every edge `V-1` times. A further successful relaxation indicates a reachable negative cycle.

**TC:** O(VE)  
**SC:** O(V)

### When to prefer it
When negative weights matter and constraints are small enough.

---

# 17. Floyd-Warshall

All-pairs shortest paths.

State:

```text
d[i][j] = shortest path from i to j
```

For every intermediate vertex `k`:

```text
d[i][j] = min(d[i][j], d[i][k] + d[k][j])
```

**TC:** O(V³)  
**SC:** O(V²)

This is a classic DP-over-graphs algorithm.

---

# 18. Minimum Spanning Tree

An MST connects all vertices with minimum total edge weight and contains no cycles.

For connected graph with V vertices, an MST has exactly V-1 edges.

Two major algorithms:

- Kruskal
- Prim

---

# 19. Kruskal + DSU

Sort all edges by weight. Process from smallest to largest. Add an edge if it connects two different components.

DSU detects whether adding the edge would form a cycle.

**TC:** O(E log E)  
**SC:** O(V)

### Why greedy is safe
The MST cut property says a lightest edge crossing an appropriate cut can belong to some MST.

---

# 20. Prim's Algorithm

Start with any vertex and repeatedly add the cheapest edge connecting the current tree to an outside vertex.

With adjacency list + binary heap:

**TC:** O(E log V)  
**SC:** O(V+E)

### Kruskal vs Prim

```text
Kruskal → globally sorts edges + DSU
Prim    → grows one tree + priority queue
```

Both are greedy MST algorithms.

---

# 21. Disjoint Set Union

DSU maintains dynamically changing connected components.

Operations:

```text
find(x)  → representative
union(a,b) → merge components
```

Use:

- path compression
- union by size/rank

Amortized complexity:

```text
O(alpha(n))
```

which is effectively constant for practical input sizes.

**Space:** O(n)

---

# 22. DSU Applications

- connected components
- redundant connection
- Kruskal MST
- account merging
- dynamic connectivity
- cycle detection in undirected graphs
- grouping equivalent objects

A key signal is:

> “Merge these groups and repeatedly ask whether two elements are connected.”

→ DSU.

---

# 23. Bridges / Critical Connections

A bridge is an edge whose removal increases the number of connected components.

Tarjan's low-link algorithm maintains discovery time and the lowest discovery time reachable through back edges.

For DFS tree edge `u-v`, if:

```text
low[v] > tin[u]
```

then `(u,v)` is a bridge.

**TC:** O(V+E)  
**SC:** O(V)

This is an advanced but important graph interview algorithm.

---

# 24. Strongly Connected Components

In a directed graph, an SCC is a maximal set where every vertex can reach every other vertex.

Know the concepts of:

- Kosaraju: two DFS passes
- Tarjan: one DFS with low-link states

Typical complexity:

**TC:** O(V+E)  
**SC:** O(V)

---

# 25. Cheapest Flight with K Stops

The stop limit adds a state dimension. Ordinary Dijkstra over only `dist[node]` may lose information because reaching a node cheaply with too many stops can be less useful than a slightly more expensive route with fewer stops.

Possible solutions:

- bounded Bellman-Ford / DP
- state-space shortest path `(node, stops)`

Typical bounded relaxation complexity: **O(KE)**.

### Key lesson
If an extra constraint affects future choices, it may need to become part of the state.

---

# 26. Word Ladder

Words are vertices; two words are connected if one legal transformation changes one character.

BFS finds the minimum number of transformations because each transformation has equal cost.

Wildcard pattern indexing can accelerate neighbor discovery.

For `N` words of length `L`, an optimized standard approach is commonly analyzed around O(NL²), depending on representation.

---

# 27. Grid as Graph

Common grid moves:

```text
up    = (-1,0)
down  = (1,0)
left  = (0,-1)
right = (0,1)
```

For each cell, check bounds before accessing it.

Many grid questions reduce to:

```text
for every cell
    if it starts an unvisited component
        BFS/DFS
```

Learn this template extremely well.

---

# 28. OA / Interview Problem Bank

## Beginner

1. DFS traversal.
2. BFS traversal.
3. Connected components.
4. Number of islands.
5. Flood fill.
6. Count provinces.
7. Detect cycle in undirected graph.
8. Bipartite graph.
9. Shortest path in unweighted graph.

## Intermediate

10. Course Schedule.
11. Course Schedule II.
12. Rotting Oranges.
13. Word Ladder.
14. Clone Graph.
15. 0-1 BFS.
16. Dijkstra.
17. Network Delay Time.
18. Cheapest Flights within K Stops.
19. Redundant Connection.
20. Kruskal MST.
21. Prim MST.
22. DSU applications.

## Advanced

23. Bellman-Ford.
24. Floyd-Warshall.
25. Bridges.
26. Articulation points.
27. Strongly Connected Components.
28. Eulerian path/circuit.
29. Topological DP on DAG.
30. Shortest path with additional state.
31. Multi-source BFS.
32. Advanced grid graph problems.

---

# 29. Frequently Asked Interview Questions

### Q1. BFS vs DFS?
BFS is natural for levels and minimum edge distance. DFS is natural for exhaustive exploration, component discovery, recursion, and structural properties.

### Q2. Why does BFS find unweighted shortest paths?
All edges have equal cost, so BFS explores vertices in increasing edge distance.

### Q3. Why does Dijkstra fail with negative edges?
Its greedy finalization assumption can become false if a later negative edge creates a shorter route.

### Q4. Dijkstra vs Bellman-Ford?
Dijkstra is faster but requires non-negative weights. Bellman-Ford handles negative weights and detects reachable negative cycles.

### Q5. When use Floyd-Warshall?
When all-pairs shortest paths are required and the number of vertices is small enough for O(V³).

### Q6. Kruskal vs Prim?
Kruskal sorts edges globally and uses DSU. Prim grows a tree from a starting vertex using the cheapest outgoing edge.

### Q7. What is a DAG?
A Directed Acyclic Graph. DAGs support topological ordering and many efficient DP formulations.

### Q8. What is topological sorting used for?
Dependency ordering: courses, builds, jobs, package dependencies, and task scheduling.

### Q9. Why is DSU nearly constant time?
Path compression and union by size/rank reduce the amortized operation cost to O(alpha(n)).

### Q10. What is a bridge?
An edge whose removal increases the number of connected components.

### Q11. What is multi-source BFS?
Initialize the queue with all sources simultaneously so distances represent the nearest source.

### Q12. How do you recognize a graph problem?
Look for entities plus relationships, paths, connectivity, dependencies, transformations, networks, or movement between states.

---

# 30. Algorithm Selection Cheat Sheet

```text
Unweighted shortest path       → BFS
0/1 weighted edges            → 0-1 BFS
Non-negative weighted graph   → Dijkstra
Negative edges                → Bellman-Ford
All-pairs shortest paths      → Floyd-Warshall
Connect all vertices cheaply  → MST
Static edge connectivity     → DFS/BFS
Dynamic group merging         → DSU
Dependencies                 → Topological sort
Mutual reachability          → SCC
Critical edges               → Bridges/Tarjan
Grid regions                 → BFS/DFS
Nearest of many sources      → Multi-source BFS
```

> **Core lesson:** Graph problems are primarily about choosing the correct interpretation of the edges and the correct invariant for traversal, distance, connectivity, or ordering.
