# Data Structures & Algorithms (DSA) — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

**Data Structures** are ways to organize and store data so operations can be performed efficiently.  
**Algorithms** are step-by-step procedures to solve a problem or perform a computation.

**Why DSA Matters:**
- Determines performance at scale (O(n) vs O(n²) is the difference between seconds and hours)
- Every major tech interview tests DSA knowledge
- Foundation for system design, compilers, databases, and operating systems

---

## 2. Complexity Analysis

### 2.1 Big-O Notation

Describes the **worst-case** growth rate of time or space as input size `n` grows.

| Notation     | Name         | Example                                      |
|--------------|--------------|----------------------------------------------|
| O(1)         | Constant     | Array access by index, HashMap get/put        |
| O(log n)     | Logarithmic  | Binary search, balanced BST operations        |
| O(n)         | Linear       | Linear search, array traversal                |
| O(n log n)   | Linearithmic | Merge sort, heap sort, efficient sorts        |
| O(n²)        | Quadratic    | Bubble sort, nested loops                     |
| O(n³)        | Cubic        | Matrix multiplication (naive)                 |
| O(2ⁿ)        | Exponential  | Subset enumeration, naive recursion           |
| O(n!)        | Factorial    | Permutation generation                        |

**Growth order:** O(1) < O(log n) < O(√n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!)

### 2.2 Space Complexity

Accounts for **extra memory** used (not including input). Recursive calls use O(depth) stack space.

### 2.3 Amortized Analysis

Average cost per operation over a sequence. Example: Dynamic array doubling — individual resize is O(n) but amortized O(1) per append.

### 2.4 Recurrence Relations

| Recurrence          | Solution    | Algorithm         |
|---------------------|-------------|-------------------|
| T(n) = T(n/2) + O(1)| O(log n)    | Binary search     |
| T(n) = T(n-1) + O(1)| O(n)        | Linear recursion  |
| T(n) = 2T(n/2) + O(n)| O(n log n) | Merge sort        |
| T(n) = 2T(n/2) + O(1)| O(n)       | Tree traversal    |
| T(n) = T(n-1) + O(n)| O(n²)       | Selection sort    |

---

## 3. Arrays

### 3.1 Properties

- Contiguous memory; O(1) access by index
- Fixed size (static) or dynamic (ArrayList/vector doubles capacity)
- Insert/delete at middle: O(n) due to shifting

### 3.2 Key Techniques

#### Two Pointers
```
Use two pointers moving inward/outward to avoid nested loops.

// Find pair summing to target in sorted array
left = 0, right = n-1
while left < right:
    sum = arr[left] + arr[right]
    if sum == target: return [left, right]
    if sum < target:  left++
    else:             right--
```

#### Sliding Window
```
Maintain a window [left, right] and expand/shrink as needed.

// Longest subarray with sum ≤ k
left = 0, sum = 0, maxLen = 0
for right in 0..n-1:
    sum += arr[right]
    while sum > k: sum -= arr[left++]
    maxLen = max(maxLen, right - left + 1)
```

#### Prefix Sum
```
Precompute cumulative sums for O(1) range sum queries.

prefix[0] = 0
prefix[i] = prefix[i-1] + arr[i-1]
rangeSum(l, r) = prefix[r+1] - prefix[l]  // O(1)
```

#### Kadane's Algorithm (Max Subarray Sum)
```
maxSum = arr[0], currentSum = arr[0]
for i in 1..n-1:
    currentSum = max(arr[i], currentSum + arr[i])
    maxSum = max(maxSum, currentSum)
```

### 3.3 Common Array Problems

| Problem                      | Technique              | Complexity      |
|------------------------------|------------------------|-----------------|
| Two Sum                      | HashMap                | O(n)            |
| 3Sum                         | Sort + Two Pointers    | O(n²)           |
| Max Subarray                 | Kadane's               | O(n)            |
| Product Except Self          | Prefix + Suffix arrays | O(n)            |
| Rotate Array                 | Reverse three times    | O(n)            |
| Trapping Rain Water          | Two Pointers / Stack   | O(n)            |
| Merge Intervals              | Sort + greedy          | O(n log n)      |
| Longest Consecutive Sequence | HashSet                | O(n)            |

---

## 4. Strings

### 4.1 Key Operations

| Operation         | Technique                          | Complexity      |
|-------------------|------------------------------------|-----------------|
| Palindrome check  | Two pointers                       | O(n)            |
| Anagram check     | Frequency array / sort             | O(n)            |
| Substring search  | KMP / Rabin-Karp                   | O(n+m)          |
| Longest palindrome substring | Expand around center    | O(n²) / Manacher O(n) |
| Valid parentheses | Stack                              | O(n)            |

### 4.2 Sliding Window on Strings

```
// Longest substring without repeating characters
seen = {}
left = 0, maxLen = 0
for right in 0..n-1:
    if s[right] in seen and seen[s[right]] >= left:
        left = seen[s[right]] + 1
    seen[s[right]] = right
    maxLen = max(maxLen, right - left + 1)
```

### 4.3 KMP (Knuth-Morris-Pratt)

```
Build failure function (lps[]) — longest proper prefix that is also suffix.
Use lps[] to skip redundant comparisons.

lps[0] = 0
i = 1, len = 0
while i < m:
    if pattern[i] == pattern[len]: lps[i++] = ++len
    elif len != 0: len = lps[len-1]
    else: lps[i++] = 0

// Search: O(n+m) vs naive O(nm)
```

---

## 5. Linked Lists

### 5.1 Types

| Type              | Description                                    | Space |
|-------------------|------------------------------------------------|-------|
| Singly Linked     | Each node has `next` pointer                   | O(n)  |
| Doubly Linked     | Each node has `prev` and `next`                | O(n)  |
| Circular Singly   | Last node's `next` points to head              | O(n)  |

### 5.2 Operations Complexity

| Operation       | Singly LL | With Tail Pointer |
|-----------------|-----------|-------------------|
| Access by index | O(n)      | O(n)              |
| Prepend         | O(1)      | O(1)              |
| Append          | O(n)      | O(1)              |
| Delete head     | O(1)      | O(1)              |
| Delete by value | O(n)      | O(n)              |
| Search          | O(n)      | O(n)              |

### 5.3 Classic Techniques

#### Floyd's Cycle Detection (Fast/Slow Pointers)
```
slow = head, fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
    if slow == fast: CYCLE DETECTED

// Find cycle start:
slow = head
while slow != fast:
    slow = slow.next
    fast = fast.next
// slow == fast == cycle start
```

#### Reverse a Linked List
```
prev = null, curr = head
while curr:
    next = curr.next
    curr.next = prev
    prev = curr
    curr = next
return prev  // new head
```

#### Find Middle Node
```
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
return slow  // middle node
```

### 5.4 Common Problems

| Problem                        | Approach                        |
|--------------------------------|---------------------------------|
| Reverse Linked List            | Iterative prev/curr/next        |
| Detect Cycle                   | Floyd's two-pointer             |
| Find Cycle Start               | Floyd's + reset one pointer     |
| Merge Two Sorted Lists         | Two pointers + dummy head       |
| Reverse Nodes in k-Group       | Reverse in chunks               |
| Copy List with Random Pointers | HashMap node→copy OR interleave |
| LRU Cache                      | HashMap + Doubly Linked List    |

---

## 6. Stacks & Queues

### 6.1 Stack — LIFO

```
push(x)    → add to top   O(1)
pop()      → remove top   O(1)
peek/top() → view top     O(1)
isEmpty()  → check empty  O(1)
```

**Applications:**
- Function call stack / recursion simulation
- Undo/redo history
- Balanced parentheses checking
- Monotonic stack problems (next greater element)
- Postfix expression evaluation

#### Monotonic Stack (Next Greater Element)
```
result = [-1] * n
stack = []  // indices
for i in 0..n-1:
    while stack and arr[stack[-1]] < arr[i]:
        result[stack.pop()] = arr[i]
    stack.append(i)
```

### 6.2 Queue — FIFO

```
enqueue(x) → add to back  O(1)
dequeue()  → remove front O(1)
peek()     → view front   O(1)
```

**Applications:**
- BFS (breadth-first search)
- Level-order tree traversal
- Sliding window maximum (monotonic deque)
- Round-robin scheduling

### 6.3 Deque (Double-Ended Queue)

Supports push/pop at both ends in O(1). Used for:
- Sliding window maximum (`collections.deque` in Python)
- Palindrome checking
- BFS + DFS combined

---

## 7. Hashing

### 7.1 Hash Function

A **hash function** maps a key to a bucket index: `h(key) → [0, capacity)`.

**Properties of a good hash function:**
- **Deterministic** — same key always produces the same hash
- **Uniform distribution** — spreads keys evenly across buckets (minimizes collisions)
- **Fast to compute** — ideally O(1)
- **Avalanche effect** — small change in key → large change in hash

**Common approaches:**
| Key type   | Technique                                      |
|------------|------------------------------------------------|
| Integer    | `key % capacity` (use prime capacity)          |
| String     | Polynomial rolling hash: `Σ c[i] * p^i mod M` |
| Object     | Combine field hashes with XOR / multiply-shift |

### 7.2 Load Factor & Rehashing

```
load_factor = number_of_elements / number_of_buckets
```

- **Target load factor:** typically 0.75 (C++ `unordered_map` default is 1.0)
- When `load_factor > threshold`, **rehash**: allocate a new (usually 2×) table and reinsert all elements — amortized O(1) per insert
- Too low → wasted memory; too high → degraded O(n) performance from long chains

### 7.3 Collision Resolution

| Method              | Description                                           | Pros / Cons                                  |
|---------------------|-------------------------------------------------------|----------------------------------------------|
| **Separate Chaining** | Each bucket holds a linked list (or small vector)   | Simple; handles high load; extra pointer overhead |
| **Linear Probing**  | On collision, scan `+1, +2, …` until empty slot      | Cache-friendly; suffers from primary clustering |
| **Quadratic Probing** | Probe at `+1², +2², +3², …`                        | Reduces primary clustering; may not cover all slots |
| **Double Hashing**  | Second hash `h2(key)` determines probe step           | Minimizes clustering; requires careful `h2` design |
| **Robin Hood Hashing** | Displace entries with shorter probe distance       | Low variance probe length; used in fast open-address tables |

### 7.4 C++ Hash Map & Hash Set API

```cpp
#include <unordered_map>
#include <unordered_set>

// --- unordered_map<K, V> ---
unordered_map<string, int> freq;
freq["apple"]++;                     // insert or increment
freq.count("apple");                 // 1 if present, 0 otherwise
freq.find("apple") != freq.end();    // safe lookup
freq.erase("apple");                 // remove key
freq.size();                         // number of entries
for (auto& [key, val] : freq) { }   // range-for (C++17 structured binding)

// Default value on missing key (inserts 0)
freq["missing"];  // creates entry with value 0

// --- unordered_set<K> ---
unordered_set<int> seen;
seen.insert(42);
seen.count(42);                      // 1 if present
seen.erase(42);

// --- Performance hints ---
freq.reserve(1 << 16);               // pre-allocate buckets to avoid rehashes
freq.max_load_factor(0.25);          // lower → fewer collisions, more memory
```

### 7.5 Custom Hash Functions

The default `std::hash` covers built-in types. For custom types or better performance, supply your own:

```cpp
// Option 1: inject into std namespace (for pair<int,int>)
struct PairHash {
    size_t operator()(const pair<int,int>& p) const {
        size_t h1 = hash<int>{}(p.first);
        size_t h2 = hash<int>{}(p.second);
        return h1 ^ (h2 * 2654435761ULL);   // Knuth multiplicative hash
    }
};
unordered_map<pair<int,int>, int, PairHash> dp;

// Option 2: combine hashes with boost-style seed mixing
size_t hashCombine(size_t seed, size_t val) {
    return seed ^ (val + 0x9e3779b9 + (seed << 6) + (seed >> 2));
}

// Option 3: anti-hack hash (randomize to defeat adversarial inputs)
struct SafeHash {
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM =
            chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};
unordered_map<int, int, SafeHash> safe_map;
```

### 7.6 Polynomial Rolling Hash (String Hashing)

Used for **O(1) substring comparison** and duplicate detection.

```cpp
// Precompute prefix hashes for string s
// hash(s[l..r]) = (H[r+1] - H[l] * P[r-l+1]) % MOD
const long long MOD = 1e9 + 7, BASE = 31;

string s = "...";
int n = s.size();
vector<long long> H(n + 1, 0), P(n + 1, 1);

for (int i = 0; i < n; i++) {
    H[i+1] = (H[i] * BASE + (s[i] - 'a' + 1)) % MOD;
    P[i+1] = P[i] * BASE % MOD;
}

// Hash of substring s[l..r] (0-indexed, inclusive)
auto getHash = [&](int l, int r) -> long long {
    return (H[r+1] - H[l] * P[r-l+1] % MOD + MOD * 2) % MOD;
};

// Use two different (BASE, MOD) pairs to reduce collision probability
```

**Applications:** Longest duplicate substring (binary search + rolling hash), Rabin-Karp pattern matching.

### 7.7 Common Hashing Patterns (C++)

```cpp
// --- Frequency count ---
unordered_map<char, int> freq;
for (char c : s) freq[c]++;

// --- Two Sum ---
unordered_map<int, int> seen;  // value → index
for (int i = 0; i < (int)nums.size(); i++) {
    int complement = target - nums[i];
    if (seen.count(complement))
        return {seen[complement], i};
    seen[nums[i]] = i;
}

// --- Anagram grouping ---
unordered_map<string, vector<string>> groups;
for (auto& word : words) {
    string key = word;
    sort(key.begin(), key.end());
    groups[key].push_back(word);
}

// --- Subarray sum equals k (prefix sum + hash map) ---
unordered_map<int, int> prefixCount{{0, 1}};
int sum = 0, count = 0;
for (int num : nums) {
    sum += num;
    count += prefixCount[sum - k];
    prefixCount[sum]++;
}

// --- Longest consecutive sequence ---
unordered_set<int> numSet(nums.begin(), nums.end());
int longest = 0;
for (int n : numSet) {
    if (!numSet.count(n - 1)) {       // start of a sequence
        int len = 1;
        while (numSet.count(n + len)) len++;
        longest = max(longest, len);
    }
}
```

### 7.8 Classic Hashing Problems

| Problem                          | Key Idea                                              | Complexity   |
|----------------------------------|-------------------------------------------------------|--------------|
| Two Sum                          | Store `value → index`; look up complement             | O(n)         |
| Group Anagrams                   | Sorted string (or char freq array) as key             | O(n·k log k) |
| Subarray Sum Equals K            | Prefix sums + frequency map                           | O(n)         |
| Longest Consecutive Sequence     | Set lookup for sequence start                         | O(n)         |
| First Non-Repeating Character    | Ordered map / two-pass frequency count                | O(n)         |
| 4-Sum Count (4 arrays)           | Split into two pairs; store pair-sum counts           | O(n²)        |
| Rabin-Karp String Match          | Rolling hash for O(n+m) average pattern search        | O(n+m)       |
| Longest Duplicate Substring      | Binary search length + rolling hash                   | O(n log n)   |
| Minimum Window Substring         | Sliding window + character frequency maps             | O(n)         |
| LRU Cache                        | `unordered_map` + doubly-linked list                  | O(1)         |

---

## 8. Trees

### 8.1 Binary Tree

```
Node { value, left, right }

Height: max edges from root to leaf
Depth:  edges from root to node
Full:   every node has 0 or 2 children
Complete: all levels filled except last (filled left to right)
Perfect: all internal nodes have 2 children; all leaves at same level
Balanced: |height(left) - height(right)| ≤ 1 for all nodes
```

### 8.2 Tree Traversals

```
// Inorder: Left → Root → Right (BST gives sorted order)
def inorder(node):
    if not node: return
    inorder(node.left)
    visit(node)
    inorder(node.right)

// Preorder: Root → Left → Right (copy tree, serialize)
// Postorder: Left → Right → Root (delete tree, evaluate expression)

// Level-order (BFS)
queue = deque([root])
while queue:
    node = queue.popleft()
    visit(node)
    if node.left:  queue.append(node.left)
    if node.right: queue.append(node.right)

// Iterative Inorder (using stack)
stack, curr = [], root
while curr or stack:
    while curr:
        stack.append(curr)
        curr = curr.left
    curr = stack.pop()
    visit(curr)
    curr = curr.right
```

### 8.3 Binary Search Tree (BST)

**BST Property:** left subtree < node < right subtree

| Operation | Average | Worst (skewed) |
|-----------|---------|----------------|
| Search    | O(log n)| O(n)           |
| Insert    | O(log n)| O(n)           |
| Delete    | O(log n)| O(n)           |

**BST Delete:**
- Node has no children → simply remove
- Node has one child → replace with child
- Node has two children → replace with **inorder successor** (smallest in right subtree)

### 8.4 AVL Tree

Self-balancing BST. After insert/delete, **rotations** restore balance factor.

```
Balance Factor = height(left) - height(right)
Valid range: {-1, 0, 1}

Rotations:
- Left Rotation:  right-heavy → rotate left
- Right Rotation: left-heavy  → rotate right
- Left-Right:     left child is right-heavy → left rotate child, then right rotate
- Right-Left:     right child is left-heavy → right rotate child, then left rotate
```

### 8.5 Lowest Common Ancestor (LCA)

```
def lca(root, p, q):
    if not root or root == p or root == q: return root
    left  = lca(root.left,  p, q)
    right = lca(root.right, p, q)
    if left and right: return root  // p in one subtree, q in other
    return left or right
```

### 8.6 Common Tree Problems

| Problem                         | Key Idea                           |
|---------------------------------|------------------------------------|
| Max depth / height              | DFS; max(left, right) + 1          |
| Diameter of binary tree         | Max left_depth + right_depth at each node |
| Balanced tree check             | Post-order; return -1 for unbalanced |
| Same tree                       | Recursively compare all nodes      |
| Invert binary tree              | Swap children recursively          |
| Path sum                        | DFS subtract target; return true at leaf |
| Serialize/Deserialize tree      | Preorder + null markers            |
| Validate BST                    | Pass min/max bounds through recursion |

---

## 9. Heaps (Priority Queue)

### 9.1 Properties

- **Min-heap:** parent ≤ children; root is the minimum
- **Max-heap:** parent ≥ children; root is the maximum
- Stored as array: parent at `i`, children at `2i+1`, `2i+2`

| Operation    | Time    |
|--------------|---------|
| insert       | O(log n)|
| extract-min/max | O(log n)|
| peek         | O(1)    |
| build heap   | O(n)    |
| heapify      | O(log n)|

### 9.2 Common Patterns

```cpp
#include <queue>
#include <vector>

// Min-heap
priority_queue<int, vector<int>, greater<int>> minHeap;
minHeap.push(3);
minHeap.push(1);
minHeap.push(2);
minHeap.top();   // returns 1
minHeap.pop();

// Max-heap (default)
priority_queue<int> maxHeap;
maxHeap.push(val);
maxHeap.top();

// K largest elements: use min-heap of size k — O(n log k)

// Heap from vector (make_heap)
make_heap(nums.begin(), nums.end());  // O(n), max-heap
```

### 9.3 Top-K Problems

```
// K most frequent elements
freq = Counter(nums)
return heapq.nlargest(k, freq.keys(), key=freq.get)

// K closest points to origin
// Sort by distance² to avoid sqrt
return heapq.nsmallest(k, points, key=lambda p: p[0]**2 + p[1]**2)

// Merge K sorted lists (using heap)
heap = [(lists[i][0], i, 0) for i in range(len(lists)) if lists[i]]
heapq.heapify(heap)
result = []
while heap:
    val, i, j = heapq.heappop(heap)
    result.append(val)
    if j+1 < len(lists[i]):
        heapq.heappush(heap, (lists[i][j+1], i, j+1))
```

### 9.4 Median from Data Stream

```
// Two heaps: max-heap for lower half, min-heap for upper half
lower = []  // max-heap (negate values)
upper = []  // min-heap

def addNum(num):
    heappush(lower, -num)
    heappush(upper, -heappop(lower))
    if len(lower) < len(upper):
        heappush(lower, -heappop(upper))

def findMedian():
    if len(lower) > len(upper): return -lower[0]
    return (-lower[0] + upper[0]) / 2
```

---

## 10. Graphs

### 10.1 Representations

```cpp
// Adjacency List (space-efficient for sparse graphs)
vector<vector<pair<int,int>>> graph(n);
graph[0].push_back({1, weight});
graph[1].push_back({0, weight});

// Adjacency Matrix (fast edge lookup for dense graphs)
vector<vector<int>> matrix(n, vector<int>(n, 0));
matrix[0][1] = weight;

// Edge List
vector<tuple<int,int,int>> edges = {{0, 1, weight}, {1, 2, weight}};
```

### 10.2 Graph Traversals

#### BFS (Breadth-First Search)
```
// Shortest path (unweighted), level-order traversal
visited = set([start])
queue = deque([start])
while queue:
    node = queue.popleft()
    for neighbor in graph[node]:
        if neighbor not in visited:
            visited.add(neighbor)
            queue.append(neighbor)
```

#### DFS (Depth-First Search)
```
// Connectivity, cycle detection, topological sort
visited = set()
def dfs(node):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor)

// Iterative DFS
stack = [start]
visited = set([start])
while stack:
    node = stack.pop()
    for neighbor in graph[node]:
        if neighbor not in visited:
            visited.add(neighbor)
            stack.append(neighbor)
```

### 10.3 Shortest Path Algorithms

#### Dijkstra's Algorithm (non-negative weights)
```
// O((V + E) log V) with min-heap
dist = {v: inf for v in graph}; dist[src] = 0
heap = [(0, src)]
while heap:
    d, u = heappop(heap)
    if d > dist[u]: continue
    for v, w in graph[u]:
        if dist[u] + w < dist[v]:
            dist[v] = dist[u] + w
            heappush(heap, (dist[v], v))
```

#### Bellman-Ford (handles negative weights)
```
// O(VE); detects negative cycles
dist = [inf] * n; dist[src] = 0
for _ in range(n-1):
    for u, v, w in edges:
        if dist[u] + w < dist[v]:
            dist[v] = dist[u] + w
// Check negative cycle: one more relaxation changes a distance
```

#### Floyd-Warshall (all-pairs shortest path)
```
// O(V³)
dist[i][j] = weight of direct edge (or inf)
for k in range(n):
    for i in range(n):
        for j in range(n):
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

### 10.4 Minimum Spanning Tree

#### Kruskal's Algorithm
```
// O(E log E) — sort edges; use Union-Find to avoid cycles
Sort edges by weight
For each edge (u, v, w) in sorted order:
    if find(u) != find(v):
        union(u, v)
        MST.add(edge)
```

#### Prim's Algorithm
```
// O((V + E) log V) — grow MST from a seed vertex using min-heap
heap = [(0, 0)]  // (weight, vertex)
inMST = set()
while heap:
    w, u = heappop(heap)
    if u in inMST: continue
    inMST.add(u)
    for v, weight in graph[u]:
        if v not in inMST:
            heappush(heap, (weight, v))
```

### 10.5 Topological Sort

For **Directed Acyclic Graphs (DAGs)** — linear ordering where all edges go forward.

```
// Kahn's Algorithm (BFS-based)
in_degree = [0] * n
for u in range(n):
    for v in graph[u]:
        in_degree[v] += 1

queue = deque([v for v in range(n) if in_degree[v] == 0])
order = []
while queue:
    u = queue.popleft()
    order.append(u)
    for v in graph[u]:
        in_degree[v] -= 1
        if in_degree[v] == 0:
            queue.append(v)

if len(order) != n: "Cycle detected"

// DFS-based: push to stack after visiting all descendants; reverse stack
```

### 10.6 Union-Find (Disjoint Set Union)

```cpp
class UnionFind {
    vector<int> parent, rank;
public:
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);  // path compression
        return parent[x];
    }
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
        return true;
    }
};
// Operations: near O(1) amortized with path compression + union by rank
```

**Applications:** Connected components, cycle detection, Kruskal's MST.

---

## 11. Sorting Algorithms

### 11.1 Comparison-Based Sorts

| Algorithm     | Best     | Average   | Worst    | Space  | Stable |
|---------------|----------|-----------|----------|--------|--------|
| Bubble Sort   | O(n)     | O(n²)     | O(n²)    | O(1)   | Yes    |
| Selection Sort| O(n²)    | O(n²)     | O(n²)    | O(1)   | No     |
| Insertion Sort| O(n)     | O(n²)     | O(n²)    | O(1)   | Yes    |
| Merge Sort    | O(n log n)| O(n log n)| O(n log n)| O(n)  | Yes    |
| Quick Sort    | O(n log n)| O(n log n)| O(n²)   | O(log n)| No   |
| Heap Sort     | O(n log n)| O(n log n)| O(n log n)| O(1) | No     |
| Tim Sort      | O(n)     | O(n log n)| O(n log n)| O(n)  | Yes    |

### 11.2 Merge Sort

```
def mergeSort(arr):
    if len(arr) <= 1: return arr
    mid = len(arr) // 2
    left  = mergeSort(arr[:mid])
    right = mergeSort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]: result.append(left[i]); i++
        else:                   result.append(right[j]); j++
    return result + left[i:] + right[j:]
```

### 11.3 Quick Sort

```
def quickSort(arr, low, high):
    if low < high:
        pi = partition(arr, low, high)
        quickSort(arr, low, pi-1)
        quickSort(arr, pi+1, high)

def partition(arr, low, high):
    pivot = arr[high]  // Lomuto partition
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i++; arr[i], arr[j] = arr[j], arr[i]
    arr[i+1], arr[high] = arr[high], arr[i+1]
    return i+1
```

**Optimization:** Random pivot selection to avoid O(n²) worst case.

### 11.4 Non-Comparison Sorts

| Algorithm      | Time    | Space  | Constraint                      |
|----------------|---------|--------|---------------------------------|
| Counting Sort  | O(n+k)  | O(k)   | Integer keys in range [0, k]    |
| Radix Sort     | O(nk)   | O(n+k) | Integer keys; k = max digits    |
| Bucket Sort    | O(n+k)  | O(n)   | Uniformly distributed floats    |

---

## 12. Searching Algorithms

### 12.1 Binary Search

```cpp
int binarySearch(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;  // avoid overflow
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

### 12.2 Binary Search Variants

```cpp
// Find leftmost position where condition is true (lower bound)
int lowerBound(vector<int>& arr, int target) {
    int left = 0, right = arr.size();
    while (left < right) {
        int mid = (left + right) / 2;
        if (arr[mid] < target) left = mid + 1;
        else right = mid;
    }
    return left;
}

// Find rightmost position where arr[mid] <= target (upper bound)
int upperBound(vector<int>& arr, int target) {
    int left = 0, right = arr.size();
    while (left < right) {
        int mid = (left + right) / 2;
        if (arr[mid] <= target) left = mid + 1;
        else right = mid;
    }
    return left;
}

// Binary search on answer space
// "Find minimum X such that condition(X) is true"
int minValue(int MIN_POSSIBLE, int MAX_POSSIBLE) {
    int left = MIN_POSSIBLE, right = MAX_POSSIBLE;
    while (left < right) {
        int mid = (left + right) / 2;
        if (condition(mid)) right = mid;
        else left = mid + 1;
    }
    return left;
}
```

---

## 13. Recursion & Backtracking

### 13.1 Recursion Template

```
def solve(params):
    # Base case
    if base_condition: return base_result

    # Recursive case
    result = combine(solve(smaller_params), ...)
    return result
```

### 13.2 Backtracking Template

```cpp
void backtrack(State& state, vector<Choice>& choices) {
    if (isSolution(state)) {
        record(state);
        return;
    }
    for (auto& choice : choices) {
        if (isValid(choice, state)) {
            makeChoice(state, choice);
            backtrack(state, remainingChoices);
            undoChoice(state, choice);          // backtrack!
        }
    }
}
```

### 13.3 Classic Backtracking Problems

#### Permutations
```cpp
vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> path;
    vector<bool> used(nums.size(), false);
    function<void()> backtrack = [&]() {
        if (path.size() == nums.size()) {
            result.push_back(path);
            return;
        }
        for (int i = 0; i < (int)nums.size(); i++) {
            if (!used[i]) {
                used[i] = true;
                path.push_back(nums[i]);
                backtrack();
                path.pop_back();
                used[i] = false;
            }
        }
    };
    backtrack();
    return result;
}
```

#### Subsets
```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> path;
    function<void(int)> backtrack = [&](int start) {
        result.push_back(path);
        for (int i = start; i < (int)nums.size(); i++) {
            path.push_back(nums[i]);
            backtrack(i + 1);
            path.pop_back();
        }
    };
    backtrack(0);
    return result;
}
```

#### N-Queens
```cpp
vector<vector<string>> solveNQueens(int n) {
    vector<vector<string>> result;
    vector<string> board(n, string(n, '.'));
    unordered_set<int> cols, diag1, diag2;
    function<void(int)> backtrack = [&](int row) {
        if (row == n) {
            result.push_back(board);
            return;
        }
        for (int col = 0; col < n; col++) {
            if (cols.count(col) || diag1.count(row-col) || diag2.count(row+col))
                continue;
            cols.insert(col); diag1.insert(row-col); diag2.insert(row+col);
            board[row][col] = 'Q';
            backtrack(row + 1);
            board[row][col] = '.';
            cols.erase(col); diag1.erase(row-col); diag2.erase(row+col);
        }
    };
    backtrack(0);
    return result;
}
```

---

## 14. Dynamic Programming

### 14.1 Framework

1. **Define state:** `dp[i]` = answer to subproblem of size i
2. **Recurrence relation:** how dp[i] depends on smaller states
3. **Base case:** smallest valid state
4. **Order of computation:** ensure dependencies computed first

**Top-Down (Memoization) vs Bottom-Up (Tabulation):**
- Memoization: recursive; only compute needed states; easier to reason
- Tabulation: iterative; no recursion overhead; sometimes O(1) space with rolling array

### 14.2 1D DP Patterns

#### Fibonacci / Climbing Stairs
```
dp[i] = dp[i-1] + dp[i-2]   // reach stair i from i-1 or i-2
```

#### House Robber
```
dp[i] = max(dp[i-1], dp[i-2] + nums[i])
// either skip house i, or rob it (can't rob i-1)
```

#### Longest Increasing Subsequence (LIS)
```
// O(n²) DP
dp[i] = max(dp[j]+1) for all j < i where nums[j] < nums[i]

// O(n log n) with patience sorting (binary search)
tails = []
for num in nums:
    pos = bisect_left(tails, num)
    if pos == len(tails): tails.append(num)
    else: tails[pos] = num
return len(tails)
```

### 14.3 2D DP Patterns

#### Longest Common Subsequence (LCS)
```
dp[i][j] = length of LCS of s1[:i] and s2[:j]

if s1[i-1] == s2[j-1]: dp[i][j] = dp[i-1][j-1] + 1
else:                   dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

#### Edit Distance
```
dp[i][j] = min operations to convert s1[:i] to s2[:j]

if s1[i-1] == s2[j-1]: dp[i][j] = dp[i-1][j-1]
else: dp[i][j] = 1 + min(dp[i-1][j],    // delete from s1
                         dp[i][j-1],    // insert into s1
                         dp[i-1][j-1]) // replace
```

#### 0/1 Knapsack
```
dp[i][w] = max value using first i items with weight capacity w

if weight[i] > w: dp[i][w] = dp[i-1][w]
else: dp[i][w] = max(dp[i-1][w], dp[i-1][w-weight[i]] + value[i])

// Space optimized — iterate weights in reverse
for i in range(n):
    for w in range(W, weight[i]-1, -1):
        dp[w] = max(dp[w], dp[w-weight[i]] + value[i])
```

### 14.4 Classic DP Problems

| Problem                        | State                                   | Transition                            |
|--------------------------------|-----------------------------------------|---------------------------------------|
| Coin Change                    | `dp[amount]` = min coins                | `dp[a] = min(dp[a-coin]+1)` for each coin |
| Unique Paths                   | `dp[i][j]` = paths to cell             | `dp[i][j] = dp[i-1][j] + dp[i][j-1]` |
| Word Break                     | `dp[i]` = can break s[:i]              | Try all words ending at i             |
| Palindrome Partitioning        | `dp[i][j]` = is palindrome            | Expand; `dp[i][j] = dp[i+1][j-1] and s[i]==s[j]` |
| Stock Buy/Sell                 | State: day + hold/not                  | Choose action each day                |
| Matrix Chain Multiplication    | `dp[i][j]` = min multiplications      | Split at each k between i and j       |

---

## 15. Greedy Algorithms

### 15.1 When to Use Greedy

Greedy works when a **locally optimal choice** leads to a **globally optimal solution**. Prove via exchange argument or matroid theory.

### 15.2 Classic Greedy Problems

| Problem                    | Greedy Choice                             |
|----------------------------|-------------------------------------------|
| Activity Selection         | Always pick activity with earliest end time |
| Fractional Knapsack        | Sort by value/weight ratio; take greedily |
| Huffman Coding             | Merge two lowest-frequency nodes first    |
| Jump Game                  | Track max reachable index at each step    |
| Gas Station Circuit        | Start from any point; reset if tank < 0  |
| Interval Scheduling        | Sort by end time; pick non-overlapping    |
| Minimum Platforms          | Sort arrivals/departures; track count     |

#### Jump Game II (Min Jumps)
```
jumps = 0, currentEnd = 0, farthest = 0
for i in range(n-1):
    farthest = max(farthest, i + nums[i])
    if i == currentEnd:
        jumps++
        currentEnd = farthest
return jumps
```

---

## 16. Trie (Prefix Tree)

```cpp
struct TrieNode {
    unordered_map<char, TrieNode*> children;
    bool is_end = false;
};

class Trie {
    TrieNode* root;
public:
    Trie() : root(new TrieNode()) {}

    void insert(const string& word) {
        TrieNode* node = root;
        for (char c : word)
            node = node->children.emplace(c, new TrieNode()).first->second;
        node->is_end = true;
    }

    bool search(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            if (!node->children.count(c)) return false;
            node = node->children[c];
        }
        return node->is_end;
    }

    bool startsWith(const string& prefix) {
        TrieNode* node = root;
        for (char c : prefix) {
            if (!node->children.count(c)) return false;
            node = node->children[c];
        }
        return true;
    }
};
```

**Applications:** Autocomplete, spell check, IP routing, word search in grid, longest prefix matching.

---

## 17. Segment Tree & Binary Indexed Tree (BIT)

### 17.1 Segment Tree

- Supports **range queries** and **point updates** in O(log n)
- Build: O(n); each node stores aggregate for a range

```cpp
class SegmentTree {
    vector<int> tree;
    void build(vector<int>& arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = (start + end) / 2;
            build(arr, 2*node, start, mid);
            build(arr, 2*node+1, mid+1, end);
            tree[node] = tree[2*node] + tree[2*node+1];
        }
    }
public:
    SegmentTree(vector<int>& arr) : tree(4 * arr.size(), 0) {
        build(arr, 1, 0, arr.size() - 1);
    }
    int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node];
        int mid = (start + end) / 2;
        return query(2*node, start, mid, l, r) +
               query(2*node+1, mid+1, end, l, r);
    }
};
```

### 17.2 Binary Indexed Tree (Fenwick Tree)

Simpler implementation; supports **prefix sum queries** and **point updates** in O(log n).

```cpp
class BIT {
    vector<int> tree;
public:
    BIT(int n) : tree(n + 1, 0) {}

    void update(int i, int delta) {
        for (; i < (int)tree.size(); i += i & (-i))  // add lowest set bit
            tree[i] += delta;
    }

    int query(int i) {  // prefix sum [1..i]
        int total = 0;
        for (; i > 0; i -= i & (-i))  // remove lowest set bit
            total += tree[i];
        return total;
    }

    int rangeQuery(int l, int r) {
        return query(r) - query(l - 1);
    }
};
```

---

## 18. Bit Manipulation

### 18.1 Bitwise Operators

| Operator | Symbol | Example (5=101, 3=011) | Result |
|----------|--------|------------------------|--------|
| AND      | &      | 101 & 011              | 001 = 1 |
| OR       | \|     | 101 \| 011             | 111 = 7 |
| XOR      | ^      | 101 ^ 011              | 110 = 6 |
| NOT      | ~      | ~101                   | ...11111010 |
| Left shift | <<   | 101 << 1               | 1010 = 10 |
| Right shift | >>  | 101 >> 1               | 010 = 2 |

### 18.2 Common Tricks

```cpp
n & (n-1)              // Clear lowest set bit; n==0 if n is power of 2
n & (-n)               // Isolate lowest set bit
n ^ n == 0             // XOR with itself = 0
n ^ 0 == n             // XOR with 0 = n
a ^ b ^ a == b         // XOR cancels duplicates (find single number)
(n >> i) & 1           // Check if i-th bit is set
n | (1 << i)           // Set i-th bit
n & ~(1 << i)          // Clear i-th bit
n ^ (1 << i)           // Toggle i-th bit
__builtin_popcount(n)  // Count set bits (Hamming weight)
```

### 18.3 Classic Bit Problems

| Problem                       | Trick                                    |
|-------------------------------|------------------------------------------|
| Single Number (all others 2x) | XOR all numbers; duplicates cancel       |
| Single Number (all others 3x) | Count bits mod 3                         |
| Count set bits                | `n & (n-1)` loop                         |
| Power of two                  | `n > 0 and (n & (n-1)) == 0`             |
| Reverse bits                  | Extract LSB, shift result left, repeat 32x |
| Sum of two integers without + | `a ^ b` (no carry) + `(a & b) << 1` (carry) |

---

## 19. Math & Number Theory

### 19.1 Essential Math for DSA

```cpp
// GCD (Euclidean algorithm) — O(log min(a,b))
int gcd(int a, int b) {
    while (b) { int t = b; b = a % b; a = t; }
    return a;
}

// LCM
int lcm(int a, int b) { return a / gcd(a, b) * b; }

// Power with modular exponentiation — O(log exp)
long long power(long long base, long long exp, long long mod) {
    long long result = 1;
    base %= mod;
    while (exp > 0) {
        if (exp % 2 == 1) result = result * base % mod;
        base = base * base % mod;
        exp /= 2;
    }
    return result;
}

// Sieve of Eratosthenes — all primes up to n in O(n log log n)
vector<int> sieve(int n) {
    vector<bool> is_prime(n + 1, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; (long long)i * i <= n; i++)
        if (is_prime[i])
            for (int j = i * i; j <= n; j += i)
                is_prime[j] = false;
    vector<int> primes;
    for (int i = 2; i <= n; i++)
        if (is_prime[i]) primes.push_back(i);
    return primes;
}
```

### 19.2 Combinatorics

```cpp
// Combinations C(n, k) with Pascal's Triangle / DP
C[0][0] = 1;
C[n][k] = C[n-1][k-1] + C[n-1][k];

// nCr with modular inverse (when mod is prime)
// C(n, r) = n! / (r! * (n-r)!) mod p
// Use Fermat's little theorem: a^(-1) ≡ a^(p-2) mod p
```

---

## 20. Problem-Solving Patterns Cheat Sheet

| Pattern               | Problems it Solves                                  | Hint to Recognize           |
|-----------------------|-----------------------------------------------------|-----------------------------|
| Two Pointers          | Sorted array pair sum, container with most water    | Sorted input, find pair     |
| Sliding Window        | Subarray/substring with constraint                  | Contiguous subarray         |
| Prefix Sum            | Range sum queries                                   | Multiple range queries      |
| Binary Search         | Search in sorted/monotonic space                    | Sorted, O(log n) asked      |
| BFS                   | Shortest path (unweighted), level-order             | Shortest, level-by-level    |
| DFS / Backtracking    | All paths, permutations, combinations               | Explore all possibilities   |
| Dynamic Programming   | Optimization with overlapping subproblems           | Max/min, count ways         |
| Greedy                | Activity selection, interval scheduling             | Local optimal = global      |
| Monotonic Stack       | Next greater/smaller element, histogram             | Range span, NGE problems    |
| Heap / Priority Queue | K-th largest, streaming median                      | K-th, top-K, streaming      |
| Union-Find            | Connected components, cycle detection               | Group/connect elements      |
| Trie                  | Prefix matching, autocomplete                       | String prefix queries       |
| Bit Manipulation      | XOR tricks, subset enumeration                      | Single number, power of 2   |
| Segment Tree / BIT    | Range queries with updates                          | Range sum/min/max + updates |
