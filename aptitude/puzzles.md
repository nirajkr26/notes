# Puzzles

> Interview-style puzzles for developing structured reasoning, probability intuition, constraint solving and clear communication.

## Table of Contents

1. How to Approach Puzzles
2. Classic Constraint Puzzles
3. Switches & Bulbs
4. Weighing / Balance Puzzles
5. Bridge & Torch
6. Water Jug Problems
7. Coin Problems
8. Calendar / Clock Puzzles
9. Probability Puzzles
10. Truth-Teller / Liar Puzzles
11. Seating & Scheduling Puzzles
12. Grid & Path Puzzles
13. Optimization Puzzles
14. Lateral Thinking
15. Interview Communication
16. Common Puzzle Mistakes
17. Practice Checklist

---

# 1. How to Approach Puzzles

The interviewer is usually testing your reasoning process, not whether you have seen the exact puzzle.

Use this framework:

```text
Clarify rules
   ↓
List variables
   ↓
List constraints
   ↓
Find invariants
   ↓
Try a small example
   ↓
Search systematically
   ↓
Prove the result
```

## Ask clarifying questions

- Can objects be reused?
- Are measurements exact?
- Is randomness allowed?
- Is the goal minimum, maximum, or any valid solution?
- Can operations be repeated?
- What information is observable?
- Is there a time/space constraint?

Never assume a rule that the puzzle does not provide.

---

# 2. Classic Constraint Puzzles

Constraint puzzles can often be represented as a graph, table, or state space.

Example:

```text
A must occur before B.
C must be adjacent to D.
E cannot be next to F.
```

Translate to:

```text
pos(A) < pos(B)
|pos(C)-pos(D)| = 1
|pos(E)-pos(F)| != 1
```

Then place the most constrained items first.

---

# 3. Switches & Bulbs

## Classic setup

Several switches are outside a room and bulbs are inside. You may manipulate switches before entering, then enter once.

### General insight

Do not think only in terms of whether a bulb is on/off. Use multiple observable states if the physical rules allow it, such as:

```text
on + warm
on + cool
off + warm
```

The exact solution depends on the number of switches, bulbs, and whether heat is observable.

**Interview lesson:** exploit every observable state permitted by the problem.

---

# 4. Weighing / Balance Puzzles

## Information principle

A balance weighing has multiple possible outcomes. For a standard two-pan balance:

```text
left heavier
balanced
right heavier
```

So one weighing can distinguish at most 3 outcome classes under ideal conditions.

This gives a useful lower-bound intuition:

```text
number of distinguishable cases ≤ 3^k
```

for k independent ternary weighings, before considering the structure of the puzzle.

## Strategy

Do not simply divide objects arbitrarily. Design each weighing so every possible outcome leaves a manageable set of candidates.

---

# 5. Bridge & Torch

Typical structure:

- Several people must cross a bridge.
- At most two cross at once.
- A torch is required.
- A pair travels at the slower person's speed.
- The objective is minimum total time.

## Key insight

The fastest person often acts as the torch carrier, but the optimal strategy depends on the crossing times.

For four people with times a ≤ b ≤ c ≤ d, compare strategies such as:

### Strategy A

```text
a+b cross
 a returns
c+d cross
 b returns
 a+b cross
```

Cost:

```text
b + a + d + b + b = a + 3b + d
```

### Strategy B

```text
a+d cross
 a returns
 a+c cross
 a returns
 a+b cross
```

Cost:

```text
d + a + c + a + b = 2a + b + c + d
```

Choose the smaller valid strategy and generalize carefully for larger groups.

---

# 6. Water Jug Problems

Model each state as:

```text
(amount in jug A, amount in jug B)
```

Allowed transitions usually include:

- Fill a jug.
- Empty a jug.
- Pour A → B.
- Pour B → A.

This becomes a graph-search problem.

## Mathematical feasibility

For jug capacities a and b, a target volume d is measurable when d is compatible with `gcd(a,b)` and does not exceed the available capacity conditions of the specific puzzle.

The gcd observation is often the fastest way to determine whether a solution can exist.

---

# 7. Coin Problems

## Counterfeit coin puzzles

First determine:

- Is the counterfeit heavier or lighter known?
- Or unknown?
- How many weighings?
- Is a balance available?

Each weighing creates up to three outcomes, so information capacity matters.

## Minimum coins / change

If the problem asks for the minimum number of coins to make an amount and arbitrary denominations are provided, do not assume greedy works.

For example, denominations `{1, 3, 4}` and amount 6:

```text
greedy: 4 + 1 + 1 → 3 coins
optimal: 3 + 3 → 2 coins
```

This is a classic demonstration of when dynamic programming beats greedy.

---

# 8. Calendar / Clock Puzzles

## Clock angle

At h:m:

```text
hour hand = 30h + 0.5m
minute hand = 6m
```

Difference:

```text
|30h - 5.5m|
```

Use the smaller angle.

## Mirror clocks

For a 12-hour analog clock, the mirror time can be found using:

```text
11:60 - displayed time
```

with careful handling of exact hour boundaries.

## Calendar invariant

Weekdays repeat modulo 7. Track odd days contributed by years/months rather than counting every day manually.

---

# 9. Probability Puzzles

## Monty Hall

Three doors contain one prize and two non-prizes. You choose one. The host, who knows where the prize is, opens a different door showing a non-prize and offers a switch.

Under the standard rules, switching wins with probability:

```text
2/3
```

Why? Your initial choice has probability 1/3 of being correct. The other two doors collectively have probability 2/3. The host's informed reveal transfers that 2/3 to the remaining unopened door.

## Conditional probability warning

Host behavior matters. If the host does not always reveal a non-prize or does not always offer the switch, the probability model changes.

**Interview lesson:** define the random process before calculating probability.

---

# 10. Truth-Teller / Liar Puzzles

Typical characters:

- Always tell the truth.
- Always lie.
- Sometimes lie.

Represent each statement as a logical proposition.

### Useful technique

For a “one question” puzzle, ask a question whose answer transforms the unknown truth/lie behavior into a predictable response.

Do not memorize a magic sentence. Understand the logical composition of:

```text
truth value × speaker behavior × question
```

---

# 11. Seating & Scheduling Puzzles

Represent time slots explicitly.

Example:

| Person | Mon | Tue | Wed | Thu | Fri |
|---|---:|---:|---:|---:|---:|
| A | × | ? | ? | ? | × |
| B | ? | × | ? | ? | ? |
| C | ? | ? | × | ? | ? |

Use:

- Fixed slots first.
- “Before/after” constraints next.
- Adjacency constraints next.
- Negative constraints last.

## Scheduling insight

If a task has a deadline and processing time, compare earliest-deadline-first or other appropriate scheduling principles. But first verify the exact objective: minimizing lateness, minimizing completion time, maximizing tasks, etc. Different objectives produce different optimal rules.

---

# 12. Grid & Path Puzzles

For grid puzzles, define a state:

```text
(row, column)
```

If additional information matters, include it:

```text
(row, column, keys_mask)
(row, column, remaining_energy)
(row, column, direction)
```

This is the same state-design idea used in graph algorithms and dynamic programming.

## Shortest path

- Unweighted moves → BFS.
- Weighted non-negative moves → Dijkstra.
- Small subset of collected items → bitmask state + BFS/DP.

---

# 13. Optimization Puzzles

Ask which mathematical structure is hiding underneath.

| Puzzle signal | Likely technique |
|---|---|
| Minimum/maximum over choices | DP / greedy / binary search |
| Repeated states | DP / memoization |
| Shortest number of moves | BFS |
| Weighted shortest path | Dijkstra |
| All combinations | Backtracking |
| Connectivity | DFS/BFS/DSU |
| Pairing/grouping | Matching / greedy / DP |
| Range operations | Prefix/difference/segment tree |
| Ordering constraints | Topological sort |

The best interview answer often reframes the puzzle as a familiar algorithmic problem.

---

# 14. Lateral Thinking

Lateral puzzles intentionally challenge assumptions.

Before solving, ask:

- Is the obvious interpretation actually stated?
- Is the object being used in the assumed way?
- Is time/space being interpreted correctly?
- Can an action be performed in a different order?
- Is the question asking what is possible, necessary, or likely?

### Important

Do not invent loopholes merely to appear clever. A strong answer should respect the stated rules and clearly identify any assumption you are relaxing.

---

# 15. Interview Communication

For an unfamiliar puzzle, narrate your reasoning:

```text
“I'll first identify the state space.”
“I need to minimize total time, so I'll compare the two plausible strategies.”
“This condition means the choices are not independent.”
“I can rule this case out because it violates constraint 3.”
```

If stuck:

1. Restate the goal.
2. Try a smaller instance.
3. Identify an invariant.
4. Consider the opposite/extreme case.
5. Ask whether the problem can be modeled as a graph, DP, probability tree, or counting problem.

Avoid silently guessing.

---

# 16. Common Puzzle Mistakes

- Not clarifying rules.
- Assuming information that was not given.
- Brute-forcing without tracking state.
- Ignoring symmetry.
- Ignoring information-theoretic limits.
- Confusing probability with certainty.
- Treating correlated events as independent.
- Finding one solution when the question asks for minimum/maximum.
- Giving a clever trick without proving it works.
- Overcomplicating a puzzle that has a simple invariant.

---

# 17. Practice Checklist

- [ ] Switches / bulbs
- [ ] Balance / weighing
- [ ] Bridge / torch
- [ ] Water jugs
- [ ] Counterfeit coins
- [ ] Minimum coin change
- [ ] Clock puzzles
- [ ] Calendar puzzles
- [ ] Probability puzzles
- [ ] Monty Hall
- [ ] Truth-teller / liar
- [ ] Seating constraints
- [ ] Scheduling
- [ ] Grid / path puzzles
- [ ] Optimization puzzles
- [ ] Lateral-thinking puzzles
- [ ] Explain solution aloud
- [ ] Prove optimality where required
