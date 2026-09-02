# Recursion

## 1. What is Recursion?

**Recursion** is a technique where a function solves a problem by calling itself on a **smaller version of the same problem**.

The two essential parts are:

1. **Base Case** — tells the recursion when to stop.
2. **Recursive Case** — reduces the problem and calls the function again.

### General structure

```cpp
returnType solve(parameters) {

    // Base Case
    if (condition) {
        return answer;
    }

    // Recursive Case
    return solve(smaller_problem);
}
```

The most important idea is:

> **A recursive function should solve one smaller instance of the same problem.**

---

# 2. The Mental Model of Recursion

Don't think:

> "The function is calling itself, so I need to understand everything happening simultaneously."

Instead think:

> **"If I already knew how to solve the smaller problem, how would I use that answer to solve the current problem?"**

This is the key to designing recursion.

For example:

```cpp
int sum(int n) {
    if (n == 0)
        return 0;

    return n + sum(n - 1);
}
```

Mathematically:

```text
sum(n) = n + sum(n - 1)
```

The recursive call handles the smaller problem:

```text
sum(n - 1)
```

The current function only needs to combine that answer with `n`.

---

# 3. The Three Questions Behind Every Recursive Solution

Before writing code, ask:

### Question 1 — What does my function represent?

Write this in plain English.

For example:

```cpp
int sum(int n)
```

means:

> `sum(n)` returns the sum of numbers from `1` to `n`.

This is called the **meaning of the recursive function**.

---

### Question 2 — What is the smaller problem?

For:

```text
sum(5)
```

the smaller problem is:

```text
sum(4)
```

For:

```text
factorial(5)
```

the smaller problem is:

```text
factorial(4)
```

For a tree:

```text
height(root)
```

the smaller problems are:

```text
height(root->left)
height(root->right)
```

---

### Question 3 — When is the problem small enough to answer directly?

That is your **base case**.

For example:

```text
sum(0) = 0
factorial(0) = 1
height(NULL) = 0
```

---

# 4. How to Recognize a Recursion Problem

Don't memorize a list of "recursion problems."

Instead, look for these patterns.

## Pattern 1 — The problem naturally contains a smaller version of itself

Example:

```text
factorial(n)
```

contains:

```text
factorial(n - 1)
```

Therefore recursion fits naturally.

---

## Pattern 2 — A problem can be defined using its previous/smaller state

Examples:

```text
factorial(n) = n × factorial(n-1)

sum(n) = n + sum(n-1)

fib(n) = fib(n-1) + fib(n-2)
```

---

## Pattern 3 — Hierarchical structures

Recursion is especially natural when the input itself has recursive structure.

Examples:

* Trees
* Linked lists
* Nested structures
* File/directory structures
* Expressions

For a binary tree:

```text
Tree
├── Left subtree
└── Right subtree
```

Each subtree is itself a smaller tree.

Therefore:

```cpp
solve(left);
solve(right);
```

is naturally recursive.

---

## Pattern 4 — Divide and Conquer

A problem can sometimes be divided into smaller independent problems.

Examples:

* Merge Sort
* Quick Sort
* Binary Search
* Divide-and-conquer algorithms

General idea:

```text
Problem
   ↓
Smaller Problem(s)
   ↓
Solve recursively
   ↓
Combine answers
```

---

# 5. Recursion vs Iteration

Many recursive problems can also be solved using loops.

For example:

```cpp
int sum(int n) {
    int ans = 0;

    for (int i = 1; i <= n; i++)
        ans += i;

    return ans;
}
```

and:

```cpp
int sum(int n) {
    if (n == 0)
        return 0;

    return n + sum(n - 1);
}
```

Both work.

So recursion is **not automatically better**.

Use recursion when it makes the problem's structure simpler or more natural.

---

# 6. The Most Important Skill — Designing the Function State

The **state** is the information required to completely describe the current recursive subproblem.

Suppose:

```cpp
solve(i)
```

represents:

> Solve the problem starting from index `i`.

Then `i` is part of the state.

Other common state variables include:

```text
index
node
left/right boundary
remaining amount
current position
current value
number of elements remaining
```

### Example

```cpp
int sum(int i, int n)
```

Could mean:

> Return the sum of numbers from `i` through `n`.

Here:

```text
i = current position
n = boundary
```

---

# 7. How to Choose Function Parameters

This is one of the most important recursion skills.

Ask:

> **What information changes when I move from the current problem to the smaller problem?**

That changing information usually becomes a parameter.

Example:

```text
Process an array from left to right.
```

The important changing information is:

```text
current index
```

So:

```cpp
solve(index)
```

is often sufficient.

For binary search:

```text
current searchable range
```

changes.

Therefore:

```cpp
solve(left, right)
```

is natural.

For a tree:

```text
current node
```

changes.

Therefore:

```cpp
solve(node)
```

is natural.

---

# 8. The Function Contract

Before coding, write:

> **`solve(parameters)` means ________.**

This one sentence prevents many recursion mistakes.

Example:

```cpp
int height(Node* root)
```

Contract:

> `height(root)` returns the height of the tree rooted at `root`.

Once this is clear, everything else becomes easier.

---

# 9. Base Case

The base case is the condition where recursion **must stop**.

A good way to discover it is:

> **"When can I answer the problem immediately without making another recursive call?"**

Examples:

### Factorial

```cpp
if (n == 0)
    return 1;
```

Because:

```text
0! = 1
```

---

### Array traversal

```cpp
if (i == n)
    return;
```

Because there are no elements left.

---

### Linked list

```cpp
if (node == nullptr)
    return;
```

Because there are no nodes left.

---

### Binary tree

```cpp
if (root == nullptr)
    return 0;
```

Because an empty tree has height `0`.

---

# 10. Base Case Is Not Always One Condition

A recursive algorithm may have multiple stopping conditions.

Example:

```cpp
solve(node)
```

could have:

```cpp
if (node == nullptr)
    return;

if (node->value == target)
    return;
```

So don't memorize:

> "Every recursive function has one base case."

Instead remember:

> **A base case is any state where recursion should stop immediately.**

---

# 11. Recursive Transition

After identifying the state and base case, ask:

> **"How do I move from the current state toward the base case?"**

Example:

```cpp
int sum(int n) {
    if (n == 0)
        return 0;

    return n + sum(n - 1);
}
```

Transition:

```text
n → n - 1
```

The important requirement is:

> **Every recursive path must eventually reach a base case.**

---

# 12. Progress Toward the Base Case

This is one of the most important rules in recursion.

Bad:

```cpp
solve(n);
```

inside `solve(n)` without changing the state.

The state never changes.

Therefore:

```text
solve(n)
→ solve(n)
→ solve(n)
→ solve(n)
→ ...
```

This causes infinite recursion.

Correct:

```cpp
solve(n - 1);
```

because:

```text
n → n-1 → n-2 → ... → 0
```

---

# 13. Visualizing the Call Stack

Consider:

```cpp
int fact(int n) {
    if (n == 0)
        return 1;

    return n * fact(n - 1);
}
```

Calling:

```cpp
fact(4);
```

creates:

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

At `fact(0)`, recursion stops.

Then the functions return backward:

```text
fact(0) = 1
   ↑
fact(1) = 1 × 1 = 1
   ↑
fact(2) = 2 × 1 = 2
   ↑
fact(3) = 3 × 2 = 6
   ↑
fact(4) = 4 × 6 = 24
```

This is the **call stack**.

---

# 14. Going Down vs Coming Back Up

Recursion has two phases.

### Going down

Recursive calls are being created:

```text
solve(4)
solve(3)
solve(2)
solve(1)
solve(0)
```

### Coming back up

The calls return:

```text
solve(0)
solve(1)
solve(2)
solve(3)
solve(4)
```

This distinction is extremely useful.

Example:

```cpp
void print(int n) {
    if (n == 0)
        return;

    cout << n << " ";

    print(n - 1);
}
```

Output:

```text
5 4 3 2 1
```

The printing happens **before** the recursive call.

Now:

```cpp
void print(int n) {
    if (n == 0)
        return;

    print(n - 1);

    cout << n << " ";
}
```

Output:

```text
1 2 3 4 5
```

The printing happens while the stack is **unwinding**.

---

# 15. Work Before vs After Recursive Call

This is a fundamental recursion pattern.

### Before recursion

```cpp
doSomething();
solve(smaller);
```

The work happens while going down.

### After recursion

```cpp
solve(smaller);
doSomething();
```

The work happens while coming back up.

This is heavily used in:

* Tree traversals
* Linked-list problems
* Recursive mathematical algorithms

---

# 16. Types of Basic Recursion

## A. Linear Recursion

Each call makes one recursive call.

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

General structure:

```text
f(n)
 ↓
f(n-1)
 ↓
f(n-2)
 ↓
...
```

---

## B. Binary / Multiple Recursion

A function makes multiple recursive calls.

Example:

```cpp
solve(left);
solve(right);
```

Common in:

* Binary trees
* Fibonacci
* Divide and conquer

Structure:

```text
          f()
        /    \
      f()    f()
     /  \    /  \
   ...  ... ... ...
```

---

# 17. Recursion on Arrays

A common pattern:

```cpp
void solve(vector<int>& arr, int i) {

    if (i == arr.size())
        return;

    // process arr[i]

    solve(arr, i + 1);
}
```

Interpretation:

```text
solve(i)
=
process current element
+
solve(remaining elements)
```

The index `i` represents the current state.

---

# 18. Recursion on Linked Lists

For a linked list:

```cpp
void solve(Node* node) {

    if (node == nullptr)
        return;

    // process current node

    solve(node->next);
}
```

Why is recursion natural?

Because:

```text
current list
=
current node + remaining list
```

And:

```text
node->next
```

represents the smaller remaining problem.

---

# 19. Recursion on Trees

Trees are one of the most important places to master recursion.

Consider:

```text
        1
       / \
      2   3
     / \
    4   5
```

A tree is naturally defined as:

```text
Root
+
Left subtree
+
Right subtree
```

Therefore:

```cpp
void dfs(Node* root) {

    if (root == nullptr)
        return;

    dfs(root->left);
    dfs(root->right);
}
```

The base case:

```cpp
root == nullptr
```

means:

> There is no subtree to process.

---

# 20. Recursion + Return Values

Recursion doesn't always use `void`.

A recursive function can **return information from the smaller problem**.

Example:

```cpp
int sum(int n) {

    if (n == 0)
        return 0;

    return n + sum(n - 1);
}
```

Think:

```text
Current answer
=
current contribution
+
answer from smaller problem
```

This is one of the most powerful recursion patterns.

---

# 21. The "Return What the Smaller Problem Gives Me" Pattern

Suppose:

```cpp
int solve(state)
```

You can think:

```text
answer(current state)
=
current work
+
answer(smaller state)
```

Examples:

```text
sum
factorial
tree height
tree sum
maximum in array
minimum in array
```

This helps you avoid trying to calculate everything manually inside every recursive call.

---

# 22. Example — Maximum Element in an Array

Suppose:

```cpp
int maximum(vector<int>& a, int i)
```

means:

> Return the maximum element from index `i` onward.

Base case:

```cpp
if (i == a.size() - 1)
    return a[i];
```

Recursive idea:

```text
maximum from i onward
=
max(current element,
    maximum from i+1 onward)
```

Code:

```cpp
int maximum(vector<int>& a, int i) {

    if (i == a.size() - 1)
        return a[i];

    return max(a[i], maximum(a, i + 1));
}
```

Notice how the **function definition itself tells us the solution**.

---

# 23. Recursion Design Framework

For almost every basic recursion problem, follow this sequence:

```text
STEP 1
Understand the problem.

        ↓

STEP 2
Ask:
"Can I solve it using a smaller version of itself?"

        ↓

STEP 3
Define the function.

        ↓

STEP 4
Write:
"solve(...) means ______."

        ↓

STEP 5
Identify the changing state.

        ↓

STEP 6
Find the smallest/finished state.

        ↓

STEP 7
Write the base case.

        ↓

STEP 8
Move toward the base case.

        ↓

STEP 9
Use the smaller problem's answer.

        ↓

STEP 10
Check complexity and stack depth.
```

---

# 24. How to Derive a Base Case Systematically

When stuck, don't guess.

Use this process:

### Step 1

Ask:

> What does my parameter represent?

### Step 2

Ask:

> What is the smallest meaningful value of that parameter?

### Step 3

Ask:

> Can I directly answer the problem at that value?

If yes → base case.

---

### Example

Suppose:

```cpp
solve(i)
```

means:

> Process array elements from `i` onward.

Smallest state:

```text
i == n
```

because there are no elements left.

Therefore:

```cpp
if (i == n)
    return;
```

---

# 25. How to Derive the Recursive Call

Once the state is known, ask:

> **"What should change so that I get closer to the base case?"**

If:

```text
base case = i == n
```

then:

```text
i → i + 1
```

is natural.

So:

```cpp
solve(i + 1);
```

If:

```text
base case = n == 0
```

then:

```text
n → n - 1
```

So:

```cpp
solve(n - 1);
```

This gives you a powerful rule:

> **The recursive transition should make measurable progress toward the base case.**

---

# 26. Direct Recursion vs Indirect Recursion

### Direct recursion

A function calls itself:

```cpp
void f() {
    f();
}
```

### Indirect recursion

Functions call each other:

```cpp
void A() {
    B();
}

void B() {
    A();
}
```

Indirect recursion is less common in basic DSA, but the concept is useful to recognize.

---

# 27. Recursion Depth

Every recursive call occupies space on the call stack.

If:

```text
f(n)
→ f(n-1)
→ f(n-2)
→ ...
```

has `n` calls active at once, the recursion stack uses:

```text
O(n)
```

space.

This is called **recursion stack space**.

---

# 28. Time Complexity of Recursion

Never determine recursion complexity simply by saying:

> "It is recursion, so O(n)."

That is incorrect.

You need to count how many calls are made.

### One recursive call

```cpp
f(n) {
    ...
    f(n-1);
}
```

Usually:

```text
O(n)
```

if each call does O(1) work.

---

### Two recursive calls

```cpp
f(n) {
    f(n-1);
    f(n-1);
}
```

This can become exponential:

```text
O(2^n)
```

depending on the exact recurrence.

---

# 29. Recurrence Relation

Recursive algorithms can often be represented using a recurrence.

For:

```cpp
f(n) {
    constant work;
    f(n-1);
}
```

we get:

```text
T(n) = T(n-1) + O(1)
```

Therefore:

```text
T(n) = O(n)
```

For:

```cpp
f(n) {
    f(n/2);
}
```

we get:

```text
T(n) = T(n/2) + O(1)
```

Therefore:

```text
T(n) = O(log n)
```

For merge sort:

```text
T(n) = 2T(n/2) + O(n)
```

Therefore:

```text
T(n) = O(n log n)
```

---

# 30. Recursion Tree

For complicated recursive algorithms, visualize the calls as a tree.

Example:

```text
f(n)
├── f(n-1)
│   ├── f(n-2)
│   └── f(n-2)
└── f(n-1)
    ├── f(n-2)
    └── f(n-2)
```

This helps understand why some recursive algorithms become exponential.

---

# 31. Memoization and Recursion

Sometimes recursion repeatedly solves the same subproblem.

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

`fib(3)` and `fib(2)` are calculated repeatedly.

The recursive definition is fine, but repeated work is inefficient.

**Memoization** stores already-computed results.

General idea:

```cpp
if (dp[state] is already calculated)
    return dp[state];

dp[state] = answer;
return dp[state];
```

This leads naturally from recursion into **top-down Dynamic Programming**.

---

# 32. Recursion and Dynamic Programming

An important distinction:

### Recursion

Defines a problem using smaller problems.

### Dynamic Programming

Usually adds:

```text
Recursion / recurrence
+
Overlapping subproblems
+
Stored results
```

So:

> **Not every recursive problem is DP.**

Recursion is a general technique.

DP is a specific optimization/problem-solving pattern.

---

# 33. Recursion and Trees vs Graphs

Trees are naturally recursive because a subtree is itself a smaller tree.

Graphs can also be traversed recursively using DFS, but graphs introduce an additional issue:

```text
cycles
```

Therefore graph DFS generally needs a:

```cpp
visited[]
```

array/set to prevent repeatedly visiting the same nodes.

---

# 34. Recursion Invariant

A useful advanced concept is the **invariant**.

An invariant is something that remains true whenever the recursive function is called.

For example:

```cpp
solve(i)
```

may maintain:

> All elements before `i` have already been processed.

Thinking in terms of invariants makes complicated recursion easier to reason about.

---

# 35. A Reliable Debugging Method

When recursion isn't working, don't randomly change the code.

Print the state:

```cpp
cout << "solve(" << n << ")" << endl;
```

For example:

```text
solve(5)
solve(4)
solve(3)
solve(2)
solve(1)
solve(0)
```

Then inspect:

1. Is the state changing?
2. Is it moving toward the base case?
3. Is the base case actually reachable?
4. Is the returned value correct?
5. Is the result being combined correctly?

---

# 36. The Most Important Recursion Formula

For many problems, think:

```text
Current Problem
=
Current Work
+
Smaller Problem
```

For example:

### Sum

```text
sum(n)
=
n + sum(n-1)
```

### Factorial

```text
fact(n)
=
n × fact(n-1)
```

### Tree Height

```text
height(root)
=
1 + max(
    height(left),
    height(right)
)
```

### Array Maximum

```text
max(i)
=
max(
    a[i],
    max(i+1)
)
```

This is the core mental model.

---

# 37. Common Mistakes and Their Solutions

## Mistake 1 — No Base Case

### Wrong

```cpp
void solve(int n) {
    cout << n;
    solve(n - 1);
}
```

Eventually:

```text
0
-1
-2
-3
...
```

### Solution

Always define when the problem is finished.

```cpp
if (n == 0)
    return;
```

---

# Mistake 2 — Base Case Is Never Reached

### Wrong

```cpp
void solve(int n) {

    if (n == 0)
        return;

    solve(n + 1);
}
```

Starting from positive `n` moves away from `0`.

### Solution

Check the direction of progress:

```text
Current state → Base state
```

---

# Mistake 3 — Recursive Call Doesn't Change the State

### Wrong

```cpp
solve(n);
```

inside:

```cpp
solve(n)
```

### Solution

Make sure at least one relevant state variable progresses:

```cpp
solve(n - 1);
```

or:

```cpp
solve(i + 1);
```

---

# Mistake 4 — Wrong Base Case Value

Example:

```cpp
int factorial(int n) {

    if (n == 0)
        return 0;   // wrong

    return n * factorial(n - 1);
}
```

Correct:

```cpp
if (n == 0)
    return 1;
```

The base case must return the mathematically correct answer for that state.

---

# Mistake 5 — Confusing Base Case With Recursive Case

Don't think:

> "I reached `n == 1`, so I'll stop because recursion usually stops at 1."

Instead ask:

> "What is the smallest state for which my function has a direct answer?"

The correct base case depends on the **meaning of the function**.

---

# Mistake 6 — Changing Too Many Things in the State

Sometimes students create:

```cpp
solve(i, sum, count, x, y, z, ...)
```

without understanding why.

### Solution

Only keep information required to completely describe the current subproblem.

Ask:

> "If I paused the algorithm right now, what information would I need to continue?"

That information belongs in the state.

---

# Mistake 7 — Not Understanding What the Function Returns

Consider:

```cpp
return n + solve(n - 1);
```

Don't read this as:

> "The function is somehow magically calculating everything."

Read it as:

> "`solve(n-1)` gives me the answer for the smaller problem. I combine that answer with `n`."

---

# Mistake 8 — Forgetting `return`

### Wrong

```cpp
int solve(int n) {

    if (n == 0)
        return 0;

    solve(n - 1);
}
```

The recursive result is discarded.

### Correct

```cpp
return n + solve(n - 1);
```

if the function's answer depends on the recursive result.

---

# Mistake 9 — Using `return` When You Should Continue

For `void` traversal:

```cpp
void solve(Node* root) {

    if (!root)
        return;

    solve(root->left);
    solve(root->right);
}
```

You don't need to return a value.

But for a function such as:

```cpp
int height(Node* root)
```

you must return the recursive result.

Understand the **function contract** first.

---

# Mistake 10 — Processing the Wrong Element

For index-based recursion:

```cpp
solve(i)
```

make sure you know whether the current element is:

```cpp
a[i]
```

or:

```cpp
a[i - 1]
```

A simple index mistake can completely change the recursion.

---

# Mistake 11 — Off-by-One Errors

Common mistakes:

```cpp
i == n
```

vs

```cpp
i == n - 1
```

Choose the base case based on what `solve(i)` actually means.

If:

> `solve(i)` processes `[i, n)`

then:

```cpp
i == n
```

is usually the stopping condition.

---

# Mistake 12 — Not Tracking the Call Stack

When confused, draw:

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
```

Then draw the returns upward.

This immediately exposes many errors.

---

# Mistake 13 — Thinking Recursion Runs in a Straight Line

Students often mentally execute:

```cpp
solve(n - 1);
cout << n;
```

as if the `cout` happens immediately.

It doesn't.

The recursive call must finish first.

Think:

```text
solve(5)
 ↓
solve(4)
 ↓
solve(3)
 ↓
...
 ↓
base case
 ↑
return
 ↑
return
 ↑
return
```

---

# Mistake 14 — Assuming Every Recursion Is O(n)

Wrong.

Examples:

```text
One call → often O(n)

Two calls → may be O(2^n)

Divide by 2 → often O(log n)

2 × n/2 subproblems → often O(n log n)
```

Always analyze the recursion tree/recurrence.

---

# Mistake 15 — Ignoring Stack Overflow

Very deep recursion can exceed the call stack.

For example:

```cpp
solve(1000000000);
```

with one recursive call per decrement is not practical.

### Solution

Consider:

* Iteration
* A more efficient recurrence
* Tail-recursive/optimized approach where applicable
* Explicit stack
* DP/memoization

---

# Mistake 16 — Using Recursion Where Iteration Is Simpler

Recursion is a tool, not a requirement.

If:

```cpp
for (...)
```

is much clearer than recursion, iteration may be preferable.

The goal is not:

> "Use recursion everywhere."

The goal is:

> **Use the appropriate technique for the structure of the problem.**

---

# 38. The Golden Checklist

Before submitting a recursive solution, verify:

```text
□ What exactly does my function mean?

□ What is the recursive state?

□ What is the smaller problem?

□ What is the base case?

□ Is the base case correct?

□ Does every recursive path reach the base case?

□ Does the state move toward the base case?

□ Am I returning the recursive result when required?

□ Am I combining the smaller answer correctly?

□ Are my indices correct?

□ What is the time complexity?

□ What is the recursion-stack space complexity?
```

---

# 39. The Most Important Rule to Remember

When you are stuck on a recursion problem, **do not immediately start writing code.**

First write:

```text
Function:
solve(...)

Meaning:
solve(...) means __________________.

State:
The changing information is __________________.

Base case:
When __________________, I can answer directly.

Transition:
From the current state, move to __________________.

Combination:
Use the smaller answer by __________________.
```

If you can fill these five lines correctly, the implementation is usually straightforward.

---

# 40. Final Mental Model

Think of recursion as:

```text
                CURRENT PROBLEM
                      │
                      │
              "Can I make it
               smaller?"
                      │
                      ▼
              SMALLER PROBLEM
                      │
                      ▼
              SMALLER PROBLEM
                      │
                      ▼
                 BASE CASE
                      │
                      ▼
                 RETURN ANSWER
                      │
                      ▼
             COMBINE / PROCESS
                      │
                      ▼
               FINAL ANSWER
```

The deepest lesson is:

> **Recursion is not primarily about a function calling itself.**

It is about **defining a problem in terms of a smaller instance of the same problem**.

Once you can consistently identify:

```text
1. Function meaning
2. State
3. Smaller problem
4. Base case
5. Transition
6. Returned result
```

recursion becomes much less mysterious.
