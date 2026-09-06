# 15. Trie — Complete Prefix-Tree Guide

A Trie is a tree designed to store strings character-by-character. It is especially useful when the question is about **prefixes** rather than only exact membership.

---

# 1. Why a Trie?

Suppose a dictionary contains:

```text
apple
app
application
banana
```

The words share prefixes. A Trie stores that shared structure once:

```text
root
 ├─ a ─ p ─ p ─ l ─ e
 │         └─ ...
 └─ b ─ a ─ n ─ a ─ n ─ a
```

For a word of length `L`, insertion/search takes O(L), independent of the total number of stored words.

A hash set also gives fast exact membership, but it does not naturally answer prefix questions.

---

# 2. Basic Node

For lowercase English letters:

```cpp
struct TrieNode {
    TrieNode* child[26];
    bool terminal;

    TrieNode() : terminal(false) {
        fill(begin(child), end(child), nullptr);
    }
};
```

Each edge represents one character. `terminal` tells whether a complete word ends at that node.

---

# 3. Insert

For each character:

1. convert character to index
2. create missing child
3. move to child
4. mark final node as terminal

**TC:** O(L)  
**SC:** O(L) worst-case new nodes

---

# 4. Search Exact Word

Walk through every character. If a required child does not exist, the word is absent. At the end, check `terminal`.

Why check terminal?

If `app` is stored, the path for `app` also exists when `apple` is stored. Path existence alone does not mean the shorter path is a complete word.

**TC:** O(L)  
**SC:** O(1) query space

---

# 5. Prefix Search

For `startsWith(prefix)`, walk the prefix. If every character exists, some stored word has that prefix.

**TC:** O(L)  
**SC:** O(1) query space

This is the canonical reason to choose a Trie.

---

# 6. Full Trie Implementation

```cpp
class Trie {
    struct Node {
        Node* child[26];
        bool terminal;

        Node() : terminal(false) {
            fill(begin(child), end(child), nullptr);
        }
    };

    Node* root;

public:
    Trie() : root(new Node()) {}

    void insert(const string& word) {
        Node* cur = root;
        for (char c : word) {
            int x = c - 'a';
            if (!cur->child[x]) cur->child[x] = new Node();
            cur = cur->child[x];
        }
        cur->terminal = true;
    }

    bool search(const string& word) const {
        Node* cur = root;
        for (char c : word) {
            int x = c - 'a';
            if (!cur->child[x]) return false;
            cur = cur->child[x];
        }
        return cur->terminal;
    }

    bool startsWith(const string& prefix) const {
        Node* cur = root;
        for (char c : prefix) {
            int x = c - 'a';
            if (!cur->child[x]) return false;
            cur = cur->child[x];
        }
        return true;
    }
};
```

**Insert/Search TC:** O(L)  
**Prefix TC:** O(L)  
**Space:** O(total number of stored characters × node overhead)

---

# 7. Trie vs Hash Set

| Requirement | Better choice |
|---|---|
| Exact word lookup | Hash set / Trie |
| Prefix lookup | Trie |
| Lexicographic traversal | Trie |
| Prefix counts | Trie |
| Simple arbitrary membership | Hash set |
| Character-by-character pruning | Trie |

Do not use a Trie merely because strings are involved. Use it when prefix structure matters.

---

# 8. Prefix Frequency

A node can store a count of how many inserted words pass through it.

Then:

```text
prefix count = count stored at prefix node
```

This supports autocomplete/popularity questions.

**Insert:** O(L)  
**Prefix count query:** O(L)

---

# 9. Autocomplete

To autocomplete a prefix:

1. Walk to its Trie node.
2. DFS from that node.
3. Collect terminal words.

Traversal cost depends on the number and total length of returned suggestions.

A Trie is useful because DFS never enters branches that do not match the prefix.

---

# 10. Replace Words

Given dictionary roots such as:

```text
cat
bat
rat
```

replace words by their shortest matching root.

Walk each word character-by-character through the Trie and stop at the first terminal node.

**TC:** proportional to total characters examined, often O(total input characters) with bounded root traversal  
**SC:** O(total dictionary characters)

---

# 11. Word Search II

Given a board and many dictionary words, doing independent DFS for every word repeats huge amounts of work.

Build a Trie of all dictionary words.

During board DFS:

```text
current path → Trie node
```

If the next character has no Trie child, stop immediately. This is powerful pruning.

### Combination of techniques

```text
Trie + DFS + backtracking
```

This is a very important advanced pattern.

Worst-case search remains exponential, but the Trie can dramatically reduce useless branches.

---

# 12. Binary Trie — Maximum XOR

A binary Trie stores bits instead of letters.

For each number, insert its binary representation from the most significant bit to the least significant bit.

To maximize XOR with `x`, at every bit prefer the opposite bit if available.

Why?

The most significant differing bit dominates all lower bits, so choosing the opposite bit greedily maximizes XOR.

For B-bit integers:

**TC:** O(nB)  
**SC:** O(nB)

With fixed 32/64-bit integers, B is constant, so this is effectively O(n).

---

# 13. Word Break with Trie + DP

For a string `s`, define:

```text
dp[i] = whether prefix s[0..i-1] can be segmented
```

From each reachable position, walk the Trie forward through the string. Whenever a terminal word is found, mark the resulting position reachable.

This combines:

```text
Trie for dictionary prefix matching
+
DP for reachable segmentation states
```

Complexity depends on maximum word length and dictionary size; with bounded maximum word length `L`, traversal is roughly O(nL) after Trie construction.

---

# 14. Advanced Trie Variants

Know the concepts of:

- compressed/radix Trie
- Trie with word frequencies
- Trie storing maximum score per prefix
- binary Trie
- Trie + backtracking
- Trie + DP
- persistent Trie concept

These appear in harder OA and competitive-programming problems.

---

# 15. Memory Considerations

An array of 26 pointers per node can consume significant memory.

Alternatives:

- `unordered_map<char, Node*>` for sparse children
- sorted child vectors
- compressed Trie/radix tree

Array children provide predictable O(1) character transition but may waste memory when each node has few children.

---

# 16. OA / Interview Problem Bank

## Beginner

1. Implement Trie.
2. Search word.
3. Starts With.
4. Insert/search multiple words.
5. Count words with prefix.
6. Autocomplete basics.

## Intermediate

7. Replace Words.
8. Longest Word in Dictionary.
9. Search suggestions.
10. Word Break with Trie.
11. Prefix frequency queries.
12. Lexicographic word traversal.

## Advanced

13. Word Search II.
14. Maximum XOR pair.
15. Maximum XOR with query constraints.
16. Binary Trie with deletion.
17. Trie + DP segmentation.
18. Compressed Trie.
19. Persistent Trie concepts.

---

# 17. Frequently Asked Interview Questions

### Q1. Why is Trie search O(L)?
The algorithm follows exactly one child per character, so it processes each character once.

### Q2. Trie vs hash table?
Hashing is usually better for exact membership. Trie is better for prefix queries, lexicographic traversal, and prefix aggregation.

### Q3. Why do we need a terminal flag?
Because a word can be a prefix of another word. The path can exist without the shorter string itself being stored.

### Q4. Why is Trie useful in Word Search II?
It shares dictionary prefixes and lets DFS stop as soon as the current board path is not a dictionary prefix.

### Q5. Why does binary Trie maximize XOR?
At each most-significant bit, choosing the opposite bit creates a `1`, which dominates all decisions at lower bits.

### Q6. Why can Trie memory be large?
A node may contain many child pointers even when only a few are actually used.

### Q7. When should you avoid a Trie?
When only exact lookup is needed and a hash set is simpler and more memory-efficient.

---

# 18. Complexity Summary

| Operation | TC | Space |
|---|---:|---:|
| Insert | O(L) | O(L) new nodes worst-case |
| Search | O(L) | O(1) query |
| Prefix | O(L) | O(1) query |
| Prefix count | O(L) | O(1) query |
| Autocomplete | O(L + output) | Output-dependent |
| Word Search II | Exponential worst-case | Trie + DFS state |
| Binary Trie XOR | O(nB) | O(nB) |
| Trie + DP Word Break | ~O(nL) bounded max word length | O(n + dictionary) |

> **Core lesson:** A Trie turns prefix relationships into paths. Whenever the problem says “starts with,” “prefix,” “autocomplete,” or “dictionary prefixes,” think Trie before reaching for a more complicated solution.
