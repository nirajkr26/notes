# Logical Reasoning

> A pattern-recognition and decision-making guide for placement tests, online assessments and interviews.

## Table of Contents

1. Reasoning Framework
2. Number & Alphabet Series
3. Analogy
4. Classification / Odd One Out
5. Coding-Decoding
6. Direction Sense
7. Blood Relations
8. Ranking & Ordering
9. Seating Arrangement
10. Puzzles & Constraint Reasoning
11. Syllogisms
12. Statements & Conclusions
13. Statements & Assumptions
14. Cause & Effect
15. Data Sufficiency
16. Venn Diagrams
17. Clocks & Calendars
18. Non-Verbal Reasoning
19. Fast Techniques
20. Common Traps
21. Practice Checklist

---

# 1. Reasoning Framework

Most reasoning questions become easier when translated into a small formal model.

```text
Words → entities → constraints → diagram/table → eliminate impossible states → answer
```

Before solving, identify:

- What is fixed?
- What can vary?
- What is mutually exclusive?
- What is conditional?
- What must be true vs could be true?

Use tables for ordering, graphs for relationships, and sets for membership.

---

# 2. Number & Alphabet Series

Look for patterns in this order:

1. Constant difference.
2. Increasing/decreasing difference.
3. Multiplication/division.
4. Alternating operations.
5. Squares/cubes/primes.
6. Two interleaved sequences.
7. Digit-level transformation.

### Example

```text
2, 6, 12, 20, 30, ?
```

Differences:

```text
4, 6, 8, 10 → next 12
```

Answer = 42.

### Alphabet positions

```text
A=1, B=2, ..., Z=26
```

For reverse positions:

```text
A=26, B=25, ..., Z=1
```

Useful identity:

```text
normal position + reverse position = 27
```

---

# 3. Analogy

Find the relationship in the first pair, then apply the same relationship to the second.

Common relationships:

- Part → whole
- Tool → function
- Cause → effect
- Synonym / antonym
- Degree / intensity
- Number transformation
- Object → category

**Trap:** choose a relationship that is specific enough to distinguish the correct option, not a vague association.

---

# 4. Classification / Odd One Out

Test categories systematically:

- Mathematical property.
- Letter pattern.
- Meaning.
- Shape.
- Direction.
- Number of factors.
- Prime/composite.
- Physical or conceptual category.

If four items share one precise rule and one does not, the exception is the answer.

---

# 5. Coding-Decoding

## Common coding types

- Alphabet shifts.
- Reverse alphabet.
- Position arithmetic.
- Character substitution.
- Word reversal.
- Rearrangement.
- Symbol substitution.

### Caesar-style shift

If each letter moves +2:

```text
A → C
B → D
X → Z
Y → A
Z → B
```

Always account for wraparound.

### Word coding

Compare the same words across multiple examples. Identify which code belongs to which word before solving the target.

---

# 6. Direction Sense

Use coordinates instead of mental visualization.

```text
          North (+y)
              ↑
West (-x) ← origin → East (+x)
              ↓
          South (-y)
```

Represent each movement as `(dx, dy)`.

```text
North = (0,+1)
South = (0,-1)
East  = (+1,0)
West  = (-1,0)
```

For final displacement:

```text
distance = √(x²+y²)
```

For direction, compare the signs of x and y.

---

# 7. Blood Relations

Build a family graph.

```text
Male   → M
Female → F
Parent → upward edge
Child  → downward edge
Sibling → same generation
```

### Reliable method

1. Start from the person whose relationship is asked.
2. Expand each relation one step at a time.
3. Mark gender only when known.
4. Avoid assuming gender from names.

### Common vocabulary

- Father's brother → paternal uncle.
- Mother's brother → maternal uncle.
- Brother's daughter → niece.
- Sister's son → nephew.
- Parent's sibling → uncle/aunt.

---

# 8. Ranking & Ordering

If a person is rank r from the top and s from the bottom:

```text
total = r + s - 1
```

If there are n people and a person is kth from the top:

```text
rank from bottom = n-k+1
```

### Comparison chains

For:

```text
A > B > C > D
```

you immediately know A is above D, but do not infer numerical gaps unless provided.

---

# 9. Seating Arrangement

## Linear arrangement

Draw numbered slots:

```text
1  2  3  4  5  6  7
```

Place the most constrained person first.

## Circular arrangement

Fix one person at the top to remove rotational duplicates.

### Facing direction

When everyone faces the center:

- Left/right are reversed relative to someone facing outward.
- Clockwise/anticlockwise must be tracked consistently.

When possible, draw arrows rather than relying on memory.

### Strategy

1. Place fixed positions.
2. Place adjacency constraints.
3. Apply “not next to” constraints.
4. Apply conditional constraints.
5. Test remaining candidates.

---

# 10. Puzzles & Constraint Reasoning

Translate statements into constraints.

Example:

```text
A must be before B.
C cannot sit next to D.
E is exactly two positions after F.
```

These become:

```text
pos(A) < pos(B)
pos(C) ≠ pos(D) ± 1
pos(E) = pos(F) + 2
```

The strongest constraints should be applied first.

## Constraint priority

```text
Exact position > fixed relation > adjacency > inequality > preference
```

---

# 11. Syllogisms

Represent statements as sets.

Example:

```text
All A are B.
All B are C.
```

Therefore:

```text
All A are C.
```

But:

```text
All A are B.
```

does **not** imply:

```text
All B are A.
```

## Common forms

- All A are B.
- No A is B.
- Some A are B.
- Some A are not B.

### Safe approach

Draw circles/sets and test whether the conclusion must be true.

**Important:** do not import real-world knowledge into formal syllogism questions unless the question explicitly asks for it.

---

# 12. Statements & Conclusions

A conclusion must follow from the given information, not from outside knowledge.

Ask:

```text
Can I construct a valid case where the statement is true but the conclusion is false?
```

If yes, the conclusion is not logically necessary.

Distinguish:

- Must be true.
- Could be true.
- Cannot be true.

---

# 13. Statements & Assumptions

An assumption is an unstated condition required for the statement/recommendation to make sense.

### Test

Try to negate the assumption.

If negating it destroys the reasoning, it is likely a necessary assumption.

Avoid choosing statements that merely support the argument but are not required.

---

# 14. Cause & Effect

Look for temporal and logical direction.

```text
Cause → Effect
```

Ask:

1. Which event can plausibly occur first?
2. Is there a direct mechanism?
3. Could both be effects of a third event?
4. Is the relationship merely correlation?

A simultaneous occurrence does not automatically establish causation.

---

# 15. Data Sufficiency

The question is usually not “can I calculate the answer?” but:

> Is the information sufficient to determine a unique answer?

### Method

Test each statement independently, then together.

Typical answer structure:

```text
I only
II only
Either I or II
Both together
Even together insufficient
```

Do not solve more than necessary. Once uniqueness is established, stop.

---

# 16. Venn Diagrams

Use Venn diagrams for:

- All/some/no statements.
- Category overlap.
- Survey questions.
- Set-counting reasoning.

For three groups, remember inclusion-exclusion:

```text
A∪B∪C = A+B+C - AB-BC-CA + ABC
```

---

# 17. Clocks & Calendars

## Clock angle

At h hours and m minutes:

```text
minute hand = 6m°
hour hand = 30h + 0.5m°
angle = |30h - 5.5m|°
```

Take the smaller of θ and 360−θ.

## Calendar

Key idea: days repeat modulo 7.

```text
ordinary year = 365 = 52 weeks + 1 day
leap year = 366 = 52 weeks + 2 days
```

A leap year is generally divisible by 4, except century years must also be divisible by 400.

---

# 18. Non-Verbal Reasoning

For figures, inspect:

1. Rotation.
2. Reflection.
3. Number of sides.
4. Shading.
5. Position of symbols.
6. Count of objects.
7. Alternation.
8. Symmetry.
9. Progressive addition/removal.

Do not assume rotation and reflection are equivalent.

---

# 19. Fast Techniques

## Elimination

Reject options that violate a single hard constraint before doing full reasoning.

## Contradiction

Assume an option is true and derive an impossible condition.

## Extremes

Try the earliest/latest, smallest/largest, or most/least constrained candidate first.

## Table Method

For multi-attribute puzzles:

| Person | Day | City | Role |
|---|---|---|---|
| A | ? | ? | ? |
| B | ? | ? | ? |
| C | ? | ? | ? |

Use ✓/× when the question is dense.

---

# 20. Common Traps

- Assuming an unstated relationship.
- Reversing left/right in circular arrangements.
- Treating “some” as “all.”
- Treating “all A are B” as “all B are A.”
- Ignoring wraparound in alphabet coding.
- Using real-world facts in formal logic.
- Solving every possibility when elimination is enough.
- Confusing “must be true” with “could be true.”
- Losing track of whether a condition is inclusive or exclusive.

---

# 21. Practice Checklist

- [ ] Number series
- [ ] Alphabet series
- [ ] Analogy
- [ ] Classification
- [ ] Coding-decoding
- [ ] Direction sense
- [ ] Blood relations
- [ ] Ranking
- [ ] Linear seating
- [ ] Circular seating
- [ ] Constraint puzzles
- [ ] Syllogisms
- [ ] Conclusions
- [ ] Assumptions
- [ ] Cause/effect
- [ ] Data sufficiency
- [ ] Venn diagrams
- [ ] Clocks
- [ ] Calendars
- [ ] Non-verbal reasoning
- [ ] Timed mixed sets
