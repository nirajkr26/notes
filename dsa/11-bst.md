# 11. Binary Search Tree — Complete Interview Guide

A Binary Search Tree (BST) is a binary tree with an ordering invariant. For a strict BST with distinct values:

```text
all values in left subtree < node < all values in right subtree
```

This ordering is what makes BST search possible.

---

# 1. Node and Invariant

```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

The most important property:

> **Inorder traversal of a valid BST produces values in sorted order.**

This single fact solves many interview questions.

---

# 2. Complexity Depends on Height

Search, insertion and deletion take O(h), where `h` is tree height.

Balanced BST:

```text
h = O(log n)
```

Skewed BST:

```text
h = O(n)
```

Therefore a BST is not automatically O(log n). The shape matters.

---

# 3. Search

At node `x`:

- target < x → go left
- target > x → go right
- target == x → found

```cpp
TreeNode* searchBST(TreeNode* root, int target) {
    while (root) {
        if (root->val == target) return root;
        if (target < root->val) root = root->left;
        else root = root->right;
    }
    return nullptr;
}
```

**TC:** O(h)  
**SC:** O(1)

---

# 4. Insert

Follow the same search path until a null child is found.

**TC:** O(h)  
**SC:** O(h) recursive or O(1) iterative.

### Duplicate policy
The problem must define what to do with equal values. Some BST definitions disallow duplicates; others consistently place them on one side.

---

# 5. Validate BST

A common wrong solution checks only:

```text
left child < root < right child
```

That is insufficient because a deeper descendant can violate the ancestor's constraint.

Example conceptually:

```text
    10
   /  \
  5    15
      /
     6   ← invalid: 6 is in the right subtree of 10
```

### Correct approach: range constraints
Each node receives an allowed `(low, high)` range.

**TC:** O(n)  
**SC:** O(h)

### Alternative
Inorder traversal must be strictly increasing for a strict BST.

---

# 6. Minimum and Maximum

In a BST:

```text
minimum = keep going left
maximum = keep going right
```

**TC:** O(h)  
**SC:** O(1) iterative.

---

# 7. Delete a Node

Three cases.

### Case 1 — Leaf
Simply remove it.

### Case 2 — One child
Replace the node with its only child.

### Case 3 — Two children
Replace the value with either:

- inorder successor = smallest value in right subtree
- inorder predecessor = largest value in left subtree

Then delete that replacement node from its original location.

**TC:** O(h)  
**SC:** O(h) recursive / O(1) iterative with careful pointer handling.

### Why successor works
Every value in the right subtree is greater than the node. The smallest one is the closest valid replacement while preserving the BST ordering.

---

# 8. Kth Smallest Element

Inorder traversal is sorted, so the kth visited node is the kth smallest.

An iterative stack can stop immediately after visiting k nodes.

**TC:** O(h+k) in a typical early-stopping traversal  
**SC:** O(h)

For repeated order-statistic queries, augment nodes with subtree sizes or use an appropriate balanced order-statistics structure.

---

# 9. Kth Largest

Reverse inorder:

```text
right → root → left
```

The kth visited node is the kth largest.

**TC:** O(h+k) typical  
**SC:** O(h)

---

# 10. LCA in BST

Unlike a general binary tree, BST ordering makes LCA easy.

For nodes `p` and `q`:

- both smaller → go left
- both larger → go right
- otherwise current node splits their paths → LCA

**TC:** O(h)  
**SC:** O(1) iterative

This is a classic example of exploiting a data-structure invariant rather than performing generic DFS.

---

# 11. Inorder Successor

The successor is the smallest value greater than the target.

If the target has a right subtree, successor is its leftmost node.

Otherwise, walk from root while remembering the lowest ancestor greater than target.

**TC:** O(h)  
**SC:** O(1) without recursion.

Predecessor is symmetric.

---

# 12. Convert Sorted Array to Balanced BST

For a sorted array, choose the middle element as root. Recursively use left half and right half.

This produces roughly balanced height.

**TC:** O(n)  
**SC:** O(log n) recursion stack for balanced construction.

---

# 13. Two Sum in BST

Two approaches:

### Hash-set approach
Traverse the tree and search for `target - x`.

**TC:** O(n) expected  
**SC:** O(n)

### Advanced iterator approach
Use one inorder iterator ascending and one reverse-inorder iterator descending, analogous to two pointers on a sorted array.

**TC:** O(n) worst-case  
**SC:** O(h)

This is an excellent example of converting a tree problem into a familiar array pattern.

---

# 14. Recover Swapped BST

Two nodes in a valid BST were accidentally swapped.

Inorder traversal should be sorted. The swapped nodes create one or two ordering violations.

Detect the misplaced nodes during inorder and swap their values back.

**TC:** O(n)  
**SC:** O(h), or O(1) with Morris traversal.

---

# 15. BST Iterator

Design an iterator supporting `next()` and `hasNext()` in average O(1) time.

Maintain a stack containing the path to the next smallest node.

Initialization pushes the entire left path. After returning a node, push the left path of its right child.

**Amortized TC:** O(1) per `next()`  
**SC:** O(h)

---

# 16. Balanced BST Concepts

A plain BST can become a linked list after inserting sorted data:

```text
1
 \
  2
   \
    3
     \
      4
```

Self-balancing trees maintain O(log n) height.

Know the concepts of:

- AVL trees
- Red-Black trees
- rotations
- balance factor

You usually do not need to implement a production-quality self-balancing tree in a standard coding interview unless explicitly asked.

---

# 17. OA / Interview Problem Bank

## Beginner

1. Search BST.
2. Insert into BST.
3. Find minimum.
4. Find maximum.
5. Inorder traversal.
6. Validate BST.

## Intermediate

7. Delete node.
8. Kth smallest.
9. Kth largest.
10. LCA of BST.
11. Inorder successor.
12. Inorder predecessor.
13. Sorted array to BST.
14. BST iterator.
15. Two Sum BST.

## Advanced

16. Recover swapped BST.
17. Construct BST from preorder.
18. Preorder verification of BST.
19. Trim BST.
20. Convert BST to greater tree.
21. Balance an existing BST.
22. Count unique BSTs.
23. Generate unique BSTs.
24. Order-statistics with augmented BST.

---

# 18. Frequently Asked Questions

### Q1. Why is BST search O(h), not O(log n)?
Because the tree may be skewed. Only a balanced BST guarantees h = O(log n).

### Q2. Why is inorder traversal sorted?
Every left-subtree value is smaller than the root and every right-subtree value is larger, so inorder visits the values in increasing order.

### Q3. Why is local parent-child validation insufficient?
A node must satisfy constraints inherited from all ancestors, not merely its immediate parent.

### Q4. Why does deletion with two children use successor/predecessor?
They are the nearest values that preserve the BST ordering when replacing the deleted node.

### Q5. BST vs heap?
BST provides ordered search/navigation. A heap only guarantees an extreme element at the root; arbitrary search is O(n).

### Q6. Why can a BST degrade to O(n)?
Repeated insertion of already sorted data can produce a completely skewed tree.

### Q7. How do you get kth smallest efficiently?
Use inorder because it enumerates BST keys in sorted order. For many queries, augment nodes with subtree sizes.

---

# 19. Complexity Summary

| Operation | Balanced | Worst skewed |
|---|---:|---:|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Min/max | O(log n) | O(n) |
| LCA | O(log n) | O(n) |
| Validate | O(n) | O(n) |
| Kth smallest | O(h+k) | O(n) |

> **Core lesson:** A BST is useful because its ordering invariant turns tree navigation into the same directional reasoning as binary search.
