# 15. Trie

A trie stores strings by characters along root-to-node paths. It is ideal when operations depend on prefixes.

## Fundamentals

For alphabet size `A`, each node can store up to `A` children and an end-of-word marker.

Insert/search a word of length `L`: **O(L)**. Delete: **O(L)** plus cleanup. Space is proportional to the total number of stored characters (times node overhead).

## Beginner

Learn insert, exact search, prefix search, and recursive/iterative traversal.

```cpp
struct TrieNode {
    array<TrieNode*, 26> child{};
    bool end = false;
    TrieNode() { child.fill(nullptr); }
};
```

## Intermediate

### Starts With
Walk the prefix; existence of the final node means some stored word has that prefix.

**TC:** O(L)  **SC:** O(1) query space.

### Word dictionary
Trie + DFS/backtracking can search words in a board while pruning impossible prefixes.

## Advanced

- Frequency/count at nodes.
- Store maximum score per prefix.
- Compressed/radix trie.
- Binary trie for XOR problems.
- Trie + DP for word segmentation.

## Important questions

1. Implement Trie — **O(L)** insert/search, **O(L)** prefix query.
2. Word Search II — Trie + DFS, significantly prunes branches; worst-case depends on board/dictionary and remains exponential in path length.
3. Replace Words — trie prefixes, roughly **O(total input characters)** plus trie traversal.
4. Maximum XOR of two numbers — binary trie, **O(Bn)** where B is bit width; **O(Bn)** space.
5. Word Break — DP + trie, often **O(n²)** traversal in standard bounded-word formulations; analyze against dictionary/word lengths.

## Trie vs hash set
Use a hash set for exact membership. Use a trie when prefix relationships, lexicographic traversal, or prefix-based aggregation are central.