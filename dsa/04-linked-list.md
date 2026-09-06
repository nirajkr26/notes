# 04. Linked List

Linked lists trade random access for cheap pointer-based insertion/deletion when the position/node is already known.

## 4.1 Fundamentals

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

| Operation | Singly linked list |
|---|---:|
| Access by index | O(n) |
| Search | O(n) |
| Insert/delete at head | O(1) |
| Insert after known node | O(1) |
| Delete after known previous node | O(1) |
| Insert/delete by value | O(n) |

## 4.2 Beginner: traversal and reversal

### Reverse a linked list

The core invariant is that `prev` is the already-reversed prefix and `cur` is the first unreversed node.

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

**TC:** O(n)  **SC:** O(1)

## 4.3 Intermediate: slow/fast pointers

Two pointers moving at different speeds solve many structural problems.

### Middle of linked list
`slow` moves one step, `fast` two. When `fast` reaches the end, `slow` is at the middle.

**TC:** O(n)  **SC:** O(1)

### Cycle detection
Floyd's algorithm: if a cycle exists, slow and fast eventually meet.

**TC:** O(n)  **SC:** O(1)

### Cycle entry
After the meeting point, place one pointer at head. Move both one step; their next meeting point is the cycle entry.

**TC:** O(n)  **SC:** O(1)

## 4.4 Dummy nodes

A dummy head eliminates special cases around deleting/inserting the real head. This is especially useful for merging and removing nodes.

### Remove Nth node from end
Use a dummy node and keep two pointers `n+1` apart. Move both until the first reaches null, then delete the second's next node.

**TC:** O(n)  **SC:** O(1)

## 4.5 Important questions

### Q1. Merge two sorted lists
Maintain a dummy node and repeatedly attach the smaller current node.

**TC:** O(n+m)  **SC:** O(1) auxiliary if nodes are reused.

### Q2. Palindrome linked list
Find middle, reverse the second half, compare halves. Optionally restore the list.

**TC:** O(n)  **SC:** O(1)

### Q3. Intersection of two linked lists
Switch each pointer to the other list after reaching null. They traverse equal total distances and meet at the intersection or null.

**TC:** O(n+m)  **SC:** O(1)

### Q4. Add two numbers
Simulate addition digit-by-digit while carrying.

**TC:** O(max(n,m))  **SC:** O(1) auxiliary, excluding output nodes.

### Q5. Reverse nodes in K-group
Reverse each full group of `k` nodes and reconnect it to the previous and next groups.

**TC:** O(n)  **SC:** O(1) iterative auxiliary.

### Q6. Sort linked list
Merge sort is preferred because linked lists do not support efficient random access.

**TC:** O(n log n)  **SC:** O(log n) recursion stack; can be O(1) with careful iterative implementation.

### Q7. Copy list with random pointer
Use a hash map for a simple solution, or interleave cloned nodes with originals for O(1) auxiliary space.

Hash map: **TC O(n), SC O(n)**.

Interleaving: **TC O(n), SC O(1)** auxiliary.

## 4.6 Advanced patterns

- Dummy head/tail.
- Fast/slow pointers.
- In-place reversal.
- Reversal of a subrange.
- Merge multiple sorted lists using a heap.
- Reorder list by splitting, reversing, and weaving.
- Detect structural relationships using pointer switching.

## 4.7 Common pitfalls

- Losing `next` before changing a pointer.
- Dereferencing `nullptr` in fast/slow logic.
- Forgetting the dummy node can simplify head deletion.
- Confusing node identity with node value in intersection problems.
- Not restoring a modified list when the caller expects it unchanged.
- Recursive reversal can use O(n) stack space; iterative reversal is O(1) auxiliary.

## 4.8 Must-solve progression

**Beginner:** traversal → insert/delete → reverse → middle.

**Intermediate:** cycle → remove Nth → merge sorted lists → palindrome → intersection.

**Advanced:** reverse K-group → reorder list → sort list → merge K lists → copy random-pointer list.