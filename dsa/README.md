# DSA — Complete Online Assessment & Interview Preparation

This folder is a **beginner-to-advanced C++ DSA textbook**, not a quick cheat sheet.

The intended learner may know little or no DSA. Each chapter therefore builds from fundamentals and gradually reaches online-assessment and interview-level problems.

## Learning order

1. [01. Arrays](./01-arrays.md)
2. [02. Binary Search](./02-binary-search.md)
3. [03. Sorting](./03-sorting.md)
4. [04. Linked List](./04-linked-list.md)
5. [05. Stack](./05-stack.md)
6. [06. Queue & Deque](./06-queue-deque.md)
7. [07. Hashing](./07-hashing.md)
8. [08. Two Pointers & Sliding Window](./08-two-pointers-sliding-window.md)
9. [09. Recursion & Backtracking](./09-recursion-backtracking.md)
10. [10. Trees](./10-trees.md)
11. [11. Binary Search Tree](./11-bst.md)
12. [12. Heap & Priority Queue](./12-heap-priority-queue.md)
13. [13. Greedy](./13-greedy.md)
14. [14. Graphs](./14-graphs.md)
15. [15. Trie](./15-trie.md)
16. [16. Dynamic Programming](./16-dynamic-programming.md)
17. [17. Bit Manipulation](./17-bit-manipulation.md)
18. [18. Advanced Data Structures](./18-advanced-data-structures.md)

> **Study rule:** do not treat the numbered list as a checklist to rush through. Master the beginner section of a chapter before moving to its advanced section.

---

# What Every Chapter Contains

Every major topic follows a consistent teaching structure:

```text
1. What is the concept?
2. Why does it exist?
3. How does it work internally?
4. Basic operations / implementation
5. Beginner examples
6. Brute-force approach
7. Bottleneck in brute force
8. Optimization insight
9. Pattern / invariant
10. Canonical C++ implementation
11. Dry-run reasoning
12. Correctness intuition
13. Time complexity
14. Space complexity
15. Edge cases
16. Common bugs
17. Beginner questions
18. Intermediate questions
19. Advanced interview/OA questions
20. Interview follow-ups
21. Mastery test
```

The goal is to make the notes usable even if you have never seen the technique before.

---

# How to Solve Every Coding Problem

Before touching the keyboard:

### Step 1 — Understand the problem
Write down exactly what is given and what must be returned.

### Step 2 — Read constraints
Constraints often tell you the expected complexity.

A rough guide:

```text
n <= 20       → exponential may be possible
n <= 100      → O(n^3) may be possible
n <= 1,000    → O(n^2) may be possible
n <= 100,000  → usually O(n log n) or O(n)
n >= 1e6      → usually close to O(n)
```

These are heuristics, not laws; always consider the actual operation cost.

### Step 3 — Build brute force
First find a correct simple solution. This tells you what work is being repeated.

### Step 4 — Identify the bottleneck
Ask:

```text
What is unnecessarily repeated?
Can I store it?
Can I maintain it?
Can I sort it?
Can I discard candidates permanently?
```

### Step 5 — Recognize the pattern
Common signals:

| Problem signal | First pattern to consider |
|---|---|
| Sorted data | Binary search / two pointers |
| Contiguous range | Sliding window / prefix sum |
| Frequency/membership | Hash map/set |
| Next greater/smaller | Monotonic stack |
| Top K | Heap |
| Level/minimum edges | BFS |
| Non-negative weighted shortest path | Dijkstra |
| Dependencies | Topological sort |
| Merge connectivity | DSU |
| All combinations | Backtracking |
| Repeated states | DP |
| Prefix queries | Trie |
| Minimum possible maximum | Binary search on answer |

### Step 6 — State the invariant
An invariant is something that remains true throughout the algorithm.

Examples:

```text
Two pointers → discarded region cannot contain the answer.
Sliding window → current window satisfies the maintained condition.
Binary search → answer remains inside the search interval.
Monotonic stack → stored candidates remain monotonic.
BFS → discovered distance is minimum edge distance.
DP → dp[state] has exactly the meaning we defined.
```

### Step 7 — State TC/SC before coding
This forces you to understand your algorithm instead of accidentally writing an inefficient solution.

### Step 8 — Code
Use the canonical implementation from the chapter as a starting point, then adapt it to the problem.

### Step 9 — Dry run edge cases
At minimum test:

```text
empty input
one element
minimum valid input
maximum-ish input
all equal
duplicates
zeros
negative values where allowed
already sorted
reverse sorted
answer at first/last position
no valid answer
```

---

# How to Use the Problem Banks

Problems are intentionally divided into:

### Beginner
You should understand the data structure and implement the basic pattern.

### Intermediate
You should recognize which known pattern applies with less prompting.

### Advanced
You should be able to derive the solution, justify correctness, and discuss alternatives.

For an important question:

```text
Attempt without notes
        ↓
If stuck: identify the pattern only
        ↓
Try again
        ↓
Read explanation
        ↓
Understand invariant
        ↓
Code yourself
        ↓
Compare TC/SC
        ↓
Re-solve later
```

Do not copy the code and count it as solved.

---

# Interview Answer Format

When an interviewer gives a problem, structure your explanation like this:

```text
1. Clarify assumptions and constraints.
2. Explain brute force.
3. State why brute force is too slow.
4. Identify the pattern/data structure.
5. Explain the invariant.
6. Explain the algorithm in plain English.
7. State TC and SC.
8. Code.
9. Dry run a small example.
10. Discuss edge cases and alternatives.
```

This communication structure is part of DSA preparation because an interview evaluates reasoning, not just accepted code.

---

# C++ Policy

These notes intentionally use **C++ only** for implementations.

Know the standard tools:

```text
vector
array
string
pair / tuple
stack
queue
priority_queue
deque
set / multiset
map / multimap
unordered_set
unordered_map
sort
lower_bound / upper_bound
reverse
accumulate
iota
```

Also understand:

- references
- pointers
- const correctness
- custom comparators
- lambda functions
- integer overflow
- recursion stack
- iterator invalidation
- STL complexity

The objective is not to memorize STL syntax. It is to know when each tool supports the required operation efficiently.

---

# Complexity Rules

Always distinguish:

- input space
- auxiliary space
- recursion stack
- output space

For example, returning `O(n)` results does not mean the algorithm necessarily uses O(n) auxiliary memory.

Also distinguish:

- worst-case
- average/expected
- amortized

For example, hash-table lookup is generally expected O(1), not an unconditional worst-case O(1) guarantee.

---

# Mastery Standard

You have mastered a chapter when you can:

- explain the concept to a beginner
- implement the basic operation without notes
- identify the pattern from a new problem
- derive the optimized solution from brute force
- state and defend the invariant
- explain correctness
- calculate TC and SC
- identify edge cases
- solve representative OA problems without memorizing exact code
- answer common interview follow-ups

> **Goal:** This folder should be sufficient to take a learner from “I don't know this topic” to “I can recognize, derive, code, and explain the standard interview problems from this topic.”
