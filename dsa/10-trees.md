# 10. Trees

A tree is a connected acyclic structure. A binary tree has at most two children per node.

```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

## 10.1 Traversals

- Preorder: root, left, right.
- Inorder: left, root, right.
- Postorder: left, right, root.
- Level order: BFS by depth.

All traversals visit each node once: **TC O(n)**. Recursive DFS uses **O(h)** stack, where `h` is tree height.

## Beginner

### Height
`height = 1 + max(height(left), height(right))`.

**TC:** O(n)  **SC:** O(h)

### Count/sum nodes
Simple DFS aggregation.

**TC:** O(n)  **SC:** O(h)

### Level order traversal
Queue nodes and process one level at a time.

**TC:** O(n)  **SC:** O(w), where `w` is maximum width.

## Intermediate

### Diameter
For every node, compute left/right heights and maximize their sum. A single postorder traversal avoids recomputing heights.

**TC:** O(n)  **SC:** O(h)

### Balanced binary tree
Return height when balanced; return a sentinel when an unbalanced subtree is found.

**TC:** O(n)  **SC:** O(h)

### Lowest Common Ancestor in a binary tree
If current is null or one target, return it. Recurse left/right; if both sides return non-null, current is the LCA.

**TC:** O(n)  **SC:** O(h)

## Important questions

1. Inorder/preorder/postorder — **O(n), O(h)**.
2. Level order — **O(n), O(w)**.
3. Maximum depth — **O(n), O(h)**.
4. Same tree — **O(n), O(h)**.
5. Invert tree — **O(n), O(h)**.
6. Diameter — **O(n), O(h)**.
7. Balanced tree — **O(n), O(h)**.
8. Maximum path sum — **O(n), O(h)**.
9. LCA — **O(n), O(h)**.
10. Zigzag level order — **O(n), O(w)**.
11. Boundary traversal — **O(n), O(h)** auxiliary excluding output.
12. Vertical traversal — typically **O(n log n)** due to ordered grouping/sorting, with **O(n)** storage.
13. Serialize/deserialize binary tree — **O(n), O(n)**.

## Advanced concepts

- Morris traversal: O(1) auxiliary space for inorder/preorder by temporarily modifying pointers.
- Threaded trees.
- Tree views: left/right/top/bottom.
- Path-based recursion: carry accumulated state down the path.
- Rerooting ideas for advanced tree DP.

## Key insight

Most tree recursion asks: **What information should this subtree return to its parent?** Once that return value is defined precisely, the recurrence usually becomes straightforward.