# Data Structures & Algorithms (DSA) — Super Detailed Interview & Exam Notes (C++)

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
- Fixed size (static) or dynamic (`std::vector` doubles capacity)
- Insert/delete at middle: O(n) due to shifting

### 3.2 Key Techniques

#### Two Pointers
Find pair summing to target in sorted array — avoids nested loops.

```cpp
// Returns {i, j} such that arr[i] + arr[j] == target, or {-1,-1}
pair<int,int> twoSum(vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1;
    while (left < right) {
        int sum = arr[left] + arr[right];
        if (sum == target) return {left, right};
        else if (sum < target) left++;
        else right--;
    }
    return {-1, -1};
}
```

#### Sliding Window
Maintain a window `[left, right]` and expand/shrink as needed.

```cpp
// Longest subarray with sum <= k
int longestSubarrayWithSumAtMostK(vector<int>& arr, int k) {
    int left = 0, sum = 0, maxLen = 0;
    for (int right = 0; right < (int)arr.size(); right++) {
        sum += arr[right];
        while (sum > k) sum -= arr[left++];
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

#### Prefix Sum
Precompute cumulative sums for O(1) range sum queries.

```cpp
// Build prefix array (1-indexed)
vector<int> buildPrefix(vector<int>& arr) {
    int n = arr.size();
    vector<int> prefix(n + 1, 0);
    for (int i = 0; i < n; i++)
        prefix[i + 1] = prefix[i] + arr[i];
    return prefix;
}
// rangeSum(l, r) = prefix[r+1] - prefix[l]  (0-indexed, inclusive)
int rangeSum(vector<int>& prefix, int l, int r) {
    return prefix[r + 1] - prefix[l];
}
```

#### Kadane's Algorithm (Max Subarray Sum)

```cpp
int maxSubarraySum(vector<int>& arr) {
    int maxSum = arr[0], currentSum = arr[0];
    for (int i = 1; i < (int)arr.size(); i++) {
        currentSum = max(arr[i], currentSum + arr[i]);
        maxSum = max(maxSum, currentSum);
    }
    return maxSum;
}
```

#### Trapping Rain Water (Two Pointers)

```cpp
int trap(vector<int>& height) {
    int left = 0, right = (int)height.size() - 1;
    int leftMax = 0, rightMax = 0, water = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            leftMax = max(leftMax, height[left]);
            water += leftMax - height[left];
            left++;
        } else {
            rightMax = max(rightMax, height[right]);
            water += rightMax - height[right];
            right--;
        }
    }
    return water;
}
```

#### Merge Intervals

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> result;
    for (auto& interval : intervals) {
        if (result.empty() || result.back()[1] < interval[0])
            result.push_back(interval);
        else
            result.back()[1] = max(result.back()[1], interval[1]);
    }
    return result;
}
```

#### Rotate Array (3 Reverses)

```cpp
void reverse(vector<int>& arr, int l, int r) {
    while (l < r) swap(arr[l++], arr[r--]);
}
void rotate(vector<int>& arr, int k) {
    int n = arr.size();
    k %= n;
    reverse(arr, 0, n - 1);
    reverse(arr, 0, k - 1);
    reverse(arr, k, n - 1);
}
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

```cpp
// Longest substring without repeating characters
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> seen;  // char -> last seen index
    int left = 0, maxLen = 0;
    for (int right = 0; right < (int)s.size(); right++) {
        auto it = seen.find(s[right]);
        if (it != seen.end() && it->second >= left)
            left = it->second + 1;
        seen[s[right]] = right;
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}

// Minimum window substring (contains all chars of t)
string minWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;
    int left = 0, matched = 0, minLen = INT_MAX, start = 0;
    for (int right = 0; right < (int)s.size(); right++) {
        char c = s[right];
        if (need.count(c) && ++window[c] == need[c]) matched++;
        while (matched == (int)need.size()) {
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                start = left;
            }
            char l = s[left++];
            if (need.count(l) && window[l]-- == need[l]) matched--;
        }
    }
    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

### 4.3 KMP (Knuth-Morris-Pratt)

Build failure function (lps[]) — longest proper prefix that is also suffix. Avoids redundant comparisons.

```cpp
// Build LPS (longest proper prefix-suffix) array — O(m)
vector<int> buildLPS(const string& pattern) {
    int m = pattern.size();
    vector<int> lps(m, 0);
    int len = 0, i = 1;
    while (i < m) {
        if (pattern[i] == pattern[len]) {
            lps[i++] = ++len;
        } else if (len != 0) {
            len = lps[len - 1];
        } else {
            lps[i++] = 0;
        }
    }
    return lps;
}

// KMP search — returns all starting indices of pattern in text — O(n+m)
vector<int> kmpSearch(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    vector<int> lps = buildLPS(pattern);
    vector<int> matches;
    int i = 0, j = 0;  // i = text index, j = pattern index
    while (i < n) {
        if (text[i] == pattern[j]) { i++; j++; }
        if (j == m) {
            matches.push_back(i - j);
            j = lps[j - 1];
        } else if (i < n && text[i] != pattern[j]) {
            if (j != 0) j = lps[j - 1];
            else i++;
        }
    }
    return matches;
}
```

### 4.4 Palindrome — Expand Around Center

```cpp
// Returns length of longest palindrome centered at (l, r)
int expand(const string& s, int l, int r) {
    while (l >= 0 && r < (int)s.size() && s[l] == s[r]) { l--; r++; }
    return r - l - 1;  // length of palindrome
}

string longestPalindrome(string s) {
    int start = 0, maxLen = 1;
    for (int i = 0; i < (int)s.size(); i++) {
        int odd  = expand(s, i, i);       // odd-length
        int even = expand(s, i, i + 1);   // even-length
        int best = max(odd, even);
        if (best > maxLen) {
            maxLen = best;
            start = i - (best - 1) / 2;
        }
    }
    return s.substr(start, maxLen);
}
```

---

## 5. Linked Lists

### 5.1 Node Definition

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

### 5.2 Types

| Type              | Description                                    | Space |
|-------------------|------------------------------------------------|-------|
| Singly Linked     | Each node has `next` pointer                   | O(n)  |
| Doubly Linked     | Each node has `prev` and `next`                | O(n)  |
| Circular Singly   | Last node's `next` points to head              | O(n)  |

### 5.3 Operations Complexity

| Operation       | Singly LL | With Tail Pointer |
|-----------------|-----------|-------------------|
| Access by index | O(n)      | O(n)              |
| Prepend         | O(1)      | O(1)              |
| Append          | O(n)      | O(1)              |
| Delete head     | O(1)      | O(1)              |
| Delete by value | O(n)      | O(n)              |
| Search          | O(n)      | O(n)              |

### 5.4 Classic Techniques

#### Reverse a Linked List

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;
    while (curr) {
        ListNode* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    return prev;  // new head
}
```

#### Floyd's Cycle Detection (Fast/Slow Pointers)

```cpp
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }
    return false;
}

// Find cycle start — after meeting point, reset one pointer to head
ListNode* detectCycleStart(ListNode* head) {
    ListNode* slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            slow = head;
            while (slow != fast) { slow = slow->next; fast = fast->next; }
            return slow;  // cycle start
        }
    }
    return nullptr;
}
```

#### Find Middle Node

```cpp
ListNode* findMiddle(ListNode* head) {
    ListNode* slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;  // middle node (second middle for even length)
}
```

#### Merge Two Sorted Lists

```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);
    ListNode* curr = &dummy;
    while (l1 && l2) {
        if (l1->val <= l2->val) { curr->next = l1; l1 = l1->next; }
        else                    { curr->next = l2; l2 = l2->next; }
        curr = curr->next;
    }
    curr->next = l1 ? l1 : l2;
    return dummy.next;
}
```

#### Reverse Nodes in k-Group

```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode* check = head;
    for (int i = 0; i < k; i++) {
        if (!check) return head;  // fewer than k nodes left
        check = check->next;
    }
    // Reverse k nodes
    ListNode* prev = nullptr, *curr = head;
    for (int i = 0; i < k; i++) {
        ListNode* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    head->next = reverseKGroup(curr, k);  // head is now tail of reversed group
    return prev;  // new head
}
```

### 5.5 Common Problems

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

```cpp
#include <stack>

stack<int> st;
st.push(x);       // add to top   O(1)
st.pop();         // remove top   O(1)
st.top();         // view top     O(1)
st.empty();       // check empty  O(1)
st.size();        // number of elements
```

**Applications:**
- Function call stack / recursion simulation
- Undo/redo history
- Balanced parentheses checking
- Monotonic stack problems (next greater element)
- Postfix expression evaluation

#### Balanced Parentheses

```cpp
bool isValid(string s) {
    stack<char> st;
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            char top = st.top(); st.pop();
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{')) return false;
        }
    }
    return st.empty();
}
```

#### Monotonic Stack (Next Greater Element)

```cpp
// For each element, find the next element strictly greater than it
vector<int> nextGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> stk;  // stack of indices
    for (int i = 0; i < n; i++) {
        while (!stk.empty() && arr[stk.top()] < arr[i]) {
            result[stk.top()] = arr[i];
            stk.pop();
        }
        stk.push(i);
    }
    return result;
}
```

#### Largest Rectangle in Histogram

```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> stk;
    int maxArea = 0;
    heights.push_back(0);  // sentinel
    for (int i = 0; i < (int)heights.size(); i++) {
        while (!stk.empty() && heights[stk.top()] > heights[i]) {
            int h = heights[stk.top()]; stk.pop();
            int w = stk.empty() ? i : i - stk.top() - 1;
            maxArea = max(maxArea, h * w);
        }
        stk.push(i);
    }
    return maxArea;
}
```

### 6.2 Queue — FIFO

```cpp
#include <queue>

queue<int> q;
q.push(x);        // add to back  O(1)
q.pop();          // remove front O(1)
q.front();        // view front   O(1)
q.back();         // view back    O(1)
q.empty();        // check empty  O(1)
```

**Applications:**
- BFS (breadth-first search)
- Level-order tree traversal
- Sliding window maximum (monotonic deque)
- Round-robin scheduling

### 6.3 Deque (Double-Ended Queue)

Supports push/pop at both ends in O(1).

```cpp
#include <deque>

deque<int> dq;
dq.push_front(x);  dq.push_back(x);
dq.pop_front();    dq.pop_back();
dq.front();        dq.back();
```

#### Sliding Window Maximum (Monotonic Deque)

```cpp
// Maximum value in every window of size k — O(n)
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;  // stores indices; front = max
    vector<int> result;
    for (int i = 0; i < (int)nums.size(); i++) {
        // Remove indices out of window
        while (!dq.empty() && dq.front() < i - k + 1) dq.pop_front();
        // Remove indices with smaller values (they can never be max)
        while (!dq.empty() && nums[dq.back()] < nums[i]) dq.pop_back();
        dq.push_back(i);
        if (i >= k - 1) result.push_back(nums[dq.front()]);
    }
    return result;
}
```

### 6.4 Stack-Based Queue (Two-Stack Queue)

```cpp
// Amortized O(1) per operation
class MyQueue {
    stack<int> inbox, outbox;
    void transfer() {
        if (outbox.empty())
            while (!inbox.empty()) { outbox.push(inbox.top()); inbox.pop(); }
    }
public:
    void push(int x) { inbox.push(x); }
    int pop()  { transfer(); int v = outbox.top(); outbox.pop(); return v; }
    int peek() { transfer(); return outbox.top(); }
    bool empty() { return inbox.empty() && outbox.empty(); }
};
```

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
freq.max_load_factor(0.25);          // lower -> fewer collisions, more memory
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
unordered_map<int, int> seen;  // value -> index
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
| Two Sum                          | Store `value -> index`; look up complement            | O(n)         |
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

### 8.1 TreeNode Definition

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

### 8.2 Binary Tree Properties

```
Height: max edges from root to leaf
Depth:  edges from root to node
Full:   every node has 0 or 2 children
Complete: all levels filled except last (filled left to right)
Perfect: all internal nodes have 2 children; all leaves at same level
Balanced: |height(left) - height(right)| <= 1 for all nodes
```

### 8.3 Tree Traversals

```cpp
// --- Recursive traversals ---
void inorder(TreeNode* node, vector<int>& result) {
    if (!node) return;
    inorder(node->left, result);   // Left -> Root -> Right (gives sorted order for BST)
    result.push_back(node->val);
    inorder(node->right, result);
}

void preorder(TreeNode* node, vector<int>& result) {
    if (!node) return;
    result.push_back(node->val);   // Root -> Left -> Right (used for copy/serialize)
    preorder(node->left, result);
    preorder(node->right, result);
}

void postorder(TreeNode* node, vector<int>& result) {
    if (!node) return;
    postorder(node->left, result);
    postorder(node->right, result);
    result.push_back(node->val);   // Left -> Right -> Root (used for delete/evaluate)
}

// --- Level-order (BFS) ---
vector<vector<int>> levelOrder(TreeNode* root) {
    if (!root) return {};
    vector<vector<int>> result;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int size = q.size();
        vector<int> level;
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front(); q.pop();
            level.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}

// --- Iterative Inorder (using stack) ---
vector<int> inorderIterative(TreeNode* root) {
    vector<int> result;
    stack<TreeNode*> stk;
    TreeNode* curr = root;
    while (curr || !stk.empty()) {
        while (curr) { stk.push(curr); curr = curr->left; }
        curr = stk.top(); stk.pop();
        result.push_back(curr->val);
        curr = curr->right;
    }
    return result;
}

// --- Iterative Preorder (using stack) ---
vector<int> preorderIterative(TreeNode* root) {
    if (!root) return {};
    vector<int> result;
    stack<TreeNode*> stk;
    stk.push(root);
    while (!stk.empty()) {
        TreeNode* node = stk.top(); stk.pop();
        result.push_back(node->val);
        if (node->right) stk.push(node->right);  // push right first
        if (node->left)  stk.push(node->left);
    }
    return result;
}
```

### 8.4 Binary Search Tree (BST)

**BST Property:** left subtree < node < right subtree

| Operation | Average | Worst (skewed) |
|-----------|---------|----------------|
| Search    | O(log n)| O(n)           |
| Insert    | O(log n)| O(n)           |
| Delete    | O(log n)| O(n)           |

```cpp
// BST Insert
TreeNode* insert(TreeNode* root, int val) {
    if (!root) return new TreeNode(val);
    if (val < root->val) root->left  = insert(root->left,  val);
    else if (val > root->val) root->right = insert(root->right, val);
    return root;
}

// BST Delete
TreeNode* deleteNode(TreeNode* root, int key) {
    if (!root) return nullptr;
    if (key < root->val) {
        root->left = deleteNode(root->left, key);
    } else if (key > root->val) {
        root->right = deleteNode(root->right, key);
    } else {
        if (!root->left)  return root->right;
        if (!root->right) return root->left;
        // Two children: replace with inorder successor (min of right subtree)
        TreeNode* minNode = root->right;
        while (minNode->left) minNode = minNode->left;
        root->val = minNode->val;
        root->right = deleteNode(root->right, minNode->val);
    }
    return root;
}

// Validate BST (pass min/max bounds)
bool isValidBST(TreeNode* node, long long minVal, long long maxVal) {
    if (!node) return true;
    if (node->val <= minVal || node->val >= maxVal) return false;
    return isValidBST(node->left,  minVal, node->val) &&
           isValidBST(node->right, node->val, maxVal);
}
bool isValidBST(TreeNode* root) {
    return isValidBST(root, LLONG_MIN, LLONG_MAX);
}
```

### 8.5 AVL Tree

Self-balancing BST. After insert/delete, **rotations** restore balance factor.

```
Balance Factor = height(left) - height(right)
Valid range: {-1, 0, 1}

Rotations:
- Left Rotation:  right-heavy -> rotate left
- Right Rotation: left-heavy  -> rotate right
- Left-Right:     left child is right-heavy -> left rotate child, then right rotate
- Right-Left:     right child is left-heavy -> right rotate child, then left rotate
```

### 8.6 Lowest Common Ancestor (LCA)

```cpp
TreeNode* lca(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root || root == p || root == q) return root;
    TreeNode* left  = lca(root->left,  p, q);
    TreeNode* right = lca(root->right, p, q);
    if (left && right) return root;   // p in one subtree, q in other
    return left ? left : right;
}
```

### 8.7 Tree Height & Diameter

```cpp
// Max depth (height) of tree
int maxDepth(TreeNode* root) {
    if (!root) return 0;
    return 1 + max(maxDepth(root->left), maxDepth(root->right));
}

// Diameter — longest path between any two nodes
int diameterOfBinaryTree(TreeNode* root) {
    int diameter = 0;
    function<int(TreeNode*)> dfs = [&](TreeNode* node) -> int {
        if (!node) return 0;
        int left  = dfs(node->left);
        int right = dfs(node->right);
        diameter = max(diameter, left + right);
        return 1 + max(left, right);
    };
    dfs(root);
    return diameter;
}

// Check if tree is balanced (|height difference| <= 1 at every node)
int checkBalanced(TreeNode* node) {
    if (!node) return 0;
    int left  = checkBalanced(node->left);
    if (left == -1) return -1;
    int right = checkBalanced(node->right);
    if (right == -1) return -1;
    if (abs(left - right) > 1) return -1;
    return 1 + max(left, right);
}
bool isBalanced(TreeNode* root) { return checkBalanced(root) != -1; }
```

### 8.8 Serialize / Deserialize Binary Tree

```cpp
// Preorder serialization with null markers
string serialize(TreeNode* root) {
    if (!root) return "#,";
    return to_string(root->val) + "," + serialize(root->left) + serialize(root->right);
}

TreeNode* deserialize(istringstream& ss) {
    string token;
    getline(ss, token, ',');
    if (token == "#") return nullptr;
    TreeNode* node = new TreeNode(stoi(token));
    node->left  = deserialize(ss);
    node->right = deserialize(ss);
    return node;
}
TreeNode* deserialize(string data) {
    istringstream ss(data);
    return deserialize(ss);
}
```

### 8.9 Common Tree Problems

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

### 9.2 C++ Priority Queue API

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

// Custom comparator (min-heap by first element of pair)
using P = pair<int,int>;
priority_queue<P, vector<P>, greater<P>> pq;

// K largest elements: maintain min-heap of size k
priority_queue<int, vector<int>, greater<int>> kLargest;
for (int x : nums) {
    kLargest.push(x);
    if ((int)kLargest.size() > k) kLargest.pop();
}
// kLargest.top() = k-th largest
```

### 9.3 Top-K Problems

```cpp
// K most frequent elements — O(n log k)
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int n : nums) freq[n]++;
    // min-heap: {count, value}
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    for (auto& [val, cnt] : freq) {
        pq.push({cnt, val});
        if ((int)pq.size() > k) pq.pop();
    }
    vector<int> result;
    while (!pq.empty()) { result.push_back(pq.top().second); pq.pop(); }
    return result;
}

// K closest points to origin — O(n log k)
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    // max-heap of size k (distance, index)
    using T = pair<int,int>;
    priority_queue<T> pq;
    for (int i = 0; i < (int)points.size(); i++) {
        int d = points[i][0]*points[i][0] + points[i][1]*points[i][1];
        pq.push({d, i});
        if ((int)pq.size() > k) pq.pop();
    }
    vector<vector<int>> result;
    while (!pq.empty()) { result.push_back(points[pq.top().second]); pq.pop(); }
    return result;
}

// Merge K sorted lists — O(N log K) where N = total nodes
// Uses min-heap of (value, list_index, element_index)
```

### 9.4 Median from Data Stream

```cpp
// Two heaps: max-heap for lower half, min-heap for upper half
class MedianFinder {
    priority_queue<int> lower;                            // max-heap
    priority_queue<int, vector<int>, greater<int>> upper; // min-heap
public:
    void addNum(int num) {
        lower.push(num);
        upper.push(lower.top()); lower.pop();
        if (lower.size() < upper.size()) {
            lower.push(upper.top()); upper.pop();
        }
    }
    double findMedian() {
        if (lower.size() > upper.size()) return lower.top();
        return (lower.top() + upper.top()) / 2.0;
    }
};
```

---

## 10. Graphs

### 10.1 Representations

```cpp
// Adjacency List (space-efficient for sparse graphs)
int n;  // number of vertices
vector<vector<pair<int,int>>> graph(n);    // graph[u] = {(v, weight), ...}
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

```cpp
// Shortest path (unweighted), level-order traversal — O(V+E)
vector<int> bfs(int start, vector<vector<int>>& graph) {
    int n = graph.size();
    vector<int> dist(n, -1);
    queue<int> q;
    dist[start] = 0;
    q.push(start);
    while (!q.empty()) {
        int node = q.front(); q.pop();
        for (int neighbor : graph[node]) {
            if (dist[neighbor] == -1) {
                dist[neighbor] = dist[node] + 1;
                q.push(neighbor);
            }
        }
    }
    return dist;
}
```

#### DFS (Depth-First Search)

```cpp
// Recursive DFS — O(V+E)
void dfs(int node, vector<vector<int>>& graph, vector<bool>& visited) {
    visited[node] = true;
    for (int neighbor : graph[node])
        if (!visited[neighbor])
            dfs(neighbor, graph, visited);
}

// Iterative DFS
void dfsIterative(int start, vector<vector<int>>& graph) {
    int n = graph.size();
    vector<bool> visited(n, false);
    stack<int> stk;
    stk.push(start);
    visited[start] = true;
    while (!stk.empty()) {
        int node = stk.top(); stk.pop();
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                stk.push(neighbor);
            }
        }
    }
}
```

### 10.3 Shortest Path Algorithms

#### Dijkstra's Algorithm (non-negative weights)

```cpp
// O((V + E) log V) with min-heap
vector<int> dijkstra(int src, vector<vector<pair<int,int>>>& graph) {
    int n = graph.size();
    vector<int> dist(n, INT_MAX);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    dist[src] = 0;
    pq.push({0, src});
    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;  // stale entry
        for (auto [v, w] : graph[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

#### Bellman-Ford (handles negative weights)

```cpp
// O(VE); detects negative cycles
vector<int> bellmanFord(int src, int n, vector<tuple<int,int,int>>& edges) {
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;
    for (int i = 0; i < n - 1; i++) {         // relax V-1 times
        for (auto [u, v, w] : edges) {
            if (dist[u] != INT_MAX && dist[u] + w < dist[v])
                dist[v] = dist[u] + w;
        }
    }
    // Check for negative cycle
    for (auto [u, v, w] : edges)
        if (dist[u] != INT_MAX && dist[u] + w < dist[v])
            return {};  // negative cycle detected
    return dist;
}
```

#### Floyd-Warshall (all-pairs shortest path)

```cpp
// O(V³)
void floydWarshall(vector<vector<int>>& dist) {
    int n = dist.size();
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                if (dist[i][k] != INT_MAX && dist[k][j] != INT_MAX)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
}
```

### 10.4 Minimum Spanning Tree

#### Kruskal's Algorithm

```cpp
// O(E log E) — sort edges; use Union-Find to avoid cycles
struct Edge { int u, v, w; };

int find(vector<int>& parent, int x) {
    if (parent[x] != x) parent[x] = find(parent, parent[x]);
    return parent[x];
}
bool unite(vector<int>& parent, vector<int>& rank, int x, int y) {
    int px = find(parent, x), py = find(parent, y);
    if (px == py) return false;
    if (rank[px] < rank[py]) swap(px, py);
    parent[py] = px;
    if (rank[px] == rank[py]) rank[px]++;
    return true;
}

int kruskal(int n, vector<Edge>& edges) {
    sort(edges.begin(), edges.end(), [](const Edge& a, const Edge& b){ return a.w < b.w; });
    vector<int> parent(n), rank(n, 0);
    iota(parent.begin(), parent.end(), 0);
    int mstWeight = 0, edgesUsed = 0;
    for (auto& e : edges) {
        if (unite(parent, rank, e.u, e.v)) {
            mstWeight += e.w;
            if (++edgesUsed == n - 1) break;
        }
    }
    return mstWeight;
}
```

#### Prim's Algorithm

```cpp
// O((V + E) log V) — grow MST from a seed vertex using min-heap
int prim(int n, vector<vector<pair<int,int>>>& graph) {
    vector<bool> inMST(n, false);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, 0});  // (weight, vertex)
    int total = 0;
    while (!pq.empty()) {
        auto [w, u] = pq.top(); pq.pop();
        if (inMST[u]) continue;
        inMST[u] = true;
        total += w;
        for (auto [v, weight] : graph[u])
            if (!inMST[v]) pq.push({weight, v});
    }
    return total;
}
```

### 10.5 Topological Sort

For **Directed Acyclic Graphs (DAGs)** — linear ordering where all edges go forward.

```cpp
// Kahn's Algorithm (BFS-based) — detects cycle if not all nodes processed
vector<int> topoSort(int n, vector<vector<int>>& graph) {
    vector<int> inDegree(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : graph[u]) inDegree[v]++;
    queue<int> q;
    for (int v = 0; v < n; v++) if (inDegree[v] == 0) q.push(v);
    vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);
        for (int v : graph[u])
            if (--inDegree[v] == 0) q.push(v);
    }
    return (int)order.size() == n ? order : vector<int>{};  // empty = cycle
}

// DFS-based topological sort
void dfsTopoSort(int node, vector<vector<int>>& graph,
                 vector<bool>& visited, stack<int>& stk) {
    visited[node] = true;
    for (int neighbor : graph[node])
        if (!visited[neighbor])
            dfsTopoSort(neighbor, graph, visited, stk);
    stk.push(node);
}

vector<int> topoSortDFS(int n, vector<vector<int>>& graph) {
    vector<bool> visited(n, false);
    stack<int> stk;
    for (int i = 0; i < n; i++)
        if (!visited[i]) dfsTopoSort(i, graph, visited, stk);
    vector<int> order;
    while (!stk.empty()) { order.push_back(stk.top()); stk.pop(); }
    return order;
}
```

### 10.6 Union-Find (Disjoint Set Union)

```cpp
class UnionFind {
    vector<int> parent, rank_;
public:
    UnionFind(int n) : parent(n), rank_(n, 0) {
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
        if (rank_[px] < rank_[py]) swap(px, py);
        parent[py] = px;
        if (rank_[px] == rank_[py]) rank_[px]++;
        return true;
    }
    bool connected(int x, int y) { return find(x) == find(y); }
};
// Operations: near O(1) amortized with path compression + union by rank
```

**Applications:** Connected components, cycle detection, Kruskal's MST.

### 10.7 Cycle Detection

```cpp
// Undirected graph — Union-Find approach
bool hasCycleUndirected(int n, vector<pair<int,int>>& edges) {
    UnionFind uf(n);
    for (auto [u, v] : edges)
        if (!uf.unite(u, v)) return true;  // same component -> cycle
    return false;
}

// Directed graph — DFS with 3-color (white=0, gray=1, black=2)
bool dfsCycle(int node, vector<vector<int>>& graph, vector<int>& color) {
    color[node] = 1;  // gray (in progress)
    for (int neighbor : graph[node]) {
        if (color[neighbor] == 1) return true;   // back edge -> cycle
        if (color[neighbor] == 0 && dfsCycle(neighbor, graph, color)) return true;
    }
    color[node] = 2;  // black (done)
    return false;
}
bool hasCycleDirected(int n, vector<vector<int>>& graph) {
    vector<int> color(n, 0);
    for (int i = 0; i < n; i++)
        if (color[i] == 0 && dfsCycle(i, graph, color)) return true;
    return false;
}
```

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

```cpp
void merge(vector<int>& arr, int left, int mid, int right) {
    vector<int> temp(right - left + 1);
    int i = left, j = mid + 1, k = 0;
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp[k++] = arr[i++];
        else                  temp[k++] = arr[j++];
    }
    while (i <= mid)   temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];
    for (int x = 0; x < k; x++) arr[left + x] = temp[x];
}

void mergeSort(vector<int>& arr, int left, int right) {
    if (left >= right) return;
    int mid = left + (right - left) / 2;
    mergeSort(arr, left,    mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}
```

### 11.3 Quick Sort

```cpp
int partition(vector<int>& arr, int low, int high) {
    // Random pivot to avoid O(n²) worst case on sorted input
    int randIdx = low + rand() % (high - low + 1);
    swap(arr[randIdx], arr[high]);
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    swap(arr[i + 1], arr[high]);
    return i + 1;
}

void quickSort(vector<int>& arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low,    pi - 1);
        quickSort(arr, pi + 1, high);
    }
}
```

### 11.4 Heap Sort

```cpp
void heapify(vector<int>& arr, int n, int i) {
    int largest = i, l = 2*i+1, r = 2*i+2;
    if (l < n && arr[l] > arr[largest]) largest = l;
    if (r < n && arr[r] > arr[largest]) largest = r;
    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapify(arr, n, largest);
    }
}

void heapSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = n/2 - 1; i >= 0; i--) heapify(arr, n, i);  // build max-heap O(n)
    for (int i = n - 1; i > 0; i--) {
        swap(arr[0], arr[i]);   // move max to end
        heapify(arr, i, 0);     // restore heap on reduced array
    }
}
```

### 11.5 Non-Comparison Sorts

| Algorithm      | Time    | Space  | Constraint                      |
|----------------|---------|--------|---------------------------------|
| Counting Sort  | O(n+k)  | O(k)   | Integer keys in range [0, k]    |
| Radix Sort     | O(nk)   | O(n+k) | Integer keys; k = max digits    |
| Bucket Sort    | O(n+k)  | O(n)   | Uniformly distributed floats    |

```cpp
// Counting Sort
void countingSort(vector<int>& arr, int maxVal) {
    vector<int> count(maxVal + 1, 0);
    for (int x : arr) count[x]++;
    int idx = 0;
    for (int v = 0; v <= maxVal; v++)
        while (count[v]--) arr[idx++] = v;
}

// STL sort (introsort — O(n log n) worst case)
sort(arr.begin(), arr.end());                                   // ascending
sort(arr.begin(), arr.end(), greater<int>());                   // descending
sort(arr.begin(), arr.end(), [](int a, int b){ return a > b; }); // custom
stable_sort(arr.begin(), arr.end());                            // stable
```

---

## 12. Searching Algorithms

### 12.1 Binary Search

```cpp
int binarySearch(vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;  // avoid overflow
        if      (arr[mid] == target) return mid;
        else if (arr[mid] <  target) left  = mid + 1;
        else                         right = mid - 1;
    }
    return -1;
}
```

### 12.2 Binary Search Variants

```cpp
// Find leftmost position where condition is true (lower_bound)
// First index where arr[i] >= target
int lowerBound(vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size();
    while (left < right) {
        int mid = (left + right) / 2;
        if (arr[mid] < target) left = mid + 1;
        else right = mid;
    }
    return left;
}

// First index where arr[i] > target (upper_bound)
int upperBound(vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size();
    while (left < right) {
        int mid = (left + right) / 2;
        if (arr[mid] <= target) left = mid + 1;
        else right = mid;
    }
    return left;
}

// Binary search on answer space
// "Find minimum X such that condition(X) is true"
// Requires: condition is monotone (false...false, true...true)
int binarySearchOnAnswer(int minPossible, int maxPossible,
                         function<bool(int)> condition) {
    int left = minPossible, right = maxPossible;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (condition(mid)) right = mid;
        else left = mid + 1;
    }
    return left;
}

// STL wrappers (require sorted container)
auto it = lower_bound(arr.begin(), arr.end(), target);
auto it2 = upper_bound(arr.begin(), arr.end(), target);
bool found = binary_search(arr.begin(), arr.end(), target);
```

### 12.3 Search in Rotated Sorted Array

```cpp
int search(vector<int>& nums, int target) {
    int left = 0, right = (int)nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        if (nums[left] <= nums[mid]) {  // left half is sorted
            if (nums[left] <= target && target < nums[mid]) right = mid - 1;
            else left = mid + 1;
        } else {                        // right half is sorted
            if (nums[mid] < target && target <= nums[right]) left = mid + 1;
            else right = mid - 1;
        }
    }
    return -1;
}
```

---

## 13. Recursion & Backtracking

### 13.1 Recursion Template

```cpp
// Generic recursion pattern
ReturnType solve(Parameters params) {
    // 1. Base case
    if (baseCondition(params)) return baseResult;

    // 2. Recursive case — break into smaller subproblems
    auto subResult = solve(smallerParams);
    return combine(subResult, ...);
}
```

### 13.2 Backtracking Template

```cpp
void backtrack(State& state, int start, vector<Result>& results) {
    if (isSolution(state)) {
        results.push_back(state);
        return;
    }
    for (int i = start; i < choices.size(); i++) {
        if (!isValid(choices[i], state)) continue;
        makeChoice(state, choices[i]);          // choose
        backtrack(state, i + 1, results);       // explore
        undoChoice(state, choices[i]);          // unchoose (backtrack)
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
        if ((int)path.size() == (int)nums.size()) {
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

#### Combination Sum

```cpp
// Find all combinations that sum to target (elements can be reused)
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    vector<vector<int>> result;
    vector<int> path;
    function<void(int, int)> backtrack = [&](int start, int remaining) {
        if (remaining == 0) { result.push_back(path); return; }
        for (int i = start; i < (int)candidates.size(); i++) {
            if (candidates[i] > remaining) break;  // prune if sorted
            path.push_back(candidates[i]);
            backtrack(i, remaining - candidates[i]);  // i not i+1 (can reuse)
            path.pop_back();
        }
    };
    sort(candidates.begin(), candidates.end());
    backtrack(0, target);
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

#### Sudoku Solver

```cpp
bool isValidPlacement(vector<vector<char>>& board, int row, int col, char c) {
    for (int i = 0; i < 9; i++) {
        if (board[row][i] == c) return false;
        if (board[i][col] == c) return false;
        if (board[3*(row/3) + i/3][3*(col/3) + i%3] == c) return false;
    }
    return true;
}

bool solveSudoku(vector<vector<char>>& board) {
    for (int r = 0; r < 9; r++) {
        for (int c = 0; c < 9; c++) {
            if (board[r][c] != '.') continue;
            for (char ch = '1'; ch <= '9'; ch++) {
                if (isValidPlacement(board, r, c, ch)) {
                    board[r][c] = ch;
                    if (solveSudoku(board)) return true;
                    board[r][c] = '.';
                }
            }
            return false;  // no valid placement
        }
    }
    return true;  // board fully filled
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

```cpp
int climbStairs(int n) {
    if (n <= 2) return n;
    int a = 1, b = 2;
    for (int i = 3; i <= n; i++) {
        int c = a + b;
        a = b; b = c;
    }
    return b;  // O(1) space
}
```

#### House Robber

```cpp
int rob(vector<int>& nums) {
    int prev2 = 0, prev1 = 0;
    for (int num : nums) {
        int curr = max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

#### Longest Increasing Subsequence (LIS)

```cpp
// O(n²) DP
int lisDP(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp(n, 1);
    int maxLen = 1;
    for (int i = 1; i < n; i++)
        for (int j = 0; j < i; j++)
            if (nums[j] < nums[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
                maxLen = max(maxLen, dp[i]);
            }
    return maxLen;
}

// O(n log n) — patience sorting with binary search
int lisFast(vector<int>& nums) {
    vector<int> tails;  // tails[i] = smallest tail of all IS of length i+1
    for (int num : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), num);
        if (it == tails.end()) tails.push_back(num);
        else *it = num;
    }
    return (int)tails.size();
}
```

### 14.3 2D DP Patterns

#### Longest Common Subsequence (LCS)

```cpp
int longestCommonSubsequence(string s1, string s2) {
    int m = s1.size(), n = s2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            if (s1[i-1] == s2[j-1]) dp[i][j] = dp[i-1][j-1] + 1;
            else                     dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
    return dp[m][n];
}
```

#### Edit Distance

```cpp
int minDistance(string s1, string s2) {
    int m = s1.size(), n = s2.size();
    // dp[i][j] = min operations to convert s1[:i] to s2[:j]
    vector<vector<int>> dp(m + 1, vector<int>(n + 1));
    for (int i = 0; i <= m; i++) dp[i][0] = i;
    for (int j = 0; j <= n; j++) dp[0][j] = j;
    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            if (s1[i-1] == s2[j-1]) dp[i][j] = dp[i-1][j-1];
            else dp[i][j] = 1 + min({dp[i-1][j],    // delete from s1
                                     dp[i][j-1],    // insert into s1
                                     dp[i-1][j-1]}); // replace
    return dp[m][n];
}
```

#### 0/1 Knapsack

```cpp
int knapsack(int W, vector<int>& weight, vector<int>& value) {
    int n = weight.size();
    // Space-optimized: 1D dp, iterate weights in reverse
    vector<int> dp(W + 1, 0);
    for (int i = 0; i < n; i++)
        for (int w = W; w >= weight[i]; w--)
            dp[w] = max(dp[w], dp[w - weight[i]] + value[i]);
    return dp[W];
}
```

#### Coin Change

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, INT_MAX);
    dp[0] = 0;
    for (int a = 1; a <= amount; a++)
        for (int coin : coins)
            if (coin <= a && dp[a - coin] != INT_MAX)
                dp[a] = min(dp[a], dp[a - coin] + 1);
    return dp[amount] == INT_MAX ? -1 : dp[amount];
}
```

#### Unique Paths

```cpp
int uniquePaths(int m, int n) {
    vector<int> dp(n, 1);  // base: one way to reach each cell in first row
    for (int i = 1; i < m; i++)
        for (int j = 1; j < n; j++)
            dp[j] += dp[j - 1];
    return dp[n - 1];
}
```

#### Word Break

```cpp
bool wordBreak(string s, vector<string>& wordDict) {
    unordered_set<string> dict(wordDict.begin(), wordDict.end());
    int n = s.size();
    vector<bool> dp(n + 1, false);
    dp[0] = true;
    for (int i = 1; i <= n; i++)
        for (int j = 0; j < i; j++)
            if (dp[j] && dict.count(s.substr(j, i - j))) { dp[i] = true; break; }
    return dp[n];
}
```

#### Longest Palindromic Substring (DP)

```cpp
string longestPalindromicSubstringDP(string s) {
    int n = s.size(), start = 0, maxLen = 1;
    vector<vector<bool>> dp(n, vector<bool>(n, false));
    for (int i = 0; i < n; i++) dp[i][i] = true;
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            dp[i][j] = (s[i] == s[j]) && (len == 2 || dp[i+1][j-1]);
            if (dp[i][j] && len > maxLen) { maxLen = len; start = i; }
        }
    }
    return s.substr(start, maxLen);
}
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

#### Jump Game (Can Reach End?)

```cpp
bool canJump(vector<int>& nums) {
    int maxReach = 0;
    for (int i = 0; i < (int)nums.size(); i++) {
        if (i > maxReach) return false;
        maxReach = max(maxReach, i + nums[i]);
    }
    return true;
}
```

#### Jump Game II (Min Jumps)

```cpp
int jump(vector<int>& nums) {
    int jumps = 0, currentEnd = 0, farthest = 0;
    for (int i = 0; i < (int)nums.size() - 1; i++) {
        farthest = max(farthest, i + nums[i]);
        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
        }
    }
    return jumps;
}
```

#### Activity Selection / Interval Scheduling

```cpp
// Maximum number of non-overlapping intervals
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end(),
         [](const vector<int>& a, const vector<int>& b){ return a[1] < b[1]; });
    int count = 0, end = INT_MIN;
    for (auto& iv : intervals) {
        if (iv[0] >= end) end = iv[1];  // no overlap, take it
        else count++;                    // overlap, remove it
    }
    return count;
}
```

#### Gas Station

```cpp
int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int total = 0, tank = 0, start = 0;
    for (int i = 0; i < (int)gas.size(); i++) {
        int diff = gas[i] - cost[i];
        total += diff;
        tank  += diff;
        if (tank < 0) { start = i + 1; tank = 0; }  // can't start from here
    }
    return total >= 0 ? start : -1;
}
```

---

## 16. Trie (Prefix Tree)

```cpp
struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() : isEnd(false) {
        fill(children, children + 26, nullptr);
    }
};

class Trie {
    TrieNode* root;
public:
    Trie() : root(new TrieNode()) {}

    void insert(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }

    bool search(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }

    bool startsWith(const string& prefix) {
        TrieNode* node = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return true;
    }

    // Count words with given prefix
    int countWordsWithPrefix(const string& prefix) {
        TrieNode* node = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!node->children[idx]) return 0;
            node = node->children[idx];
        }
        // DFS count from here
        int count = 0;
        function<void(TrieNode*)> dfs = [&](TrieNode* n) {
            if (n->isEnd) count++;
            for (int i = 0; i < 26; i++)
                if (n->children[i]) dfs(n->children[i]);
        };
        dfs(node);
        return count;
    }
};
```

**Applications:** Autocomplete, spell check, IP routing, word search in grid, longest prefix matching.

---

## 17. Segment Tree & Binary Indexed Tree (BIT)

### 17.1 Segment Tree

- Supports **range queries** and **point updates** in O(log n)
- Build: O(n); each node stores aggregate for a range
- Use 4*n size array to store the tree

```cpp
class SegmentTree {
    int n;
    vector<int> tree;

    void build(vector<int>& arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = (start + end) / 2;
            build(arr, 2*node, start, mid);
            build(arr, 2*node+1, mid+1, end);
            tree[node] = tree[2*node] + tree[2*node+1];  // sum; change for min/max
        }
    }

    void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
        } else {
            int mid = (start + end) / 2;
            if (idx <= mid) update(2*node,   start, mid,   idx, val);
            else            update(2*node+1, mid+1, end,   idx, val);
            tree[node] = tree[2*node] + tree[2*node+1];
        }
    }

    int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;      // out of range
        if (l <= start && end <= r) return tree[node]; // fully in range
        int mid = (start + end) / 2;
        return query(2*node, start, mid, l, r) +
               query(2*node+1, mid+1, end, l, r);
    }

public:
    SegmentTree(vector<int>& arr) : n(arr.size()), tree(4 * arr.size(), 0) {
        build(arr, 1, 0, n - 1);
    }
    void update(int idx, int val)     { update(1, 0, n-1, idx, val); }
    int  query(int l, int r)          { return query(1, 0, n-1, l, r); }
};
```

### 17.2 Binary Indexed Tree (Fenwick Tree)

Simpler implementation; supports **prefix sum queries** and **point updates** in O(log n).

```cpp
class BIT {
    int n;
    vector<int> tree;
public:
    BIT(int n) : n(n), tree(n + 1, 0) {}

    void update(int i, int delta) {        // 1-indexed
        for (; i <= n; i += i & (-i))      // add lowest set bit
            tree[i] += delta;
    }

    int query(int i) {                     // prefix sum [1..i]
        int total = 0;
        for (; i > 0; i -= i & (-i))       // remove lowest set bit
            total += tree[i];
        return total;
    }

    int rangeQuery(int l, int r) {         // sum [l..r] (1-indexed)
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
n & (n-1)              // Clear lowest set bit; n==0 iff n is power of 2
n & (-n)               // Isolate lowest set bit (LSB)
n ^ n                  // == 0
n ^ 0                  // == n
a ^ b ^ a              // == b (XOR cancels duplicates — find single number)
(n >> i) & 1           // Check if i-th bit is set
n | (1 << i)           // Set i-th bit
n & ~(1 << i)          // Clear i-th bit
n ^ (1 << i)           // Toggle i-th bit
__builtin_popcount(n)  // Count set bits (Hamming weight) — GCC intrinsic
__builtin_ctz(n)       // Count trailing zeros (position of LSB)
__builtin_clz(n)       // Count leading zeros
```

### 18.3 Classic Bit Problems

```cpp
// Single Number — all others appear twice
int singleNumber(vector<int>& nums) {
    int result = 0;
    for (int n : nums) result ^= n;
    return result;
}

// Count set bits (Brian Kernighan's algorithm)
int countBits(int n) {
    int count = 0;
    while (n) { n &= (n - 1); count++; }
    return count;
}

// Power of two
bool isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}

// Reverse bits of a 32-bit integer
uint32_t reverseBits(uint32_t n) {
    uint32_t result = 0;
    for (int i = 0; i < 32; i++) {
        result = (result << 1) | (n & 1);
        n >>= 1;
    }
    return result;
}

// Sum of two integers without +/-
int getSum(int a, int b) {
    while (b != 0) {
        unsigned carry = (unsigned)(a & b) << 1;
        a = a ^ b;
        b = carry;
    }
    return a;
}

// Enumerate all subsets of a set of size n
for (int mask = 0; mask < (1 << n); mask++) {
    // mask has bit i set iff element i is in this subset
    for (int i = 0; i < n; i++)
        if (mask & (1 << i)) { /* element i is in subset */ }
}
```

### 18.4 Classic Bit Problems Table

| Problem                       | Trick                                    |
|-------------------------------|------------------------------------------|
| Single Number (all others 2x) | XOR all numbers; duplicates cancel       |
| Single Number (all others 3x) | Count bits mod 3                         |
| Count set bits                | `n & (n-1)` loop                         |
| Power of two                  | `n > 0 && (n & (n-1)) == 0`             |
| Reverse bits                  | Extract LSB, shift result left, repeat 32x |
| Sum without +                 | `a ^ b` (no carry) + `(a & b) << 1` (carry) |
| Missing number (0..n)         | XOR all indices and all values           |

---

## 19. Math & Number Theory

### 19.1 Essential Math for DSA

```cpp
// GCD (Euclidean algorithm) — O(log min(a,b))
int gcd(int a, int b) {
    while (b) { int t = b; b = a % b; a = t; }
    return a;
}
// C++17: __gcd(a, b) or std::gcd(a, b)

// LCM — lcm(a,b) = a/gcd(a,b)*b (divide first to avoid overflow)
long long lcm(long long a, long long b) { return a / gcd(a, b) * b; }

// Power with modular exponentiation — O(log exp)
long long power(long long base, long long exp, long long mod) {
    long long result = 1;
    base %= mod;
    while (exp > 0) {
        if (exp & 1) result = result * base % mod;
        base = base * base % mod;
        exp >>= 1;
    }
    return result;
}

// Sieve of Eratosthenes — all primes up to n in O(n log log n)
vector<int> sieve(int n) {
    vector<bool> isPrime(n + 1, true);
    isPrime[0] = isPrime[1] = false;
    for (int i = 2; (long long)i * i <= n; i++)
        if (isPrime[i])
            for (int j = i * i; j <= n; j += i)
                isPrime[j] = false;
    vector<int> primes;
    for (int i = 2; i <= n; i++)
        if (isPrime[i]) primes.push_back(i);
    return primes;
}

// Check if prime in O(sqrt(n))
bool isPrime(int n) {
    if (n < 2) return false;
    for (int i = 2; (long long)i * i <= n; i++)
        if (n % i == 0) return false;
    return true;
}
```

### 19.2 Combinatorics

```cpp
// Pascal's Triangle — C(n, k) in O(n²) precompute
const int MAXN = 1001;
long long C[MAXN][MAXN];
void buildPascal() {
    C[0][0] = 1;
    for (int n = 1; n < MAXN; n++) {
        C[n][0] = 1;
        for (int k = 1; k <= n; k++)
            C[n][k] = C[n-1][k-1] + C[n-1][k];
    }
}

// nCr with modular inverse (Fermat's little theorem, mod p must be prime)
// C(n, r) = n! / (r! * (n-r)!) mod p
// Use: a^(-1) mod p = a^(p-2) mod p
long long nCr(int n, int r, long long MOD) {
    if (r > n) return 0;
    long long num = 1, den = 1;
    for (int i = 0; i < r; i++) {
        num = num * ((n - i) % MOD) % MOD;
        den = den * ((i + 1) % MOD) % MOD;
    }
    return num * power(den, MOD - 2, MOD) % MOD;
}
```

### 19.3 Fast I/O (Competitive Programming)

```cpp
// At top of main() for faster cin/cout
ios_base::sync_with_stdio(false);
cin.tie(nullptr);
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
