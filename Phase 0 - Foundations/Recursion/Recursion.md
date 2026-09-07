# Recursion

## 1. What is Recursion?

**Recursion** is a technique where a function solves a problem by calling itself on a **smaller version of the same problem**.

Every recursive solution needs:

1. **Base Case** — when to stop.
2. **Recursive Case** — how to reduce the problem.

```cpp
returnType solve(parameters) {
    if (base_case)
        return answer;

    return solve(smaller_problem);
}
```

### Core idea

> **Current Problem → Smaller Problem → Base Case → Return → Final Answer**

---

# 2. The Most Important Mental Model

Don't think:

> "How does the function execute all these calls?"

Think:

> **"If I already knew the answer to the smaller problem, how could I use it to solve the current problem?"**

Example:

```cpp
int sum(int n) {
    if (n == 0)
        return 0;

    return n + sum(n - 1);
}
```

Meaning:

```text
sum(n) = n + sum(n - 1)
```

`sum(n - 1)` solves the smaller problem.
The current function only combines that answer with `n`.

---

# 3. The 5 Questions to Ask Before Coding

For any recursion problem, answer these:

### 1. What does my function mean?

Write:

```text
solve(...) means __________________.
```

Example:

```cpp
int sum(int n)
```

means:

> `sum(n)` returns the sum from `1` to `n`.

### 2. What is the state?

What information completely describes the current subproblem?

Common state:

```text
index
node
left, right
remaining amount
current position
number of elements remaining
```

Examples:

```cpp
solve(i)          // current index
solve(node)       // current node
solve(left,right) // current range
```

### 3. What is the smaller problem?

Examples:

```text
sum(n)       → sum(n - 1)
factorial(n) → factorial(n - 1)
solve(i)     → solve(i + 1)
solve(node)  → solve(node->left/right)
```

### 4. What is the base case?

Ask:

> **"When can I answer immediately without another recursive call?"**

Examples:

```cpp
if (n == 0) return 0;
```

```cpp
if (i == n) return;
```

```cpp
if (node == nullptr) return;
```

### 5. How do I combine the smaller answer?

Examples:

```cpp
return n + solve(n - 1);
```

```cpp
return n * solve(n - 1);
```

```cpp
return max(a[i], solve(i + 1));
```

---

# 4. State — The Most Important Design Skill

The **state** is the information needed to completely describe the current subproblem.

Ask:

> **"What changes when I move to the smaller problem?"**

That changing information usually becomes a parameter.

### Array

```cpp
solve(i)
```

`i` = current index.

### Binary Search

```cpp
solve(left, right)
```

`left/right` = current searchable range.

### Tree

```cpp
solve(node)
```

`node` = current subtree.

### Rule

> **Only keep parameters that are necessary to describe the current subproblem.**

---

# 5. Base Case

The base case is the smallest/finished state that can be answered directly.

Examples:

### Factorial

```cpp
int fact(int n) {
    if (n == 0)
        return 1;

    return n * fact(n - 1);
}
```

### Array Traversal

```cpp
void solve(vector<int>& a, int i) {
    if (i == a.size())
        return;

    // process a[i]

    solve(a, i + 1);
}
```

### Linked List

```cpp
void solve(Node* node) {
    if (node == nullptr)
        return;

    solve(node->next);
}
```

### Tree

```cpp
void solve(Node* root) {
    if (root == nullptr)
        return;

    solve(root->left);
    solve(root->right);
}
```

A function can have **multiple stopping conditions** if different states require immediate answers.

---

# 6. Recursive Transition

After finding the base case, ask:

> **"What change moves me toward the base case?"**

Examples:

```text
n → n - 1
i → i + 1
right → mid - 1
node → node->left
```

The critical rule:

> **Every recursive path must eventually reach a base case.**

Bad:

```cpp
solve(n);
```

if `n` never changes.

Correct:

```cpp
solve(n - 1);
```

---

# 7. Call Stack

Each recursive call is placed on the **call stack**.

For:

```cpp
fact(4)
```

the calls go down:

```text
fact(4)
 ↓
fact(3)
 ↓
fact(2)
 ↓
fact(1)
 ↓
fact(0)
```

Then the calls return upward:

```text
fact(0) = 1
 ↑
fact(1) = 1
 ↑
fact(2) = 2
 ↑
fact(3) = 6
 ↑
fact(4) = 24
```

### Two phases

**Going down:** recursive calls are created.

**Coming back up:** results are returned and combined.

This is important for understanding code before vs. after the recursive call.

---

# 8. Before vs. After Recursion

### Work before recursive call

```cpp
cout << n;
solve(n - 1);
```

Output:

```text
5 4 3 2 1
```

### Work after recursive call

```cpp
solve(n - 1);
cout << n;
```

Output:

```text
1 2 3 4 5
```

Why?

Because code after the recursive call executes during **stack unwinding**.

---

# 9. Common Recursion Patterns

## A. Linear Recursion

One recursive call:

```cpp
solve(n - 1);
```

Examples:

```text
factorial
sum
array traversal
linked-list traversal
binary search
```

Structure:

```text
f(n)
 ↓
f(n-1)
 ↓
f(n-2)
 ↓
...
```

## B. Multiple Recursion

More than one recursive call:

```cpp
solve(left);
solve(right);
```

Examples:

```text
binary trees
divide and conquer
Fibonacci
```

Structure:

```text
       f()
      /   \
    f()   f()
   / \   / \
  ... ... ...
```

---

# 10. Recursion on Common Data Structures

## Array

```cpp
void solve(vector<int>& a, int i) {
    if (i == a.size())
        return;

    // process a[i]

    solve(a, i + 1);
}
```

Think:

```text
current element + remaining array
```

## Linked List

```cpp
void solve(Node* node) {
    if (node == nullptr)
        return;

    // process node

    solve(node->next);
}
```

Think:

```text
current node + remaining list
```

## Binary Tree

```cpp
void solve(Node* root) {
    if (root == nullptr)
        return;

    solve(root->left);
    solve(root->right);
}
```

Think:

```text
root + left subtree + right subtree
```

Trees are especially suitable for recursion because a subtree is itself a smaller tree.

---

# 11. Recursion With Return Values

A powerful pattern is:

```text
Current Answer
=
Current Work
+
Answer From Smaller Problem
```

Examples:

### Sum

```cpp
return n + sum(n - 1);
```

### Factorial

```cpp
return n * fact(n - 1);
```

### Maximum

```cpp
return max(a[i], maximum(a, i + 1));
```

### Tree Height

```cpp
return 1 + max(height(root->left),
               height(root->right));
```

The key idea:

> **Let the smaller recursive call solve the smaller problem. Then use its answer.**

---

# 12. How to Recognize Recursion

Recursion is a natural choice when:

### 1. The problem contains a smaller version of itself

```text
factorial(n)
→ factorial(n-1)
```

### 2. The problem is hierarchical

Examples:

```text
Trees
Linked Lists
Nested structures
Directories
Expressions
```

### 3. Divide and Conquer

Examples:

```text
Merge Sort
Quick Sort
Binary Search
```

### Important

> **Recursion is a technique, not a requirement.**

Many recursive problems can also be solved iteratively.

Use recursion when it makes the structure simpler or more natural.

---

# 13. Recursion Complexity

Never assume:

> "Recursion = O(n)"

Complexity depends on the number of calls and work per call.

### One call

```text
T(n) = T(n-1) + O(1)
```

Usually:

```text
O(n)
```

### Divide by 2

```text
T(n) = T(n/2) + O(1)
```

Usually:

```text
O(log n)
```

### Merge Sort

```text
T(n) = 2T(n/2) + O(n)
```

```text
O(n log n)
```

### Multiple calls

```cpp
f(n) {
    f(n - 1);
    f(n - 1);
}
```

Can become exponential:

```text
O(2^n)
```

depending on the exact recurrence.

---

# 14. Recursion Stack Space

Every active recursive call occupies stack space.

If the maximum number of simultaneously active calls is `n`:

```text
Stack Space = O(n)
```

Example:

```text
f(n)
 ↓
f(n-1)
 ↓
f(n-2)
 ↓
...
```

So always consider:

```text
Time Complexity
+
Recursion Stack Space
```

---

# 15. Memoization

Sometimes recursion solves the same state repeatedly.

Example:

```text
fib(5)
├── fib(4)
│   ├── fib(3)
│   └── fib(2)
└── fib(3)
    ├── fib(2)
    └── fib(1)
```

`fib(3)` and `fib(2)` are repeated.

**Memoization** stores already-computed results:

```cpp
if (dp[state] is already calculated)
    return dp[state];

dp[state] = answer;
return dp[state];
```

This leads to **top-down Dynamic Programming**.

> Not every recursive problem is DP.
> DP generally involves overlapping subproblems + storing results.

---

# 16. Common Mistakes

### 1. No base case

```cpp
solve(n - 1);
```

without stopping.

**Fix:** define when the problem is finished.

---

### 2. Base case cannot be reached

```cpp
if (n == 0) return;
solve(n + 1);
```

Starting with positive `n` moves away from the base case.

**Fix:** check the direction of progress.

---

### 3. State does not change

```cpp
solve(n);
```

**Fix:**

```cpp
solve(n - 1);
```

or:

```cpp
solve(i + 1);
```

---

### 4. Wrong base-case value

For factorial:

```cpp
if (n == 0)
    return 0; // wrong
```

Correct:

```cpp
return 1;
```

The base case must return the correct answer for that state.

---

### 5. Forgetting `return`

Wrong:

```cpp
solve(n - 1);
```

when the function needs the result.

Correct:

```cpp
return n + solve(n - 1);
```

---

### 6. Too many unnecessary parameters

Don't create:

```cpp
solve(i, sum, count, x, y, z);
```

unless they are actually required.

Ask:

> **"What information is necessary to completely describe this subproblem?"**

---

### 7. Off-by-one errors

Be careful with:

```cpp
i == n
```

vs.

```cpp
i == n - 1
```

The correct condition depends on what `solve(i)` means.

---

### 8. Ignoring stack depth

Very deep recursion can cause stack overflow.

If recursion depth is extremely large, consider:

```text
iteration
DP
more efficient recurrence
explicit stack
```

---

# 17. Debugging Recursion

When recursion behaves unexpectedly, print the state:

```cpp
cout << "solve(" << n << ")" << endl;
```

Then check:

```text
1. What does the function mean?
2. Is the state changing?
3. Is it moving toward the base case?
4. Can the base case actually be reached?
5. Is the base-case answer correct?
6. Is the recursive result returned?
7. Is the result combined correctly?
8. Are the indices correct?
```

When confused, draw the call stack:

```text
solve(5)
 ↓
solve(4)
 ↓
solve(3)
 ↓
solve(2)
 ↓
solve(1)
 ↓
base case
 ↑
return
 ↑
return
```

---

# 18. The Recursion Design Template

Before writing code, fill this out:

```text
Function:
solve(...)

Meaning:
solve(...) means __________________.

State:
The changing information is __________________.

Base Case:
When __________________, I can answer directly.

Transition:
Move from __________________ to __________________.

Combination:
Use the smaller answer by __________________.
```

If these are clear, the implementation usually becomes much easier.

---

# 19. Golden Checklist

Before submitting a recursive solution:

```text
□ What does my function mean?
□ What is the state?
□ What is the smaller problem?
□ What is the base case?
□ Is the base-case answer correct?
□ Does every path reach the base case?
□ Does the state move toward the base case?
□ Do I need to return the recursive result?
□ How is the smaller answer combined?
□ Are my indices correct?
□ What is the time complexity?
□ What is the recursion-stack space?
```

---

# 20. Final Mental Model

Remember these **6 things**:

```text
1. Function Meaning
2. State
3. Smaller Problem
4. Base Case
5. Transition
6. Returned Result
```

The core formula is:

```text
CURRENT PROBLEM
      ↓
MAKE IT SMALLER
      ↓
RECURSIVE CALL
      ↓
BASE CASE
      ↓
RETURN
      ↓
COMBINE ANSWER
```

> **Recursion is not mainly about a function calling itself.**

> **It is about defining a problem using a smaller instance of the same problem.**
