# 11. Binary Search Tree

A BST maintains `left < root < right` for distinct keys (or follows a defined duplicate policy).

## Fundamentals

Search, insert and delete take **O(h)** where `h` is height. Balanced BST: O(log n); skewed BST: O(n).

## Inorder property

Inorder traversal of a valid BST is sorted. This is the most useful invariant for BST questions.

## Beginner

### Search
Compare target with current node and descend left/right.

**TC:** O(h)  **SC:** O(h) recursive or O(1) iterative.

### Insert
Follow the search path and attach the new node at the null position.

**TC:** O(h)  **SC:** O(h) recursive.

## Intermediate

### Delete
Three cases: leaf, one child, two children. For two children, replace with inorder successor/predecessor, then delete that replacement node.

**TC:** O(h)  **SC:** O(h) recursive.

### Validate BST
Carry an allowable `(low, high)` range down the tree, or perform inorder and ensure strict increase.

**TC:** O(n)  **SC:** O(h)

### Kth smallest
Inorder visits keys in sorted order; stop after `k` nodes.

**TC:** O(h+k) average depending on traversal stop; **SC:** O(h).

## Important questions

1. Search/insert — **O(h), O(h)** recursive.
2. Delete — **O(h), O(h)** recursive.
3. Validate BST — **O(n), O(h)**.
4. Kth smallest/largest — **O(h+k), O(h)** without subtree-size augmentation.
5. LCA in BST — **O(h), O(1)** iterative.
6. Inorder successor/predecessor — **O(h), O(1)** iterative when parent pointers are available; otherwise O(h) traversal.
7. Convert sorted array to balanced BST — **O(n), O(log n)** recursion.
8. Recover swapped BST — **O(n), O(h)** or O(1) with Morris traversal.
9. Two Sum in BST — **O(n), O(n)** using a set; advanced iterator approach can reduce auxiliary space.

## Advanced

Augment nodes with subtree sizes to answer kth-order-statistic queries in O(log n) in a balanced tree. Self-balancing trees such as AVL and Red-Black trees maintain O(log n) height.

## Pitfalls

- Do not validate a BST using only parent-child comparisons; deeper descendants can violate the ordering.
- Decide whether duplicates are allowed and where they go.
- Distinguish node value from node identity.
- State whether complexity is in terms of `n` or height `h`.