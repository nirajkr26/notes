# 09. Recursion & Backtracking — From First Principles to Search Problems

Recursion is a way of solving a problem by asking the same function to solve a smaller problem. Backtracking extends this idea to **explore choices, undo the choice, and explore another choice**.

These topics are essential for trees, divide-and-conquer, permutations, subsets, combination problems, N-Queens, Sudoku, and many OA problems.

---

# 1. How Recursion Works

A recursive function calls itself with a smaller/simpler state.

Every correct recursion needs:

1. A **base case**.
2. A **progress rule** that moves toward the base case.
3. A **recursive relationship** that combines smaller results.

Example:

```cpp
int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

For `factorial(4)`:

```text
4 * factorial(3)
4 * 3 * factorial(2)
4 * 3 * 2 * factorial(1)
4 * 3 * 2 * 1
```

**TC:** O(n)  
**SC:** O(n) recursion stack

---

# 2. Call Stack

Each recursive call creates a stack frame containing local variables, parameters, and return information.

Deep recursion therefore consumes stack memory.

A function with recursion depth `h` generally uses O(h) stack space.

### Common interview question
Why can an O(n)-time recursive algorithm also be O(n)-space?

Because its calls remain active on the call stack until the base case returns.

---

# 3. Recursion Tree

For:

```cpp
f(n) {
    f(n-1);
    f(n-1);
}
```

there are roughly `2^n` calls.

Understanding the recursion tree helps estimate complexity.

For example, naive Fibonacci:

```cpp
int fib(int n) {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```

has exponential time because the same subproblems are recomputed repeatedly.

**TC:** O(2^n) approximately  
**SC:** O(n)

Dynamic programming later fixes the repeated-state problem.

---

# 4. Recursion vs Iteration

Use recursion when the problem naturally has hierarchical/self-similar structure:

- trees
- divide and conquer
- backtracking
- DFS

Use iteration when recursion depth may be dangerous or when the state is naturally sequential.

A recursive algorithm is not automatically slower; the important issue is total work and stack usage.

---

# 5. Divide and Conquer

Divide and conquer usually follows:

```text
Divide → solve subproblems → combine
```

Examples:

- merge sort
- quicksort
- binary search
- closest pair

For merge sort:

```text
T(n) = 2T(n/2) + O(n)
```

which gives O(n log n).

---

# 6. Backtracking Mental Model

Backtracking is best understood as a decision tree.

At every state:

```text
choose
  ↓
recurse
  ↓
undo
  ↓
try next choice
```

Generic template:

```cpp
void backtrack(State& state) {
    if (isComplete(state)) {
        answer.push_back(state);
        return;
    }

    for (auto choice : choices(state)) {
        apply(choice);
        backtrack(state);
        undo(choice);
    }
}
```

### The critical invariant
After `undo(choice)`, the state must be exactly what it was before `apply(choice)`.

If that invariant breaks, later branches are contaminated by earlier choices.

---

# 7. Generate All Subsets

For every element there are two choices:

```text
include
exclude
```

Therefore there are `2^n` subsets.

### C++

```cpp
void dfs(int i, const vector<int>& a,
         vector<int>& path, vector<vector<int>>& ans) {
    if (i == (int)a.size()) {
        ans.push_back(path);
        return;
    }

    dfs(i + 1, a, path, ans);       // exclude

    path.push_back(a[i]);
    dfs(i + 1, a, path, ans);       // include
    path.pop_back();
}
```

**TC:** O(n·2^n) including copying each subset  
**SC:** O(n) recursion/path excluding output

### Why `2^n`?
Each of n elements independently has two choices.

---

# 8. Subsets With Duplicates

If the input contains duplicates, identical subsets can be generated multiple times.

Sort first, then at the same recursion depth skip equal values:

```text
if (i > start && a[i] == a[i-1]) skip
```

The subtle point is **same recursion depth**. Equal values can still be used at different depths when the problem allows them.

---

# 9. Generate Permutations

For permutations, each position can choose one of the unused elements.

There are `n!` permutations.

An in-place swap approach:

```cpp
void permute(int pos, vector<int>& a, vector<vector<int>>& ans) {
    if (pos == (int)a.size()) {
        ans.push_back(a);
        return;
    }

    for (int i = pos; i < (int)a.size(); ++i) {
        swap(a[pos], a[i]);
        permute(pos + 1, a, ans);
        swap(a[pos], a[i]);
    }
}
```

**TC:** O(n·n!) including output copying  
**SC:** O(n) recursion

### Why swap back?
It restores the array so the next choice begins from the same state.

---

# 10. Combination Sum

You can choose a candidate repeatedly while the remaining target permits it.

Sort candidates when it allows early stopping:

```text
if candidate > remaining → later candidates are also too large
```

That is an example of pruning based on sorted order.

Complexity is exponential in the general case and depends on candidate values and target.

---

# 11. Combination Sum II

Each candidate can be used once.

Sort input and skip duplicates at the same depth.

The key distinction from Combination Sum is the recursive next index:

```text
reuse allowed → recurse with same index
use once       → recurse with index + 1
```

This small change completely changes the state space.

---

# 12. Letter Combinations of a Phone Number

Each digit maps to several letters.

For each position, try every mapped letter and recurse to the next digit.

If each digit has at most 4 choices:

**TC:** O(4^n · n) including output strings  
**SC:** O(n) recursion excluding output

This is a simple example of a Cartesian-product decision tree.

---

# 13. Palindrome Partitioning

Partition a string into substrings such that every substring is a palindrome.

At position `start`, try every ending index. If `s[start..end]` is a palindrome, choose it and recurse.

### Optimization
Precompute palindrome information with DP or expand around centers depending on constraints.

This shows how backtracking and DP can work together: backtracking explores partitions while DP avoids repeatedly checking the same palindrome state.

---

# 14. N-Queens

Place one queen per row so that no two queens attack each other.

A queen conflicts through:

```text
same column
same main diagonal: row-col
same anti-diagonal: row+col
```

Maintain sets/boolean arrays for these constraints.

At each row, try only safe columns.

### Complexity
Worst-case exponential, commonly bounded by O(n!) for the search arrangement. Exact complexity depends on pruning and implementation.

**SC:** O(n) auxiliary for columns/diagonals and recursion, excluding output.

### Important lesson
Good backtracking is not brute force over every board configuration. It rejects invalid partial states as early as possible.

---

# 15. Sudoku Solver

Choose an empty cell and try digits `1..9`.

A digit is valid if it is absent from:

- current row
- current column
- current 3×3 box

After placing it, recurse. If no completion is possible, undo and try another digit.

### Bitmask optimization
Rows, columns, and boxes can store used digits as bitmasks, making membership checks O(1).

Worst-case complexity is exponential.

---

# 16. Word Search

Start DFS from every board cell matching the first character.

At each step:

1. mark current cell used
2. explore up/down/left/right
3. unmark before returning

If word length is `L` and board has `R*C` cells:

**Worst TC:** O(RC · 4^L)  
**SC:** O(L) recursion, or O(RC) if using a separate visited matrix

### In-place marking
Instead of a visited matrix, temporarily change the cell and restore it afterward, giving O(1) additional visited storage.

---

# 17. Pruning

Pruning means proving a branch cannot lead to a valid answer and stopping immediately.

Examples:

- remaining target becomes negative
- sorted candidate exceeds remaining target
- queen conflicts with an existing queen
- Sudoku digit already appears in row/column/box
- current partial sum already exceeds a fixed positive target

### Important interview point
The theoretical worst case may remain exponential even with pruning, but good pruning can make the actual search dramatically smaller.

---

# 18. Backtracking State Design

Before writing code, define the state.

For subsets:

```text
index + current path
```

For permutations:

```text
position + chosen/unused information
```

For N-Queens:

```text
row + occupied columns + diagonals
```

For Sudoku:

```text
board + used digits
```

If the state does not contain enough information to make the next decision correctly, the recursion is incomplete.

---

# 19. Memoization Connection

Suppose recursive calls reach the same state repeatedly.

Instead of solving it again, cache its result.

This converts:

```text
pure recursion
      ↓
repeated states
      ↓
memoization
      ↓
DP
```

This distinction is crucial:

- Backtracking usually explores different choices producing different states.
- DP is especially useful when many paths converge to the **same state**.

---

# 20. Important OA / Interview Problem Bank

## Beginner

1. Factorial.
2. Fibonacci.
3. Sum of first N numbers.
4. Power function.
5. Reverse a string recursively.
6. Check palindrome recursively.
7. Print numbers forward/backward.
8. Generate binary strings.
9. Generate subsets.

## Intermediate

10. Subsets II.
11. Permutations.
12. Permutations II.
13. Combination Sum.
14. Combination Sum II.
15. Phone keypad combinations.
16. Generate parentheses.
17. Palindrome partitioning.
18. Word Search.
19. Rat in a Maze.
20. Maze path counting.

## Advanced

21. N-Queens.
22. Sudoku Solver.
23. Word Search II with Trie.
24. Expression Add Operators.
25. Partition to K equal subsets.
26. Matchsticks to Square.
27. Hamiltonian path search.
28. Advanced constraint-satisfaction backtracking.

---

# 21. Frequently Asked Interview Questions

### Q1. What are the three requirements of recursion?
A base case, progress toward the base case, and a correct recursive relationship.

### Q2. Why does naive Fibonacci become exponential?
The same Fibonacci states are calculated repeatedly, producing an exponential recursion tree.

### Q3. What is backtracking?
Systematic exploration of choices where a choice is applied, recursively explored, and then undone so another choice can be tried.

### Q4. Why is subset generation O(2^n)?
Every element has two choices: included or excluded.

### Q5. Why is permutation generation O(n!)?
There are n choices for the first position, n-1 for the second, and so on.

### Q6. Why do we sort before duplicate-aware backtracking?
Equal values become adjacent, making duplicate choices easy to detect and skip.

### Q7. Why must we undo a choice?
Without undoing, the next sibling branch would inherit the previous branch's state and generate incorrect results.

### Q8. What is pruning?
Stopping a branch as soon as we can prove it cannot produce a valid solution.

### Q9. Recursion vs DP?
Recursion defines the decomposition. DP adds caching or bottom-up evaluation when states overlap.

### Q10. Why can backtracking remain exponential even after optimization?
The number of valid combinations/permutations itself can be exponential or factorial, so output size may impose an exponential lower bound.

---

# 22. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Factorial | O(n) | O(n) stack |
| Fibonacci naive | O(2^n) | O(n) |
| Subsets | O(n2^n) | O(n) excluding output |
| Permutations | O(n·n!) | O(n) excluding output |
| Combination Sum | Exponential | O(target depth) |
| Phone combinations | O(4^n·n) | O(n) excluding output |
| N-Queens | Exponential | O(n) excluding output |
| Sudoku | Exponential | O(1)/O(n) depending state representation |
| Word Search | O(RC·4^L) | O(L) with in-place marking |

> **Core lesson:** Backtracking is not “try everything blindly.” It is structured state-space search: define the state, make a choice, preserve the invariant, prune impossible branches, and undo the choice perfectly.
