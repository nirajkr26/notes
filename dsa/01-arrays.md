# 01. Arrays — From Zero to Interview Level

Arrays are the starting point for DSA because almost every other topic eventually uses contiguous storage, indexing, traversal, or array-based state.

This chapter is intentionally written for a beginner. Do not just memorize the solutions: learn to ask what information is available at each index, what state can be carried forward, and which brute-force work is being repeated.

---

## 1. What is an array?

An array stores elements of the same type in contiguous memory.

For `int a[n]`, the address of `a[i]` can be computed directly from the base address and the index, which is why random access is O(1).

In modern C++, `vector<int>` is usually the practical interview choice because its size can grow dynamically.

### Basic C++ usage

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {10, 20, 30, 40};
    cout << a[2] << '\n';
    a.push_back(50);
    return 0;
}
```

### Complexity table

| Operation | Array / `vector` | Explanation |
|---|---:|---|
| `a[i]` | O(1) | Direct address calculation |
| Update `a[i]` | O(1) | Direct access |
| Linear search | O(n) | May inspect all elements |
| Append to vector | O(1) amortized | Occasional resize costs O(n) |
| Insert at beginning | O(n) | Elements shift right |
| Delete in middle | O(n) | Elements shift left |

### Important interview question: why is vector append O(1) amortized?

A vector keeps extra capacity. Most `push_back` operations simply write into unused capacity. Occasionally it must allocate a larger block and copy/move all existing elements, which costs O(n). Over a long sequence of appends, those expensive resizes are infrequent enough that the average cost per append is O(1) amortized.

---

# 2. Beginner Level

## 2.1 Traverse an array

Traversal means visiting every element once.

```cpp
void printArray(const vector<int>& a) {
    for (int x : a) cout << x << ' ';
}
```

**TC:** O(n)  
**SC:** O(1) auxiliary

### Questions to practice

- Find minimum and maximum.
- Count even/odd numbers.
- Count occurrences of a target.
- Check whether the array is sorted.
- Find the first and last index of a value using linear scan.

These are simple, but they teach the most important skill in DSA: identify what must be remembered while scanning.

---

## 2.2 Find minimum and maximum

Maintain the best value seen so far.

```cpp
pair<int, int> minMax(const vector<int>& a) {
    int mn = a[0], mx = a[0];
    for (int x : a) {
        mn = min(mn, x);
        mx = max(mx, x);
    }
    return {mn, mx};
}
```

**TC:** O(n)  
**SC:** O(1)

### Common mistake
Starting with `mn = 0, mx = 0` fails when all values are positive or all values are negative. Initialize from the data or use correct sentinel values.

---

## 2.3 Reverse an array

Use two pointers. The left pointer starts at the first element and the right pointer at the last. Swap them and move inward.

```cpp
void reverseArray(vector<int>& a) {
    int l = 0, r = (int)a.size() - 1;
    while (l < r) {
        swap(a[l], a[r]);
        ++l;
        --r;
    }
}
```

**TC:** O(n)  
**SC:** O(1)

### Why does it work?
After each swap, the two outer positions are already correct. The unsolved region becomes smaller while the invariant remains true.

---

## 2.4 Move zeroes to the end

Goal: preserve the relative order of non-zero values.

A slow approach repeatedly shifts values, which can become O(n²). The better approach keeps a write pointer for the next non-zero position.

```cpp
void moveZeroes(vector<int>& a) {
    int write = 0;
    for (int x : a) {
        if (x != 0) a[write++] = x;
    }
    while (write < (int)a.size()) a[write++] = 0;
}
```

**TC:** O(n)  
**SC:** O(1)

### Invariant
Everything before `write` is already the correct sequence of non-zero elements.

---

# 3. Prefix Sum

Prefix sum is one of the highest-value array techniques for online assessments.

Suppose:

```text
a = [3, 2, 5, 1]
```

Define:

```text
prefix[0] = 0
prefix[1] = 3
prefix[2] = 5
prefix[3] = 10
prefix[4] = 11
```

Then the sum from index `l` through `r` is:

```text
prefix[r + 1] - prefix[l]
```

### Why?
`prefix[r+1]` contains everything through `r`, while `prefix[l]` contains everything before `l`. Subtracting removes the unwanted prefix.

### C++ template

```cpp
vector<long long> buildPrefix(const vector<int>& a) {
    vector<long long> p(a.size() + 1, 0);
    for (int i = 0; i < (int)a.size(); ++i) {
        p[i + 1] = p[i] + a[i];
    }
    return p;
}
```

**Build TC:** O(n)  
**Range query TC:** O(1)  
**SC:** O(n)

### Interview question: why use `long long`?
Because the sum of many `int` values can exceed the `int` range even if every individual element fits inside an `int`.

---

# 4. Difference Array

Prefix sums answer many range-sum queries efficiently. Difference arrays solve the opposite situation: **many range updates**.

Suppose we repeatedly do:

```text
add x to every index [l, r]
```

Updating every element directly is O(length of range). Instead:

```text
diff[l]     += x
diff[r + 1] -= x
```

A final prefix sum reconstructs the actual array.

**TC:** O(n + q) for `q` range updates  
**SC:** O(n)

### When to recognize it
Words like:

- add to every element in a range
- perform many interval updates
- apply q operations and output the final array

should immediately make you consider a difference array.

---

# 5. Prefix/Suffix Techniques

## 5.1 Product of array except self

For each index `i`, we want:

```text
(product of everything left of i) × (product of everything right of i)
```

The important observation is that we can build the answer in two passes without division.

```cpp
vector<long long> productExceptSelf(const vector<int>& a) {
    int n = a.size();
    vector<long long> ans(n, 1);

    long long pref = 1;
    for (int i = 0; i < n; ++i) {
        ans[i] = pref;
        pref *= a[i];
    }

    long long suff = 1;
    for (int i = n - 1; i >= 0; --i) {
        ans[i] *= suff;
        suff *= a[i];
    }
    return ans;
}
```

**TC:** O(n)  
**SC:** O(1) auxiliary besides the required output

### Why avoid division?
Division creates problems with zero values and may violate the problem's intended constraints.

---

# 6. Kadane's Algorithm — Maximum Subarray

Problem: find the contiguous subarray with maximum sum.

### Brute force
Try every start and every end: O(n²), or O(n³) with repeated sums.

### Key observation
At position `i`, the best subarray ending at `i` is either:

1. Start fresh at `a[i]`.
2. Extend the best subarray ending at `i-1`.

Therefore:

```text
bestEndingHere = max(a[i], bestEndingHere + a[i])
```

```cpp
long long maxSubarraySum(const vector<int>& a) {
    long long cur = a[0];
    long long best = a[0];

    for (int i = 1; i < (int)a.size(); ++i) {
        cur = max((long long)a[i], cur + a[i]);
        best = max(best, cur);
    }
    return best;
}
```

**TC:** O(n)  
**SC:** O(1)

### Critical edge case
For `[-5, -2, -8]`, the answer is `-2`, not `0`. Initializing `best = 0` is incorrect unless the problem explicitly allows the empty subarray.

### Extension: maximum subarray with indices
Store the candidate start whenever you start a fresh subarray, and save the best `[l,r]` whenever `best` improves.

This is a common interview follow-up: **“Can you also return the actual subarray?”**

---

# 7. Hash Map + Array: Two Sum

Given an array and target, find two indices whose values sum to target.

### Brute force
Try every pair: O(n²).

### Optimization
For current value `x`, we need `target - x`. Store previously seen values in a hash map.

```cpp
vector<int> twoSum(const vector<int>& a, int target) {
    unordered_map<int, int> index;

    for (int i = 0; i < (int)a.size(); ++i) {
        int need = target - a[i];
        if (index.count(need)) return {index[need], i};
        index[a[i]] = i;
    }
    return {};
}
```

**TC:** O(n) average  
**SC:** O(n)

### Why check before inserting?
It avoids using the same array element twice.

### Interview follow-up
If the array is already sorted, you can solve it using two pointers in O(n) time and O(1) extra space.

---

# 8. Best Time to Buy and Sell Stock

You may buy once and sell once.

For a sale on day `i`, the best buying day before it is the day with the minimum price seen so far.

```cpp
int maxProfit(const vector<int>& prices) {
    int minPrice = prices[0];
    int best = 0;

    for (int price : prices) {
        best = max(best, price - minPrice);
        minPrice = min(minPrice, price);
    }
    return best;
}
```

**TC:** O(n)  
**SC:** O(1)

### Invariant
Before processing each day, `minPrice` is the minimum price among all previous days.

---

# 9. Majority Element — Boyer-Moore Voting

Find a value appearing more than `n/2` times.

The algorithm maintains a candidate and a balance:

- Same value as candidate → `count++`.
- Different value → `count--`.
- `count == 0` → choose a new candidate.

```cpp
int majorityElement(const vector<int>& a) {
    int candidate = 0;
    int count = 0;

    for (int x : a) {
        if (count == 0) candidate = x;
        count += (x == candidate ? 1 : -1);
    }
    return candidate;
}
```

**TC:** O(n)  
**SC:** O(1)

### Important caveat
This guarantees the answer only when the problem guarantees a majority exists. Otherwise perform a second pass to verify the candidate.

### Why does it work?
Every non-majority value can cancel at most one occurrence of the majority candidate. A value with more than half of all occurrences cannot be completely canceled.

---

# 10. Missing Number

Numbers are from `0` to `n` with exactly one missing.

XOR has useful cancellation:

```text
x ^ x = 0
x ^ 0 = x
```

So XORing all expected values and all actual values leaves the missing number.

```cpp
int missingNumber(const vector<int>& a) {
    int n = a.size();
    int ans = n;
    for (int i = 0; i < n; ++i) ans ^= i ^ a[i];
    return ans;
}
```

**TC:** O(n)  
**SC:** O(1)

---

# 11. Rotate Array

Rotate right by `k` positions.

If `k >= n`, first use `k %= n`.

### In-place three-reversal technique

For:

```text
[1 2 3 4 5 6 7], k=3
```

1. Reverse all → `[7 6 5 4 3 2 1]`
2. Reverse first `k` → `[5 6 7 4 3 2 1]`
3. Reverse rest → `[5 6 7 1 2 3 4]`

**TC:** O(n)  
**SC:** O(1)

This is worth learning because the exact technique appears in string rotations, cyclic rearrangements, and sequence transformations.

---

# 12. Dutch National Flag — Sort 0, 1, 2

Maintain three regions:

```text
[0 ... low-1]   = 0
[low ... mid-1] = 1
[mid ... high]  = unknown
[high+1 ... n-1] = 2
```

When the current value is:

- `0`: swap with `low`, increment both `low` and `mid`.
- `1`: increment `mid`.
- `2`: swap with `high`, decrement `high`, but do **not** increment `mid` yet because the swapped value has not been inspected.

```cpp
void sort012(vector<int>& a) {
    int low = 0, mid = 0, high = (int)a.size() - 1;

    while (mid <= high) {
        if (a[mid] == 0) {
            swap(a[low++], a[mid++]);
        } else if (a[mid] == 1) {
            ++mid;
        } else {
            swap(a[mid], a[high--]);
        }
    }
}
```

**TC:** O(n)  
**SC:** O(1)

---

# 13. 3Sum

Find all unique triples with sum zero.

### Brute force
Three nested loops: O(n³).

### Better approach
Sort first. Then fix one element and solve a two-sum problem using two pointers.

### Why sort?
Sorting gives order, which allows two pointers to decide which side to move. It also makes duplicate skipping possible.

**TC:** O(n²) after sorting  
**SC:** O(1) auxiliary, excluding output

### Duplicate handling
There are two levels:

1. Skip duplicate fixed values.
2. After finding a valid pair, skip equal left/right values.

A frequent interview bug is returning duplicate triplets.

---

# 14. Trapping Rain Water

Given heights, calculate how much water remains after raining.

For a position `i`:

```text
water[i] = min(maxLeft[i], maxRight[i]) - height[i]
```

A prefix/suffix-array solution uses O(n) space.

The advanced two-pointer solution uses O(1) auxiliary space. Maintain `leftMax` and `rightMax`. The smaller side is safe to finalize because the other side has a boundary at least as tall.

**TC:** O(n)  
**SC:** O(1)

### Interview explanation
Do not simply say “use two pointers.” Explain why the shorter side can be processed. The limiting boundary is the smaller of the two sides, so once the smaller side's maximum is known, the opposite boundary cannot reduce the water below that bound.

---

# 15. Maximum Product Subarray

This is more subtle than Kadane because multiplication behaves differently from addition.

A negative value can turn a very small negative product into a very large positive product.

Therefore maintain:

```text
maxEndingHere
minEndingHere
```

For each number `x`, the new candidates are:

```text
x
x * oldMax
x * oldMin
```

**TC:** O(n)  
**SC:** O(1)

### Pattern to remember
Whenever multiplication plus negative values appears, ask whether you need to track both extremes.

---

# 16. Subarray Sum Equals K

This is a foundational prefix-sum + hashing problem.

Let the current prefix sum be `P`.

A subarray ending here has sum `k` when:

```text
P - previousPrefix = k
```

Therefore:

```text
previousPrefix = P - k
```

Store how many times each prefix sum has appeared.

```cpp
int subarraySumK(const vector<int>& a, int k) {
    unordered_map<long long, int> freq;
    freq[0] = 1;

    long long prefix = 0;
    int answer = 0;

    for (int x : a) {
        prefix += x;
        if (freq.count(prefix - k)) {
            answer += freq[prefix - k];
        }
        ++freq[prefix];
    }
    return answer;
}
```

**TC:** O(n) average  
**SC:** O(n)

### Why initialize `freq[0] = 1`?
It represents the empty prefix before the array starts. Without it, a subarray beginning at index `0` would be missed.

### Why does this work with negative numbers?
Because the technique does not depend on window sums being monotonic. This is exactly why it is preferred over a positive-only sliding-window approach when negative numbers exist.

---

# 17. Subarray XOR = K

The same idea works with XOR.

If:

```text
prefixXor ^ previousPrefix = k
```

then:

```text
previousPrefix = prefixXor ^ k
```

Store frequencies of prefix XOR values.

**TC:** O(n) average  
**SC:** O(n)

This is a good example of a reusable mental pattern rather than a one-off trick.

---

# 18. Merge Intervals

Given intervals such as:

```text
[1,3], [2,6], [8,10], [9,12]
```

Sort by starting point. Once sorted, the only interval that can overlap the current merged interval is the next interval in order.

### Algorithm

1. Sort by start.
2. If the next interval starts after the current end, start a new merged interval.
3. Otherwise extend the current end.

**TC:** O(n log n)  
**SC:** O(n) output

### Common variant
“Insert Interval” uses the same overlap reasoning but can be solved in O(n) if the input intervals are already sorted.

---

# 19. Array Partitioning Patterns

Many interview problems are easier if you split the array into regions with a pointer invariant.

Common patterns:

### Write pointer
Used for:

- move zeroes
- remove duplicates
- compact valid elements

### Fast/slow pointers
Common in linked lists but also useful in arrays for partitioning and in-place filtering.

### Left/right boundaries
Used in:

- 2Sum sorted array
- 3Sum
- trapping rain water
- container with most water

The important part is not the pointer names. The important part is knowing **why moving one pointer cannot lose the optimal answer**.

---

# 20. Advanced Question: Longest Consecutive Sequence

Given an unsorted array, find the longest run of consecutive integers.

Sorting gives O(n log n). A hash set can do expected O(n).

### Insight
Only start a sequence at a number `x` when `x-1` does not exist. Then count `x, x+1, x+2...`.

**TC:** O(n) expected  
**SC:** O(n)

### Why is it still linear?
Although there is a nested loop, every value participates in sequence expansion only when it is the start of a sequence, so the total amount of successful expansion across the entire input remains linear in the expected analysis.

---

# 21. Advanced Question: Maximum Subarray Sum with One Deletion

A useful DP-on-array extension of Kadane is to track two states:

```text
noDelete[i]  = best sum ending at i with no deletion
oneDelete[i] = best sum ending at i with exactly one deletion
```

Transitions:

```text
noDelete = max(x, noDelete + x)
oneDelete = max(previousOneDelete + x, previousNoDelete)
```

The second expression corresponds to deleting the current element.

**TC:** O(n)  
**SC:** O(1)

This question teaches how a simple greedy-looking scan can evolve into a state DP.

---

# 22. Coordinate Compression

Suppose values can be as large as `10^9`, but there are only `n <= 2 * 10^5` distinct values and you need an indexed structure.

Sort the distinct values and map each value to a compact index `0..m-1`.

Typical applications:

- Fenwick trees
- segment trees
- offline frequency queries
- inversion counting

**TC:** O(n log n) for sorting + O(n) mapping  
**SC:** O(n)

### Interview question
Why not allocate an array of size `10^9`? Because complexity depends on the value range, not only on `n`. Compression converts a huge sparse domain into the small domain that actually occurs.

---

# 23. How to Choose the Technique

When you see an array problem, ask these questions in order:

### Question 1: Is the array sorted?
Try:

- binary search
- two pointers
- lower/upper bound

### Question 2: Is the problem about a contiguous segment?
Try:

- sliding window
- prefix sums
- prefix state + hash map
- Kadane/DP

### Question 3: Do I need counts or membership?
Try:

- `unordered_map`
- `unordered_set`

### Question 4: Are there many range updates or queries?
Try:

- prefix sum
- difference array
- Fenwick tree
- segment tree

### Question 5: Can sorting expose structure?
Try sorting before:

- intervals
- pair/triple sum
- greedy choices
- duplicate elimination

### Question 6: Is the answer an extreme value?
Ask whether a prefix/suffix maximum/minimum can maintain it in one pass.

---

# 24. Online Assessment Question Bank

The following progression is intended to be solved in order.

## Level A — absolute beginner

1. Find minimum and maximum.
2. Reverse array.
3. Check if sorted.
4. Count frequency of a value.
5. Find second largest element.
6. Remove duplicates from sorted array.
7. Move zeroes.
8. Left rotate by one.
9. Left rotate by k.
10. Find missing number.

For each, write the brute force idea first and then the O(n) or in-place optimization.

## Level B — core interview

11. Two Sum.
12. Best Time to Buy and Sell Stock.
13. Majority Element.
14. Maximum Subarray.
15. Maximum Product Subarray.
16. Product of Array Except Self.
17. Subarray Sum Equals K.
18. Subarray XOR Equals K.
19. Merge Intervals.
20. Sort 0/1/2.
21. 3Sum.
22. Container With Most Water.
23. Trapping Rain Water.
24. Longest Consecutive Sequence.

## Level C — advanced

25. Maximum Subarray with One Deletion.
26. Maximum sum circular subarray.
27. Count inversions.
28. Count smaller elements after self.
29. Subarray with equal number of 0s and 1s.
30. Longest zero-sum subarray.
31. Four Sum.
32. Merge overlapping and adjacent intervals with custom boundary rules.
33. Difference-array range update simulation.
34. Coordinate compression + indexed queries.
35. Maximum XOR pair.

---

# 25. Frequently Asked Array Interview Questions

### Q1. Array vs linked list?
Array provides O(1) indexing because storage is contiguous. A linked list requires traversal for indexed access but can insert/delete efficiently when the relevant node pointer is already known.

### Q2. Why is inserting in the middle O(n)?
Elements after the insertion point may need to move to create space.

### Q3. What is amortized O(1)?
A sequence of operations has constant average cost even if occasional individual operations are expensive.

### Q4. Prefix sum or sliding window?
Use prefix sums when arbitrary range queries matter or when negative values make window movement non-monotonic. Use sliding windows when the window can be expanded/shrunk while maintaining a valid state.

### Q5. When does a hash map beat sorting?
When exact lookup/frequency is the main task and you do not need ordering. Expected lookup is O(1), giving an O(n) average solution for many problems.

### Q6. Why can sorting still be preferable?
Sorting provides deterministic ordering and may enable two pointers, greedy strategies, duplicate handling, or binary search.

### Q7. Why is `long long` important?
Sums, products, prefix states, and pair computations can overflow `int` even when the input elements individually fit inside `int`.

### Q8. Why is Kadane O(n) despite looking at previous subarrays?
It stores only the best subarray ending at the previous position. All inferior candidates can be discarded permanently.

### Q9. What is the difference between subarray and subsequence?
A subarray is contiguous. A subsequence preserves order but may skip elements.

### Q10. Why can a nested loop still be O(n)?
If each element enters and leaves a maintained data structure at most once, the total work can still be linear. This is common in monotonic stacks/deques and some two-pointer techniques.

---

# 26. Common Bugs Checklist

- Off-by-one range boundaries.
- Using `int` for large sums.
- Forgetting `k %= n` before rotation.
- Returning duplicates in 3Sum/4Sum.
- Sorting and accidentally losing original indices.
- Initializing maximum to zero when all values can be negative.
- Using sliding-window sum logic with negative values.
- Forgetting `prefix[0] = 0` in prefix-sum code.
- Forgetting `freq[0] = 1` in prefix-state counting problems.
- Assuming `unordered_map` is worst-case O(1).
- Modifying input when the problem expects it unchanged.

---

# 27. Complexity Summary

| Problem / Pattern | Typical TC | Typical SC |
|---|---:|---:|
| Traversal | O(n) | O(1) |
| Reverse | O(n) | O(1) |
| Prefix sums | O(n) build | O(n) |
| Difference array | O(n+q) | O(n) |
| Kadane | O(n) | O(1) |
| Two Sum | O(n) average | O(n) |
| Majority Element | O(n) | O(1) |
| Missing Number | O(n) | O(1) |
| Sort 0/1/2 | O(n) | O(1) |
| 3Sum | O(n²) | O(1) auxiliary |
| Merge Intervals | O(n log n) | O(n) output |
| Trapping Rain Water | O(n) | O(1) |
| Longest Consecutive | O(n) expected | O(n) |
| Subarray Sum K | O(n) expected | O(n) |
| Coordinate Compression | O(n log n) | O(n) |

---

# 28. Mastery Test

You are ready to move to Binary Search when you can solve these without seeing notes:

1. Two Sum.
2. Maximum Subarray.
3. Move Zeroes.
4. Rotate Array.
5. Majority Element.
6. Product Except Self.
7. Subarray Sum K.
8. Merge Intervals.
9. 3Sum.
10. Trapping Rain Water.

But more importantly, you should be able to answer **why** the optimization works, state the invariant, and give TC/SC before writing code.

> **Core lesson:** Arrays are not about memorizing 30 tricks. They are about learning to maintain a small amount of information while scanning a large input.
