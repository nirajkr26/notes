# 10. Trees — From Fundamentals to Advanced Interview Problems

A tree is a hierarchical, connected, acyclic structure. A binary tree allows each node to have at most two children.

Trees are one of the most important DSA topics because interviewers can test recursion, BFS, DFS, stacks, queues, hashing, and dynamic programming through a single structure.

---

# 1. Tree Terminology

```text
          1          ← root
        /   \
       2     3
      / \
     4   5
```

- **Root:** node with no parent.
- **Parent:** node directly above another node.
- **Child:** node directly below a parent.
- **Leaf:** node with no children.
- **Depth:** distance from root to a node.
- **Height:** longest downward path from a node to a leaf.
- **Subtree:** a node together with all descendants.
- **Degree:** number of children.

For a tree with `n` nodes there are exactly `n-1` edges.

---

# 2. Binary Tree Node

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;

    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

A binary tree is not necessarily sorted. That distinction becomes important when comparing binary trees with BSTs.

---

# 3. DFS Traversals

There are three standard depth-first traversals.

```text
Preorder  = Root → Left → Right
Inorder   = Left → Root → Right
Postorder = Left → Right → Root
```

For the example above:

```text
Preorder  = 1 2 4 5 3
Inorder   = 4 2 5 1 3
Postorder = 4 5 2 3 1
```

### Recursive inorder

```cpp
void inorder(TreeNode* root, vector<int>& ans) {
    if (!root) return;
    inorder(root->left, ans);
    ans.push_back(root->val);
    inorder(root->right, ans);
}
```

**TC:** O(n)  
**SC:** O(h), where `h` is tree height.

### Why recursion fits trees
Every node naturally decomposes into a left subtree and right subtree. The recursive function can ask each subtree to solve the same problem.

---

# 4. Iterative Traversals

Knowing recursive traversal is not enough for interviews. Be comfortable converting DFS to an explicit stack.

### Iterative inorder idea

1. Keep moving left while possible.
2. Push each node.
3. Pop the top, process it.
4. Move to its right subtree.

**TC:** O(n)  
**SC:** O(h)

Iterative preorder and postorder use similar stack-state reasoning.

---

# 5. Level Order Traversal — BFS

Use a queue.

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    if (!root) return {};

    queue<TreeNode*> q;
    q.push(root);
    vector<vector<int>> ans;

    while (!q.empty()) {
        int sz = q.size();
        vector<int> level;

        while (sz--) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);

            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        ans.push_back(level);
    }
    return ans;
}
```

**TC:** O(n)  
**SC:** O(w), maximum tree width.

### Why capture `q.size()`?
It represents exactly the nodes belonging to the current level before their children are added.

---

# 6. Maximum Depth / Height

```cpp
int maxDepth(TreeNode* root) {
    if (!root) return 0;
    return 1 + max(maxDepth(root->left), maxDepth(root->right));
}
```

**TC:** O(n)  
**SC:** O(h)

The recurrence is:

```text
height(node) = 1 + max(height(left), height(right))
```

with `height(null)=0` under this convention.

---

# 7. Count and Sum Nodes

These are beginner exercises for learning subtree-return recursion.

```text
answer(node) = node.value + answer(left) + answer(right)
```

**TC:** O(n)  
**SC:** O(h)

### General tree-recursion question
Ask:

> **What information does the current subtree need to return to its parent?**

This question becomes the foundation of diameter, balance, maximum path sum, and tree DP.

---

# 8. Same Tree

Two trees are identical if:

1. Both nodes are null, or
2. Both exist, have equal values, and their left/right subtrees are identical.

**TC:** O(n)  
**SC:** O(h)

This is a basic but important structural-recursion problem.

---

# 9. Invert Binary Tree

Swap left and right children recursively.

```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;

    swap(root->left, root->right);
    invertTree(root->left);
    invertTree(root->right);
    return root;
}
```

**TC:** O(n)  
**SC:** O(h)

---

# 10. Balanced Binary Tree

A tree is height-balanced if for every node:

```text
abs(height(left) - height(right)) <= 1
```

A naive solution calculates height separately for every node and can become O(n²).

Optimized solution returns a sentinel when an unbalanced subtree is found.

**TC:** O(n)  
**SC:** O(h)

### Pattern
Combine validation and information gathering into one postorder traversal.

---

# 11. Diameter of Binary Tree

Diameter is the longest path between two nodes.

At a node, a path passing through that node can have:

```text
leftHeight + rightHeight
```

So perform postorder DFS, calculate subtree height, and update a global answer.

**TC:** O(n)  
**SC:** O(h)

### Common mistake
Do not recompute height independently for every node; that can produce O(n²).

---

# 12. Maximum Path Sum

A path may start and end anywhere, but it must follow connected parent-child edges.

At node `x`:

```text
best downward contribution = x + max(0, leftGain, rightGain)
```

But a path passing through `x` can use both sides:

```text
leftGain + x + rightGain
```

**TC:** O(n)  
**SC:** O(h)

### Important insight
The value returned to the parent is not necessarily the global best path. The parent can use only one downward branch, while the global answer may use both.

---

# 13. Lowest Common Ancestor in Binary Tree

For nodes `p` and `q`:

- If root is null, return null.
- If root is `p` or `q`, return root.
- Search both subtrees.
- If both return non-null, current node is the LCA.
- Otherwise return whichever side found a target.

**TC:** O(n)  
**SC:** O(h)

The algorithm assumes the target-node conditions required by the problem; if targets may be absent, existence verification may be needed.

---

# 14. Path Sum

For root-to-leaf target sum, subtract each node's value as you descend.

The important detail is that the endpoint must be a **leaf** if the problem says root-to-leaf.

**TC:** O(n)  
**SC:** O(h)

Variants include:

- return whether path exists
- return all paths
- count paths
- path sum not necessarily starting at root

---

# 15. Binary Tree Right Side View

At each level, the last node processed by BFS is visible from the right.

Alternatively, DFS right-first and record the first node encountered at each depth.

**TC:** O(n)  
**SC:** O(w) BFS or O(h) DFS excluding output.

### Interview lesson
The same question can have multiple correct traversals. Choose the one whose state naturally matches the requirement.

---

# 16. Zigzag Level Order

Perform BFS level by level. Reverse every other level, or use a direction flag and write values from opposite ends.

**TC:** O(n)  
**SC:** O(w)

---

# 17. Serialize and Deserialize

Serialization converts a tree into a storable/transmittable sequence. Null markers are necessary to preserve structure.

For example:

```text
1,2,null,null,3,null,null
```

A preorder traversal with explicit null markers is sufficient.

**TC:** O(n)  
**SC:** O(n)

### Why are null markers necessary?
Without them, different tree structures can produce the same sequence of node values.

---

# 18. Vertical / Boundary / View Problems

These problems combine traversal with coordinate or boundary metadata.

### Boundary traversal
Collect:

1. root
2. left boundary excluding leaves
3. leaves
4. right boundary in reverse

**TC:** O(n)  
**SC:** O(h) auxiliary excluding output.

### Vertical traversal
Associate each node with a column coordinate and often a row/value ordering. Use BFS or DFS plus ordered containers.

Typical complexity is O(n log n) when sorting coordinate groups.

---

# 19. Morris Traversal

Morris inorder traversal achieves O(1) auxiliary space by temporarily modifying right pointers to create threads.

High-level idea:

- Find inorder predecessor.
- Temporarily connect predecessor to current node.
- Return through the thread after processing the left subtree.
- Restore the pointer.

**TC:** O(n)  
**SC:** O(1)

### Interview caveat
Morris temporarily modifies the tree. The algorithm restores the structure before finishing, but this mutation may matter if the environment has strict side-effect requirements.

---

# 20. Important OA / Interview Questions

## Beginner

1. Tree node implementation.
2. Preorder traversal.
3. Inorder traversal.
4. Postorder traversal.
5. Level order.
6. Maximum depth.
7. Count nodes.
8. Sum nodes.
9. Same tree.
10. Invert tree.

## Intermediate

11. Balanced binary tree.
12. Diameter.
13. Path sum.
14. All root-to-leaf paths.
15. LCA.
16. Right-side view.
17. Zigzag traversal.
18. Minimum depth.
19. Symmetric tree.
20. Construct tree from preorder + inorder.
21. Construct tree from inorder + postorder.
22. Flatten binary tree to linked list.

## Advanced

23. Maximum path sum.
24. Serialize/deserialize.
25. Vertical traversal.
26. Boundary traversal.
27. Top/bottom view.
28. Morris traversal.
29. Burn a binary tree from a target node.
30. Nodes at distance K.
31. Recover complex tree relationships.
32. Tree DP problems.

---

# 21. Construct Tree from Traversals

Given preorder and inorder:

```text
preorder: root appears first
inorder:  left subtree | root | right subtree
```

The first unused preorder value identifies the root. Its position in inorder splits the tree into left and right ranges.

A hash map from value to inorder index avoids repeatedly searching the inorder array.

**TC:** O(n) expected  
**SC:** O(n)

This problem teaches recursive range decomposition and hash-map acceleration.

---

# 22. Flatten Binary Tree

Convert the tree into a preorder linked list using right pointers.

Possible approaches:

- reverse preorder recursion
- iterative pointer rewiring
- Morris-style predecessor connection

A carefully designed iterative solution can achieve:

**TC:** O(n)  
**SC:** O(1) auxiliary

---

# 23. Burn Binary Tree / Distance K

Convert parent-child relationships into an undirected graph conceptually by recording parent pointers. Then BFS from the target.

**TC:** O(n)  
**SC:** O(n)

### Key lesson
Sometimes the tree is easier to solve after adding the missing direction: parent links.

---

# 24. Frequently Asked Interview Questions

### Q1. Binary tree vs BST?
A binary tree only limits the number of children. A BST additionally maintains an ordering invariant between left subtree, root, and right subtree.

### Q2. DFS vs BFS on trees?
DFS is natural for subtree properties and path recursion. BFS is natural for levels, minimum depth, and views.

### Q3. Why is recursive tree traversal O(h) space?
At most one root-to-leaf chain of recursive calls is active at a time.

### Q4. What is the worst-case height?
For a skewed tree with n nodes, height is O(n). For a balanced tree, height is O(log n).

### Q5. Why can naive diameter be O(n²)?
If height is recomputed independently at every node, the same subtrees are traversed repeatedly.

### Q6. What should a tree DFS return?
Return exactly the information the parent needs: height, gain, validity, count, or some other subtree summary.

### Q7. Why do tree serialization formats need null markers?
Values alone do not uniquely identify structure. Null positions preserve where children are absent.

### Q8. When should you use parent pointers?
For problems involving distance between arbitrary nodes, ancestors, or movement both upward and downward.

---

# 25. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| DFS traversal | O(n) | O(h) |
| BFS level order | O(n) | O(w) |
| Height | O(n) | O(h) |
| Same tree | O(n) | O(h) |
| Invert | O(n) | O(h) |
| Balanced tree | O(n) | O(h) |
| Diameter | O(n) | O(h) |
| Maximum path sum | O(n) | O(h) |
| LCA | O(n) | O(h) |
| Serialize | O(n) | O(n) |
| Construct from traversals | O(n) expected | O(n) |
| Morris traversal | O(n) | O(1) |

> **Core lesson:** Most tree problems become easier when you stop thinking about the entire tree and instead ask what one subtree can tell its parent.
