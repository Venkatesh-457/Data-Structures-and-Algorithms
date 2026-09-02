# Bit-Level State Storage and Bit Packing

## 1. The Core Idea

A computer stores integers using **binary bits**.

For example, an 8-bit value can look like:

```text
10110100
```

Each position contains either:

```text
0 → off / false
1 → on / true
```

This gives us an important technique:

> **Instead of storing many small boolean states separately, we can store them inside the individual bits of one integer.**

For example, instead of:

```cpp
bool visited[8];
```

we can use:

```cpp
uint8_t visited;
```

and let each bit represent one state:

```text
bit:      7 6 5 4 3 2 1 0
          ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
visited:  1 0 1 1 0 0 1 0
```

This is called **bit packing** or **bit-level state storage**.

---

# 2. Why Does This Work?

An integer does not only represent a decimal number.

The same bits can be interpreted as independent flags.

For example:

```text
00000101
```

as a number is:

```text
5
```

But we can interpret it as:

```text
bit 0 → true
bit 1 → false
bit 2 → true
bit 3 → false
...
```

So one integer can store multiple boolean values.

---

# 3. The Basic Technique

Suppose we want to store 8 boolean states.

We can use:

```cpp
uint8_t state = 0;
```

Then:

```cpp
state |= (1u << i);
```

sets bit `i`.

To check:

```cpp
if (state & (1u << i)) {
    // bit i is set
}
```

To clear:

```cpp
state &= ~(1u << i);
```

To toggle:

```cpp
state ^= (1u << i);
```

The important point is:

```text
One bit → One boolean state
```

---

# 4. Why `uint64_t` Is Useful

`uint64_t` is an unsigned integer containing exactly **64 bits**.

```cpp
uint64_t x;
```

Therefore it can be viewed as:

```text
bit: 63 62 61 ... 3 2 1 0
```

Each bit can represent one boolean state.

So one `uint64_t` can store up to:

```text
64 independent boolean states
```

For example:

```text
bit 0  → state 0
bit 1  → state 1
bit 2  → state 2
...
bit 63 → state 63
```

This is extremely useful when the number of states is small enough to fit into 64 bits.

---

# 5. Example: Storing 64 Visited Flags

Instead of:

```cpp
bool visited[64];
```

we can represent the same information using:

```cpp
uint64_t visited = 0;
```

To mark state `i` as visited:

```cpp
visited |= (1ULL << i);
```

To check:

```cpp
if (visited & (1ULL << i)) {
    // already visited
}
```

Now the entire 64-element boolean array is represented by **one 64-bit integer**.

---

# 6. Why `1ULL`?

You may see:

```cpp
1ULL << i
```

instead of:

```cpp
1 << i
```

`ULL` means:

```text
unsigned long long
```

So:

```cpp
1ULL
```

is an unsigned integer with a sufficiently wide integer type for 64-bit bit operations.

This is particularly important when shifting toward high bit positions.

For example:

```cpp
1ULL << 50
```

creates a value with only bit 50 set.

---

# 7. `uint64_t` vs `unsigned long long`

These are related but conceptually different.

### `uint64_t`

```cpp
uint64_t
```

means:

> An unsigned integer type that is exactly 64 bits wide, if the implementation provides such a type.

It is defined in:

```cpp
#include <cstdint>
```

This is useful when you specifically need **exactly 64 bits**.

### `unsigned long long`

```cpp
unsigned long long
```

is a standard C++ integer type with a guaranteed minimum width, but its exact width is implementation-dependent.

On most modern competitive-programming systems it is 64 bits.

### Practical rule

If your algorithm specifically depends on:

```text
exactly 64 bits
```

prefer:

```cpp
uint64_t
```

If you simply need a normal large unsigned integer:

```cpp
unsigned long long
```

is also common.

---

# 8. Why This Can Save Memory

Suppose we need:

```text
64 boolean values
```

A simple approach is:

```cpp
bool arr[64];
```

But a packed representation can store them in:

```cpp
uint64_t mask;
```

Conceptually:

```text
64 booleans
        ↓
64 bits
        ↓
1 × uint64_t
```

This can be especially valuable when the structure is repeated many times.

For example:

```cpp
uint64_t visited[1000000];
```

stores one million groups of 64 boolean states using a compact representation.

---

# 9. Bit Packing Is More General Than Boolean Storage

Bits don't have to represent only booleans.

We can also pack **small integer values** into different groups of bits.

For example, suppose we know:

```text
value A → needs 3 bits
value B → needs 4 bits
value C → needs 2 bits
```

We can store all three inside one integer:

```text
[ C: 2 bits ][ B: 4 bits ][ A: 3 bits ]
```

This is called **bit packing**.

The difference is:

### Bitmasking

Usually:

```text
1 bit → one yes/no condition
```

### Bit packing

Can be:

```text
multiple bits → one small integer
```

---

# 10. Example of Bit Packing

Suppose:

```text
age     → needs 7 bits
level   → needs 4 bits
status  → needs 2 bits
```

Instead of storing three separate integers, they could theoretically be packed into one integer.

Conceptually:

```text
| status | level | age |
|  2 bit | 4 bit |7bit|
```

The advantage is compact storage.

However, **do not automatically pack everything**. It makes code more complicated, so it should be used when memory or representation efficiency actually matters.

---

# 11. Integer Types as Bit Containers

C++ provides several fixed-width integer types.

The most useful ones for bit-level work are:

| Type       | Bits | Signed? |
| ---------- | ---: | ------- |
| `uint8_t`  |    8 | No      |
| `uint16_t` |   16 | No      |
| `uint32_t` |   32 | No      |
| `uint64_t` |   64 | No      |
| `int8_t`   |    8 | Yes     |
| `int16_t`  |   16 | Yes     |
| `int32_t`  |   32 | Yes     |
| `int64_t`  |   64 | Yes     |

These come from:

```cpp
#include <cstdint>
```

---

# 12. When to Use `uint8_t`

Use:

```cpp
uint8_t
```

when you need exactly **8 bits**.

Maximum unsigned value:

```text
255
```

because:

```text
2^8 - 1 = 255
```

Good use cases:

* Up to 8 boolean flags
* Compact byte-like storage
* Small values from `0` to `255`
* Bit manipulation where 8 bits are enough

Example:

```cpp
uint8_t flags = 0;
```

---

# 13. When to Use `uint16_t`

Use:

```cpp
uint16_t
```

when you need exactly **16 bits**.

Maximum value:

```text
2^16 - 1
= 65535
```

Good use cases:

* Up to 16 boolean flags
* Small unsigned values
* Compact protocol/data representations
* Bit-level algorithms requiring exactly 16 bits

---

# 14. When to Use `uint32_t`

Use:

```cpp
uint32_t
```

when you need exactly **32 bits**.

Maximum value:

```text
2^32 - 1
```

This is particularly useful for:

* 32 independent flags
* Bitmasks
* Hashing-related bit operations
* Bit manipulation algorithms
* Compact state representation

Example:

```cpp
uint32_t mask = 0;
```

---

# 15. When to Use `uint64_t`

Use:

```cpp
uint64_t
```

when you need exactly **64 bits**.

Maximum value:

```text
2^64 - 1
```

It is especially useful when:

* You need up to 64 boolean flags.
* A bitmask may require more than 32 bits.
* You want a fixed-width 64-bit representation.
* You need to pack several small fields into one integer.
* You are doing low-level bit manipulation.
* You need a compact representation of a large set of boolean states.

Example:

```cpp
uint64_t mask = 0;
```

---

# 16. When `uint64_t` Is NOT Appropriate

Do not use `uint64_t` just because it can store many bits.

If you need:

```text
1000 boolean states
```

then one `uint64_t` isn't enough.

You would need multiple 64-bit values.

For example:

```cpp
vector<uint64_t> bits;
```

Or you might use:

```cpp
vector<bool>
```

or:

```cpp
bitset<1000>
```

depending on the problem.

---

# 17. `std::bitset`

C++ also provides:

```cpp
#include <bitset>

bitset<N>
```

A `bitset` represents a **fixed number of bits**.

For example:

```cpp
bitset<100> bits;
```

stores exactly 100 bits.

You can do:

```cpp
bits[5] = 1;
```

Check:

```cpp
if (bits[5]) {
}
```

Set:

```cpp
bits.set(5);
```

Clear:

```cpp
bits.reset(5);
```

Toggle:

```cpp
bits.flip(5);
```

---

# 18. `uint64_t` vs `bitset`

This distinction is important.

### Use `uint64_t` when:

You need at most 64 bits and want to perform direct integer bit operations.

```cpp
uint64_t mask;
```

Typical:

```cpp
mask |= (1ULL << i);
```

### Use `bitset<N>` when:

You know the number of bits at compile time and it may be larger than 64.

```cpp
bitset<1000> mask;
```

You get convenient operations such as:

```cpp
mask.set(i);
mask.reset(i);
mask.flip(i);
mask.test(i);
```

---

# 19. `uint64_t` vs `bitset<N>` Example

Suppose you need 10 flags.

You can use:

```cpp
uint64_t mask;
```

or:

```cpp
bitset<10> mask;
```

Both can represent 10 bits.

But if your main goal is compact integer-style manipulation:

```cpp
mask |= (1ULL << i);
```

`uint64_t` is natural.

If you want convenient fixed-size bit storage:

```cpp
bitset<10> mask;
```

is often cleaner.

---

# 20. `vector<bool>`

C++ has a special specialization:

```cpp
vector<bool>
```

Unlike an ordinary:

```cpp
vector<int>
```

it is designed to store boolean values in a packed representation.

For example:

```cpp
vector<bool> visited(1000000);
```

can store many boolean values efficiently.

Use it when:

* You need a dynamically sized collection of booleans.
* The number of flags is not known at compile time.
* You want packed boolean storage without manually managing bits.

You generally don't use `vector<bool>` when you specifically need to manipulate individual bits as an integer mask.

---

# 21. `vector<uint64_t>`

When you have many bits, you can store them in multiple 64-bit blocks.

For example, suppose you need:

```text
200 bits
```

You can use:

```cpp
vector<uint64_t> bits(4);
```

because:

```text
4 × 64 = 256 bits
```

Conceptually:

```text
bits[0] → bits 0–63
bits[1] → bits 64–127
bits[2] → bits 128–191
bits[3] → bits 192–255
```

This technique is useful when you want manual control over large bit sets.

---

# 22. `std::bitset` vs `vector<uint64_t>`

### `bitset<N>`

Use when:

```text
N is known at compile time
```

Example:

```cpp
bitset<1000> bits;
```

### `vector<uint64_t>`

Use when:

```text
number of bits is determined at runtime
```

Example:

```cpp
int n;
cin >> n;

vector<uint64_t> bits((n + 63) / 64);
```

This gives you a dynamically sized bit storage structure.

---

# 23. Choosing the Right Representation

A useful decision process:

```text
How many boolean states?
        |
        v
      ≤ 8?
        |
        +---- yes → uint8_t
        |
        v
     ≤ 16?
        |
        +---- yes → uint16_t
        |
        v
     ≤ 32?
        |
        +---- yes → uint32_t
        |
        v
     ≤ 64?
        |
        +---- yes → uint64_t
        |
        v
Known size at compile time?
        |
        +---- yes → bitset<N>
        |
        v
Dynamic number of bits?
        |
        +---- yes → vector<uint64_t>
```

This is not an absolute rule. Choose based on what makes the algorithm simplest while meeting memory/performance requirements.

---

# 24. Fixed-Width Types vs Normal `int`

You may wonder:

> Why not just use `int`?

An `int` is commonly 32 bits, but the C++ standard does not make it exactly 32 bits on every possible system.

If your algorithm relies on an exact number of bits:

```cpp
uint32_t
uint64_t
```

makes your intention explicit.

For example:

```cpp
uint64_t mask;
```

clearly communicates:

> This variable is being used as a 64-bit bit container.

---

# 25. Important: Signed vs Unsigned

For bit storage, unsigned types are often easier to reason about.

Prefer:

```cpp
uint64_t
```

for a 64-bit collection of flags.

Rather than:

```cpp
int64_t
```

when the value is not supposed to represent a negative number.

Why?

Because the highest bit of a signed integer participates in representing the sign.

For pure bit storage, unsigned types make the intention clearer.

---

# 26. Shift Range Matters

Suppose:

```cpp
uint64_t x;
```

and you want bit `i`.

Use:

```cpp
1ULL << i
```

For a 64-bit value, valid bit positions are:

```text
0 through 63
```

So:

```cpp
1ULL << 63
```

is valid.

But:

```cpp
1ULL << 64
```

is not a valid way to represent a 64th bit because there are only 64 positions:

```text
0 ... 63
```

---

# 27. The Same Trick Can Store Other Things

The technique is not limited to "visited" flags.

A bit can represent:

```text
visited
collected
active
enabled
selected
locked
available
permission
feature enabled
```

For example:

```text
bit 0 → has sword
bit 1 → has key
bit 2 → has shield
bit 3 → door unlocked
bit 4 → quest completed
```

One integer can represent the complete collection of these states.

---

# 28. Bitmask vs Bit Packing

These terms are related but should not be confused.

### Bitmask

Uses individual bits mainly as flags:

```text
bit 0 → A
bit 1 → B
bit 2 → C
```

Example:

```cpp
uint64_t mask;
```

### Bit packing

Stores multiple values inside different groups of bits:

```text
| value C | value B | value A |
```

For example:

```text
3 bits + 4 bits + 5 bits
```

inside one integer.

So:

> **Bitmasking is primarily about individual bit flags; bit packing is about fitting multiple small values into a compact bit representation.**

---

# 29. When Should You Actually Use This Technique?

Use bit-level storage when at least one of these is true:

### 1. You have many boolean states

Example:

```text
64 switches
```

Instead of 64 separate flags, one `uint64_t` can represent them.

### 2. You need a compact state

Useful in:

* BFS
* DFS
* Dynamic Programming
* Memoization
* Graph algorithms

### 3. You need fast bit operations

Operations such as:

```cpp
&
|
^
<<
>>
```

operate on many bits simultaneously.

### 4. Memory is important

Especially when the structure contains a huge number of repeated states.

### 5. You need subset representation

If you have:

```text
n ≤ 64
```

boolean items, a 64-bit integer can represent their subset.

---

# 30. When Should You NOT Use It?

Do not use bit packing simply because it looks clever.

Avoid it when:

* The number of states is huge.
* The values are large and don't fit into a few bits.
* Normal variables are already sufficiently memory-efficient.
* Packing makes the code unnecessarily difficult.
* Readability is more important than saving a small amount of memory.
* You need dynamic bit counts but a simpler structure works well.

A good competitive-programming principle is:

> **Use bit-level optimization when the constraints justify it, not just because it is possible.**

---

# 31. Performance Perspective

Bit operations are generally very cheap:

```cpp
&
|
^
~
<<
>>
```

and modern CPUs can process many bits simultaneously.

For example:

```cpp
uint64_t a, b;

uint64_t common = a & b;
```

checks/intersects 64 bit positions in one integer operation.

This is one reason bit representations can be very efficient.

---

# 32. Example: Finding Common Flags

Suppose:

```text
A = 101101
B = 111001
```

Then:

```text
A & B
```

gives:

```text
101101
111001
------
101001
```

The `1` bits represent flags that are present in **both** sets.

This is one of the powerful advantages of representing sets as bits.

---

# 33. Example: Combining Flags

Suppose:

```text
A = 001101
B = 110001
```

Then:

```text
A | B
```

gives:

```text
001101
110001
------
111101
```

The result contains everything present in either set.

---

# 34. Example: Comparing Sets

Suppose:

```text
A = 10101
B = 10101
```

Then:

```cpp
A == B
```

directly tells us that the bit patterns are identical.

This is useful because an entire collection of boolean states can be compared using one integer comparison.

---

# 35. A Powerful Concept: State Compression

The larger idea behind all of this is:

> **State compression means representing a large amount of state information using a smaller representation.**

For example:

```text
64 boolean values
        ↓
64 bits
        ↓
1 uint64_t
```

This can make algorithms significantly more memory-efficient.

It is especially powerful when used with:

```text
BFS
DFS
DP
Memoization
Graph algorithms
Subset algorithms
```

---

# 36. Connection to Your State-Space BFS Knowledge

You previously learned that the complete BFS state might be:

```text
(row, column, energy, mask)
```

The important principle was:

> If information affects the future, it belongs in the state.

Bit-level storage adds another idea:

> Once that state information consists of many boolean conditions, those conditions can often be compressed into bits.

So these are two separate concepts:

```text
State-space BFS
    ↓
What information must I remember?

Bitmasking / bit packing
    ↓
How can I represent that information efficiently?
```

This distinction is important.

---

# 37. Common Patterns to Memorize

### Set bit

```cpp
mask |= (1ULL << i);
```

### Check bit

```cpp
mask & (1ULL << i)
```

### Clear bit

```cpp
mask &= ~(1ULL << i);
```

### Toggle bit

```cpp
mask ^= (1ULL << i);
```

### Create first `n` bits as 1

```cpp
(1ULL << n) - 1
```

### Check whether all required bits are present

```cpp
(mask & required) == required
```

This last pattern is particularly useful.

---

# 38. `required` Mask

Suppose:

```text
required = 10110
```

means:

```text
these particular flags are required
```

and:

```text
mask = 11110
```

means:

```text
these flags are currently available
```

We can check:

```cpp
if ((mask & required) == required)
```

This asks:

> Does `mask` contain every bit required by `required`?

This is a very common bitmask pattern.

---

# 39. Final Comparison Table

| Representation     | Best for                                         |          Size |
| ------------------ | ------------------------------------------------ | ------------: |
| `bool`             | Single boolean                                   |       1 value |
| `uint8_t`          | Up to 8 packed flags / 8-bit value               |        8 bits |
| `uint16_t`         | Up to 16 packed flags / 16-bit value             |       16 bits |
| `uint32_t`         | Up to 32 packed flags / 32-bit value             |       32 bits |
| `uint64_t`         | Up to 64 packed flags / 64-bit value             |       64 bits |
| `bitset<N>`        | Fixed-size bit collection                        |        N bits |
| `vector<bool>`     | Dynamic boolean collection                       |        Packed |
| `vector<uint64_t>` | Large dynamic bit collection with manual control | 64 bits/block |

---

# 40. Final Mental Model

Think of an integer like a row of tiny switches:

```text
uint64_t
┌───┬───┬───┬───┬───┬───┬───┬───┬─── ...
│ 1 │ 0 │ 1 │ 1 │ 0 │ 0 │ 1 │ 0 │
└───┴───┴───┴───┴───┴───┴───┴───┴───
  ↑   ↑   ↑   ↑   ↑   ↑   ↑   ↑
```

Each bit can represent something.

For flags:

```text
1 bit → 1 boolean
```

For packed values:

```text
several bits → 1 small integer
```

Therefore:

```text
BIT-LEVEL REPRESENTATION
        │
        ├── Bitmasking
        │     └── One bit = one flag
        │
        └── Bit Packing
              └── Several bits = one small value
```

The key question to ask when solving a problem is:

> **Can the information I need to remember be represented compactly using bits?**

If the answer is yes, bit-level storage may give you a much more memory-efficient representation.
