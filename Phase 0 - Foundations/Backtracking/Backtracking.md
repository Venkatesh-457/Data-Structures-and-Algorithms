# Backtracking

## 1. What is Backtracking?

**Backtracking** is a technique for exploring possible choices by:

> **Choose → Explore → Undo → Try Next**

It is usually implemented using recursion.

Example:

```text
choose 1
   ↓
explore
   ↓
undo 1
   ↓
choose 2
   ↓
explore
```

### Backtracking =

```text
Recursion + Choices + Undo
```

Normal recursion does not necessarily have choices or undoing.

---

# 2. When Should You Think About Backtracking?

Backtracking is useful when a problem requires exploring different possible configurations.

Common examples:

```text
Subsets
Permutations
Combinations
Combination Sum
N-Queens
Sudoku
Maze / Grid paths
Word Search
String Partitioning
Constraint problems
```

Strong signal:

> **"I need to make a sequence of choices, and if a choice doesn't work, I need to undo it and try another."**

---

# 3. The Decision Tree

Backtracking can be visualized as a **decision tree**.

Example: subsets of `[1,2]`

```text
             start
            /     \
        take 1   skip 1
         /  \      /  \
     take2 skip2 take2 skip2
```

Think:

```text
Level → one decision
Edge  → one choice
Node  → current state
Leaf  → complete solution / dead end
```

Each root-to-leaf path represents one possible sequence of choices.

---

# 4. The 5 Things You Must Identify

Before coding, identify:

### 1. State

> What describes my current partial solution?

Examples:

```text
index
path
current position
used elements
remaining target
board
visited cells
```

### 2. Choices

> What can I choose next?

Examples:

```text
take / skip
choose an element
choose a number
move in a direction
place a queen
choose a character
```

### 3. Constraints

> Which choices are invalid?

Examples:

```text
sum > target
queen attacks another queen
cell already visited
duplicate branch
invalid Sudoku placement
```

### 4. Goal

> When is the solution complete?

Examples:

```text
index == n
path.size() == k
path.size() == n
row == n
reached destination
target == 0
```

### 5. Undo

> What did I change that must be restored?

Examples:

```text
path.pop_back()
used[i] = false
visited[r][c] = false
remove queen
restore board
```

---

# 5. The Fundamental Template

```cpp
void backtrack(state) {

    if (complete) {
        save_answer();
        return;
    }

    for (each choice) {

        // Choose
        make_choice();

        // Explore
        backtrack(new_state);

        // Undo
        undo_choice();
    }
}
```

The three most important operations are:

```cpp
make_choice();
backtrack(...);
undo_choice();
```

---

# 6. Why Undo Is Necessary

Suppose:

```text
path = [1]
```

Choose `2`:

```text
path = [1,2]
```

After exploring everything beginning with `[1,2]`, we need to try:

```text
[1,3]
```

So we must restore:

```text
path = [1]
```

Code:

```cpp
path.push_back(2);
backtrack(...);
path.pop_back();
```

Without undoing, the previous branch contaminates the next branch.

### Golden rule

> **After returning from recursion, restore the state to exactly what it was before the choice.**

---

# 7. The Most Important Backtracking Pattern

```cpp
path.push_back(x);   // Choose

backtrack(...);      // Explore

path.pop_back();     // Undo
```

This same idea applies to almost everything:

```text
path
used[]
visited[][]
board
frequency
counters
sets
remaining values
```

If you modify shared state, ask:

> **"How do I restore it?"**

---

# 8. Base Case in Backtracking

The base case usually means:

> **A complete solution has been constructed.**

Examples:

### Permutations

```cpp
if (path.size() == n)
```

### Choose `k` elements

```cpp
if (path.size() == k)
```

### N-Queens

```cpp
if (row == n)
```

### Grid

```cpp
if (r == targetRow && c == targetCol)
```

The condition depends on what **complete solution** means.

---

# 9. Base Case vs Pruning

These are different.

### Base Case

The solution is complete.

```text
"We are done."
```

### Pruning

The current branch cannot possibly produce a valid solution.

```text
"This branch is useless."
```

Example:

```cpp
if (currentSum > target)
    return;
```

This is pruning, not a successful solution.

---

# 10. Pruning

**Pruning** means stopping a branch early when you can prove that it cannot produce a valid answer.

Without pruning:

```text
Explore everything
```

With pruning:

```text
Explore only promising branches
```

Example:

```cpp
if (currentSum > target)
    return;
```

This is safe only when future choices **cannot decrease** the sum.

If negative numbers are allowed, this condition may incorrectly remove valid solutions.

### Golden rule

> **Only prune when you can prove that the branch cannot become valid.**

---

# 11. Subsets

For every element, there are two choices:

```text
Take it
OR
Skip it
```

Example:

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

    // Skip
    solve(i + 1);
}
```

This produces:

```text
2^n
```

possible subsets.

---

# 12. Loop-Based Backtracking

A very important pattern for combinations and similar problems:

```cpp
void backtrack(int start) {

    for (int i = start; i < n; i++) {

        path.push_back(nums[i]);

        backtrack(i + 1);

        path.pop_back();
    }
}
```

The important idea is:

```cpp
backtrack(i + 1);
```

After choosing `nums[i]`, only later elements are considered.

This prevents going backward and generating different orders of the same combination.

---

# 13. Combination vs Permutation

This distinction is **extremely important**.

## Combination

Order does **not** matter.

```text
[1,2] == [2,1]
```

Usually use:

```cpp
start
```

and recurse with:

```cpp
backtrack(i + 1);
```

Think:

> **Move forward. Don't reuse earlier positions.**

---

## Permutation

Order **does** matter.

```text
[1,2] != [2,1]
```

At every level, any unused element may be selected.

Typical pattern:

```cpp
for (int i = 0; i < n; i++) {

    if (used[i])
        continue;

    used[i] = true;
    path.push_back(nums[i]);

    backtrack();

    path.pop_back();
    used[i] = false;
}
```

Think:

> **At every level, choose any element that hasn't been used.**

---

# 14. `start` vs `used[]`

Remember this distinction:

| Technique | Main purpose                                                     |
| --------- | ---------------------------------------------------------------- |
| `start`   | Prevent going backward; useful for combinations                  |
| `used[]`  | Track which elements are currently used; useful for permutations |

### Combination

```cpp
backtrack(i + 1);
```

### Permutation

```cpp
if (!used[i]) {
    used[i] = true;
    ...
    used[i] = false;
}
```

Don't confuse the two.

---

# 15. Duplicate Handling

Suppose:

```text
[1,1,2]
```

Blindly exploring both `1`s can produce duplicate answers.

Common approach:

```cpp
sort(nums.begin(), nums.end());

if (i > start && nums[i] == nums[i - 1])
    continue;
```

The important idea:

> **Skip equal values at the same recursion level.**

Do **not** think of this as:

> "Never use the same value again."

Using the second `1` at a **deeper level** can be completely valid:

```text
[1,1]
```

So:

```text
Same level → may skip duplicate
Different level → may still use duplicate
```

---

# 16. `used[]` vs Duplicate Skipping

These solve different problems.

### `used[]`

Asks:

> **"Is this particular element already in my current path?"**

Used mainly for permutations.

### Duplicate skipping

Asks:

> **"Have I already explored an equivalent branch at this recursion level?"**

Used to avoid duplicate answers.

Don't mix these concepts.

---

# 17. Constraint Backtracking

Many difficult problems are **constraint satisfaction problems**.

General structure:

```text
Choose
  ↓
Check constraint
  ↓
Valid?
 ├── No → Skip
 └── Yes
      ↓
    Explore
      ↓
     Undo
```

Examples:

```text
N-Queens
Sudoku
Graph Coloring
Crossword
Maze
```

The earlier an invalid branch is rejected, the less search is required.

---

# 18. N-Queens Pattern

For each row:

```text
Try every column
      ↓
Is this position safe?
      ↓
Yes → place queen
      ↓
Solve next row
      ↓
Remove queen
```

Conceptually:

```cpp
for (each column) {

    if (!safe)
        continue;

    place_queen();

    backtrack(next_row);

    remove_queen();
}
```

The safety check is also a form of pruning.

---

# 19. Grid Backtracking

For grid problems, choices might be:

```text
up
down
left
right
```

A state could be:

```cpp
solve(row, col)
```

Often we need a `visited` structure.

```cpp
visited[r][c] = true;

solve(nextRow, nextCol);

visited[r][c] = false;
```

The final line restores the state so another path can use the cell.

---

# 20. Backtracking vs DFS

### DFS

Describes a traversal strategy:

> **Go deep before exploring siblings.**

### Backtracking

Describes a search technique:

> **Make a choice → explore → undo → try another choice.**

Backtracking is commonly implemented using DFS-style recursion, but:

> **DFS ≠ Backtracking**

A normal tree DFS doesn't necessarily involve making and undoing choices.

---

# 21. Backtracking vs Brute Force

### Brute Force

Try possibilities, often without intelligently stopping early.

### Backtracking

Systematically explores possibilities while:

```text
maintaining state
checking constraints
pruning
undoing choices
```

So:

> **Backtracking is often an organized and pruned form of brute-force search.**

---

# 22. Backtracking vs Dynamic Programming

Backtracking asks:

> **"What choices can I explore?"**

DP asks:

> **"Have I already solved this state?"**

If the same state is reached repeatedly:

```text
same state
   ↓
same future possibilities
```

memoization may help.

So ask:

> **"Am I solving the same state repeatedly?"**

If yes, investigate DP/memoization.

---

# 23. Backtracking + Bitmasking

Sometimes state such as:

```text
Which elements are used?
```

can be represented using a bitmask.

Example:

```text
10110
```

Each bit represents whether an element is selected/used.

This can replace:

```cpp
bool used[n];
```

with a compact integer state.

This becomes especially useful when combining:

```text
Backtracking + Bitmasking
```

---

# 24. Three Major Types of Backtracking Problems

## 1. Enumeration

Find **all** solutions.

Examples:

```text
All subsets
All permutations
All combinations
```

You generally must explore the entire relevant search space.

---

## 2. Feasibility

Find **whether at least one** solution exists.

Examples:

```text
Can Sudoku be solved?
Can the maze be solved?
Can N-Queens be solved?
```

Once a solution is found, stop.

```cpp
if (backtrack(...))
    return true;
```

---

## 3. Optimization

Find the **best** solution.

Examples:

```text
Minimum cost
Maximum score
Minimum number of choices
```

Maintain the best answer and prune branches that cannot beat it.

---

# 25. Returning `true` / `false`

For problems where you only need **one valid solution**:

```cpp
bool backtrack(state) {

    if (complete)
        return true;

    for (choice : choices) {

        if (!valid(choice))
            continue;

        make_choice();

        if (backtrack(new_state))
            return true;

        undo_choice();
    }

    return false;
}
```

Meaning:

```text
true  → a solution exists below this branch
false → this branch failed
```

This avoids unnecessary exploration.

---

# 26. Complexity

Backtracking is often exponential or factorial.

Common search-space sizes:

```text
Subsets:
2^n

Binary choices:
2^n

Permutations:
n!

Choose k:
C(n,k)
```

But actual complexity also depends on:

```text
work per state
copying answers
duplicate handling
constraint checks
pruning
```

A useful estimate is:

```text
Number of states/branches
×
Work per state
```

---

# 27. Output Size Matters

If the problem asks for all subsets, there are:

```text
2^n
```

subsets.

Therefore, simply producing the output already requires exponential work.

So don't automatically reject a backtracking solution because it is exponential.

> **If the output itself is exponential, exponential time may be unavoidable.**

---

# 28. State Invariant

A very useful way to reason about backtracking:

> **At every recursive call, the current state must exactly represent the choices made on the path to that call.**

Example:

```text
path = [1,3]
```

means:

```text
We chose 1, then 3.
```

After returning:

```cpp
path.pop_back();
```

we restore:

```text
path = [1]
```

This is why correct undo operations are so important.

---

# 29. One Level = One Decision

A useful mental model:

> **Each recursion level usually represents one decision.**

### Permutations

```text
Level 0 → choose first element
Level 1 → choose second element
Level 2 → choose third element
```

### N-Queens

```text
Level 0 → choose column for row 0
Level 1 → choose column for row 1
Level 2 → choose column for row 2
```

### Combinations

```text
Level 0 → choose first element
Level 1 → choose second element
Level 2 → choose third element
```

If you understand what each level represents, the recursion becomes much easier to design.

---

# 30. How to Solve a New Backtracking Problem

Use this process:

### Step 1 — Identify the decisions

> What choices can I make at this state?

### Step 2 — Define the state

> What information describes my current partial solution?

### Step 3 — Define the goal

> When is the solution complete?

### Step 4 — Define constraints

> Which choices are invalid?

### Step 5 — Identify pruning

> When can I prove this branch can never work?

### Step 6 — Choose

Modify the state.

### Step 7 — Explore

Call recursion.

### Step 8 — Undo

Restore the state.

### Step 9 — Analyze

```text
search-space size
×
work per state
```

---

# 31. Universal Template

When you are stuck, start from this:

```cpp
void backtrack(state) {

    if (complete) {
        save_answer();
        return;
    }

    for (each choice) {

        if (!valid(choice))
            continue;

        make_choice();

        backtrack(new_state);

        undo_choice();
    }
}
```

Then determine:

```text
state
choices
complete
valid
make_choice
undo_choice
```

These six pieces usually define the entire solution.

---

# 32. Common Mistakes

### 1. Forgetting Undo

```cpp
path.push_back(x);
backtrack(...);
```

Missing:

```cpp
path.pop_back();
```

---

### 2. Undoing Too Early

Wrong:

```cpp
path.push_back(x);
path.pop_back();
backtrack(...);
```

The recursive call never sees `x`.

Correct:

```cpp
path.push_back(x);
backtrack(...);
path.pop_back();
```

---

### 3. Wrong Base Case

Don't blindly use:

```cpp
if (i == n)
```

Ask:

> **Does this state actually represent a complete solution?**

---

### 4. State Is Incomplete

If the function doesn't have enough information to describe the current situation, the recursion cannot make correct decisions.

Ask:

> **"Can I completely describe the current partial solution from my state?"**

---

### 5. Wrong `start` / `used[]`

Remember:

```text
Combination → start
Permutation  → used[]
```

when those patterns fit the problem.

---

### 6. Incorrect Duplicate Handling

Don't blindly add:

```cpp
if (i > start && nums[i] == nums[i - 1])
```

to every problem.

Duplicate handling depends on the exact problem.

---

### 7. Incorrect Pruning

Never prune simply because a branch "looks bad."

Ask:

> **"Can this branch still become a valid solution?"**

If yes, don't prune.

---

### 8. Shared State Not Restored

If you modify:

```text
path
visited
used
board
frequency
```

make sure it is restored when necessary.

---

### 9. Unnecessary State Copying

Passing a large state by value can repeatedly copy it.

Often use a reference:

```cpp
backtrack(vector<int>& path)
```

and manually undo changes.

---

### 10. Continuing After Finding a Solution

If only one solution is required:

```cpp
if (backtrack(...))
    return true;
```

Stop as soon as the answer is found.

---

# 33. Quick Pattern Recognition

| Problem type       | Typical pattern                            |
| ------------------ | ------------------------------------------ |
| Subsets            | Take / Skip                                |
| Combinations       | `start` + loop                             |
| Permutations       | `used[]` + loop                            |
| Combination Sum    | `start` + target + pruning                 |
| N-Queens           | Row + column choices + constraints         |
| Sudoku             | Empty cell + possible values + constraints |
| Grid path          | Position + visited + directions            |
| Find one solution  | Return `true/false`                        |
| Find all solutions | Store every valid solution                 |

---

# 34. Final Mental Model

Whenever you see a backtracking problem, think:

```text
                 CURRENT STATE
                       ↓
                 What can I choose?
                  /     |     \
                 /      |      \
             Choice A Choice B Choice C
                ↓        ↓        ↓
             Explore  Explore  Explore
                ↓        ↓        ↓
               Undo     Undo     Undo
```

And remember:

```text
State
 ↓
Choices
 ↓
Constraints
 ↓
Choose
 ↓
Explore
 ↓
Undo
 ↓
Next Choice
```

---

# 35. The 7 Things to Remember

If you remember nothing else, remember these:

```text
1. Backtracking = Choose → Explore → Undo

2. Each recursion level usually represents one decision.

3. State describes the current partial solution.

4. Base case means the solution is complete.

5. Pruning means the branch cannot possibly work.

6. Combination → usually move forward with start.
   Permutation → usually track used elements.

7. Every modification to shared state must be correctly undone.
```

### One-sentence definition

> **Backtracking systematically explores a decision tree by making a choice, recursively exploring it, undoing that choice, and trying the next choice.**
