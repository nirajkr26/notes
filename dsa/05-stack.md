# 05. Stack — From LIFO Basics to Monotonic Stack

A stack follows **LIFO: Last In, First Out**. The last item inserted is the first item removed.

Stacks appear everywhere in interviews: parentheses, expression parsing, recursion simulation, DFS, undo operations, and monotonic-stack optimization.

---

# 1. Stack Fundamentals

Core operations:

```text
push(x)  → insert
pop()    → remove top
top()    → inspect top
empty()  → check emptiness
```

All are O(1) using `std::stack`.

```cpp
stack<int> st;
st.push(10);
st.push(20);
cout << st.top(); // 20
st.pop();
```

**TC:** O(1) per basic operation  
**SC:** O(n)

### Why not use a vector directly?
You can. `vector::push_back`, `back`, and `pop_back` also provide the required stack behavior. `stack` communicates intent more clearly.

---

# 2. Implement Stack Using Array

Maintain an array/vector and an index representing the top.

Important cases:

- overflow if fixed capacity is full
- underflow if popping an empty stack

All operations remain O(1).

This question tests data-structure fundamentals rather than algorithmic tricks.

---

# 3. Valid Parentheses

Given characters such as:

```text
()[]{ }
```

check whether brackets are correctly nested.

### Why a stack?
The most recently opened bracket must be the first bracket closed. That is exactly LIFO.

### C++

```cpp
bool isValid(const string& s) {
    stack<char> st;

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty()) return false;

            char open = st.top();
            st.pop();

            if ((c == ')' && open != '(') ||
                (c == ']' && open != '[') ||
                (c == '}' && open != '{')) {
                return false;
            }
        }
    }
    return st.empty();
}
```

**TC:** O(n)  
**SC:** O(n)

### Common mistakes

- Forgetting to check empty stack before `top()`.
- Returning true while unmatched opening brackets remain.
- Matching values without respecting nesting order.

---

# 4. Min Stack

Design a stack supporting:

```text
push
pop
top
getMin
```

all in O(1).

### Key idea
At every stack position, store the minimum value seen up to that point.

Conceptually:

```text
(value, minimum-so-far)
```

If values are:

```text
5, 3, 7, 2
```

stored minima become:

```text
(5,5)
(3,3)
(7,3)
(2,2)
```

Then the current minimum is simply the top pair's second value.

**TC:** O(1) per operation  
**SC:** O(n)

### Interview follow-up
Can you reduce memory further?

Yes, an encoding/difference technique can store less metadata, but it is more error-prone. The pair approach is usually the best interview baseline.

---

# 5. Queue Using Two Stacks

A queue is FIFO, while a stack is LIFO.

Use:

```text
input stack → receives new elements
output stack → serves front elements
```

When output stack is empty, transfer everything from input to output. Reversing twice restores FIFO order.

### Complexity
Each element moves from input to output at most once.

**Amortized TC:** O(1) per operation  
**Worst individual transfer:** O(n)  
**SC:** O(n)

### Interview lesson
Do not call every operation O(n) merely because one operation can transfer many elements. Use amortized analysis over the entire sequence.

---

# 6. Monotonic Stack

This is one of the highest-value stack patterns.

A monotonic stack maintains values or indices in increasing/decreasing order.

Why is this useful?

Suppose a new value is larger than values sitting on a decreasing stack. Those smaller values have just found their next greater element and may never matter again.

So we can permanently discard them.

### Why O(n)?
Every element is:

- pushed at most once
- popped at most once

Therefore total stack operations are O(n), even though there is a nested `while` loop.

---

# 7. Next Greater Element

For each element, find the first greater element to its right.

Example:

```text
[2, 1, 5, 3, 4]
```

answer:

```text
[5, 5, -1, 4, -1]
```

### Approach
Scan from right to left.

Maintain a decreasing stack of possible next-greater candidates.

For current `x`:

1. Pop values `<= x` because they cannot be the next greater element for `x`.
2. If stack is non-empty, its top is the answer.
3. Push `x`.

### C++

```cpp
vector<int> nextGreater(const vector<int>& a) {
    int n = a.size();
    vector<int> ans(n, -1);
    stack<int> st;

    for (int i = n - 1; i >= 0; --i) {
        while (!st.empty() && st.top() <= a[i]) {
            st.pop();
        }
        if (!st.empty()) ans[i] = st.top();
        st.push(a[i]);
    }
    return ans;
}
```

**TC:** O(n) amortized  
**SC:** O(n)

---

# 8. Next Greater Element II — Circular Array

The array wraps around, so an element can find a greater value after the end by continuing from the beginning.

A common trick is to process indices from `2n-1` down to `0` using `i % n`.

Only store answers for the first `n` positions.

**TC:** O(n)  
**SC:** O(n)

### Recognition
If the problem says **circular array + next greater/smaller**, think “process twice with modulo.”

---

# 9. Daily Temperatures

For each day, find how many days until a warmer temperature.

Instead of storing values, store **indices** because the answer requires a distance.

Maintain a decreasing stack of indices.

When `temperature[i] > temperature[stack.top()]`, the current day resolves the previous day's answer:

```text
answer[old] = i - old
```

**TC:** O(n)  
**SC:** O(n)

### Key lesson
When a monotonic-stack question asks “how far,” “how many positions,” or “which index,” store indices rather than values.

---

# 10. Stock Span

For each day, find the number of consecutive previous days whose price is less than or equal to today's price.

This is essentially a previous-greater-element problem.

Store indices in a decreasing stack and pop all dominated prices.

**TC:** O(n) amortized  
**SC:** O(n)

---

# 11. Largest Rectangle in Histogram

Given bar heights, find the largest rectangle.

For every bar, imagine it is the limiting height. We need to know how far left and right that height can extend.

A naive solution computes boundaries for each bar in O(n²).

A monotonic increasing stack finds boundaries in O(n).

### Core idea
When a smaller bar arrives, the current bar can no longer extend to the right. Pop it and calculate its maximal width.

If bar `i` is popped and the new stack top is `left`, then:

```text
width = currentIndex - left - 1
area = height[i] * width
```

**TC:** O(n) amortized  
**SC:** O(n)

### Important edge case
Append a virtual height `0` at the end conceptually so all remaining bars are popped and evaluated.

---

# 12. Maximal Rectangle in Binary Matrix

Convert each row into a histogram of consecutive ones.

For each row:

1. Update column heights.
2. Solve largest rectangle in histogram.

If matrix dimensions are `R × C`:

**TC:** O(RC)  
**SC:** O(C)

This is an important example of reducing a 2D problem to a known 1D problem.

---

# 13. Remove K Digits

Given a number string, remove `k` digits to produce the smallest possible number.

### Greedy insight
If a digit is followed by a smaller digit, keeping the larger earlier digit makes the number worse. Remove it while removals remain.

Use a monotonic increasing stack.

**TC:** O(n) amortized  
**SC:** O(n)

### Why greedy works
Removing an earlier larger digit improves a more significant position, which dominates any improvement possible by changing a later position.

---

# 14. Decode / Evaluate Expressions

Stacks are useful when operators and nested parentheses must be processed in reverse-nesting order.

Classic problems:

- Evaluate Reverse Polish Notation.
- Basic Calculator.
- Infix to postfix.
- Postfix evaluation.
- Expression conversion.

The general idea is to defer operations until enough operands/context are available.

---

# 15. Asteroid Collision

Process asteroids left to right.

A collision can happen only when:

```text
previous asteroid moves right
current asteroid moves left
```

The stack represents the surviving sequence. Repeatedly compare the incoming asteroid against the top while a collision is possible.

**TC:** O(n) amortized  
**SC:** O(n)

### Pattern
A stack can represent the **current valid prefix** while new elements interact only with the most recent unresolved element.

---

# 16. Online Assessment Problem Set

## Beginner

1. Implement stack.
2. Push/pop/top/empty.
3. Reverse a stack.
4. Insert at bottom of stack.
5. Valid parentheses.
6. Remove adjacent duplicates.
7. Evaluate postfix expression.
8. Implement queue using two stacks.

## Intermediate

9. Min Stack.
10. Next Greater Element.
11. Previous Greater Element.
12. Next Smaller Element.
13. Stock Span.
14. Daily Temperatures.
15. Next Greater Element II.
16. Asteroid Collision.
17. Remove K Digits.
18. Simplify Path.

## Advanced

19. Largest Rectangle in Histogram.
20. Maximal Rectangle.
21. Trapping Rain Water using stack.
22. Basic Calculator.
23. Decode nested string.
24. Sum of subarray minimums.
25. Sum of subarray ranges.
26. Online stock span.
27. Design a stack with O(1) min/max.

---

# 17. Frequently Asked Interview Questions

### Q1. Why is stack LIFO useful for parentheses?
The most recently opened bracket must be closed first, exactly matching LIFO behavior.

### Q2. Why is a monotonic stack O(n)?
Every element is inserted once and removed at most once, so total stack operations are linear.

### Q3. Why store indices instead of values?
Indices let us calculate distances and boundaries while still accessing the corresponding values through the original array.

### Q4. Increasing vs decreasing monotonic stack?
It depends on which relation you are searching for. Next greater commonly uses a decreasing candidate stack; next smaller commonly uses an increasing one.

### Q5. Why does largest rectangle use a monotonic stack?
It identifies the first smaller boundary on both sides, allowing each bar's maximal rectangle to be calculated once.

### Q6. Why is queue using two stacks amortized O(1)?
An element can be transferred from the input stack to the output stack only once before it is removed.

### Q7. When should I suspect a monotonic stack?
Look for phrases such as:

- next greater
- next smaller
- previous greater
- previous smaller
- nearest larger/smaller
- first position where a value becomes dominated
- contribution of each element to ranges

### Q8. Why can elements be discarded permanently?
Once a stronger candidate appears in the required direction, the weaker candidate can never become the answer for a future position where the stronger candidate is also available.

---

# 18. Complexity Summary

| Problem | TC | SC |
|---|---:|---:|
| Basic stack operation | O(1) | O(1) per operation |
| Valid parentheses | O(n) | O(n) |
| Min stack | O(1) each | O(n) |
| Queue using two stacks | O(1) amortized | O(n) |
| Next greater | O(n) | O(n) |
| Daily temperatures | O(n) | O(n) |
| Stock span | O(n) | O(n) |
| Largest rectangle | O(n) | O(n) |
| Maximal rectangle | O(RC) | O(C) |
| Remove K digits | O(n) | O(n) |
| Asteroid collision | O(n) | O(n) |

> **Core lesson:** The stack is not only a container. In advanced problems it becomes a way to remember exactly the unresolved candidates that still have a chance to affect the future.
