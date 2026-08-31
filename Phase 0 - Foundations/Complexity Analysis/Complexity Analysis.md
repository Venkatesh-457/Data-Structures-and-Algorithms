# Complexity Analysis (Big-O)

Complexity analysis is used to measure how an algorithm's **time and memory requirements grow as the input size `n` increases**.

It helps us compare algorithms without depending on the machine, programming language, or exact execution time.

---

## 1. What is `n`?

`n` usually represents the **size of the input**.

Examples:

* Array of `n` elements → `n = array.size()`
* String of length `n` → `n = string.length()`
* Graph with `V` vertices and `E` edges → complexity is usually expressed using `V` and `E`

The goal is not to calculate the exact number of operations.

We care about **how the number of operations grows with `n`**.

---

# 2. Big-O Notation

Big-O describes the **upper-bound growth rate** of an algorithm.

For example:

```text
3n² + 5n + 10
```

is:

```text
O(n²)
```

because for large `n`, the `n²` term dominates.

### Rules

Ignore:

* Constants
* Lower-order terms
* Machine-dependent details

Examples:

```text
O(5n)       → O(n)

O(n + 100)  → O(n)

O(3n² + n)  → O(n²)

O(n³ + n²)  → O(n³)
```

---

# 3. Common Time Complexities

From generally better to worse:

```text
O(1)
O(log n)
O(n)
O(n log n)
O(n²)
O(n³)
O(2ⁿ)
O(n!)
```

For large inputs, the difference between these complexities can be enormous.

---

# 4. O(1) — Constant

The number of operations does not depend on `n`.

```cpp
int x = arr[0];
```

Whether the array has 10 or 10,000,000 elements, this takes approximately the same amount of work.

```text
Time: O(1)
Space: O(1)
```

---

# 5. O(log n) — Logarithmic

This is one of the **most important complexities to understand**.

The key idea:

> **The problem size is repeatedly divided by a constant factor.**

For example:

```text
n
n/2
n/4
n/8
n/16
...
1
```

How many times can we divide `n` by `2` before reaching `1`?

```text
n / 2^k = 1

n = 2^k

k = log₂(n)
```

Therefore:

```text
O(log n)
```

### Example: Binary Search

Every step eliminates approximately half of the search space.

```text
1,000,000
500,000
250,000
125,000
...
1
```

Only about `log₂(n)` steps are required.

For `n = 1,000,000`:

```text
log₂(1,000,000) ≈ 20
```

That's why binary search is extremely efficient.

### Recognition rule

If you see:

```cpp
while (n > 1)
    n /= 2;
```

think:

```text
O(log n)
```

More generally:

```cpp
n /= k
```

repeatedly → `O(logₖ n)`

In Big-O, the base is usually omitted:

```text
O(log n)
```

because different constant bases differ only by a constant factor.

---

# 6. O(n) — Linear

The work grows directly with `n`.

```cpp
for (int i = 0; i < n; i++) {
    // work
}
```

If `n` doubles, the work approximately doubles.

```text
Time: O(n)
```

Example:

```text
n = 10       → ~10 operations
n = 100      → ~100 operations
n = 1,000    → ~1,000 operations
```

---

# 7. O(n log n)

Usually appears when:

* We process `n` elements
* And each element/level involves `log n` work

Examples:

* Merge Sort
* Heap Sort
* Many efficient sorting algorithms

Conceptually:

```text
n × log n
```

So:

```text
O(n log n)
```

This is generally considered very efficient for sorting.

---

# 8. O(n²) — Quadratic

Usually appears with **nested loops** over the same `n` elements.

```cpp
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        // work
    }
}
```

Total:

```text
n × n = n²
```

Therefore:

```text
O(n²)
```

Common examples:

* Bubble Sort
* Selection Sort
* Comparing every pair of elements

---

# 9. O(n³) — Cubic

Three nested loops:

```cpp
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++)
        for (int k = 0; k < n; k++)
            // work
```

Total:

```text
n × n × n = n³
```

Therefore:

```text
O(n³)
```

Usually becomes impractical much sooner than `O(n²)`.

---

# 10. O(2ⁿ) — Exponential

This is another **extremely important complexity**.

The key idea:

> **The amount of work multiplies as the input grows.**

A common source is a recursive algorithm that makes **two recursive calls** for every state.

Example:

```cpp
solve(n) {
    solve(n - 1);
    solve(n - 1);
}
```

The recursion tree approximately looks like:

```text
                 n
              /     \
           n-1       n-1
          /  \       /  \
       n-2  n-2   n-2  n-2
       ...
```

Number of nodes approximately:

```text
2ⁿ
```

Therefore:

```text
O(2ⁿ)
```

### Growth

```text
n = 10  → 1,024
n = 20  → 1,048,576
n = 30  → 1,073,741,824
```

A small increase in `n` can cause a huge increase in work.

### Recognition rule

If each recursive call creates approximately **2 independent branches**:

```text
T(n) ≈ 2T(n-1)
```

think:

```text
O(2ⁿ)
```

This commonly appears in:

* Naive Fibonacci
* Subset generation
* Some backtracking problems
* Exhaustive binary decision trees

---

# 11. O(kⁿ) — General Exponential Complexity

`2ⁿ` is only one type of exponential complexity.

If every state creates `k` branches:

```text
T(n) ≈ kT(n-1)
```

then:

```text
O(kⁿ)
```

Examples:

```text
2 branches → O(2ⁿ)

3 branches → O(3ⁿ)

4 branches → O(4ⁿ)
```

The important idea is:

> **The input is in the exponent.**

That is what makes exponential algorithms grow so rapidly.

---

# 12. O(n!) — Factorial

Factorial complexity grows even faster than exponential complexity.

It commonly appears when generating **all permutations**.

For `n` elements:

```text
First position  → n choices
Second          → n-1 choices
Third           → n-2 choices
...
Last            → 1 choice
```

Total:

```text
n × (n-1) × (n-2) × ... × 1

= n!
```

Therefore:

```text
O(n!)
```

Example:

```text
5!  = 120
10! = 3,628,800
15! = 1,307,674,368,000
```

This becomes huge extremely quickly.

---

# 13. Logarithmic vs Exponential

This distinction is **extremely important**.

### Logarithmic

```text
O(log n)
```

Input increases → work increases **very slowly**.

Typical pattern:

```text
n → n/2 → n/4 → n/8 → ...
```

Think:

> **Repeatedly divide.**

Examples:

* Binary Search
* Height of a balanced binary tree
* Operations on some balanced data structures

---

### Exponential

```text
O(2ⁿ)
```

Input increases → work increases **extremely rapidly**.

Typical pattern:

```text
1 → 2 → 4 → 8 → 16 → 32 → ...
```

Think:

> **Repeatedly multiply/branch.**

Examples:

* Subset generation
* Naive recursive Fibonacci
* Many brute-force/backtracking solutions

---

# 14. The Most Important Mental Trick

### Division → Logarithm

If the input repeatedly becomes:

```text
n/2
n/4
n/8
n/16
```

think:

```text
O(log n)
```

### Multiplication / Branching → Exponential

If the number of possibilities repeatedly becomes:

```text
1
2
4
8
16
32
...
```

think:

```text
O(2ⁿ)
```

### Repeated choices

If there are:

```text
k choices at each of n levels
```

think:

```text
O(kⁿ)
```

---

# 15. Multiple Loops

### Sequential loops

```cpp
for (...)        // O(n)
for (...)        // O(n)
```

Total:

```text
O(n + n)
= O(2n)
= O(n)
```

Sequential complexities are **added**, then simplified.

---

### Nested loops

```cpp
for (...)
    for (...)
```

Total:

```text
O(n × n)
= O(n²)
```

Nested complexities are generally **multiplied**.

---

# 16. Different Input Sizes

Be careful when there are multiple independent inputs.

```cpp
for (int i = 0; i < n; i++)
    ...

for (int j = 0; j < m; j++)
    ...
```

Complexity:

```text
O(n + m)
```

Nested:

```cpp
for (int i = 0; i < n; i++)
    for (int j = 0; j < m; j++)
        ...
```

Complexity:

```text
O(n × m)
```

Do not automatically replace everything with `n`.

---

# 17. Space Complexity

Space complexity measures **additional memory used by the algorithm** as input size grows.

Example:

```cpp
vector<int> temp(n);
```

requires memory proportional to `n`.

```text
Space: O(n)
```

A few variables:

```cpp
int sum = 0;
int i = 0;
```

use constant extra space:

```text
Space: O(1)
```

---

# 18. Recursion and Space

Recursive calls consume the **call stack**.

Example:

```cpp
void solve(int n) {
    if (n == 0) return;
    solve(n - 1);
}
```

There are `n` active calls at maximum.

Therefore:

```text
Time:  O(n)
Space: O(n)
```

Even if the algorithm does not create an explicit array, recursion can still use `O(n)` stack space.

---

# 19. Auxiliary Space

When discussing algorithm space complexity, we often care about **extra space beyond the input itself**.

For example:

```cpp
void solve(vector<int>& arr) {
    int sum = 0;
}
```

The array already exists and is passed by reference.

Additional space:

```text
O(1)
```

But:

```cpp
vector<int> temp(arr.size());
```

requires:

```text
O(n)
```

additional space.

---

# 20. Best, Average and Worst Case

An algorithm can have different complexities depending on the input.

### Best Case

Minimum amount of work.

### Average Case

Expected/typical amount of work.

### Worst Case

Maximum amount of work.

For many DSA problems, **worst-case complexity** is the most important measure.

---

# 21. Complexity Comparison

For large `n`:

```text
O(1)        → Excellent
O(log n)    → Excellent
O(n)        → Good
O(n log n)  → Good
O(n²)       → Can be acceptable for smaller n
O(n³)       → Usually expensive
O(2ⁿ)       → Very expensive
O(n!)       → Extremely expensive
```

This is not an absolute rule.

The actual acceptable complexity depends heavily on the **constraints** of the problem.

---

# 22. Quick Recognition Table

| Pattern                         | Complexity   |
| ------------------------------- | ------------ |
| Constant number of operations   | `O(1)`       |
| `n / 2` repeatedly              | `O(log n)`   |
| Single loop                     | `O(n)`       |
| Divide and process all elements | `O(n log n)` |
| Two nested `n` loops            | `O(n²)`      |
| Three nested `n` loops          | `O(n³)`      |
| 2 recursive branches per level  | `O(2ⁿ)`      |
| `k` branches per level          | `O(kⁿ)`      |
| Generate all permutations       | `O(n!)`      |

---

# 23. Quick Revision

Remember these patterns:

```text
Constant work
        ↓
      O(1)

Repeated division
        ↓
    O(log n)

One complete pass
        ↓
      O(n)

Divide + process everything
        ↓
    O(n log n)

Two nested loops
        ↓
     O(n²)

Three nested loops
        ↓
     O(n³)

Two recursive branches
        ↓
     O(2ⁿ)

k recursive branches
        ↓
     O(kⁿ)

All permutations
        ↓
      O(n!)
```

### Golden Rule

> **Don't memorize complexity only by the code's appearance. Understand how the number of operations changes as `n` changes.**

The two patterns to remember most strongly:

```text
Repeatedly DIVIDE → LOGARITHMIC → O(log n)

Repeatedly BRANCH/MULTIPLY → EXPONENTIAL → O(kⁿ)
```

That mental model will help you derive complexity instead of simply memorizing it.

# 24. Constraints → Time → Suitable Complexity

In competitive programming, **constraints are one of the biggest clues about the intended complexity**.

A rough C++ competitive-programming estimate is:

```text
10⁶ simple operations  ≈ 0.01 second
10⁷ simple operations  ≈ 0.1 second
10⁸ simple operations  ≈ 1 second
10⁹ simple operations  ≈ 10 seconds
```

These are only rough estimates. Real execution time depends on the operation, compiler, CPU, memory access, STL usage, and judge environment.

> **Important:** Big-O tells us how an algorithm grows. The actual number of operations and constants determine the practical running time.

---

## Quick Complexity Guide

| Approximate Operations |   Rough Time | Practical Meaning  |
| ---------------------: | -----------: | ------------------ |
|                  `10³` | `~0.00001 s` | Extremely fast     |
|                  `10⁴` |  `~0.0001 s` | Extremely fast     |
|                  `10⁵` |   `~0.001 s` | Very fast          |
|                  `10⁶` |    `~0.01 s` | Very fast          |
|                  `10⁷` |     `~0.1 s` | Fast               |
|                  `10⁸` |       `~1 s` | Usually reasonable |
|                  `10⁹` |      `~10 s` | Usually too slow   |
|                 `10¹⁰` |     `~100 s` | Impractical        |

---

# 25. Constraint → Recommended Complexity

These are **rough guidelines**, assuming a typical contest time limit around 1–2 seconds.

|               `n` | Usually Target                                 |
| ----------------: | ---------------------------------------------- |
|          `n ≤ 10` | `O(n!)`, `O(2ⁿ)` may be possible               |
|          `n ≤ 20` | `O(2ⁿ)`                                        |
|          `n ≤ 25` | `O(2ⁿ)` with optimization / pruning            |
|         `n ≤ 100` | `O(n³)` may be possible                        |
|         `n ≤ 500` | `O(n³)` may be borderline; `O(n²)` preferred   |
|       `n ≤ 1,000` | `O(n²)`                                        |
|       `n ≤ 5,000` | `O(n²)` may be possible with simple operations |
|      `n ≤ 10,000` | Usually `O(n log n)` or better                 |
|     `n ≤ 100,000` | `O(n log n)` or `O(n)`                         |
|   `n ≤ 1,000,000` | Usually `O(n)` or `O(n log n)`                 |
|  `n ≤ 10,000,000` | Usually `O(n)`                                 |
| `n ≤ 100,000,000` | Usually `O(n)` with very small constants       |
|         `n ≥ 10⁹` | Usually `O(log n)` or `O(1)`                   |

These are **not strict rules**. Always consider the actual operation count and time limit.

---

# 26. Understanding Why Constraints Matter

Suppose:

```text
n = 100,000
```

An `O(n²)` solution requires approximately:

```text
n² = 100,000²
   = 10,000,000,000
   = 10¹⁰ operations
```

That could take roughly:

```text
~100 seconds
```

under the rough `10⁸ operations/second` model.

So `O(n²)` is generally **not acceptable** for `n = 100,000`.

Instead, look for:

```text
O(n)
O(n log n)
O(log n)
```

---

# 27. Example: `n = 1,000,000`

Compare different complexities:

### O(n)

```text
10⁶ operations
≈ 0.01 s
```

Very good.

### O(n log n)

```text
10⁶ × log₂(10⁶)
≈ 10⁶ × 20
≈ 2 × 10⁷ operations
≈ 0.2 s
```

Usually very good.

### O(n²)

```text
(10⁶)²
= 10¹² operations
≈ 10,000 seconds
```

Clearly impossible under normal contest limits.

This is why constraints immediately eliminate certain approaches.

---

# 28. Exponential Complexity and Constraints

Exponential algorithms become dangerous very quickly.

For `O(2ⁿ)`:

|  `n` |                `2ⁿ` |
| ---: | ------------------: |
| `10` |             `1,024` |
| `15` |            `32,768` |
| `20` |         `1,048,576` |
| `25` |        `33,554,432` |
| `30` |     `1,073,741,824` |
| `40` | `1,099,511,627,776` |

Therefore:

```text
n ≤ 20
→ O(2ⁿ) is commonly feasible

n ≈ 25
→ O(2ⁿ) can be possible with optimization

n ≈ 30
→ O(2ⁿ) is usually too expensive

n ≥ 40
→ O(2ⁿ) is generally impractical
```

However, **backtracking is different** because pruning can eliminate huge portions of the theoretical search tree.

---

# 29. Factorial Complexity

For `O(n!)`:

|  `n` |                `n!` |
| ---: | ------------------: |
|  `5` |               `120` |
|  `8` |            `40,320` |
| `10` |         `3,628,800` |
| `12` |       `479,001,600` |
| `15` | `1,307,674,368,000` |

Therefore:

```text
n ≤ 10
→ O(n!) may be feasible

n ≈ 12
→ potentially expensive

n ≥ 15
→ generally impractical
```

This is why problems asking you to generate **all permutations** usually have very small constraints.

---

# 30. Cubic Complexity

For `O(n³)`:

```text
n = 100
→ 10⁶ operations
→ ~0.01 s

n = 500
→ 125 × 10⁶ operations
→ ~1.25 s

n = 1,000
→ 10⁹ operations
→ ~10 s
```

Therefore:

```text
n ≈ 100
→ O(n³) is comfortable

n ≈ 500
→ O(n³) may be possible depending on constants/time limit

n ≈ 1,000
→ O(n³) is usually too slow
```

This explains why algorithms such as Floyd-Warshall, which are `O(V³)`, are generally used only when `V` is relatively small.

---

# 31. Quadratic Complexity

For `O(n²)`:

```text
n = 100
→ 10⁴ operations
→ ~0.0001 s

n = 1,000
→ 10⁶ operations
→ ~0.01 s

n = 10,000
→ 10⁸ operations
→ ~1 s

n = 100,000
→ 10¹⁰ operations
→ ~100 s
```

Therefore:

```text
n ≤ 1,000
→ O(n²) is usually comfortable

n ≈ 10,000
→ O(n²) may be borderline

n ≥ 100,000
→ O(n²) is generally too slow
```

---

# 32. Logarithmic Complexity

`O(log n)` is extremely powerful.

For binary search:

```text
n = 10⁶
→ log₂(n) ≈ 20

n = 10⁹
→ log₂(n) ≈ 30

n = 10¹⁸
→ log₂(n) ≈ 60
```

Even for enormous values:

```text
n = 10¹⁸
→ only about 60 divisions by 2
```

This is why problems with constraints around:

```text
10⁹
10¹²
10¹⁸
```

often require:

```text
O(log n)
O(log² n)
O(1)
```

or another complexity that does not depend linearly on the enormous value of `n`.

---

# 33. The Most Useful Contest Mental Model

When you see a constraint, immediately ask:

```text
What is n?
        ↓
How many operations will my algorithm perform?
        ↓
Can those operations fit within the time limit?
```

A useful first approximation:

```text
n ≈ 10⁵
→ think O(n log n) / O(n)

n ≈ 10⁶
→ think O(n) / O(n log n) depending on constants

n ≈ 10⁷
→ think O(n)

n ≈ 10⁹
→ think O(log n) / O(1)

n ≈ 20
→ O(2ⁿ) may be intended

n ≈ 10
→ O(n!) may be intended
```

---

# 34. Don't Blindly Follow the Table

The table is a **starting point, not a law**.

For example:

```text
O(n²)
```

with `n = 10⁵` is normally impossible.

But:

```text
O(n² / 64)
```

may have very different practical behavior depending on what each operation does.

Similarly:

```text
O(n log n)
```

does not automatically mean fast if each operation is expensive.

Always consider:

* Time limit
* Number of test cases
* Constant factors
* Actual operations
* Memory usage
* Input/output cost
* Worst-case behavior

---

# 35. Multiple Test Cases

This is extremely important.

If there are `T` test cases and each has size `n`, total complexity may be:

```text
O(T × n)
```

or:

```text
O(T × n²)
```

For example:

```text
T = 100
n = 10,000
```

An `O(n²)` solution performs approximately:

```text
100 × 10⁸
= 10¹⁰ operations
```

So always check whether the constraint is:

```text
n ≤ 10⁵
```

or:

```text
Σn ≤ 10⁵
```

The second is dramatically more useful because the **sum of all input sizes** is bounded.

---

# 36. Final Constraint Cheat Sheet

```text
┌─────────────────────┬──────────────────────────────┐
│ Constraint           │ Typical Target               │
├─────────────────────┼──────────────────────────────┤
│ n ≤ 10               │ O(n!), O(2ⁿ)                │
│ n ≤ 20               │ O(2ⁿ)                       │
│ n ≤ 100              │ O(n³)                       │
│ n ≤ 500              │ O(n³) / O(n²)               │
│ n ≤ 1,000            │ O(n²)                       │
│ n ≤ 10,000           │ O(n²) / O(n log n)           │
│ n ≤ 100,000          │ O(n log n) / O(n)            │
│ n ≤ 1,000,000        │ O(n) / O(n log n)            │
│ n ≤ 10,000,000       │ O(n)                         │
│ n ≥ 10⁹              │ O(log n) / O(1)              │
│ n ≥ 10¹⁸             │ O(log n) / O(1)              │
└─────────────────────┴──────────────────────────────┘
```

### Golden Rule

> **Read the constraints before designing the algorithm.**

The constraint often tells you what complexity the problem is expecting.

```text
Small n
→ Brute Force / Backtracking / Exponential

Medium n
→ O(n²) / O(n³)

Large n
→ O(n log n) / O(n)

Very Large n
→ O(log n) / O(1)
```

And always remember:

```text
10⁸ simple operations ≈ 1 second
```

as a **rough C++ mental benchmark**, not an exact guarantee.
