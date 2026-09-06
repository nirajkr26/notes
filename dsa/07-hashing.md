# 07. Hashing

Hashing maps keys to buckets, giving expected O(1) insertion, lookup, and deletion. In C++, use `unordered_map`/`unordered_set` when ordering is unnecessary and `map` when sorted keys are useful.

## Beginner

Learn frequency maps, membership tests, duplicate detection, and grouping.

```cpp
unordered_map<int, int> freq;
for (int x : a) ++freq[x];
```

**TC:** O(n) average, **SC:** O(n).

## Intermediate patterns

### Two Sum
Store each previous value and look up `target-x`.

**TC:** O(n) average, **SC:** O(n).

### Longest consecutive sequence
Put all values in a set. Start a sequence only when `x-1` is absent; then advance while consecutive values exist.

**TC:** O(n) expected, **SC:** O(n).

### Subarray sum = K
Prefix sum + frequency map. Works with negative values unlike a simple positive-only sliding window.

**TC:** O(n) expected, **SC:** O(n).

### Longest subarray with a condition
Convert the condition into a prefix state and store the earliest index at which each state appeared. Keeping the earliest index maximizes future length.

## Important questions

1. Contains duplicate — **O(n) expected, O(n)**.
2. Two Sum — **O(n) expected, O(n)**.
3. Group Anagrams — **O(n·k log k)** with sorted-string keys or roughly **O(n·k)** with frequency signatures; **O(nk)** space.
4. Longest Consecutive Sequence — **O(n) expected, O(n)**.
5. Subarray Sum Equals K — **O(n) expected, O(n)**.
6. Count subarrays with XOR K — **O(n) expected, O(n)**.
7. 4Sum — sort + nested loop + two pointers, **O(n³)** and output-dependent space.
8. Isomorphic Strings — two-way mapping, **O(n), O(k)**.
9. Top K Frequent Elements — frequency map + heap/buckets; heap **O(n log k)**, **O(n)** space.

## Advanced idea: custom hashing

For composite keys such as pairs, either encode safely when bounds allow or provide a custom hash. Avoid collision-prone arithmetic encodings unless constraints make them provably safe.

## Pitfalls

- Average O(1) is not a worst-case guarantee.
- Use `long long` for prefix sums when values can overflow `int`.
- In prefix-index problems, initialize the neutral state before processing the first element.
- Decide whether duplicate values should map to earliest index, latest index, or a count based on the problem.