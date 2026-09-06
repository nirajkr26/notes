# 01. Arrays

Arrays are the foundation of most DSA problems. Learn them before moving to more specialized structures.

## 1.1 Fundamentals

An array stores elements in contiguous memory. For an array of `n` elements:

| Operation | Complexity | Why |
|---|---:|---|
| Access `a[i]` | O(1) | Address is computed directly |
| Update `a[i]` | O(1) | Direct access |
| Search unsorted | O(n) | May inspect every element |
| Insert/delete at end | O(1) amortized for `vector` | Usually no shifting |
| Insert/delete at beginning/middle | O(n) | Elements may need shifting |

Use `vector<int>` for most interview problems unless a fixed-size array is sufficient.

## 1.2 Beginner: traversal and basic manipulation

Typical tasks: find min/max, reverse, rotate, count occurrences, remove duplicates from sorted data, move zeroes, and check whether an array is sorted.

### Reverse an array

```cpp
void reverseArray(vector<int>& a) {
    int l = 0, r = (int)a.size() - 1;
    while (l < r) swap(a[l++], a[r--]);
}
```

**TC:** O(n)  **SC:** O(1)

### Move zeroes to the end

The write pointer marks the next position that should contain a non-zero value.

```cpp
void moveZeroes(vector<int>& a) {
    int write = 0;
    for (int x : a) if (x != 0) a[write++] = x;
    while (write < (int)a.size()) a[write++] = 0;
}
```

**TC:** O(n)  **SC:** O(1)

## 1.3 Intermediate: prefix/suffix techniques

### Prefix sum

Define `prefix[i]` as the sum of the first `i` elements. Then the sum of `[l, r]` is `prefix[r+1] - prefix[l]`.

**Build TC:** O(n)  **Query TC:** O(1)  **SC:** O(n)

### Difference array

For many range additions, instead of modifying every element in `[l,r]`, add `x` at `l` and `-x` at `r+1`, then take a prefix sum.

**TC:** O(n + q) for `q` range updates. **SC:** O(n).

### Product except self

Use prefix products from the left and suffix products from the right. Avoid division so zeros are handled naturally.

**TC:** O(n)  **SC:** O(1) auxiliary space if output is excluded.

## 1.4 Kadane's algorithm

For maximum subarray sum, `bestEndingHere` means the best sum of a subarray that must end at the current position.

```cpp
long long maxSubarraySum(const vector<int>& a) {
    long long cur = a[0], ans = a[0];
    for (int i = 1; i < (int)a.size(); ++i) {
        cur = max((long long)a[i], cur + a[i]);
        ans = max(ans, cur);
    }
    return ans;
}
```

**TC:** O(n)  **SC:** O(1)

**Pitfall:** initializing the answer to `0` is wrong when every number is negative.

## 1.5 Hash/prefix pattern: subarray sum = K

If current prefix sum is `p`, an earlier prefix `p-k` forms a subarray with sum `k`. Store the number of times each prefix sum has appeared.

```cpp
int subarraySumK(const vector<int>& a, int k) {
    unordered_map<long long, int> freq{{0, 1}};
    long long pref = 0;
    int ans = 0;
    for (int x : a) {
        pref += x;
        if (freq.count(pref - k)) ans += freq[pref - k];
        ++freq[pref];
    }
    return ans;
}
```

**TC:** O(n) average  **SC:** O(n)

## 1.6 Important questions

### Q1. Two Sum
**Idea:** store previously seen values in a hash map and look for `target-x`.

**TC:** O(n) average  **SC:** O(n)

### Q2. Best Time to Buy and Sell Stock
Track the minimum price so far and the best profit ending today.

**TC:** O(n)  **SC:** O(1)

### Q3. Maximum Subarray
Use Kadane's invariant described above.

**TC:** O(n)  **SC:** O(1)

### Q4. Majority Element
Boyer-Moore voting maintains a candidate whose vote balance is positive.

**TC:** O(n)  **SC:** O(1)

### Q5. Missing Number
XOR all indices and values; equal values cancel.

**TC:** O(n)  **SC:** O(1)

### Q6. Merge Intervals
Sort by start time and merge while intervals overlap.

**TC:** O(n log n)  **SC:** O(n) for output; auxiliary sorting space depends on implementation.

### Q7. 3Sum
Sort, fix one value, and solve the remaining pair with two pointers. Skip duplicates carefully.

**TC:** O(n²)  **SC:** O(1) auxiliary, excluding output.

### Q8. Trapping Rain Water
Two-pointer solution keeps left/right maximums and processes the side with smaller boundary.

**TC:** O(n)  **SC:** O(1)

### Q9. Maximum Product Subarray
Track both maximum and minimum product ending at each position because a negative number can swap their roles.

**TC:** O(n)  **SC:** O(1)

### Q10. Rotate Array
Use three reversals: reverse all, reverse first `k`, reverse remaining elements.

**TC:** O(n)  **SC:** O(1)

## 1.7 Advanced patterns

- Prefix sum + hash map: count/locate subarrays.
- Prefix XOR + hash map: XOR-range problems.
- Difference array: bulk range updates.
- Monotonic stack: next greater/smaller and contribution problems.
- Two pointers: sorted pair/triple and partition problems.
- Sliding window: contiguous range with maintainable state.
- Coordinate compression: map large sparse values to compact indices.

## 1.8 Edge cases checklist

- Empty array.
- One element.
- All values equal.
- All values negative.
- Zeros and duplicates.
- `k = 0`, `k = n`, or `k > n`.
- Integer overflow: use `long long` for large sums/products.
- Output may require original indices rather than sorted positions.

## Interview rule

Before coding any array problem, ask: **Is it sorted? Is it contiguous? Do I need frequencies? Can I maintain a prefix/suffix state? Can two pointers eliminate candidates?**