# Data Structures & Algorithms — Detailed Interview, Exam & Multi-Language Guide

> **Languages:** C++ + Python + JavaScript
>
> **Focus:** problem-solving patterns, complexity, data structures, algorithms, interview strategy, implementation templates, edge cases, and coding-round preparation across common company tech stacks.

---

## 1. DSA Mental Model

Data structures organize information so operations can be performed efficiently. Algorithms describe how to transform input into output.

The interview skill is **pattern recognition**, not memorizing hundreds of solutions.

```text
Problem → Constraints → Brute force → Bottleneck → Pattern → Data structure
       → Invariant/correctness → Complexity → Code → Edge cases
```

### Pattern recognition

| Signal | Pattern |
|---|---|
| Sorted input | Binary search / two pointers |
| Contiguous range | Sliding window / prefix sum |
| Frequency or membership | Hash map / set |
| Next greater/smaller | Monotonic stack |
| Top K / repeated min/max | Heap |
| Shortest unweighted path | BFS |
| Non-negative weighted shortest path | Dijkstra |
| Dependencies/order | Topological sort |
| Connectivity | DFS/BFS/DSU |
| All combinations | Backtracking |
| Repeated overlapping states | DP |
| Prefix lookup | Trie |
| “Can we achieve X?” | Binary search on answer |

---

# 2. Complexity Analysis

| Complexity | Typical example |
|---|---|
| O(1) | Array access |
| O(log n) | Binary search |
| O(n) | Single traversal |
| O(n log n) | Efficient comparison sort |
| O(n²) | Pairwise comparison |
| O(2ⁿ) | Subset enumeration |
| O(n!) | Permutations |

Big-O describes asymptotic growth. Real performance also depends on constants, allocations, cache locality, runtime, and input size.

### Best / average / worst case

State which one you mean. Hash lookup is typically O(1) average but can degrade to O(n) worst case.

### Space

Separate input storage, auxiliary space, recursion stack, and output space.

### Amortized analysis

A dynamic array append is O(1) amortized even though occasional resizing costs O(n).

---

# 3. Interview Problem-Solving Framework

1. Restate the problem.
2. Clarify constraints and assumptions.
3. Give brute force.
4. Identify the bottleneck.
5. Recognize the pattern.
6. Choose the data structure.
7. State the invariant.
8. Explain correctness.
9. Analyze time and space.
10. Code.
11. Test edge cases.

Useful heuristic:

```text
n ≈ 20    → exponential may be possible
n ≈ 10³   → O(n²) may be possible
n ≈ 10⁵   → usually O(n log n) or O(n)
```

---

# 4. Arrays

Dynamic arrays typically provide O(1) indexing and O(1) amortized append; middle insertion/deletion is O(n).

## 4.1 Two Sum — Hash Map

**Time:** O(n) average. **Space:** O(n).

### C++
```cpp
vector<int> twoSum(const vector<int>& nums, int target) {
  unordered_map<int,int> index;
  for (int i=0;i<(int)nums.size();++i) {
    int need=target-nums[i];
    if (index.count(need)) return {
      index[need],i}
      ;
      index[nums[i]]=i;
    }
    return {
    }
    ;
  }
```
### Python
```python
def two_sum(nums:
    list[int], target: int) -> list[int]:
index = {}
for i, x in enumerate(nums):
need = target - x
if need in index:
return [index[need], i]
index[x] = i
return []
```
### JavaScript
```javascript
function twoSum(nums, target) {
  const index = new Map();
  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (index.has(need)) return [index.get(need), i];
    index.set(nums[i], i);
  }
  return [];
}
```

## 4.2 Maximum Subarray — Kadane

Track the best sum ending at the current position. **Time:** O(n), **space:** O(1).

### C++
```cpp
long long maxSubarraySum(const vector<int>& nums) {
  long long current=nums[0], best=nums[0];
  for (int i=1;i<(int)nums.size();++i) {
    current=max((long long)nums[i],current+nums[i]);
    best=max(best,current);
  }
  return best;
}
```
### Python
```python
def max_subarray_sum(nums:
    list[int]) -> int:
current = best = nums[0]
for x in nums[1:]:
current = max(x, current + x)
best = max(best, current)
return best
```
### JavaScript
```javascript
function maxSubarraySum(nums) {
  let current = nums[0], best = nums[0];
  for (let i = 1; i < nums.length; i++) {
    current = Math.max(nums[i], current + nums[i]);
    best = Math.max(best, current);
  }
  return best;
}
```

**Pitfall:** initializing the answer to zero fails for all-negative input.

## 4.3 Prefix Sum

`prefix[i+1] = prefix[i] + nums[i]`; inclusive range `[l,r]` is `prefix[r+1]-prefix[l]`.

### C++
```cpp
vector<long long> buildPrefix(const vector<int>& a) {
  vector<long long> p(a.size()+1);
  for (size_t i=0;i<a.size();++i) p[i+1]=p[i]+a[i];
  return p;
}
```
### Python
```python
def build_prefix(a:
    list[int]) -> list[int]:
p = [0] * (len(a) + 1)
for i, x in enumerate(a):
p[i + 1] = p[i] + x
return p
```
### JavaScript
```javascript
function buildPrefix(a) {
  const p = new Array(a.length + 1).fill(0);
  for (let i = 0; i < a.length; i++) p[i + 1] = p[i] + a[i];
  return p;
}
```

## 4.4 Merge Intervals

Sort by start, then merge overlaps. **Time:** O(n log n).

### C++
```cpp
vector<vector<int>> mergeIntervals(vector<vector<int>> a) {
  sort(a.begin(),a.end());
  vector<vector<int>> ans;
  for (auto &x:a) {
    if (ans.empty() || ans.back()[1] < x[0]) ans.push_back(x);
    else ans.back()[1]=max(ans.back()[1],x[1]);
  }
  return ans;
}
```
### Python
```python
def merge_intervals(a:
    list[list[int]]) -> list[list[int]]:
a.sort()
ans = []
for start, end in a:
if not ans or ans[-1][1] < start:
ans.append([start, end])
else:
ans[-1][1] = max(ans[-1][1], end)
return ans
```
### JavaScript
```javascript
function mergeIntervals(a) {
  a.sort((x, y) => x[0] - y[0]);
  const ans = [];
  for (const [start, end] of a) {
    if (!ans.length || ans[ans.length - 1][1] < start) ans.push([start, end]);
    else ans[ans.length - 1][1] = Math.max(ans[ans.length - 1][1], end);
  }
  return ans;
}
```

**JavaScript pitfall:** default numeric `sort()` is lexicographic.

---

# 5. Strings

Master frequency counting, two pointers, sliding windows, palindrome expansion, KMP/Z, and rolling hash.

## 5.1 Valid Anagram

### C++
```cpp
bool isAnagram(const string& a,const string& b){
  if(a.size()!=b.size()) return false;
  array<int,26> f{
  }
  ;
  for(char c:a) ++f[c-'a'];
  for(char c:b) --f[c-'a'];
  return all_of(f.begin(),f.end(),[](int x){return x==0;});
}
```
### Python
```python
from collections import Counter
def is_anagram(a:
    str, b: str) -> bool:
return Counter(a) == Counter(b)
```
### JavaScript
```javascript
function isAnagram(a, b) {
  if (a.length !== b.length) return false;
  const f = new Map();
  for (const c of a) f.set(c, (f.get(c) ?? 0) + 1);
  for (const c of b) {
    if (!f.has(c)) return false;
    const n = f.get(c) - 1;
    if (n === 0) f.delete(c);
    else f.set(c, n);
  }
  return f.size === 0;
}
```

## 5.2 Longest Unique Substring

Sliding window + last-seen index. **Time:** O(n).

### C++
```cpp
int longestUnique(const string& s){
  unordered_map<char,int> last;
  int left=0,best=0;
  for(int right=0;right<(int)s.size();++right){
    if(last.count(s[right])) left=max(left,last[s[right]]+1);
    last[s[right]]=right;
    best=max(best,right-left+1);
  }
  return best;
}
```
### Python
```python
def longest_unique(s:
    str) -> int:
last = {}
left = best = 0
for right, c in enumerate(s):
if c in last:
left = max(left, last[c] + 1)
last[c] = right
best = max(best, right - left + 1)
return best
```
### JavaScript
```javascript
function longestUnique(s) {
  const last = new Map();
  let left = 0, best = 0;
  for (let right = 0; right < s.length; right++) {
    const c = s[right];
    if (last.has(c)) left = Math.max(left, last.get(c) + 1);
    last.set(c, right);
    best = Math.max(best, right - left + 1);
  }
  return best;
}
```

---

# 6. Hashing

| Concept | C++ | Python | JavaScript |
|---|---|---|---|
| Hash map | `unordered_map` | `dict` | `Map` |
| Hash set | `unordered_set` | `set` | `Set` |
| Ordered map | `map` | library/sorted structure | no direct built-in equivalent |

Average hash lookup is O(1); worst case can degrade.

### Frequency

```cpp
unordered_map<int,int> freq;
for(int x:nums) ++freq[x];
```

```python
from collections import Counter
freq = Counter(nums)
```

```javascript
const freq = new Map();
for (const x of nums) freq.set(x, (freq.get(x) ?? 0) + 1);
```

---

# 7. Two Pointers

Use when sorted order or another monotonic property lets pointer movement eliminate candidates.

## Pair Sum in Sorted Array

### C++
```cpp
pair<int,int> pairSum(const vector<int>& a,int target){
  int l=0,r=(int)a.size()-1;
  while(l<r){
    long long s=(long long)a[l]+a[r];
    if(s==target) return {
      l,r}
      ;
      if(s<target) ++l;
      else --r;
    }
    return {
      -1,-1}
      ;
    }
```
### Python
```python
def pair_sum(a:
    list[int], target: int) -> tuple[int, int]:
l, r = 0, len(a) - 1
while l < r:
s = a[l] + a[r]
if s == target:
    return l, r
if s < target:
    l += 1
else:
    r -= 1
return -1, -1
```
### JavaScript
```javascript
function pairSum(a, target) {
  let l=0,r=a.length-1;
  while(l<r){
    const s=a[l]+a[r];
    if(s===target) return [l,r];
    if(s<target) l++;
    else r--;
  }
  return [-1,-1];
}
```

---

# 8. Sliding Window

Use for contiguous ranges when window state can be updated incrementally.

## Fixed Window Maximum Sum

### C++
```cpp
long long maxWindow(const vector<int>& a,int k){
  if(k<=0 || k>(int)a.size()) return 0;
  long long w=0;
  for(int i=0;i<k;++i) w+=a[i];
  long long best=w;
  for(int i=k;i<(int)a.size();++i){
    w+=a[i]-a[i-k];
    best=max(best,w);
  }
  return best;
}
```
### Python
```python
def max_window(a:
    list[int], k: int) -> int:
if k <= 0 or k > len(a):
    return 0
w = sum(a[:k]);
best = w
for i in range(k, len(a)):
w += a[i] - a[i-k]
best = max(best, w)
return best
```
### JavaScript
```javascript
function maxWindow(a,k){
  if(k<=0 || k>a.length) return 0;
  let w=0;
  for(let i=0;i<k;i++) w+=a[i];
  let best=w;
  for(let i=k;i<a.length;i++){
    w+=a[i]-a[i-k];
    best=Math.max(best,w);
  }
  return best;
}
```

**Important:** variable-window sum techniques such as “shrink while sum > k” generally require non-negative values. Negative values break monotonicity.

---

# 9. Prefix Sum — Subarray Sum = K

If `prefix[j] - prefix[i] = k`, then the earlier prefix is `prefix[j]-k`. Store counts.

### C++
```cpp
int subarraySumK(const vector<int>& a,int k){
  unordered_map<long long,int> cnt{
    {
      0,1}
    }
    ;
    long long p=0;
    int ans=0;
    for(int x:a){
      p+=x;
      if(cnt.count(p-k)) ans+=cnt[p-k];
      ++cnt[p];
    }
    return ans;
  }
```
### Python
```python
def subarray_sum_k(a:
    list[int], k: int) -> int:
cnt = {0: 1};
p = ans = 0
for x in a:
p += x
ans += cnt.get(p-k, 0)
cnt[p] = cnt.get(p, 0) + 1
return ans
```
### JavaScript
```javascript
function subarraySumK(a,k){
  const cnt=new Map([[0,1]]);
  let p=0,ans=0;
  for(const x of a){
    p+=x;
    ans+=cnt.get(p-k)??0;
    cnt.set(p,(cnt.get(p)??0)+1);
  }
  return ans;
}
```

---

# 10. Binary Search

The general pattern is searching a monotonic predicate.

## Standard Search

### C++
```cpp
int binarySearch(const vector<int>& a,int target){
  int l=0,r=(int)a.size()-1;
  while(l<=r){
    int m=l+(r-l)/2;
    if(a[m]==target) return m;
    if(a[m]<target) l=m+1;
    else r=m-1;
  }
  return -1;
}
```
### Python
```python
def binary_search(a:
    list[int], target: int) -> int:
l, r = 0, len(a)-1
while l <= r:
m = l + (r-l)//2
if a[m] == target:
    return m
if a[m] < target:
    l = m+1
else:
    r = m-1
return -1
```
### JavaScript
```javascript
function binarySearch(a,target){
  let l=0,r=a.length-1;
  while(l<=r){
    const m=l+Math.floor((r-l)/2);
    if(a[m]===target) return m;
    if(a[m]<target) l=m+1;
    else r=m-1;
  }
  return -1;
}
```

## Lower Bound

First index where `a[i] >= target`.

### C++
```cpp
int lowerBound(const vector<int>& a,int target){
  int l=0,r=a.size();
  while(l<r){
    int m=l+(r-l)/2;
    if(a[m]<target) l=m+1;
    else r=m;
  }
  return l;
}
```
### Python
```python
from bisect import bisect_left
def lower_bound(a, target):
    return bisect_left(a, target)
```
### JavaScript
```javascript
function lowerBound(a,target){
  let l=0,r=a.length;
  while(l<r){
    const m=l+Math.floor((r-l)/2);
    if(a[m]<target) l=m+1;
    else r=m;
  }
  return l;
}
```

### Binary search on answer

If feasibility looks like `false false false true true`, binary search the first feasible answer. Common examples: minimum capacity/speed, page allocation, maximum minimum distance.

---

# 11. Linked Lists

Linked lists trade random access for pointer rewiring.

## Node

### C++
```cpp
struct ListNode {
  int val;
  ListNode* next;
  ListNode(int x):val(x),next(nullptr){
  }
}
;
```
### Python
```python
class ListNode:
def __init__(self, val=0, next=None):
    self.val, self.next = val, next
```
### JavaScript
```javascript
class ListNode {
  constructor(val=0,next=null){
    this.val=val;
    this.next=next;
  }
}
```

## Reverse List

### C++
```cpp
ListNode* reverseList(ListNode* head){
  ListNode *prev=nullptr,*cur=head;
  while(cur){
    auto* next=cur->next;
    cur->next=prev;
    prev=cur;
    cur=next;
  }
  return prev;
}
```
### Python
```python
def reverse_list(head):
prev=None;
cur=head
while cur:
nxt=cur.next;
cur.next=prev;
prev,cur=cur,nxt
return prev
```
### JavaScript
```javascript
function reverseList(head){
  let prev=null,cur=head;
  while(cur){
    const next=cur.next;
    cur.next=prev;
    prev=cur;
    cur=next;
  }
  return prev;
}
```

## Cycle Detection — Floyd

### C++
```cpp
bool hasCycle(ListNode* head){
  ListNode *slow=head,*fast=head;
  while(fast&&fast->next){
    slow=slow->next;
    fast=fast->next->next;
    if(slow==fast) return true;
  }
  return false;
}
```
### Python
```python
def has_cycle(head):
slow=fast=head
while fast and fast.next:
slow=slow.next;
fast=fast.next.next
if slow is fast:
    return True
return False
```
### JavaScript
```javascript
function hasCycle(head){
  let slow=head,fast=head;
  while(fast&&fast.next){
    slow=slow.next;
    fast=fast.next.next;
    if(slow===fast) return true;
  }
  return false;
}
```

Other must-know list problems: find middle, merge sorted lists, remove nth from end, reverse k-group, palindrome list, copy random-pointer list, and LRU cache.

---

# 12. Stacks

LIFO. Applications: parentheses, DFS, parsing, monotonic-stack problems.

## Valid Parentheses

### C++
```cpp
bool validParentheses(const string& s){
  stack<char> st;
  for(char c:s){
    if(c=='('||c=='['||c=='{') st.push(c);
    else {
      if(st.empty()) return false;
      char t=st.top();
      st.pop();
      if((c==')'&&t!='(')||(c==']'&&t!='[')||(c=='}'&&t!='{')) return false;
    }
  }
  return st.empty();
}
```
### Python
```python
def valid_parentheses(s):
pairs={')':'(',']':'[','}':'{'};
st=[]
for c in s:
if c in '([{':
    st.append(c)
elif not st or st.pop()!=pairs[c]:
    return False
return not st
```
### JavaScript
```javascript
function validParentheses(s){
  const pairs=new Map([[')','('],[']','['],['}','{']]), st=[];
  for(const c of s){
    if(pairs.has(c)){
      if(st.pop()!==pairs.get(c)) return false;
    }
    else st.push(c);
  }
  return st.length===0;
}
```

## Monotonic Stack — Next Greater

Each element is pushed/popped at most once → O(n).

### C++
```cpp
vector<int> nextGreater(const vector<int>& a){
  vector<int> ans(a.size(),-1);
  stack<int> st;
  for(int i=0;i<(int)a.size();++i){
    while(!st.empty()&&a[st.top()]<a[i]){
      ans[st.top()]=a[i];
      st.pop();
    }
    st.push(i);
  }
  return ans;
}
```
### Python
```python
def next_greater(a):
ans=[-1]*len(a);
st=[]
for i,x in enumerate(a):
while st and a[st[-1]]<x:
    ans[st.pop()]=x
st.append(i)
return ans
```
### JavaScript
```javascript
function nextGreater(a){
  const ans=new Array(a.length).fill(-1),st=[];
  for(let i=0;i<a.length;i++){
    while(st.length&&a[st.at(-1)]<a[i]) ans[st.pop()]=a[i];
    st.push(i);
  }
  return ans;
}
```

---

# 13. Queues & Deques

### Queue templates

**C++**
```cpp
queue<int> q;
q.push(source);
while(!q.empty()){
  int u=q.front();
  q.pop();
}
```

**Python**
```python
from collections import deque
q=deque([source])
while q:
    u=q.popleft()
```

**JavaScript**
```javascript
const q=[source];
let head=0;
while(head<q.length){
  const u=q[head++];
}
```

**JS note:** avoid repeated `shift()` for large queues.

## Sliding Window Maximum

Use a decreasing deque of indices. **Time:** O(n).

### C++
```cpp
vector<int> maxWindow(const vector<int>& a,int k){
  deque<int> d;
  vector<int> ans;
  for(int i=0;i<(int)a.size();++i){
    while(!d.empty()&&d.front()<=i-k)d.pop_front();
    while(!d.empty()&&a[d.back()]<=a[i])d.pop_back();
    d.push_back(i);
    if(i>=k-1)ans.push_back(a[d.front()]);
  }
  return ans;
}
```
### Python
```python
from collections import deque
def max_window_values(a,k):
d=deque();
ans=[]
for i,x in enumerate(a):
while d and d[0]<=i-k:d.popleft()
while d and a[d[-1]]<=x:d.pop()
d.append(i)
if i>=k-1:ans.append(a[d[0]])
return ans
```
### JavaScript
```javascript
function maxWindowValues(a,k){
  const d=[],ans=[];
  let head=0;
  for(let i=0;i<a.length;i++){
    while(head<d.length&&d[head]<=i-k)head++;
    while(head<d.length&&a[d.at(-1)]<=a[i])d.pop();
    d.push(i);
    if(i>=k-1)ans.push(a[d[head]]);
  }
  return ans;
}
```

---

# 14. Sorting

| Algorithm | Best | Average | Worst | Stable |
|---|---:|---:|---:|---|
| Bubble | O(n) | O(n²) | O(n²) | Yes |
| Insertion | O(n) | O(n²) | O(n²) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | Usually no |
| Merge | O(n log n) | O(n log n) | O(n log n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | Usually no |
| Heap | O(n log n) | O(n log n) | O(n log n) | No |

Know how to implement merge sort and partition-based quicksort, but prefer the language's optimized sort in production.

### Merge sort — all three stacks

**C++**
```cpp
void mergeSort(vector<int>& a){
  if(a.size()<=1)return;
  vector<int> t(a.size());
  function<void(int,int)> f=[&](int l,int r){
    if(r-l<=1)return;
    int m=l+(r-l)/2;
    f(l,m);
    f(m,r);
    int i=l,j=m,k=l;
    while(i<m&&j<r)t[k++]=a[i]<=a[j]?a[i++]:a[j++];
    while(i<m)t[k++]=a[i++];
    while(j<r)t[k++]=a[j++];
    for(int p=l;p<r;++p)a[p]=t[p];
  }
  ;
  f(0,a.size());
}
```
**Python**
```python
def merge_sort(a):
if len(a)<=1:return a[:]
m=len(a)//2;
l=merge_sort(a[:m]);
r=merge_sort(a[m:]);
out=[];
i=j=0
while i<len(l) and j<len(r):
if l[i]<=r[j]:out.append(l[i]);
i+=1
else:out.append(r[j]);
j+=1
return out+l[i:]+r[j:]
```
**JavaScript**
```javascript
function mergeSort(a){
  if(a.length<=1)return [...a];
  const m=Math.floor(a.length/2);
  const l=mergeSort(a.slice(0,m)),r=mergeSort(a.slice(m)),out=[];
  let i=0,j=0;
  while(i<l.length&&j<r.length)out.push(l[i]<=r[j]?l[i++]:r[j++]);
  return out.concat(l.slice(i),r.slice(j));
}
```

---

# 15. Recursion & Backtracking

Pattern: **choose → recurse → undo**.

## Generate Subsets

### C++
```cpp
void dfs(const vector<int>& a,int i,vector<int>& path,vector<vector<int>>& ans){
  if(i==(int)a.size()){
    ans.push_back(path);
    return;
  }
  dfs(a,i+1,path,ans);
  path.push_back(a[i]);
  dfs(a,i+1,path,ans);
  path.pop_back();
}
```
### Python
```python
def subsets(a):
ans=[];
path=[]
def dfs(i):
if i==len(a):
    ans.append(path.copy());
return
dfs(i+1);
path.append(a[i]);
dfs(i+1);
path.pop()
dfs(0);
return ans
```
### JavaScript
```javascript
function subsets(a){
  const ans=[],path=[];
  function dfs(i){
    if(i===a.length){
      ans.push([...path]);
      return;
    }
    dfs(i+1);
    path.push(a[i]);
    dfs(i+1);
    path.pop();
  }
  dfs(0);
  return ans;
}
```

**Complexity:** O(n·2ⁿ) if each result is copied.

Other must-know backtracking: permutations, combination sum, subsets with duplicates, N-Queens, word search, and Sudoku.

---

# 16. Trees

A tree with n nodes has n−1 edges.

## Node

### C++
```cpp
struct TreeNode{
  int val;
  TreeNode*left,*right;
  TreeNode(int x):val(x),left(nullptr),right(nullptr){
  }
}
;
```
### Python
```python
class TreeNode:
def __init__(self,val=0,left=None,right=None):
    self.val,self.left,self.right=val,left,right
```
### JavaScript
```javascript
class TreeNode{
  constructor(val=0,left=null,right=null){
    this.val=val;
    this.left=left;
    this.right=right;
  }
}
```

## Traversals

```text
Preorder: root → left → right
Inorder: left → root → right
Postorder: left → right → root
```

**C++**
```cpp
void inorder(TreeNode* r,vector<int>&o){
  if(!r)return;
  inorder(r->left,o);
  o.push_back(r->val);
  inorder(r->right,o);
}
```
**Python**
```python
def inorder(r,out):
if not r:return
inorder(r.left,out);
out.append(r.val);
inorder(r.right,out)
```
**JavaScript**
```javascript
function inorder(r,out=[]){
  if(!r)return out;
  inorder(r.left,out);
  out.push(r.val);
  inorder(r.right,out);
  return out;
}
```

## Level Order

**C++**
```cpp
vector<vector<int>> levelOrder(TreeNode* root){
  if(!root)return{
  }
  ;
  queue<TreeNode*>q{
    {
      root}
    }
    ;
    vector<vector<int>>ans;
    while(!q.empty()){
      int n=q.size();
      vector<int>v;
      while(n--){
        auto*x=q.front();
        q.pop();
        v.push_back(x->val);
        if(x->left)q.push(x->left);
        if(x->right)q.push(x->right);
      }
      ans.push_back(v);
    }
    return ans;
  }
```
**Python**
```python
from collections import deque
def level_order(root):
if not root:return []
q=deque([root]);
ans=[]
while q:
level=[]
for _ in range(len(q)):
x=q.popleft();
level.append(x.val)
if x.left:q.append(x.left)
if x.right:q.append(x.right)
ans.append(level)
return ans
```
**JavaScript**
```javascript
function levelOrder(root){
  if(!root)return[];
  const q=[root];
  let h=0,ans=[];
  while(h<q.length){
    const end=q.length,level=[];
    while(h<end){
      const x=q[h++];
      level.push(x.val);
      if(x.left)q.push(x.left);
      if(x.right)q.push(x.right);
    }
    ans.push(level);
  }
  return ans;
}
```

## Maximum Depth

**C++**
```cpp
int maxDepth(TreeNode*r){
  return r?1+max(maxDepth(r->left),maxDepth(r->right)):0;
}
```
**Python**
```python
def max_depth(r):
    return 0 if not r else 1+max(max_depth(r.left),max_depth(r.right))
```
**JavaScript**
```javascript
function maxDepth(r){
  return r?1+Math.max(maxDepth(r.left),maxDepth(r.right)):0;
}
```

## LCA — Binary Tree

**C++**
```cpp
TreeNode* lca(TreeNode*r,TreeNode*p,TreeNode*q){
  if(!r||r==p||r==q)return r;
  auto*l=lca(r->left,p,q),*x=lca(r->right,p,q);
  return l&&x?r:l?l:x;
}
```
**Python**
```python
def lca(r,p,q):
if not r or r is p or r is q:return r
l=lca(r.left,p,q);
x=lca(r.right,p,q)
return r if l and x else l or x
```
**JavaScript**
```javascript
function lca(r,p,q){
  if(!r||r===p||r===q)return r;
  const l=lca(r.left,p,q),x=lca(r.right,p,q);
  return l&&x?r:l||x;
}
```

---

# 17. Binary Search Trees

BST invariant: every left value is smaller and every right value is larger (according to the problem's duplicate policy).

## Validate BST

Use global bounds or inorder monotonicity; local child comparisons are insufficient.

**C++**
```cpp
bool valid(TreeNode*r,long long lo,long long hi){
  if(!r)return true;
  if(r->val<=lo||r->val>=hi)return false;
  return valid(r->left,lo,r->val)&&valid(r->right,r->val,hi);
}
```
**Python**
```python
def valid(r,lo=float('-inf'),hi=float('inf')):
if not r:return True
if not lo<r.val<hi:return False
return valid(r.left,lo,r.val) and valid(r.right,r.val,hi)
```
**JavaScript**
```javascript
function validBST(r,lo=-Infinity,hi=Infinity){
  if(!r)return true;
  if(!(lo<r.val&&r.val<hi))return false;
  return validBST(r.left,lo,r.val)&&validBST(r.right,r.val,hi);
}
```

---

# 18. Heaps & Priority Queues

- Peek: O(1).
- Insert: O(log n).
- Extract: O(log n).
- Build heap: O(n).

## Top K

Keep a min-heap of size K for K largest elements.

**C++**
```cpp
vector<int> topK(const vector<int>&a,int k){
  priority_queue<int,vector<int>,greater<int>>q;
  for(int x:a){
    q.push(x);
    if((int)q.size()>k)q.pop();
  }
  vector<int>r;
  while(!q.empty()){
    r.push_back(q.top());
    q.pop();
  }
  return r;
}
```
**Python**
```python
import heapq
def top_k(a,k):
h=[]
for x in a:
heapq.heappush(h,x)
if len(h)>k:heapq.heappop(h)
return h
```
**JavaScript**
```javascript
// JavaScript has no standard heap;
implement MinHeap or use the interview platform's heap.
```

---

# 19. Graphs

- Adjacency list: O(V+E) space.
- Adjacency matrix: O(V²) space.

## DFS

**C++**
```cpp
void dfs(int u,const vector<vector<int>>&g,vector<bool>&seen){
  seen[u]=true;
  for(int v:g[u])if(!seen[v])dfs(v,g,seen);
}
```
**Python**
```python
def dfs(u,g,seen):
seen[u]=True
for v in g[u]:
if not seen[v]:dfs(v,g,seen)
```
**JavaScript**
```javascript
function dfs(u,g,seen){
  seen[u]=true;
  for(const v of g[u])if(!seen[v])dfs(v,g,seen);
}
```

## BFS — Unweighted Shortest Path

**C++**
```cpp
vector<int>bfs(int s,const vector<vector<int>>&g){
  vector<int>d(g.size(),-1);
  queue<int>q;
  q.push(s);
  d[s]=0;
  while(!q.empty()){
    int u=q.front();
    q.pop();
    for(int v:g[u])if(d[v]==-1){
      d[v]=d[u]+1;
      q.push(v);
    }
  }
  return d;
}
```
**Python**
```python
from collections import deque
def bfs(s,g):
d=[-1]*len(g);
d[s]=0;
q=deque([s])
while q:
u=q.popleft()
for v in g[u]:
if d[v]==-1:d[v]=d[u]+1;
q.append(v)
return d
```
**JavaScript**
```javascript
function bfs(s,g){
  const d=new Array(g.length).fill(-1),q=[s];
  let h=0;
  d[s]=0;
  while(h<q.length){
    const u=q[h++];
    for(const v of g[u])if(d[v]===-1){
      d[v]=d[u]+1;
      q.push(v);
    }
  }
  return d;
}
```

## Topological Sort — Kahn

A DAG has a valid ordering. If fewer than V nodes are processed, a cycle exists.

**C++**
```cpp
vector<int> topo(int n,const vector<vector<int>>&g){
  vector<int>in(n);
  for(int u=0;u<n;++u)for(int v:g[u])++in[v];
  queue<int>q;
  for(int i=0;i<n;++i)if(!in[i])q.push(i);
  vector<int>o;
  while(!q.empty()){
    int u=q.front();
    q.pop();
    o.push_back(u);
    for(int v:g[u])if(--in[v]==0)q.push(v);
  }
  return o.size()==(size_t)n?o:vector<int>{
  }
  ;
}
```
**Python**
```python
from collections import deque
def topo(n,g):
indeg=[0]*n
for u in range(n):
for v in g[u]:indeg[v]+=1
q=deque(i for i,d in enumerate(indeg) if d==0);
o=[]
while q:
u=q.popleft();
o.append(u)
for v in g[u]:
indeg[v]-=1
if indeg[v]==0:q.append(v)
return o if len(o)==n else []
```
**JavaScript**
```javascript
function topo(n,g){
  const d=new Array(n).fill(0);
  for(let u=0;u<n;u++)for(const v of g[u])d[v]++;
  const q=[];
  let h=0;
  for(let i=0;i<n;i++)if(d[i]===0)q.push(i);
  const o=[];
  while(h<q.length){
    const u=q[h++];
    o.push(u);
    for(const v of g[u])if(--d[v]===0)q.push(v);
  }
  return o.length===n?o:[];
}
```

## Dijkstra

Use only when edge weights are non-negative. Adjacency list + binary heap: O((V+E) log V).

**C++**
```cpp
vector<long long>dijkstra(int s,const vector<vector<pair<int,int>>>&g){
  const long long INF=LLONG_MAX/4;
  vector<long long>d(g.size(),INF);
  using S=pair<long long,int>;
  priority_queue<S,vector<S>,greater<S>>q;
  d[s]=0;
  q.push({0,s});
  while(!q.empty()){
    auto [du,u]=q.top();
    q.pop();
    if(du!=d[u])continue;
    for(auto [v,w]:g[u])if(d[v]>du+w){
      d[v]=du+w;
      q.push({d[v],v});
    }
  }
  return d;
}
```
**Python**
```python
import heapq
def dijkstra(s,g):
INF=10**30;
d=[INF]*len(g);
d[s]=0;
h=[(0,s)]
while h:
du,u=heapq.heappop(h)
if du!=d[u]:continue
for v,w in g[u]:
nd=du+w
if nd<d[v]:d[v]=nd;
heapq.heappush(h,(nd,v))
return d
```
**JavaScript**
```javascript
// Use a MinHeap of [distance,node]. JavaScript has no standard heap.
function dijkstra(s,g,heap){
  const d=new Array(g.length).fill(Infinity);
  d[s]=0;
  heap.push([0,s]);
  while(heap.size){
    const [du,u]=heap.pop();
    if(du!==d[u])continue;
    for(const [v,w] of g[u]){
      const nd=du+w;
      if(nd<d[v]){
        d[v]=nd;
        heap.push([nd,v]);
      }
    }
  }
  return d;
}
```

Other graph algorithms: Bellman-Ford, Floyd-Warshall, Prim, Kruskal, bipartite check, SCC, and DSU.

---

# 20. Disjoint Set Union

Path compression + union by size/rank gives O(α(n)) amortized operations.

**C++**
```cpp
class DSU{
  vector<int>p,sz;
  public:DSU(int n):p(n),sz(n,1){
    iota(p.begin(),p.end(),0);
  }
  int find(int x){
    return p[x]==x?x:p[x]=find(p[x]);
  }
  bool unite(int a,int b){
    a=find(a);
    b=find(b);
    if(a==b)return false;
    if(sz[a]<sz[b])swap(a,b);
    p[b]=a;
    sz[a]+=sz[b];
    return true;
  }
}
;
```
**Python**
```python
class DSU:
def __init__(self,n):self.p=list(range(n));
self.sz=[1]*n
def find(self,x):
if self.p[x]!=x:self.p[x]=self.find(self.p[x])
return self.p[x]
def union(self,a,b):
a,b=self.find(a),self.find(b)
if a==b:return False
if self.sz[a]<self.sz[b]:a,b=b,a
self.p[b]=a;
self.sz[a]+=self.sz[b];
return True
```
**JavaScript**
```javascript
class DSU{
  constructor(n){
    this.p=Array.from({length:n},(_,i)=>i);
    this.sz=new Array(n).fill(1);
  }
  find(x){
    if(this.p[x]!==x)this.p[x]=this.find(this.p[x]);
    return this.p[x];
  }
  union(a,b){
    a=this.find(a);
    b=this.find(b);
    if(a===b)return false;
    if(this.sz[a]<this.sz[b])[a,b]=[b,a];
    this.p[b]=a;
    this.sz[a]+=this.sz[b];
    return true;
  }
}
```

Applications: Kruskal, dynamic connectivity, components, undirected cycle detection.

---

# 21. Greedy Algorithms

A greedy algorithm needs a correctness argument, not just an intuition that the local choice “looks best.” Common proofs include exchange arguments, cut properties, and staying-ahead arguments.

Classic problems:

- Activity selection → earliest finish.
- Fractional knapsack → highest value/weight.
- Interval partitioning → min-heap of finishing times.
- Kruskal/Prim → MST cut property.

**Do not use fractional-knapsack reasoning for 0/1 knapsack.**

---

# 22. Dynamic Programming

DP usually requires overlapping subproblems + optimal substructure.

```text
State → Transition → Base case → Order → Answer
```

## Climbing Stairs

**C++**
```cpp
int climbStairs(int n){
  int a=1,b=1;
  for(int i=2;i<=n;++i){
    int c=a+b;
    a=b;
    b=c;
  }
  return b;
}
```
**Python**
```python
def climb_stairs(n):
a=b=1
for _ in range(2,n+1):a,b=b,a+b
return b
```
**JavaScript**
```javascript
function climbStairs(n){
  let a=1,b=1;
  for(let i=2;i<=n;i++)[a,b]=[b,a+b];
  return b;
}
```

## 0/1 Knapsack

**C++**
```cpp
int knapsack01(const vector<int>&w,const vector<int>&v,int C){
  vector<int>dp(C+1);
  for(int i=0;i<(int)w.size();++i)for(int c=C;c>=w[i];--c)dp[c]=max(dp[c],v[i]+dp[c-w[i]]);
  return dp[C];
}
```
**Python**
```python
def knapsack01(w,v,C):
dp=[0]*(C+1)
for wi,vi in zip(w,v):
for c in range(C,wi-1,-1):dp[c]=max(dp[c],vi+dp[c-wi])
return dp[C]
```
**JavaScript**
```javascript
function knapsack01(w,v,C){
  const dp=new Array(C+1).fill(0);
  for(let i=0;i<w.length;i++)for(let c=C;c>=w[i];c--)dp[c]=Math.max(dp[c],v[i]+dp[c-w[i]]);
  return dp[C];
}
```

**Critical:** backward capacity iteration prevents reusing an item in 0/1 knapsack.

Master DP families: 1D, grid, knapsack, subset sum, LCS, edit distance, LIS, interval DP, tree DP, and bitmask DP.

---

# 23. Tries

Trie operation: O(L), where L is key length.

**C++**
```cpp
struct TrieNode{
  array<TrieNode*,26>child{
  }
  ;
  bool terminal=false;
  TrieNode(){
    child.fill(nullptr);
  }
}
;
```
**Python**
```python
class TrieNode:
def __init__(self):self.children={};
self.terminal=False
```
**JavaScript**
```javascript
class TrieNode{
  constructor(){
    this.children=new Map();
    this.terminal=false;
  }
}
```

Applications: autocomplete, prefix search, dictionary lookup, word search, XOR trie.

---

# 24. Bit Manipulation

```text
x ^ x = 0
x ^ 0 = x
x ^ y ^ x = y
n & (n-1) removes the lowest set bit
```

## Single Number

**C++**
```cpp
int singleNumber(const vector<int>&a){
  int x=0;
  for(int v:a)x^=v;
  return x;
}
```
**Python**
```python
def single_number(a):
x=0
for v in a:x^=v
return x
```
**JavaScript**
```javascript
function singleNumber(a){
  let x=0;
  for(const v of a)x^=v;
  return x;
}
```

**JS caution:** bitwise operators use signed 32-bit integer conversion. For larger exact integers, consider `BigInt` and its different operator rules.

---

# 25. Math & Number Theory

## GCD — Euclid

**C++**
```cpp
long long gcd(long long a,long long b){
  while(b){
    long long t=a%b;
    a=b;
    b=t;
  }
  return llabs(a);
}
```
**Python**
```python
def gcd(a,b):
while b:a,b=b,a%b
return abs(a)
```
**JavaScript**
```javascript
function gcd(a,b){
  while(b!==0)[a,b]=[b,a%b];
  return Math.abs(a);
}
```

Know fast exponentiation (O(log n)) and the Sieve of Eratosthenes (O(n log log n)).

---

# 26. Advanced Patterns

Know when to reach for:

- Monotonic queue.
- Difference array.
- Coordinate compression.
- Fenwick tree/BIT — O(log n) update/query.
- Segment tree — O(log n) query/update; lazy propagation for supported range updates.
- Meet in the middle.
- Bitmask DP.
- Sweep line.
- Reservoir sampling.
- KMP/Z algorithm.
- Rolling hash.
- SCC algorithms.
- Advanced shortest paths.

---

# 27. Complexity Cheat Sheet

| Structure / Algorithm | Typical complexity |
|---|---:|
| Array access | O(1) |
| Hash lookup | O(1) average |
| Balanced BST | O(log n) |
| Heap insert/extract | O(log n) |
| Binary search | O(log n) |
| Merge/heap sort | O(n log n) |
| BFS/DFS | O(V+E) |
| Dijkstra | O((V+E) log V) |
| Bellman-Ford | O(VE) |
| Floyd-Warshall | O(V³) |
| Topological sort | O(V+E) |
| DSU | O(α(n)) amortized |
| Trie operation | O(L) |

---

# 28. Language-Specific Interview Notes

## C++

Know `vector`, `string`, `unordered_map`, `unordered_set`, `map`, `set`, `stack`, `queue`, `deque`, `priority_queue`, `sort`, `lower_bound`, iterators, references, const correctness, comparators, `long long`, and pointer lifetime.

## Python

Know `list`, `dict`, `set`, `collections.deque`, `heapq`, `bisect`, `Counter`, `defaultdict`, `itertools`, comprehensions, tuple unpacking, recursion limits, and copy/slicing costs.

## JavaScript

Know `Array`, `Map`, `Set`, numeric sort comparator, array+head queue, custom heap, `Number.MAX_SAFE_INTEGER`, `BigInt`, `Map` vs object, and recursion-depth considerations.

### Translation table

```text
C++ unordered_map  → Python dict → JavaScript Map
C++ unordered_set  → Python set → JavaScript Set
C++ priority_queue → Python heapq → JavaScript custom heap
C++ queue          → Python deque → JavaScript array + head
C++ lower_bound    → Python bisect_left → JavaScript binary search
```

Translate the **algorithm and invariant**, not syntax line-by-line.

---

# 29. Common Interview Mistakes

1. Coding before constraints.
2. Skipping the brute-force baseline.
3. Forgetting all-negative cases.
4. Ignoring integer overflow.
5. Using sum-based sliding window with negative numbers.
6. Mixing binary-search interval conventions.
7. Using BFS for weighted shortest paths.
8. Using Dijkstra with negative edges.
9. Forgetting disconnected graph components.
10. Forgetting recursion stack space.
11. Mutating input when not allowed.
12. JavaScript default numeric sort.
13. Repeated JavaScript `shift()` on large queues.
14. Unnecessary Python slicing in recursion.
15. C++ iterator/reference invalidation.
16. Treating hash tables as guaranteed O(1).
17. Missing duplicate handling in backtracking.
18. Confusing subsequence with substring/subarray.
19. Ignoring output-size lower bounds.
20. Not testing edge cases.

---

# 30. Interview Questions & Answers

### Q1. Why can O(n) be slower than O(n log n)?

Big-O ignores constants, allocations, cache locality, runtime overhead, and actual input sizes.

### Q2. Hash map vs sorting?

Hashing is excellent for average O(1) membership/counting. Sorting is better when ordering or two-pointer techniques matter and gives deterministic O(n log n) comparison sorting behavior.

### Q3. Why is binary search O(log n)?

Each iteration discards about half the remaining search space.

### Q4. Why is BFS shortest path for unweighted graphs?

BFS visits nodes by number of edges from the source, so the first discovery is a minimum-edge path.

### Q5. Why does BFS not solve general weighted shortest paths?

It minimizes edge count, not total edge weight.

### Q6. Why does Dijkstra require non-negative weights?

Its greedy finalization step relies on the fact that a later path cannot become smaller through a negative edge.

### Q7. DFS vs BFS?

DFS is natural for exhaustive traversal, recursion, backtracking, and structural graph problems. BFS is natural for levels and minimum edge distance.

### Q8. What makes a problem suitable for sliding window?

It concerns a contiguous range and permits efficient incremental state updates. Variable sum windows additionally need appropriate monotonicity, commonly non-negative values.

### Q9. What is a binary-search invariant?

A statement describing where the answer can still exist; every pointer update must preserve it.

### Q10. Memoization vs tabulation?

Memoization is top-down recursion plus caching; tabulation is bottom-up state computation.

### Q11. Subarray vs subsequence?

Subarray is contiguous; subsequence preserves order but can skip elements.

### Q12. Trie vs hash set?

Hash sets are excellent for exact membership; tries also support prefix operations.

### Q13. Why is a monotonic stack O(n)?

Every element is pushed once and popped at most once.

### Q14. Why is DSU nearly constant time?

Path compression and union by size/rank yield O(α(n)) amortized complexity.

### Q15. What is topological sorting used for?

Dependency ordering in DAGs: builds, courses, tasks, package dependencies, and similar systems.

### Q16. What happens if a dependency graph has a cycle?

No valid topological order exists.

### Q17. Why sort intervals before merging?

Sorting places potentially overlapping intervals together, allowing a linear sweep.

### Q18. When should greedy be rejected?

When local choices cannot be proven to preserve global optimality.

### Q19. What is amortized O(1)?

Average cost over a sequence is constant even if occasional individual operations are expensive.

### Q20. How do you choose C++, Python, or JavaScript?

Use the language in which you can most reliably express the algorithm, complexity, and edge cases, then adapt it to the company's stack.

### Q21. Should you memorize all three implementations?

Memorize the algorithm, invariant, and complexity first. Practice translating the same pattern across all three languages.

### Q22. C++ vs Python for deep graph DFS?

Python recursion can hit recursion-depth limits, so iterative DFS may be safer. C++ can also overflow the stack on extremely deep recursion.

### Q23. What changes in JavaScript numeric algorithms?

`Number` is floating-point and has exact integer precision only through `Number.MAX_SAFE_INTEGER`. Bitwise operations use 32-bit integer conversion. Use `BigInt` when exact larger integers are required.

### Q24. Why is Python concise for DSA?

Dictionaries, sets, `deque`, `heapq`, `Counter`, sorting, and comprehensions reduce implementation overhead. Hidden copies and interpreter overhead can still matter.

### Q25. Why is C++ common in competitive programming?

The STL provides efficient, rich data structures and algorithms, while the language offers strong performance control.

---

# 31. Practice Roadmap

## Level 1

Complexity → Arrays → Strings → Hashing → Two pointers → Sliding window → Prefix sums → Binary search.

## Level 2

Linked lists → Stack → Queue/deque → Trees → BST → Heap.

## Level 3

DFS/BFS → Components → Cycles → Bipartite → Topological sort → Shortest paths → MST → DSU.

## Level 4

Greedy → 1D/2D DP → Knapsack → LCS/LIS → Interval DP → Tree DP → Bitmask DP.

## Level 5

Trie → Fenwick → Segment tree → Monotonic queue → Sweep line → Meet-in-the-middle → Advanced graph algorithms.

### Multi-language practice

For each important problem:

```text
1. Solve in your strongest language.
2. Explain the invariant without code.
3. Re-implement in the company's likely stack.
4. Compare standard-library equivalents.
5. Test the same edge cases in all languages.
```

---

# 32. Final Revision Checklist

- [ ] Big-O and amortized analysis
- [ ] Arrays and strings
- [ ] Hashing
- [ ] Two pointers
- [ ] Sliding window
- [ ] Prefix sums / difference arrays
- [ ] Binary search / answer search
- [ ] Linked lists
- [ ] Stacks / monotonic stack
- [ ] Queues / monotonic deque
- [ ] Sorting
- [ ] Backtracking
- [ ] Tree DFS/BFS
- [ ] BST
- [ ] Heap / Top K
- [ ] Graph DFS/BFS
- [ ] Cycle detection
- [ ] Topological sort
- [ ] Dijkstra / Bellman-Ford
- [ ] MST / DSU
- [ ] Greedy proofs
- [ ] DP state/transition/base case
- [ ] Trie
- [ ] Bit manipulation
- [ ] Fenwick / segment tree awareness
- [ ] C++ STL
- [ ] Python standard library
- [ ] JavaScript Map/Set/Array/heap behavior
- [ ] Language-specific numeric/runtime pitfalls

---

# 33. Interview Communication Template

```text
1. “Let me confirm the constraints.”
2. “A brute-force approach would be …”
3. “The bottleneck is …”
4. “This looks like a [pattern] problem because …”
5. “I’ll maintain this invariant …”
6. “This gives O(time) and O(space).”
7. Code.
8. Test edge cases.
9. Mention language-specific considerations if relevant.
```

A strong DSA interview answer demonstrates **pattern recognition, correctness, complexity awareness, edge-case discipline, and the ability to translate the same algorithm across the company's technology stack**.