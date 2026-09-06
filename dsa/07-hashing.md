# 07. Hashing — Frequency, Lookup and Prefix-State Techniques

Hashing gives fast expected lookup by mapping a key to a bucket. In C++, the main tools are `unordered_map` and `unordered_set`.

Hashing is one of the most useful ways to turn repeated searching from O(n²) into expected O(n).

---

# 1. Hash Table Fundamentals

```cpp
unordered_map<int, int> freq;
freq[10]++;
```

Useful operations:

```cpp
freq.count(x)
freq[x]
freq.erase(x)
freq.find(x)
```

Expected complexity:

| Operation | Average | Worst |
|---|---:|---:|
| Insert | O(1) | O(n) |
| Find | O(1) | O(n) |
| Erase | O(1) | O(n) |

`unordered_set` stores keys only; `unordered_map` stores key-value pairs.

For ordered operations, use `set`/`map`, which generally provide O(log n).

---

# 2. Frequency Counting

Given:

```text
[2,1,2,3,1,2]
```

frequency becomes:

```text
1 → 2
2 → 3
3 → 1
```

```cpp
unordered_map<int, int> freq;
for (int x : a) ++freq[x];
```

**TC:** O(n) expected  
**SC:** O(n)

### When to use a fixed array instead
If values are guaranteed to lie in a small range such as lowercase letters, an array is simpler and faster:

```cpp
array<int, 26> freq{};
for (char c : s) ++freq[c - 'a'];
```

This is O(n) time and O(1) space relative to input size.

---

# 3. Contains Duplicate

Insert every value into a set. If it already exists, a duplicate has been found.

**TC:** O(n) expected  
**SC:** O(n)

This is the simplest example of using hashing for membership rather than counting.

---

# 4. Two Sum

For each `x`, search for `target-x` among previously seen elements.

**TC:** O(n) expected  
**SC:** O(n)

The important design choice is to check before inserting the current value so the same element is not reused.

---

# 5. Group Anagrams

Anagrams have identical character frequencies.

Possible key:

```text
sorted characters
```

or a 26-count signature.

Sorting each word costs O(k log k), while counting characters can reduce it to O(k) for a fixed alphabet.

For `n` strings of average length `k`:

**TC:** O(nk log k) with sorting keys  
**TC:** O(nk) with frequency signatures for a fixed alphabet  
**SC:** O(nk) for stored groups/keys

### Interview lesson
When multiple objects need grouping by an invariant, turn that invariant into a hashable key.

---

# 6. Longest Consecutive Sequence

Given an unsorted array, find the longest consecutive run.

### Sorting solution
O(n log n).

### Hash-set solution
Insert all values into a set. Start a sequence only when `x-1` does not exist.

```cpp
int longestConsecutive(const vector<int>& a) {
    unordered_set<int> s(a.begin(), a.end());
    int best = 0;

    for (int x : s) {
        if (s.count(x - 1)) continue;

        int y = x;
        while (s.count(y)) ++y;
        best = max(best, y - x);
    }
    return best;
}
```

**TC:** O(n) expected  
**SC:** O(n)

### Why isn't this O(n²)?
Sequence expansion happens only from sequence starts. Every value belongs to at most one successful expansion chain in the overall analysis.

---

# 7. Prefix Sum + Hash Map

This is one of the most important patterns in DSA.

If:

```text
prefix[j] - prefix[i] = k
```

then:

```text
prefix[i] = prefix[j] - k
```

So at every position, look for the required earlier prefix state in a hash map.

This turns many subarray-count problems into O(n) expected solutions.

---

# 8. Subarray Sum Equals K

```cpp
int subarraySumK(const vector<int>& a, int k) {
    unordered_map<long long, int> count;
    count[0] = 1;

    long long prefix = 0;
    int answer = 0;

    for (int x : a) {
        prefix += x;
        if (count.count(prefix - k))
            answer += count[prefix - k];
        ++count[prefix];
    }
    return answer;
}
```

**TC:** O(n) expected  
**SC:** O(n)

### Why store counts rather than only an index?
Because multiple earlier prefixes can have the same value, and every one forms a valid subarray ending at the current position.

---

# 9. Longest Zero-Sum Subarray

If two prefix sums are equal at indices `i` and `j`, then:

```text
prefix[j] - prefix[i] = 0
```

so the subarray between them has sum zero.

Store the **first index** at which each prefix sum appears. The earliest index produces the longest possible subarray later.

**TC:** O(n) expected  
**SC:** O(n)

### General rule
For maximum-length prefix-state problems, store the earliest occurrence.

For counting problems, store frequencies.

---

# 10. Equal Number of 0s and 1s

Transform:

```text
0 → -1
1 → +1
```

Then a subarray with equal zeros and ones has sum zero.

Now the problem becomes longest zero-sum subarray, solved with prefix sums + earliest index.

**TC:** O(n) expected  
**SC:** O(n)

### Pattern recognition
When a problem asks for equal counts of two categories, look for a transformation into `+1/-1`.

---

# 11. Prefix XOR + Hash Map

For XOR:

```text
A ^ B = K
```

implies:

```text
A = B ^ K
```

Therefore, just like prefix sums, maintain frequencies of previous prefix XOR states.

This solves count-subarrays-with-XOR-K in O(n) expected time.

---

# 12. Isomorphic Strings

Two strings are isomorphic when each character from the first maps consistently to exactly one character in the second, and vice versa.

You need two maps/arrays to enforce a one-to-one mapping.

**TC:** O(n)  
**SC:** O(k), where `k` is alphabet size.

### Why two directions?
A one-way map can allow two different source characters to map to the same target character, which violates isomorphism.

---

# 13. Top K Frequent Elements

First count frequencies.

Then either:

1. Sort by frequency: O(n log n).
2. Keep a min-heap of size `k`: O(n log k).
3. Use buckets indexed by frequency: O(n) under suitable representation.

Heap is the most general interview answer.

**TC:** O(n log k)  
**SC:** O(n)

---

# 14. 4Sum

Sort the array, fix two values, then use two pointers for the remaining pair.

**TC:** O(n³)  
**SC:** O(1) auxiliary excluding output

Hashing can also be used for pair sums, but memory and duplicate handling become more involved.

### Interview lesson
The “best” data structure depends on the constraints. Do not automatically hash every sum if sorting provides a simpler deterministic solution.

---

# 15. Custom Hashing

C++ can hash standard primitive keys automatically. Composite keys may require a custom hash.

Typical use cases:

- pair coordinates
- state tuples
- custom structs
- grid coordinates

Avoid unsafe encodings such as `x * 100000 + y` unless constraints prove there can be no collisions or overflow.

---

# 16. Hash Map vs Map

| Feature | `unordered_map` | `map` |
|---|---|---|
| Structure | Hash table | Balanced tree |
| Average lookup | O(1) | O(log n) |
| Worst lookup | O(n) | O(log n) |
| Ordered keys | No | Yes |
| Lower/upper bound | No ordering guarantee | Yes |

Use `map` when sorted iteration, predecessor/successor, or deterministic ordered behavior is needed.

---

# 17. Online Assessment Problem Bank

## Beginner

1. Count frequencies.
2. Contains duplicate.
3. First unique character.
4. Intersection of two arrays.
5. Two Sum.
6. Happy Number.
7. Valid Anagram.
8. Group identical records by key.

## Intermediate

9. Group Anagrams.
10. Longest Consecutive Sequence.
11. Subarray Sum K.
12. Longest Zero-Sum Subarray.
13. Equal 0s and 1s.
14. Count XOR K.
15. Isomorphic Strings.
16. Top K Frequent.
17. 4Sum.
18. Longest substring with K distinct characters.

## Advanced

19. Subarray with maximum frequency condition.
20. Count subarrays divisible by K.
21. Count equal binary subarrays.
22. Randomized set design.
23. LRU-cache hash component.
24. Coordinate/state hashing.
25. Custom hash for graph/grid states.

---

# 18. Frequently Asked Interview Questions

### Q1. Why is hash lookup expected O(1)?
A good hash function distributes keys across buckets so the expected number of items examined per lookup stays constant.

### Q2. Is `unordered_map` guaranteed O(1)?
No. O(1) is expected/average behavior; worst-case can be O(n).

### Q3. Why use a set instead of a map?
When only membership matters and no associated value/count needs to be stored.

### Q4. Why store earliest prefix index?
For longest-length questions, the earliest occurrence maximizes the distance to any later equal state.

### Q5. Why store frequency for subarray counting?
Every earlier matching prefix represents a different valid subarray ending at the current position.

### Q6. Why does equal prefix sum imply zero-sum subarray?
Their difference equals the sum of the elements between the two prefix positions.

### Q7. Why transform 0/1 into -1/+1?
Equal counts then produce total sum zero, reducing the problem to zero-sum subarray detection.

### Q8. Hashing vs sorting?
Hashing is usually simpler for membership/counting. Sorting is useful when order, deterministic behavior, two pointers, or greedy processing is needed.

### Q9. Why can negative values break sliding-window sum techniques?
Adding/removing elements is no longer guaranteed to move the sum monotonically, so shrinking the window based on the sum may discard valid answers.

---

# 19. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Frequency count | O(n) expected | O(n) |
| Contains duplicate | O(n) expected | O(n) |
| Two Sum | O(n) expected | O(n) |
| Group Anagrams | O(nk) fixed alphabet | O(nk) |
| Longest Consecutive | O(n) expected | O(n) |
| Subarray Sum K | O(n) expected | O(n) |
| Longest zero-sum | O(n) expected | O(n) |
| XOR subarrays | O(n) expected | O(n) |
| Top K frequent | O(n log k) | O(n) |
| 4Sum | O(n³) | O(1) auxiliary |

> **Core lesson:** Hashing is most powerful when you can turn “search all previous possibilities” into “look up the exact state I need.”
