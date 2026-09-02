# Bitmasking — Complete Notes

## 1. What is Bitmasking?

**Bitmasking** is a technique where we use the **bits of an integer to store multiple yes/no conditions**.

Instead of storing:

```text
item 0 → collected
item 1 → not collected
item 2 → collected
item 3 → not collected
```

using an array like:

```cpp
bool collected[4];
```

we can store all of them inside one integer:

```text
bit:      3 2 1 0
          ↓ ↓ ↓ ↓
          0 1 0 1
```

Here:

* `1` → condition is true / item is present
* `0` → condition is false / item is absent

So:

```text
0101
```

represents:

```text
item 0 → collected
item 1 → not collected
item 2 → collected
item 3 → not collected
```

This integer is called a **bitmask**.

---

# 2. Why Do We Need Bitmasking?

Suppose there are 5 different items:

```text
A B C D E
```

At any point, some may be collected and some may not be.

There are:

```text
2^5 = 32
```

possible combinations.

For example:

```text
00000 → nothing collected

00001 → A collected

00101 → A and C collected

11111 → everything collected
```

Instead of storing the entire collection using an array, we can represent it with **one integer**.

This is especially useful when:

* Number of items is small.
* We need to track subsets.
* We need to remember which things have been visited/collected/used.
* We are doing BFS/DFS/DP over different combinations.

---

# 3. Understanding Bits

An integer is represented internally using binary.

For example:

```text
13 = 1101
```

The positions are:

```text
bit position:   3 2 1 0
                ↓ ↓ ↓ ↓
binary:         1 1 0 1
```

Each position represents a power of 2:

```text
bit 0 → 2^0 = 1
bit 1 → 2^1 = 2
bit 2 → 2^2 = 4
bit 3 → 2^3 = 8
```

Therefore:

```text
1101
```

means:

```text
8 + 4 + 0 + 1 = 13
```

---

# 4. One Bit Can Represent One Boolean

This is the main idea behind bitmasking.

Suppose we have 4 items:

```text
A B C D
```

Assign:

```text
A → bit 0
B → bit 1
C → bit 2
D → bit 3
```

Then:

```text
item       bit
A          0
B          1
C          2
D          3
```

Now:

```text
0000
```

means:

```text
A = false
B = false
C = false
D = false
```

And:

```text
0101
```

means:

```text
A = true
B = false
C = true
D = false
```

---

# 5. Setting a Bit

Suppose we want to mark item `i` as collected.

We use:

```cpp
mask |= (1 << i);
```

### Example

Initially:

```text
mask = 0000
```

We collect item `0`.

```cpp
mask |= (1 << 0);
```

`1 << 0` gives:

```text
0001
```

Therefore:

```text
0000
OR
0001
----
0001
```

Now item 0 is marked.

---

# 6. What Does `1 << i` Mean?

This is one of the most important expressions in bitmasking.

```cpp
1 << i
```

means:

> Move the binary `1` to position `i`.

Examples:

```text
1 << 0 = 0001
1 << 1 = 0010
1 << 2 = 0100
1 << 3 = 1000
```

So:

```cpp
1 << i
```

creates a mask containing **only bit `i`**.

---

# 7. Checking Whether a Bit Is Set

To check whether item `i` has been collected:

```cpp
if (mask & (1 << i))
```

Example:

```text
mask = 0101
```

Check item 2:

```text
0101
&
0100
----
0100
```

Result is non-zero.

Therefore:

```text
item 2 is collected
```

Check item 1:

```text
0101
&
0010
----
0000
```

Result is zero.

Therefore:

```text
item 1 is not collected
```

### General pattern

```cpp
if (mask & (1 << i))
```

means:

> Is bit `i` set?

---

# 8. Clearing a Bit

Sometimes we want to mark an item as unused/uncollected.

We use:

```cpp
mask &= ~(1 << i);
```

For example:

```text
mask = 1111
```

Clear bit 2:

```text
1 << 2 = 0100
```

Invert it:

```text
~0100 = 1011
```

Then:

```text
1111
&
1011
----
1011
```

Bit 2 becomes `0`.

---

# 9. Toggling a Bit

To change:

```text
0 → 1
```

or:

```text
1 → 0
```

we use XOR:

```cpp
mask ^= (1 << i);
```

Example:

```text
mask = 0101
```

Toggle bit 1:

```text
0101
XOR
0010
----
0111
```

Bit 1 changed from `0` to `1`.

If we toggle it again:

```text
0111
XOR
0010
----
0101
```

It changes back to `0`.

---

# 10. The Four Important Bitmask Operations

These are the most important patterns to remember.

### Set bit

```cpp
mask |= (1 << i);
```

Meaning:

> Make bit `i` equal to 1.

---

### Check bit

```cpp
mask & (1 << i)
```

Meaning:

> Is bit `i` equal to 1?

---

### Clear bit

```cpp
mask &= ~(1 << i);
```

Meaning:

> Make bit `i` equal to 0.

---

### Toggle bit

```cpp
mask ^= (1 << i);
```

Meaning:

> Change bit `i`.

---

# 11. Representing a Set Using a Bitmask

Suppose:

```text
A B C D
```

and we have:

```text
mask = 1011
```

Mapping:

```text
bit 0 → A
bit 1 → B
bit 2 → C
bit 3 → D
```

Therefore:

```text
D → 1 → present
C → 0 → absent
B → 1 → present
A → 1 → present
```

So the set is:

```text
{A, B, D}
```

A bitmask is therefore a compact way of representing a **subset**.

---

# 12. Number of Possible Subsets

If there are `n` items, each item has two possibilities:

```text
present
absent
```

Therefore:

```text
2 × 2 × 2 × ... × 2
        n times
```

which gives:

```text
2^n
```

possible subsets.

For example:

```text
n = 3

2^3 = 8
```

The masks are:

```text
000
001
010
011
100
101
110
111
```

These represent all possible subsets.

---

# 13. `allMask`

This is extremely common in bitmask problems.

Suppose there are `n` items.

We want a mask where the first `n` bits are all `1`.

We can create it using:

```cpp
int allMask = (1 << n) - 1;
```

### Example: n = 4

```cpp
1 << 4
```

gives:

```text
10000
```

Subtract 1:

```text
10000
 -   1
-----
01111
```

Therefore:

```cpp
allMask = 15;
```

Binary:

```text
1111
```

This means:

> All 4 items are present.

---

# 14. Checking Whether Everything Is Collected

Suppose:

```cpp
allMask = (1 << n) - 1;
```

Then:

```cpp
if (mask == allMask)
```

means:

> All `n` items have been collected.

Example:

```text
mask    = 1011
allMask = 1011
```

Therefore:

```text
mask == allMask
```

is true.

---

# 15. Why Bitmasking Is Powerful in BFS

Now connect this to **State-Space BFS**.

Suppose a grid contains 4 pieces of litter:

```text
L0
L1
L2
L3
```

Your position alone is not enough to describe the state.

For example, you could be at:

```text
(r = 3, c = 4)
```

with:

```text
nothing collected
```

or at the exact same position with:

```text
L0 and L2 collected
```

These are different states because your future possibilities are different.

So our state becomes:

```text
(row, column, mask)
```

If energy also matters:

```text
(row, column, energy, mask)
```

---

# 16. Litter ID Mapping

Suppose the grid is:

```text
S . L . .
. . . L .
. L . . .
```

We assign IDs:

```text
first L → ID 0
second L → ID 1
third L → ID 2
```

We can store:

```text
litterId[r][c]
```

For example:

```text
L at (0,2) → ID 0
L at (1,3) → ID 1
L at (2,1) → ID 2
```

Now:

```text
bit 0 → litter 0
bit 1 → litter 1
bit 2 → litter 2
```

---

# 17. Collecting a Litter

Suppose we currently have:

```text
mask = 001
```

This means:

```text
litter 0 → collected
litter 1 → not collected
litter 2 → not collected
```

Now we enter litter 1.

We use:

```cpp
mask |= (1 << 1);
```

So:

```text
001
OR
010
---
011
```

Now:

```text
litter 0 → collected
litter 1 → collected
litter 2 → not collected
```

---

# 18. Important: We Usually Don't Need to Check Before Setting

If we enter a litter cell:

```cpp
mask |= (1 << litterId);
```

is enough.

Why?

Because OR-ing with `1` keeps a bit as `1`.

Example:

```text
already collected:

011
OR
010
---
011
```

Nothing breaks.

So:

```cpp
mask |= (1 << id);
```

can safely be used whether or not that litter was already collected.

---

# 19. Bitmask + Visited

This is where bitmasking becomes extremely useful with BFS.

Suppose:

```text
row = 2
column = 3
mask = 0101
```

This is one state.

Another state:

```text
row = 2
column = 3
mask = 0111
```

has the same position but a different collection status.

Therefore these are **different BFS states**.

So:

```cpp
visited[row][column][mask]
```

is needed.

If energy is also part of the state:

```cpp
visited[row][column][energy][mask]
```

Each different mask represents a different possible future.

---

# 20. Why `visited[row][column]` Is Wrong Here

Suppose:

```text
State A:
(2,3, mask=0011)

State B:
(2,3, mask=1111)
```

They are at the same cell.

But:

```text
A → only some litter collected
B → all litter collected
```

Their future is different.

Therefore:

```cpp
visited[2][3]
```

cannot distinguish them.

We need:

```cpp
visited[2][3][0011]
visited[2][3][1111]
```

This is the same State-Space BFS principle:

> If something affects future decisions, it must be included in the state.

---

# 21. Bitmasking and Energy Are Different Ideas

In the classroom problem, we may have:

```text
(row, col, energy, mask)
```

Here:

### `energy`

Represents:

> How much energy is currently available?

### `mask`

Represents:

> Which litter cells have already been collected?

Both are state information, but they are represented differently.

For example:

```text
energy = 7
```

is simply a number.

While:

```text
mask = 0101
```

uses individual bits to represent multiple yes/no conditions.

---

# 22. Bitmasking Does Not Mean Every Number Must Be Binary

This is important.

You might see:

```cpp
int energy = 20;
```

That does **not** mean energy is being bitmasked.

Bitmasking specifically means we are intentionally using individual bits for separate information.

For example:

```cpp
mask = 010101
```

where:

```text
bit 0 → item 0
bit 1 → item 1
bit 2 → item 2
...
```

---

# 23. Bitmasking a `bool` Array

Without bitmasking:

```cpp
bool collected[5];
```

We have 5 separate values.

With bitmasking:

```cpp
int mask;
```

we can store those 5 boolean values inside the bits of one integer.

For example:

```text
collected:

item 0 → true
item 1 → false
item 2 → true
item 3 → false
item 4 → true
```

becomes:

```text
10101
```

This is the main space-saving idea.

---

# 24. Enumerating All Subsets

Another common use of bitmasking is generating every possible subset.

Suppose:

```text
n = 3
```

There are:

```text
2^3 = 8
```

subsets.

We can do:

```cpp
for (int mask = 0; mask < (1 << n); mask++) {
    
}
```

For `n = 3`:

```text
mask = 0 → 000
mask = 1 → 001
mask = 2 → 010
mask = 3 → 011
mask = 4 → 100
mask = 5 → 101
mask = 6 → 110
mask = 7 → 111
```

Every possible combination is covered.

---

# 25. Finding Which Items Are Present

Inside the subset loop:

```cpp
for (int i = 0; i < n; i++) {
    if (mask & (1 << i)) {
        // item i is present
    }
}
```

So:

```cpp
for (int mask = 0; mask < (1 << n); mask++) {
    for (int i = 0; i < n; i++) {
        if (mask & (1 << i)) {
            // use item i
        }
    }
}
```

This pattern appears frequently in:

* Subset problems
* Dynamic programming
* Traveling Salesman Problem
* Assignment problems
* Graph problems
* BFS with keys/items
* State-space search

---

# 26. Common Bitwise Operators

You should know these operators:

| Operator | Name        | Purpose              |                  |
| -------- | ----------- | -------------------- | ---------------- |
| `&`      | AND         | Check/intersect bits |                  |
| `        | `           | OR                   | Set/combine bits |
| `^`      | XOR         | Toggle/difference    |                  |
| `~`      | NOT         | Invert bits          |                  |
| `<<`     | Left shift  | Move bits left       |                  |
| `>>`     | Right shift | Move bits right      |                  |

The most important ones initially are:

```cpp
&
|
<<
```

---

# 27. `&` — AND

A bit is `1` only if both bits are `1`.

```text
0 & 0 = 0
0 & 1 = 0
1 & 0 = 0
1 & 1 = 1
```

This makes it useful for checking a particular bit.

```cpp
mask & (1 << i)
```

---

# 28. `|` — OR

A bit becomes `1` if either bit is `1`.

```text
0 | 0 = 0
0 | 1 = 1
1 | 0 = 1
1 | 1 = 1
```

This makes it useful for setting a bit.

```cpp
mask |= (1 << i);
```

---

# 29. `^` — XOR

XOR gives `1` when the bits are different.

```text
0 ^ 0 = 0
0 ^ 1 = 1
1 ^ 0 = 1
1 ^ 1 = 0
```

Therefore it is useful for toggling.

```cpp
mask ^= (1 << i);
```

---

# 30. Left Shift

```cpp
1 << i
```

moves `1` to bit position `i`.

Examples:

```text
1 << 0 → 0001
1 << 1 → 0010
1 << 2 → 0100
1 << 3 → 1000
```

This is the foundation of most basic bitmask code.

---

# 31. Right Shift

Right shift moves bits toward the right.

For example:

```text
1010 >> 1
```

gives:

```text
0101
```

It can be used to inspect bits and perform certain division-by-two operations for non-negative integers.

For basic bitmasking, you will mostly encounter:

```cpp
1 << i
```

rather than needing `>>` immediately.

---

# 32. `1 << n` and the Number of Masks

If there are `n` items:

```cpp
1 << n
```

is equivalent to:

```text
2^n
```

Therefore:

```cpp
for (int mask = 0; mask < (1 << n); mask++)
```

iterates through all:

```text
2^n
```

possible masks.

---

# 33. Important Constraint

Bitmasking is particularly useful when `n` is **small**.

For example:

```text
n = 10

2^10 = 1024
```

Very manageable.

But:

```text
n = 30

2^30 ≈ 1 billion
```

Now iterating through every mask is usually too expensive.

Therefore, when you see:

> There are at most 10 items.

or:

> There are at most 15 cities.

or:

> There are at most 20 special nodes.

you should immediately think:

**Bitmasking may be useful.**

---

# 34. Bitmask Size in the Classroom Problem

Suppose the maximum number of litter cells is:

```text
10
```

Then we need only:

```text
10 bits
```

to represent all litter states.

Number of possible masks:

```text
2^10 = 1024
```

So:

```cpp
int mask;
```

is more than enough.

The masks range from:

```text
0000000000
```

to:

```text
1111111111
```

---

# 35. `allMask` for 10 Litter Cells

If:

```cpp
int id = 10;
```

then:

```cpp
int allMask = (1 << id) - 1;
```

gives:

```text
(1 << 10) - 1
```

which is:

```text
10000000000
-
         1
-----------
01111111111
```

So:

```text
allMask = 1023
```

Binary:

```text
1111111111
```

When:

```cpp
mask == allMask
```

all 10 litter cells have been collected.

---

# 36. Zero Items Case

Suppose there are no litter cells.

Then:

```cpp
id = 0;
```

Therefore:

```cpp
allMask = (1 << 0) - 1;
```

which gives:

```text
1 - 1 = 0
```

So:

```cpp
allMask = 0;
```

And the initial mask is also:

```cpp
mask = 0;
```

Therefore:

```cpp
mask == allMask
```

immediately.

This correctly means:

> There is nothing to collect.

---

# 37. Bitmasking With `uint64_t`

You may also see:

```cpp
uint64_t visited;
```

This is another application of the same idea.

A `uint64_t` contains **64 bits**.

Therefore, each bit can represent a boolean state.

For example:

```text
bit 0 → energy 0 visited
bit 1 → energy 1 visited
bit 2 → energy 2 visited
...
bit 50 → energy 50 visited
```

To check energy `e`:

```cpp
visited & (1ULL << e)
```

To mark energy `e`:

```cpp
visited |= (1ULL << e);
```

This is also bitmasking.

The only difference is **what each bit represents**.

---

# 38. Two Different Masks in the Same Problem

In the classroom problem you can encounter:

### Litter mask

```cpp
mask
```

where:

```text
bit 0 → litter 0
bit 1 → litter 1
bit 2 → litter 2
...
```

### Energy visited mask

```cpp
visitedEnergy
```

where:

```text
bit 0 → energy 0
bit 1 → energy 1
bit 2 → energy 2
...
```

Same technique:

```text
one bit = one yes/no state
```

Different meaning.

---

# 39. The Most Important Mental Model

Do not memorize:

```cpp
mask |= (1 << i)
```

as a random formula.

Understand it as:

```text
1 << i
```

↓

> Create a mask targeting bit `i`.

Then:

```text
mask |= ...
```

↓

> Turn that bit on.

Similarly:

```cpp
mask & (1 << i)
```

means:

> Look at bit `i`.

Once you understand this, the syntax becomes natural.

---

# 40. Common Mistakes

### Mistake 1: Forgetting zero-based bit positions

Usually:

```text
item 0 → bit 0
item 1 → bit 1
item 2 → bit 2
```

not:

```text
item 0 → bit 1
```

---

### Mistake 2: Using `1 << n` as the final mask

If there are `n` items:

```cpp
1 << n
```

is **not** the mask containing all `n` items.

It is:

```text
1000...000
```

with bit `n` set.

The mask containing the first `n` bits is:

```cpp
(1 << n) - 1
```

---

### Mistake 3: Using only position in State-Space BFS

If collected items affect the future:

```cpp
visited[r][c]
```

is insufficient.

You need:

```cpp
visited[r][c][mask]
```

---

### Mistake 4: Confusing bitwise operators with logical operators

Bitwise:

```cpp
&
|
^
```

Logical:

```cpp
&&
||
!
```

For bitmasking, we use the **bitwise** operators.

---

# 41. Complexity

If there are `n` items:

```text
Number of possible masks = 2^n
```

If we combine a bitmask with BFS on a grid of size:

```text
R × C
```

then the number of possible:

```text
(row, column, mask)
```

states is approximately:

```text
R × C × 2^n
```

If energy is also part of the state and has `E` possible values:

```text
R × C × E × 2^n
```

This is why bitmasking works well when `n` is small.

---

# 42. Bitmasking Pattern to Remember

For `n` items:

```cpp
int mask = 0;

int allMask = (1 << n) - 1;
```

Set item `i`:

```cpp
mask |= (1 << i);
```

Check item `i`:

```cpp
if (mask & (1 << i)) {
    
}
```

Clear item `i`:

```cpp
mask &= ~(1 << i);
```

Toggle item `i`:

```cpp
mask ^= (1 << i);
```

Check everything:

```cpp
if (mask == allMask) {
    
}
```

Enumerate all subsets:

```cpp
for (int mask = 0; mask < (1 << n); mask++) {
    
}
```

---

# 43. How to Recognize Bitmasking Problems

When reading a problem, look for phrases like:

* "There are at most 10..."
* "Collect all..."
* "Visit every..."
* "Which items have been collected?"
* "Which keys have been obtained?"
* "Each node can be selected or not selected."
* "Visit a subset of nodes."
* "Every possible combination..."
* "All tasks must be completed."
* "Track which special objects have been visited."

Then ask:

> Can I represent each yes/no condition using one bit?

If yes, bitmasking may be appropriate.

---

# 44. Connection to State-Space BFS

The progression you should remember is:

```text
Normal BFS
   ↓
Grid BFS
   ↓
State-Space BFS
   ↓
Additional information in state
   ↓
Small number of boolean conditions
   ↓
Bitmask those conditions
```

For the classroom problem:

```text
Position
   ↓
(row, col)

Energy matters
   ↓
(row, col, energy)

Collected litter matters
   ↓
(row, col, energy, mask)
```

Here:

```text
mask
```

efficiently stores:

> Which litter cells have already been collected?

---

# 45. Final Cheat Sheet

```text
BITMASKING
│
├── One bit = one boolean condition
│
├── 1 << i
│   └── Target bit i
│
├── Set bit
│   └── mask |= (1 << i)
│
├── Check bit
│   └── mask & (1 << i)
│
├── Clear bit
│   └── mask &= ~(1 << i)
│
├── Toggle bit
│   └── mask ^= (1 << i)
│
├── All n bits set
│   └── (1 << n) - 1
│
├── Number of possible masks
│   └── 2^n
│
└── Common use
    ├── Subsets
    ├── BFS states
    ├── DP
    ├── Keys/items
    ├── Visited objects
    └── Small sets of objects
```

## One sentence to remember

> **Bitmasking uses individual bits of an integer to efficiently represent multiple yes/no conditions, especially when the number of conditions is small.**
