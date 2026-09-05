# Backtracking

## 1. What is Backtracking?

**Backtracking** is a problem-solving technique used to explore a set of possible choices by:

1. Making a choice
2. Exploring the consequences of that choice
3. Undoing the choice
4. Trying another choice

The core idea is:

> **Choose → Explore → Undo → Try Next Choice**

Backtracking is usually implemented using **recursion**.

---

# 2. Recursion vs Backtracking

Backtracking is built on recursion, but **not every recursion is backtracking**.

### Normal recursion

```text
Solve smaller problem
        ↓
Recursive call
        ↓
Return answer
```

Example:

```cpp
int factorial(int n) {
    if (n == 0)
        return 1;

    return n * factorial(n - 1);
}
```

There is no choice exploration and no undo operation.

---

### Backtracking

```text
Make a choice
      ↓
Explore
      ↓
Undo the choice
      ↓
Make another choice
```

Example:

```cpp
choose 1
    ↓
explore
    ↓
remove 1

choose 2
    ↓
explore
    ↓
remove 2
```

So:

> **Backtracking = Recursion + Choices + Undoing choices**

---

# 3. Why Do We Need Backtracking?

Some problems ask us to find **all possible configurations** or search among many possible choices.

Examples:

* Generate all subsets
* Generate all permutations
* Generate combinations
* Combination Sum
* N-Queens
* Sudoku
* Rat in a Maze
* Word Search
* Partition a string into valid pieces
* Constraint satisfaction problems

For example, for:

```text
[1, 2, 3]
```

we may need to generate:

```text
[]
[1]
[2]
[3]
[1,2]
[1,3]
[2,3]
[1,2,3]
```

There are many possible choices.

Backtracking systematically explores them.

---

# 4. The Backtracking Decision Tree

Suppose we want to generate subsets of:

```text
[1, 2]
```

At each element we have two choices:

```text
              start
             /     \
         choose 1  skip 1
          /   \      /   \
       choose2 skip2 choose2 skip2
```

This forms a **decision tree**.

Each root-to-leaf path represents one possible solution.

This is one of the most important ways to visualize backtracking.

---

# 5. The Fundamental Backtracking Template

The general structure is:

```cpp
void backtrack(state) {

    if (solution_is_complete) {
        save_answer();
        return;
    }

    for (each available choice) {

        // Choose
        make_choice();

        // Explore
        backtrack(new_state);

        // Undo
        undo_choice();
    }
}
```

The three most important lines are:

```cpp
make_choice();

backtrack(...);

undo_choice();
```

The undo operation is what gives backtracking its name.

---

# 6. The Three Main Components

Every backtracking problem can usually be understood through:

## 1. State

What describes the current situation?

Examples:

```text
current index
current path
current board
used elements
current position
remaining target
visited cells
```

---

## 2. Choices

What can I do from the current state?

Examples:

```text
choose an element
choose a number
move up/down/left/right
place a queen in a column
choose a character
```

---

## 3. Goal

When have I constructed a complete valid solution?

Examples:

```text
index == n
path.size() == k
all queens placed
target == 0
reached destination
```

---

# 7. The Most Important Backtracking Question

Before coding, ask:

> **"At this state, what choices do I have?"**

Then:

> **"What happens if I make each choice?"**

Then:

> **"After exploring that choice, what must I undo?"**

These three questions often reveal the entire solution.

---

# 8. Choose → Explore → Undo

Consider:

```cpp
path.push_back(x);

backtrack(...);

path.pop_back();
```

These represent:

```text
Choose:
    add x

Explore:
    recursively solve the remaining problem

Undo:
    remove x
```

This pattern appears everywhere in backtracking.

---

# 9. Why Do We Need to Undo?

Suppose:

```text
path = [1]
```

We choose:

```text
2
```

Now:

```text
path = [1,2]
```

After exploring all solutions beginning with `[1,2]`, we want to try:

```text
[1,3]
```

So we must restore:

```text
path = [1]
```

before choosing `3`.

That is why:

```cpp
path.pop_back();
```

is necessary.

Without undoing:

```text
[1,2]
[1,2,3]
[1,2,3,4]
...
```

the state from one branch contaminates another branch.

---

# 10. State Restoration

Backtracking relies on an important principle:

> **After returning from a recursive call, restore the state to exactly what it was before the choice.**

If you modify:

```text
path
visited
board
used[]
frequency
remaining target
```

you must make sure the modification is correctly reversed when required.

---

# 11. Example — Generate All Subsets

Given:

```text
[1,2,3]
```

At each index:

```text
choose current element
OR
skip current element
```

A recursive state can be:

```cpp
backtrack(index, path)
```

Meaning:

> Generate all subsets using elements from `index` onward, given the current `path`.

---

### Decision

For element `nums[index]`:

```text
Choice 1:
Take it

Choice 2:
Don't take it
```

This produces a binary decision tree.

---

# 12. Two Common Ways to Implement Subsets

## Method 1 — Explicit Take / Don't Take

```cpp
void solve(int i) {

    if (i == n) {
        answer.push_back(path);
        return;
    }

    // Take
    path.push_back(nums[i]);
    solve(i + 1);
    path.pop_back();

    // Don't take
    solve(i + 1);
}
```

---

## Method 2 — Loop-Based Backtracking

```cpp
void solve(int start) {

    answer.push_back(path);

    for (int i = start; i < n; i++) {

        path.push_back(nums[i]);

        solve(i + 1);

        path.pop_back();
    }
}
```

Both are valid.

The second pattern is especially useful for:

* Combinations
* Subsets
* Combination Sum
* Choosing `k` elements

---

# 13. The Loop-Based Backtracking Template

One of the most important templates is:

```cpp
void backtrack(int start) {

    if (condition)
        return;

    for (int i = start; i < n; i++) {

        // Choose
        path.push_back(nums[i]);

        // Explore
        backtrack(i + 1);

        // Undo
        path.pop_back();
    }
}
```

The `start` parameter controls which choices are still available.

---

# 14. Why `start` Is Important

Suppose:

```text
[1,2,3]
```

After choosing `1`, we should consider:

```text
2,3
```

but we shouldn't go backward and choose `1` again.

Therefore:

```cpp
backtrack(i + 1);
```

moves the starting point forward.

This prevents:

```text
[1,1]
[2,2]
```

and prevents generating combinations in different orders.

---

# 15. Combination vs Permutation

This distinction is extremely important.

## Combination

Order does **not** matter.

```text
[1,2]
[2,1]
```

represent the same combination.

Therefore, after choosing `1`, we usually don't go backward.

Pattern:

```cpp
backtrack(i + 1);
```

---

## Permutation

Order **does** matter.

```text
[1,2]
[2,1]
```

are different permutations.

Therefore, we may need to choose any unused element at every level.

Pattern:

```cpp
for (int i = 0; i < n; i++) {
    if (!used[i]) {
        ...
    }
}
```

---

# 16. Permutation Template

```cpp
void backtrack() {

    if (path.size() == n) {
        answer.push_back(path);
        return;
    }

    for (int i = 0; i < n; i++) {

        if (used[i])
            continue;

        // Choose
        used[i] = true;
        path.push_back(nums[i]);

        // Explore
        backtrack();

        // Undo
        path.pop_back();
        used[i] = false;
    }
}
```

The important state is:

```text
path
used[]
```

---

# 17. Why `used[]` Is Needed

Suppose:

```text
nums = [1,2,3]
```

After:

```text
path = [1]
```

we cannot choose `1` again.

`used[]` records which elements are currently in the path.

Example:

```text
used = [true, false, false]
```

means:

```text
1 → already used
2 → available
3 → available
```

---

# 18. Backtracking State Can Be Explicit or Implicit

Sometimes the state is stored in variables:

```cpp
path
used[]
```

Sometimes it is represented by parameters:

```cpp
index
remaining
row
column
```

Sometimes both are used.

The goal is:

> **The state must contain enough information to describe the current partial solution.**

---

# 19. Base Case in Backtracking

In backtracking, the base case usually means:

> **A complete solution has been constructed.**

Examples:

### Permutations

```cpp
if (path.size() == n)
```

### Combination of size `k`

```cpp
if (path.size() == k)
```

### N-Queens

```cpp
if (row == n)
```

### Grid path

```cpp
if (r == destinationRow &&
    c == destinationColumn)
```

The base case depends on what "complete solution" means.

---

# 20. Base Case vs Pruning

These are different.

### Base case

The solution is complete.

```text
"We are done."
```

### Pruning

The current path cannot possibly produce a valid solution.

```text
"This branch is useless."
```

Example:

```cpp
if (sum > target)
    return;
```

This is pruning, not a successful base case.

---

# 21. Pruning

**Pruning** means stopping exploration of a branch as soon as we know that branch cannot produce a valid answer.

Without pruning:

```text
Explore everything
```

With pruning:

```text
Explore only promising branches
```

This can dramatically improve performance.

---

# 22. Example of Pruning

Suppose we need:

```text
sum = target
```

and current sum becomes:

```text
currentSum > target
```

If all remaining numbers are positive, the branch can never recover.

Therefore:

```cpp
if (currentSum > target)
    return;
```

We cut off that branch.

---

# 23. Important Condition for Safe Pruning

Never prune just because something "looks bad."

You need a logical guarantee that:

> **No valid solution can exist below this state.**

For example:

```cpp
if (sum > target)
    return;
```

is valid only when future choices cannot reduce `sum`.

If negative numbers are allowed, the sum could later decrease, so that pruning may be incorrect.

---

# 24. Pruning Is a Major Optimization

Suppose there are theoretically:

```text
2^n
```

possibilities.

A good pruning condition may prevent large portions of the search tree from being explored.

Conceptually:

```text
Without pruning:

             root
        /     |     \
      ...    ...    ...
     /  \    / \    / \
   huge search tree


With pruning:

             root
        /     |     \
      ...    ✕     ...
             ↑
          stopped
```

---

# 25. Backtracking Search Tree

The search tree represents:

```text
Level = number of decisions made
Node = current state
Edge = a choice
Leaf = complete solution or dead end
```

This is extremely useful for understanding complexity.

For example, in permutations:

```text
Level 0 → choose 1 of n
Level 1 → choose 1 of n-1
Level 2 → choose 1 of n-2
...
```

Number of leaves:

```text
n!
```

---

# 26. Backtracking Complexity

Backtracking often has exponential or factorial complexity.

Common search-space sizes:

```text
Subsets:
2^n

Permutations:
n!

Binary choices for n decisions:
2^n

Choosing k elements:
C(n,k)
```

But this is only the **number of possible solutions/states**.

Actual complexity also depends on:

* Work done at each state
* Copying answers
* Pruning
* Duplicate handling
* Validation cost

---

# 27. Why Backtracking Can Still Be the Correct Approach

Even if the complexity is exponential, it may be unavoidable.

If the problem asks:

> "Generate all subsets"

there are:

```text
2^n
```

subsets.

You cannot output `2^n` objects in less than roughly `O(2^n)` time because the output itself has that size.

This is called an **output-size lower bound**.

---

# 28. Backtracking for Optimization Problems

Backtracking isn't limited to generating all answers.

It can also find:

* Minimum
* Maximum
* Best configuration
* Feasible configuration

Example:

> Find the minimum number of elements needed to reach a target.

You can explore possible choices and maintain:

```cpp
best = min(best, currentAnswer);
```

Pruning can then eliminate branches that cannot beat the current best.

---

# 29. Feasibility vs Enumeration vs Optimization

Many backtracking problems fall into three categories.

### 1. Enumeration

Find **all** solutions.

Examples:

```text
All subsets
All permutations
All combinations
```

---

### 2. Feasibility

Find **whether at least one** solution exists.

Examples:

```text
Can N-Queens be solved?
Can the maze be solved?
Can the Sudoku be completed?
```

Once a valid answer is found, you may stop.

---

### 3. Optimization

Find the **best** solution.

Examples:

```text
Minimum cost
Maximum score
Shortest valid configuration
```

Backtracking explores candidates while maintaining the best result.

---

# 30. Duplicate Elements

Duplicates create a major problem in backtracking.

Suppose:

```text
[1,1,2]
```

If we blindly explore both `1`s, we may generate duplicate solutions.

For example:

```text
[1,2]
```

could be generated multiple times.

We need a strategy to avoid duplicate branches.

---

# 31. Sorting for Duplicate Handling

A common technique:

```cpp
sort(nums.begin(), nums.end());
```

Now equal values are adjacent.

Then:

```cpp
if (i > start && nums[i] == nums[i - 1])
    continue;
```

This means:

> At the same recursion level, don't start another branch with the same value.

This distinction is extremely important:

> **Duplicates are usually skipped at the same depth, not globally.**

---

# 32. Same Level vs Different Level

Consider:

```text
[1,1,2]
```

Choosing the first `1` and then choosing the second `1` can be valid:

```text
[1,1]
```

But choosing the second `1` as another starting choice at the same level creates a duplicate branch.

Therefore:

```cpp
if (i > start && nums[i] == nums[i - 1])
    continue;
```

checks the current recursion level.

---

# 33. Do Not Confuse Duplicate Values With Used Elements

These are different concepts.

### `used[]`

Used for permutation-style problems.

Question:

> Has this particular element already been placed in the current path?

### Duplicate skipping

Question:

> Have I already started an equivalent branch at this recursion depth?

They solve different problems.

---

# 34. Backtracking With Constraints

Many important backtracking problems are **constraint satisfaction problems**.

We construct a solution step by step while ensuring that every partial solution satisfies the constraints.

Examples:

* N-Queens
* Sudoku
* Graph coloring
* Crossword solving

General idea:

```text
Choose
 ↓
Check constraints
 ↓
Valid?
 ├── No → Undo / Skip
 └── Yes
       ↓
     Explore
       ↓
      Undo
```

---

# 35. N-Queens Example

Goal:

> Place `N` queens on an `N × N` chessboard so that no two queens attack each other.

We can process:

```text
one row at a time
```

At each row:

```text
Try every column
```

For each position:

```text
Is it safe?
```

If yes:

```text
place queen
solve next row
remove queen
```

This is classic backtracking.

---

# 36. Constraint Checking

For N-Queens, a placement is invalid if another queen exists in:

```text
same column
same diagonal
```

So before choosing:

```text
if position is unsafe
    skip
```

This is a form of **pruning**.

The earlier we reject an invalid branch, the less search we perform.

---

# 37. Constraint Representation

A constraint can be stored using:

```text
arrays
sets
hash sets
bitmasks
boolean matrices
```

For example:

```cpp
vector<bool> column(n);
vector<bool> diagonal1(2*n);
vector<bool> diagonal2(2*n);
```

Then checking whether a position is safe becomes very fast.

This is where backtracking and **bitmasking** can eventually be combined.

---

# 38. Grid Backtracking

Some problems involve moving through a grid.

Typical choices:

```text
up
down
left
right
```

A state might be:

```cpp
solve(row, col)
```

or:

```cpp
solve(row, col, ...)
```

Usually we need to prevent cycles:

```cpp
visited[row][col] = true;
```

Then:

```cpp
solve(nextRow, nextCol);
```

and afterward:

```cpp
visited[row][col] = false;
```

The final step is the backtracking undo.

---

# 39. Why `visited` Must Often Be Undone

Suppose a cell is visited on one path:

```text
A → B → C
```

When returning from that path, another path may legitimately need to use `C`.

Therefore:

```cpp
visited[r][c] = true;

solve(...);

visited[r][c] = false;
```

The second line restores the state.

---

# 40. Backtracking vs DFS

These concepts are related but not identical.

### DFS

Depth First Search describes a **traversal strategy**:

> Explore deeply before exploring siblings.

### Backtracking

Describes a **search technique**:

> Build a candidate solution, explore it, undo it, and try another choice.

Backtracking often uses DFS because the search naturally goes deep into one candidate before returning.

So:

> **Backtracking is commonly implemented using DFS-style recursion.**

But DFS itself is not necessarily backtracking.

---

# 41. Backtracking vs Brute Force

They are related but different.

### Brute force

Try every possibility, often without stopping early.

### Backtracking

Systematically explores possibilities while:

* Maintaining state
* Rejecting invalid partial solutions
* Undoing choices
* Pruning unnecessary branches

Therefore:

> **Backtracking is often a smarter form of brute-force search.**

---

# 42. Backtracking vs Dynamic Programming

Backtracking:

> Explore different choices.

DP:

> Reuse answers to repeated states.

A backtracking tree may contain:

```text
same state
    ↓
same state
```

multiple times.

If the future depends only on that state and the same state appears repeatedly, memoization/DP may help.

So a useful question is:

> **"Am I solving the same state repeatedly?"**

If yes, DP may be applicable.

---

# 43. Backtracking + Memoization

Some problems combine both.

General idea:

```text
Backtracking
     +
Repeated states
     ↓
Memoization
```

Instead of exploring the same state again, store its result.

However, don't add memoization automatically.

First understand the state and search tree.

---

# 44. State Compression

Sometimes the state contains a large amount of information.

Example:

```text
Which elements have been used?
```

Instead of:

```cpp
bool used[n];
```

we can represent the same information with a bitmask:

```text
00000
```

Each bit represents whether an element has been used.

For example:

```text
10110
```

could mean:

```text
element 0 → unused
element 1 → used
element 2 → used
element 3 → unused
element 4 → used
```

This can make the state compact and fast.

---

# 45. Common Backtracking Templates

## Template 1 — Choose / Undo

```cpp
void backtrack(state) {

    if (complete) {
        save();
        return;
    }

    for (choice : choices) {

        make(choice);

        backtrack(newState);

        undo(choice);
    }
}
```

---

## Template 2 — Start Index

```cpp
void backtrack(int start) {

    if (complete) {
        save();
        return;
    }

    for (int i = start; i < n; i++) {

        path.push_back(nums[i]);

        backtrack(i + 1);

        path.pop_back();
    }
}
```

Useful for:

```text
subsets
combinations
combination sum
```

---

## Template 3 — Used Array

```cpp
void backtrack() {

    if (complete) {
        save();
        return;
    }

    for (int i = 0; i < n; i++) {

        if (used[i])
            continue;

        used[i] = true;
        path.push_back(nums[i]);

        backtrack();

        path.pop_back();
        used[i] = false;
    }
}
```

Useful for:

```text
permutations
```

---

## Template 4 — Constraint Backtracking

```cpp
bool backtrack(state) {

    if (complete)
        return true;

    for (choice : choices) {

        if (!valid(choice))
            continue;

        make(choice);

        if (backtrack(newState))
            return true;

        undo(choice);
    }

    return false;
}
```

Useful when you only need to find **one valid solution**.

---

# 46. Returning `true` From Backtracking

When the problem asks for **any one solution**, you don't necessarily need to explore everything.

Example:

```cpp
if (backtrack(...))
    return true;
```

This means:

> A valid solution was found below this branch. Stop searching.

This can save enormous amounts of work.

---

# 47. Returning `false`

If every choice fails:

```cpp
return false;
```

This tells the previous level:

> "This path cannot produce a solution. Try another choice."

This creates a very clean recursive communication pattern.

---

# 48. Backtracking State Invariant

A powerful way to reason about backtracking:

> **At the beginning of every recursive call, the state must represent exactly the choices made on the path from the root to that call.**

For example:

```text
path = [1,3]
```

means:

> We chose `1`, then `3`.

When returning:

```cpp
path.pop_back();
```

restores:

```text
path = [1]
```

This invariant is extremely important.

---

# 49. The "One Level = One Decision" Rule

A useful mental model:

> **Each recursion level usually represents one decision.**

For permutations:

```text
Level 0 → choose first element
Level 1 → choose second element
Level 2 → choose third element
```

For N-Queens:

```text
Level 0 → choose column for row 0
Level 1 → choose column for row 1
Level 2 → choose column for row 2
```

For combinations:

```text
Level 0 → choose first element
Level 1 → choose second element
Level 2 → choose third element
```

This makes the recursion tree much easier to understand.

---

# 50. A Systematic Method to Solve Backtracking Problems

When you encounter a new problem:

### Step 1 — Ask whether choices exist

> "At the current state, are there multiple possible decisions?"

If yes, backtracking may be appropriate.

---

### Step 2 — Define the state

Ask:

> "What information completely describes my current partial solution?"

---

### Step 3 — Define the choices

Ask:

> "What can I choose next?"

---

### Step 4 — Define the goal

Ask:

> "When is my solution complete?"

---

### Step 5 — Define invalid states

Ask:

> "When can I prove this branch cannot work?"

These become pruning conditions.

---

### Step 6 — Make the choice

Update the state.

---

### Step 7 — Recurse

Explore the consequence.

---

### Step 8 — Undo

Restore the state.

---

### Step 9 — Analyze complexity

Estimate:

```text
number of states/branches
×
work per state
```

---

# 51. The Backtracking Checklist

Before coding:

```text
□ What is my state?

□ What does solve(...) mean?

□ What is one recursion level representing?

□ What choices are available?

□ What is my base case?

□ What makes a state invalid?

□ What can I prune?

□ What state do I modify?

□ How do I undo that modification?

□ Do I need start index?

□ Do I need used[]?

□ Do I need duplicate skipping?

□ Does order matter?

□ Am I finding all solutions or only one?

□ Could states repeat?

□ Would memoization help?

□ What is the search-space size?

□ What is the time complexity?

□ What is the recursion-stack space?
```

---

# 52. Common Backtracking Mistakes

## Mistake 1 — Forgetting the Undo

Wrong:

```cpp
path.push_back(x);
backtrack(...);
```

Correct:

```cpp
path.push_back(x);

backtrack(...);

path.pop_back();
```

Without undoing, later branches inherit the previous branch's state.

---

# 53. Mistake 2 — Undoing Too Early

Wrong:

```cpp
path.push_back(x);
path.pop_back();

backtrack(...);
```

The recursive call never sees the choice.

Correct:

```cpp
path.push_back(x);
backtrack(...);
path.pop_back();
```

The choice must remain active during exploration.

---

# 54. Mistake 3 — Incorrect Base Case

Don't simply use:

```cpp
if (i == n)
```

because it looks familiar.

Ask:

> "Does `i == n` actually mean a complete solution?"

The base case must match the meaning of the state.

---

# 55. Mistake 4 — Choosing the Wrong State

If the function doesn't know enough information to continue the search correctly, the state is incomplete.

Ask:

> "If I stopped here, could I completely describe the current partial solution?"

If not, add the missing information.

---

# 56. Mistake 5 — Using `start` When You Need `used[]`

If order matters:

```text
[1,2]
[2,1]
```

must both be generated.

Using only:

```cpp
start
```

usually prevents going backward.

For permutations, use:

```cpp
used[]
```

instead.

---

# 57. Mistake 6 — Using `used[]` When You Need `start`

For combinations, order usually doesn't matter.

Using a `used[]` array may generate:

```text
[1,2]
[2,1]
```

which are duplicates.

Use a forward `start` index when appropriate.

---

# 58. Mistake 7 — Forgetting Duplicate Handling

For input such as:

```text
[1,1,2]
```

you may generate duplicate answers.

Common solution:

```cpp
sort(nums.begin(), nums.end());

if (i > start && nums[i] == nums[i - 1])
    continue;
```

But remember:

> Duplicate skipping depends on the exact problem.

Don't blindly add this condition everywhere.

---

# 59. Mistake 8 — Incorrect Pruning

Wrong pruning can remove valid solutions.

Example:

```cpp
if (sum > target)
    return;
```

is not always valid.

It is safe only if future choices cannot decrease the sum.

Always ask:

> **"Can this branch ever become valid later?"**

If yes → don't prune.

---

# 60. Mistake 9 — Modifying Shared State Without Restoring It

For example:

```cpp
visited[r][c] = true;
```

must often be followed by:

```cpp
visited[r][c] = false;
```

after recursion.

The same principle applies to:

```text
path
used[]
board
frequency
counters
sets
maps
```

---

# 61. Mistake 10 — Copying the Entire State Unnecessarily

This:

```cpp
backtrack(vector<int> path)
```

may copy `path` at every recursive call.

Often it is more efficient to use:

```cpp
backtrack(vector<int>& path)
```

and explicitly undo:

```cpp
path.push_back(x);
backtrack(path);
path.pop_back();
```

This reduces unnecessary copying.

---

# 62. Mistake 11 — Forgetting That Answers Are Also Stored

If you do:

```cpp
answer.push_back(path);
```

the vector is copied.

But if you store references/pointers to a mutable `path`, all results may appear identical after backtracking.

Usually:

```cpp
answer.push_back(path);
```

is the safe approach.

---

# 63. Mistake 12 — Confusing "Current Path" With "Final Answer"

`path` usually represents:

> **The current partial solution.**

It does not necessarily represent a complete answer.

Only save it when:

```text
the problem's completion condition is satisfied
```

unless the problem specifically wants every prefix, such as subset generation.

---

# 64. Mistake 13 — Not Understanding Recursion Levels

If each level represents one decision, make sure you know:

```text
Level 0 → ?
Level 1 → ?
Level 2 → ?
```

If you cannot explain what each level means, the recursion design is probably unclear.

---

# 65. Mistake 14 — Exploring After Finding the Required Answer

If the problem asks:

> "Does a solution exist?"

and you already found one, continuing to search is unnecessary.

Use:

```cpp
return true;
```

and propagate it upward.

---

# 66. Mistake 15 — Ignoring the Output Size

If a problem asks for all:

```text
2^n subsets
```

you cannot expect polynomial time.

The output itself is exponential.

Always consider:

> **How many solutions must I actually produce?**

---

# 67. Backtracking Pattern Recognition

When reading a problem, these phrases are strong signals:

```text
"Generate all..."
"Find all possible..."
"Return all..."
"Choose..."
"Arrange..."
"Place..."
"Partition..."
"Select..."
"Try every..."
"Find a valid configuration..."
"Can we construct..."
"All possible combinations..."
"All permutations..."
```

These don't guarantee backtracking, but they should make you consider it.

---

# 68. A More Important Signal Than Keywords

Don't depend only on keywords.

Ask:

> **"Does the solution require making a sequence of choices where an earlier choice can be undone so that I can try another choice?"**

If yes, backtracking is a strong candidate.

---

# 69. The Universal Backtracking Mental Model

Whenever you see a backtracking problem, imagine:

```text
                    STATE
                      │
              What choices exist?
                 /    |    \
                /     |     \
            Choice 1 Choice 2 Choice 3
               │        │        │
             state     state     state
               │        │        │
            recurse   recurse   recurse
               │        │        │
             undo     undo      undo
```

Every branch represents a different decision.

---

# 70. Backtracking in One Sentence

Remember this:

> **Backtracking explores a decision tree by making a choice, recursively exploring it, undoing the choice, and then trying the next choice.**

---

# 71. The Five Things You Must Identify

For any new backtracking problem, identify:

```text
1. STATE
   What have I decided so far?

2. CHOICES
   What can I choose next?

3. CONSTRAINTS
   Which choices are invalid?

4. GOAL
   When is the solution complete?

5. UNDO
   What must I restore after exploring a choice?
```

If these five are clear, the implementation is usually straightforward.

---

# 72. Final Backtracking Formula

The most important pattern to remember:

```text
                Current State
                     │
              ┌──────┴──────┐
              │             │
           Choice A       Choice B
              │             │
           Explore       Explore
              │             │
            Undo          Undo
              │             │
           Choice B       Choice A
```

Or in code:

```cpp
for (each choice) {

    // 1. Choose
    make_choice();

    // 2. Explore
    backtrack();

    // 3. Undo
    undo_choice();
}
```

---

# 73. Final Learning Path

A good order for learning backtracking is:

```text
1. Recursion
      ↓
2. Decision Trees
      ↓
3. Choose → Explore → Undo
      ↓
4. Subsets
      ↓
5. Combinations
      ↓
6. Permutations
      ↓
7. Combination Sum
      ↓
8. Duplicate Handling
      ↓
9. Pruning
      ↓
10. Constraint Problems
      ↓
11. Grid Backtracking
      ↓
12. N-Queens
      ↓
13. Sudoku
      ↓
14. Backtracking + Bitmasking
      ↓
15. Backtracking + Memoization
```

The most important progression is:

```text
Recursion
   ↓
Choices
   ↓
Decision Tree
   ↓
State
   ↓
Choose
   ↓
Explore
   ↓
Undo
   ↓
Prune
```

Once this sequence becomes natural, most backtracking problems stop looking like completely different problems. They become variations of the same fundamental search pattern.
