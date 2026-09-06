# 04. Linked Lists — From Pointers to Advanced Interview Problems

A linked list is a sequence of nodes connected through pointers. Unlike an array, nodes do not need to occupy contiguous memory.

The central skill is **pointer manipulation**. Most linked-list bugs happen because a pointer to the remaining list is lost before a link is changed.

---

# 1. Node Fundamentals

### Singly linked-list node

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

A list:

```text
head
 ↓
[10|•] → [20|•] → [30|null]
```

`head` stores the address of the first node. Each node stores data and the address of the next node.

### Complexity

| Operation | Complexity |
|---|---:|
| Access ith node | O(n) |
| Search | O(n) |
| Insert at head | O(1) |
| Delete head | O(1) |
| Insert after known node | O(1) |
| Delete after known previous node | O(1) |
| Insert at end with tail pointer | O(1) |
| Search + insert at value | O(n) |

The phrase **“known node”** matters. Finding that node may itself cost O(n).

---

# 2. Traversal

```cpp
void printList(ListNode* head) {
    while (head) {
        cout << head->val << ' ';
        head = head->next;
    }
}
```

**TC:** O(n)  
**SC:** O(1)

### Important pointer rule
Changing the local `head` pointer does not destroy the list. It only moves your local pointer. The actual links remain unchanged unless you assign to `node->next`.

---

# 3. Insert at Head

```cpp
ListNode* insertAtHead(ListNode* head, int x) {
    ListNode* node = new ListNode(x);
    node->next = head;
    return node;
}
```

**TC:** O(1)  
**SC:** O(1) auxiliary, plus the newly allocated node.

### Why this is easier than an array
No existing element needs to move. We simply redirect one pointer.

---

# 4. Delete a Node

If we know the previous node:

```cpp
prev->next = prev->next->next;
```

If the deleted node is dynamically allocated and ownership requires it, save it before unlinking and `delete` it.

### Common mistake
Never write:

```cpp
node->next = node->next->next;
```

without first verifying that `node->next` exists.

---

# 5. Reverse a Linked List — Most Important Pattern

Suppose:

```text
1 → 2 → 3 → 4 → null
```

We want:

```text
4 → 3 → 2 → 1 → null
```

At every step maintain three pointers:

```text
prev = already reversed part
cur  = first node not reversed
next = saved remainder
```

### C++

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* cur = head;

    while (cur) {
        ListNode* next = cur->next;
        cur->next = prev;
        prev = cur;
        cur = next;
    }
    return prev;
}
```

**TC:** O(n)  
**SC:** O(1)

### Why save `next` first?
After `cur->next = prev`, the original next node would otherwise become unreachable from `cur`. Saving it preserves the remaining list.

### Recursive version
Recursive reversal is useful for understanding recursion, but it uses O(n) stack space in the worst case. For interview memory efficiency, prefer iterative reversal when possible.

---

# 6. Find Middle — Slow/Fast Pointers

Use:

```text
slow → one step
fast → two steps
```

When `fast` reaches the end, `slow` is near the middle.

```cpp
ListNode* middleNode(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}
```

**TC:** O(n)  
**SC:** O(1)

For an even-length list, this version returns the second middle.

### Why is this pattern powerful?
It finds a position without knowing the length first. It is the foundation for palindrome checking, merge sort, cycle analysis, and many other list problems.

---

# 7. Detect Cycle — Floyd's Algorithm

A cycle means some node eventually points back to an earlier node.

Example:

```text
1 → 2 → 3 → 4
        ↑     ↓
        └─────┘
```

If one pointer moves one step and another two steps, the faster pointer eventually catches the slower pointer inside a cycle.

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
```

**TC:** O(n)  
**SC:** O(1)

### Important distinction
Compare pointers (`slow == fast`), not values. Two different nodes can contain the same value.

---

# 8. Find Cycle Entry

After slow and fast meet, reset one pointer to `head`. Move both one step at a time. Their next meeting point is the cycle entrance.

### Why?
Let:

- `x` = distance from head to cycle entry
- `y` = distance from entry to meeting point
- `z` = remaining cycle distance

At meeting:

```text
slow = x + y
fast = x + y + k(y+z)
```

Because fast moves twice as far:

```text
2(x+y) = x+y+k(y+z)
```

which implies the remaining distance relationships needed for the reset-pointer method.

**TC:** O(n)  
**SC:** O(1)

This proof is worth understanding for interviews rather than memorizing the code.

---

# 9. Remove Nth Node From End

Naively find length, then make a second traversal. That is O(n) time and still acceptable.

The elegant one-pass solution uses two pointers separated by `n` nodes and a dummy node.

### Why dummy node?
If the node to remove is the original head, there is no previous real node. A dummy gives every removable node a predecessor.

```text
dummy → 1 → 2 → 3 → 4
```

**TC:** O(n)  
**SC:** O(1)

### Key pattern
Whenever you need the node `k` positions from the end, consider maintaining a fixed gap between two pointers.

---

# 10. Merge Two Sorted Linked Lists

Given:

```text
1 → 4 → 7
2 → 3 → 8
```

Repeatedly attach the smaller current node.

A dummy node makes the head case trivial.

```cpp
ListNode* mergeTwoLists(ListNode* a, ListNode* b) {
    ListNode dummy(0);
    ListNode* tail = &dummy;

    while (a && b) {
        if (a->val <= b->val) {
            tail->next = a;
            a = a->next;
        } else {
            tail->next = b;
            b = b->next;
        }
        tail = tail->next;
    }

    tail->next = a ? a : b;
    return dummy.next;
}
```

**TC:** O(n+m)  
**SC:** O(1) auxiliary

### Why is this better than creating new nodes?
The existing nodes are already valid. We only rearrange links.

---

# 11. Palindrome Linked List

A list is a palindrome if its values read the same forward and backward.

### O(n) space solution
Copy values into an array and compare from both ends.

### O(1) auxiliary solution
1. Find middle.
2. Reverse second half.
3. Compare first and second halves.
4. Optionally restore the second half.

**TC:** O(n)  
**SC:** O(1)

### Interview lesson
When asked to reduce space, look for in-place reversal plus slow/fast pointers.

---

# 12. Intersection of Two Linked Lists

Two lists may merge physically:

```text
A: a1 → a2 ┐
           ↓
           c1 → c2 → c3
           ↑
B: b1 → b2 ┘
```

Intersection means the **same node object**, not merely equal values.

### Pointer switching
Let `p` traverse A then B. Let `q` traverse B then A.

Each pointer travels exactly:

```text
length(A) + length(B)
```

So they align at the intersection.

**TC:** O(n+m)  
**SC:** O(1)

---

# 13. Add Two Numbers

Digits are stored in reverse order.

Example:

```text
2 → 4 → 3
5 → 6 → 4
```

represents `342 + 465 = 807`.

Process both lists with a carry exactly like elementary addition.

**TC:** O(max(n,m))  
**SC:** O(1) auxiliary excluding output nodes

### Edge cases

- Different lengths.
- Final carry.
- Both lists empty if allowed by the problem.
- Zero values.

---

# 14. Reverse Linked List II — Reverse a Subrange

Reverse positions `[left, right]` without reversing the entire list.

The important idea is to locate the node immediately before the range and perform repeated head-insertion within the range.

**TC:** O(n)  
**SC:** O(1)

This problem tests whether you can manipulate local links without breaking the prefix and suffix.

---

# 15. Reverse Nodes in K-Group

Given:

```text
1 → 2 → 3 → 4 → 5
k = 2
```

produce:

```text
2 → 1 → 4 → 3 → 5
```

Only complete groups are reversed.

### Strategy

1. Check whether `k` nodes exist.
2. Reverse exactly those nodes.
3. Connect previous group to the reversed group.
4. Continue.

**TC:** O(n) because each node is processed a constant number of times.  
**SC:** O(1) iterative auxiliary.

### Common bug
Reversing a partial final group when the problem says incomplete groups must remain unchanged.

---

# 16. Reorder List

Transform:

```text
L0 → L1 → L2 → L3 → L4
```

into:

```text
L0 → L4 → L1 → L3 → L2
```

Three known patterns combine:

1. Find middle.
2. Reverse second half.
3. Alternately weave both halves.

**TC:** O(n)  
**SC:** O(1)

### Why this problem is important
It tests whether you can combine multiple basic linked-list techniques into a larger algorithm.

---

# 17. Sort a Linked List

Merge sort is natural because linked lists support sequential access efficiently but not random access.

### Why not quicksort?
You can implement it, but partitioning and random access are less natural. Merge sort gives predictable O(n log n) behavior and can merge by pointer rewiring.

**TC:** O(n log n)  
**SC:** O(log n) recursion stack in the standard recursive version.

A carefully implemented bottom-up merge sort can use O(1) auxiliary space.

---

# 18. Merge K Sorted Lists

Use a min-heap containing the current smallest node from each list.

Each extracted node contributes its next node to the heap.

If there are `k` lists and `N` total nodes:

**TC:** O(N log k)  
**SC:** O(k)

Alternative: divide-and-conquer pairwise merging also achieves O(N log k) and can have useful constants.

---

# 19. Copy List with Random Pointer

Each node has:

```cpp
next
random
```

A simple solution uses a map from original node address to copied node address.

**TC:** O(n)  
**SC:** O(n)

### O(1) auxiliary technique
Interleave copies:

```text
original1 → copy1 → original2 → copy2 → ...
```

Set random pointers using the interleaved relationship, then separate the two lists.

**TC:** O(n)  
**SC:** O(1) auxiliary

This is an advanced pointer-manipulation problem and is frequently used to distinguish candidates who understand links from candidates who only know STL containers.

---

# 20. Doubly Linked List

A doubly linked node stores both directions:

```cpp
struct Node {
    int val;
    Node* prev;
    Node* next;
};
```

Advantages:

- O(1) deletion when node is known.
- Easy backward traversal.
- Natural for LRU Cache.

Disadvantages:

- More memory.
- More pointers to maintain.
- More opportunities for link bugs.

---

# 21. LRU Cache — Classic Advanced Question

An LRU cache needs:

1. O(1) lookup by key.
2. O(1) removal/insertion according to recency.

Use:

```text
unordered_map<key, list-node>
        +
doubly linked list
```

The list stores most recently used items at the front and least recently used at the back.

Hash map gives O(1) expected lookup; doubly linked list gives O(1) movement/removal when the node is known.

**Get:** O(1) expected  
**Put:** O(1) expected  
**Space:** O(capacity)

### Design insight
When a problem asks for two O(1) operations that seem to require different structures, combine complementary data structures.

---

# 22. Beginner → Advanced Problem Set

## Beginner

1. Create a node.
2. Traverse list.
3. Insert at head.
4. Insert at tail.
5. Delete head.
6. Delete a value.
7. Search a value.
8. Reverse list.
9. Find length.
10. Find middle.

## Intermediate

11. Detect cycle.
12. Find cycle entry.
13. Remove Nth node from end.
14. Merge two sorted lists.
15. Remove duplicates.
16. Palindrome list.
17. Intersection of two lists.
18. Add two numbers.
19. Reverse a subrange.
20. Rotate list.

## Advanced

21. Reverse nodes in K-group.
22. Reorder list.
23. Sort list.
24. Merge K lists.
25. Copy random-pointer list.
26. Flatten multilevel doubly linked list.
27. LRU cache.
28. LFU cache concept.
29. Clone complex linked structure.
30. Implement a memory-friendly list abstraction.

---

# 23. Frequently Asked Interview Questions

### Q1. Array vs linked list?
Arrays provide O(1) random access but middle insertion/deletion costs O(n). Linked lists provide O(1) insertion/deletion when the relevant node or predecessor is known, but indexed access is O(n).

### Q2. Why use a dummy node?
It eliminates special cases involving the head, making insertion/deletion and merging logic uniform.

### Q3. Why is fast/slow useful?
Two different traversal speeds reveal middle positions and cycles without needing extra memory.

### Q4. Why compare pointers for intersection?
The problem asks for the same physical node, not two nodes containing the same value.

### Q5. Why does Floyd cycle detection use O(1) space?
It stores only a constant number of pointers and does not maintain a visited set.

### Q6. Why is linked-list merge sort efficient?
Merging requires only pointer rewiring and sequential traversal; random access is not needed.

### Q7. Why can a list insertion be O(1) but searching still O(n)?
Insertion is O(1) only after the insertion location is known. Finding that location may require traversal.

### Q8. Why is LRU Cache usually hash map + doubly linked list?
The map gives key lookup, while the list gives O(1) removal and recency ordering.

### Q9. Why save `next` during reversal?
Because changing `cur->next` destroys the original forward link unless it has already been saved.

### Q10. Recursive vs iterative reversal?
Both are O(n) time, but recursive reversal uses O(n) call-stack space while iterative reversal uses O(1) auxiliary space.

---

# 24. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Traverse | O(n) | O(1) |
| Reverse | O(n) | O(1) |
| Middle | O(n) | O(1) |
| Cycle detection | O(n) | O(1) |
| Cycle entry | O(n) | O(1) |
| Remove Nth from end | O(n) | O(1) |
| Merge two lists | O(n+m) | O(1) |
| Palindrome | O(n) | O(1) |
| Intersection | O(n+m) | O(1) |
| Reverse K-group | O(n) | O(1) |
| Sort list | O(n log n) | O(log n) recursive |
| Merge K lists | O(N log k) | O(k) |
| Copy random list | O(n) | O(n) map / O(1) advanced |
| LRU cache operation | O(1) expected | O(capacity) |

---

# 25. Linked List Debugging Checklist

Before running the code mentally:

```text
1. Is head null?
2. Is there only one node?
3. Did I save next before changing a link?
4. Can fast or fast->next be null?
5. Did I accidentally create a cycle?
6. Did I reconnect the prefix?
7. Did I reconnect the suffix?
8. Am I comparing node addresses or values?
9. Do I need a dummy node?
10. Did I preserve the required final head?
```

> **Core lesson:** Linked-list problems are mostly about maintaining pointer invariants. Draw the links on paper, identify the prefix/suffix that must remain reachable, and change one pointer at a time.
