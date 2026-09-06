# 17. Bit Manipulation

Bits are useful for compact state representation, XOR cancellation, masks, and performance-critical integer logic.

## Fundamentals

- `x & 1`: test lowest bit.
- `x << k`: shift left by k bits (beware overflow/sign issues).
- `x >> k`: shift right.
- `x ^ x = 0`, `x ^ 0 = x`.
- `x & (x-1)`: clears the lowest set bit.
- `x & -x`: isolates the lowest set bit for two's-complement integers.

## Beginner

### Check/set/clear a bit
For bit `k`, use masks `1LL << k` and `&`, `|`, `^` operations.

**TC:** O(1)  **SC:** O(1)

### Count set bits
Repeatedly clear the lowest set bit. Number of iterations equals number of set bits.

**TC:** O(number of set bits), **SC:** O(1). C++ also provides `__builtin_popcount` / `__builtin_popcountll`.

### Single number
XOR all values; duplicates cancel and the unique value remains.

**TC:** O(n)  **SC:** O(1)

## Intermediate

### Power of two
A positive integer is a power of two iff `x & (x-1) == 0`.

### Subsets with bit masks
For `n` elements, mask `0..2^n-1` represents every subset.

**TC:** O(n·2^n), **SC:** O(1) auxiliary excluding output.

## Advanced

### XOR basis / linear basis
Maintain independent bit vectors to answer maximum XOR and representability questions. Complexity depends on bit width, commonly O(nB).

### Bitmask DP
Represent a subset by an integer mask. Useful when `n` is small.

Typical **TC:** O(2^n n), **SC:** O(2^n).

## Important questions

1. Single Number — **O(n), O(1)**.
2. Number of 1 Bits — **O(B)** or O(popcount), **O(1)**.
3. Counting Bits 0..N — **O(N), O(N)**.
4. Missing Number — XOR, **O(n), O(1)**.
5. Reverse Bits — **O(B), O(1)**.
6. Power of Two — **O(1), O(1)**.
7. Sum of Two Integers without `+` — XOR for sum without carry and AND/shift for carry; **O(B), O(1)**.
8. Maximum XOR pair — binary trie **O(nB), O(nB)** or more specialized linear-basis approaches.
9. Subset enumeration — **O(n2^n)** if each subset is materialized.
10. TSP bitmask DP — **O(n²2^n), O(n2^n)**.

## Pitfalls

Signed shifts and overflow are language-sensitive. Use unsigned types where appropriate for bit-level manipulation, and use `1ULL << k` when a wide unsigned mask is required.