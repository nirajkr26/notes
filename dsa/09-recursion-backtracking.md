# 09. Recursion & Backtracking

Recursion solves a problem by solving smaller instances. Backtracking explores choices, undoes a choice, and tries the next one.

## 9.1 Recursion fundamentals

Every recursive function needs:

1. Base case.
2. Progress toward the base case.
3. Correct combination of the recursive result.

Example: factorial has **TC O(n)** and **SC O(n)** recursion stack.

## 9.2 Divide and conquer

Merge sort is recursive divide-and-conquer: split, solve both halves, combine. **TC O(n log n), SC O(n)**.

## 9.3 Backtracking template

```cpp
void backtrack(State& state) {
    if (complete(state)) {
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

The crucial invariant is that after `undo`, the state is exactly as it was before the choice.

## Beginner

- Print numbers 1..N.
- Sum array recursively.
- Reverse a string.
- Generate binary strings.

Typical recursion stack: **O(n)**.

## Intermediate

### Subsets
For each element, choose include/exclude.

**TC:** O(n·2^n) including copying outputs; **SC:** O(n) recursion excluding output.

### Permutations
Swap the current position with each possible remaining position.

**TC:** O(n·n!) including output; **SC:** O(n) recursion excluding output.

### Combination Sum
Choose candidates repeatedly while respecting the remaining target. Sort/prune where monotonicity allows it.

Complexity depends on target/candidate values; exponential in the general case.

## Advanced

### N-Queens
Place one queen per row while maintaining occupied columns and diagonals.

**TC:** exponential (commonly bounded by O(n!)); **SC:** O(n) auxiliary sets/recursion, excluding output.

### Sudoku
Choose an empty cell, try valid digits, recurse, and undo on failure. Bitmasks can make validity checks O(1).

### Word Search
DFS from each cell while marking the current path; undo the mark on return.

**TC:** O(RC·4^L) worst-case, **SC:** O(L) recursion plus visited representation.

## Important questions

1. Subsets — **O(n·2^n)** with output copying.
2. Subsets II — sort and skip equal choices at the same recursion depth.
3. Permutations — **O(n·n!)** with output.
4. Permutations II — frequency counts or sorted duplicate skipping.
5. Combination Sum — exponential.
6. Combination Sum II — no reuse; sort and skip duplicates.
7. Letter combinations of phone number — **O(4^n·n)** output-sensitive.
8. Palindrome partitioning — exponential output-sensitive.
9. N-Queens — exponential.
10. Sudoku solver — exponential worst-case.
11. Word Search — **O(RC·4^L)** worst-case.

## Pruning questions

Before exploring a branch, ask: Can a simple bound prove this branch cannot produce a solution? If yes, stop immediately. Good pruning often matters more than micro-optimizing the recursive code.