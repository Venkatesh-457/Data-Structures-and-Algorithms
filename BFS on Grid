# BFS in Grids

## 1. What is BFS?

**BFS (Breadth-First Search)** explores a graph level by level.

It first visits all nodes at distance `1` from the starting node, then all nodes at distance `2`, then distance `3`, and so on.

This property makes BFS extremely useful for finding the **minimum number of moves/edges** when every move has the same cost.

### Basic idea

```text
Start
 ↓
All states reachable in 1 move
 ↓
All states reachable in 2 moves
 ↓
All states reachable in 3 moves
 ↓
...
```

If we encounter the target for the first time during BFS, that is the minimum number of moves.

---

# 2. BFS and a Grid

A grid can be treated as a graph.

For example:

```text
. . .
. . .
. . .
```

Each cell is a node.

Two cells are connected if the student/player can move between them.

For the standard 4-directional grid:

```text
        Up
         ↑
Left ← (r,c) → Right
         ↓
       Down
```

The four possible movements are:

```cpp
dr = {-1, 1, 0, 0};
dc = {0, 0, -1, 1};
```

For a cell `(r,c)`:

```cpp
nr = r + dr[i];
nc = c + dc[i];
```

This is the standard way to generate the four neighboring cells.

---

# 3. Valid Grid Position

Whenever BFS tries to move to `(nr,nc)`, we first check whether that position is valid.

A cell is invalid if:

1. It goes outside the grid.
2. It is an obstacle.

Example:

```cpp
bool isValid(int r, int c, int n, int m) {
    if (r < 0 || r >= n || c < 0 || c >= m)
        return false;

    return true;
}
```

If obstacles exist:

```cpp
if (grid[r][c] == 'X')
    return false;
```

So the general condition becomes:

```cpp
return r >= 0 && r < n &&
       c >= 0 && c < m &&
       grid[r][c] != 'X';
```

---

# 4. Basic Grid BFS

A basic grid BFS looks like:

```cpp
queue<pair<int,int>> q;

q.push({sr, sc});
visited[sr][sc] = true;

while (!q.empty()) {

    auto [r, c] = q.front();
    q.pop();

    for (int i = 0; i < 4; i++) {

        int nr = r + dr[i];
        int nc = c + dc[i];

        if (!isValid(nr, nc))
            continue;

        if (visited[nr][nc])
            continue;

        visited[nr][nc] = true;
        q.push({nr, nc});
    }
}
```

The important pattern is:

```text
Take current cell
      ↓
Generate its neighbors
      ↓
Reject invalid neighbors
      ↓
Reject already visited neighbors
      ↓
Add new neighbors to queue
```

---

# 5. Why Do We Need `visited`?

Without `visited`, BFS can keep moving back and forth forever.

Example:

```text
A → B
↑   ↓
D ← C
```

From `A`, we can reach `B`.

From `B`, we can return to `A`.

Then:

```text
A → B → A → B → A → ...
```

Therefore, once we have processed/reached a state, we generally don't want to repeatedly add the same state.

For a simple grid:

```cpp
visited[r][c]
```

answers:

> Have I already visited this cell?

---

# 6. Why Mark Visited When Pushing?

A common mistake is to mark a cell visited only when popping it.

Prefer:

```cpp
if (!visited[nr][nc]) {
    visited[nr][nc] = true;
    q.push({nr,nc});
}
```

rather than waiting until the state comes out of the queue.

Why?

Suppose two different cells can both reach `D`:

```text
A → D
B → D
```

While processing `A`, we put `D` into the queue.

Before `D` is popped, we process `B`.

If we haven't marked `D` yet, `B` can also put `D` into the queue.

So `D` appears twice.

Marking it when inserting into the queue prevents duplicate insertion.

---

# 7. BFS Gives Minimum Moves

Suppose:

```text
S . .
. . .
. . T
```

BFS processes:

```text
distance 0:
S

distance 1:
cells one move away

distance 2:
cells two moves away

distance 3:
cells three moves away

...
```

Therefore, the first time we reach `T`, we have found the minimum number of moves.

This relies on an important condition:

> **Every move has the same cost.**

If one move costs `1` and another costs `5`, ordinary BFS is no longer sufficient for shortest path.

---

# 8. Two Ways to Track Distance

## Method 1: Store distance in the queue

You can make the state:

```cpp
queue<tuple<int,int,int>> q;
```

where:

```text
row
column
distance
```

Example:

```cpp
q.push({sr, sc, 0});
```

Then:

```cpp
auto [r, c, dist] = q.front();
```

and the next state has:

```cpp
dist + 1
```

---

## Method 2: BFS Levels

Instead of storing distance in every queue element, process the queue level by level.

```cpp
int moves = 0;

while (!q.empty()) {

    int size = q.size();

    for (int i = 0; i < size; i++) {

        // process one level
    }

    moves++;
}
```

Meaning:

```text
Queue before processing → states at distance moves
```

After processing the entire queue level:

```cpp
moves++;
```

Now the next level represents one additional move.

This is especially useful when the BFS state itself already contains several pieces of information.

---

# 9. Obstacles

Grid BFS commonly contains cells that cannot be entered.

For example:

```text
S . X .
. X . .
. . . T
```

`X` represents an obstacle.

When generating neighbors:

```cpp
if (grid[nr][nc] == 'X')
    continue;
```

The obstacle is simply excluded from the graph.

---

# 10. Important BFS Principle: What Is a Node?

This is one of the most important concepts.

In simple BFS:

```text
Node = grid cell
```

Therefore:

```cpp
visited[r][c]
```

is sufficient.

But sometimes being at the same cell does **not** mean that we are in the same situation.

That leads to **State-Space BFS**.

---

# 11. State-Space BFS

Consider a problem where your situation depends on:

```text
position
energy
```

Being at:

```text
(3,5), energy = 2
```

is different from:

```text
(3,5), energy = 10
```

Even though the position is identical.

Therefore, the BFS node is no longer simply:

```text
(r,c)
```

It becomes:

```text
(r,c,energy)
```

Consequently, the visited structure must also distinguish them:

```cpp
visited[r][c][energy]
```

---

# 12. Why `visited[r][c]` Can Be Wrong

Suppose we reach cell `D` through two different paths:

```text
Path 1:
A → B → D

Path 2:
A → C → D
```

Assume the paths leave us with different resources:

```text
B → D gives energy = 2

C → D gives energy = 10
```

If we only use:

```cpp
visited[D]
```

then after reaching `D` once, we might reject the second arrival.

That can be incorrect.

The second arrival represents a different state.

Therefore:

```text
visited[cell]
```

is not enough.

We need:

```text
visited[cell][energy]
```

---

# 13. General Rule for State

A **state** must contain every piece of information that can affect what actions are possible in the future.

Ask:

> If I reach the same position again, can my future possibilities be different?

If the answer is **yes**, then position alone is not enough to describe the state.

For example:

```text
Position only:
(r,c)

Position + energy:
(r,c,energy)

Position + key status:
(r,c,keyStatus)

Position + energy + key status:
(r,c,energy,keyStatus)
```

The state becomes larger as more information affects future decisions.

---

# 14. State-Space BFS Mental Model

Think of every complete state as a node in a larger graph.

For example:

```text
(r,c,energy)
```

is one node.

A move produces another node:

```text
(r,c,energy)
        ↓ move
(nr,nc,newEnergy)
```

So BFS is still doing exactly the same thing:

```text
Current state
      ↓
Generate possible next states
      ↓
Check whether next state was visited
      ↓
Add next state
```

The only difference is that the "node" now contains more information.

---

# 15. Multi-Dimensional `visited`

If the state is:

```text
(r,c,energy)
```

then:

```cpp
visited[r][c][energy]
```

is natural.

If the state later becomes:

```text
(r,c,energy,someOtherState)
```

then:

```cpp
visited[r][c][energy][someOtherState]
```

may be required.

The dimensions of `visited` should correspond to the information that defines a unique state.

---

# 16. State Transition

Suppose the current state is:

```text
(r,c,energy)
```

and we move to:

```text
(nr,nc)
```

The new state might be:

```text
(nr,nc,energy-1)
```

because moving costs one unit of energy.

The general process is:

```text
Current state
(r,c,energy)
      ↓
Choose direction
      ↓
Calculate new position
(nr,nc)
      ↓
Calculate new state information
newEnergy
      ↓
Complete next state
(nr,nc,newEnergy)
```

---

# 17. Resources and State

A resource such as energy is not merely a variable.

If the amount of energy changes what moves are possible, then it is part of the BFS state.

For example:

```text
State A:
(5,5,energy=1)

State B:
(5,5,energy=10)
```

These are different states because their future possibilities are different.

This is a general pattern that appears in many problems:

```text
position + fuel
position + health
position + remaining moves
position + keys
position + switches
position + collected objects
```

---

# 18. Reset / Recharge Cells

Some problems contain a special cell that changes a resource.

For example:

```text
R = recharge/reset
```

Suppose:

```text
current energy = 2
maximum energy = 10
```

After entering `R`:

```text
new energy = 10
```

The important point is:

> **The next BFS state must contain the updated resource value.**

So:

```text
Current:
(r,c,2)

Move to R:

New:
(nr,nc,10)
```

The reset changes the state.

---

# 19. Do Not Mix Current State and New State

A very common source of bugs is checking or storing the old state when we actually need the new state.

Think in this exact order:

```text
Current state
      ↓
Try move
      ↓
Check whether move is possible
      ↓
Calculate new state
      ↓
Check visited for NEW state
      ↓
Mark NEW state visited
      ↓
Push NEW state
```

For example:

```cpp
int newEnergy = currEnergy - 1;
```

Then:

```cpp
if (visited[nr][nc][newEnergy])
    continue;
```

not:

```cpp
if (visited[nr][nc][currEnergy])
```

because we are entering the state with `newEnergy`.

---

# 20. The Complete State-Space BFS Pattern

A generic state-space BFS looks like:

```cpp
queue<State> q;

q.push(startState);
visited[startState] = true;

while (!q.empty()) {

    State current = q.front();
    q.pop();

    for (each possible move) {

        State next = calculateNextState(current, move);

        if (next is invalid)
            continue;

        if (visited[next])
            continue;

        if (next is target)
            return distance + 1;

        visited[next] = true;
        q.push(next);
    }
}

return -1;
```

The main skill is not memorizing this code.

The main skill is learning to answer:

1. What is a state?
2. What information defines a state?
3. What changes after a move?
4. What makes a move invalid?
5. What makes a state already visited?
6. What condition means we have reached the goal?

---

# 21. State vs Variable

Not every variable needs to be part of the BFS state.

For example:

```cpp
n
m
maximumEnergy
grid
```

are fixed throughout the problem.

They don't need to be stored in every queue element.

But:

```text
row
column
current energy
```

can change as BFS progresses, so they belong to the state.

A useful question is:

> **Can this value be different when I reach the same position through another path, and can that difference affect the future?**

If yes, it probably belongs in the state.

---

# 22. Example of State Identification

Suppose a problem says:

> A player moves through a grid. Each move consumes one energy. Some cells restore energy.

Start with:

```text
Where am I?
```

Answer:

```text
(r,c)
```

Then ask:

```text
Does anything else affect my future moves?
```

Yes:

```text
energy
```

Therefore:

```text
State = (r,c,energy)
```

And:

```text
visited[r][c][energy]
```

This state-identification process is much more important than memorizing a particular BFS template.

---

# 23. When Ordinary BFS Is Not Enough

BFS works directly when every transition has the same cost.

If the edges have different costs, for example:

```text
A → B = 1
A → C = 5
```

then ordinary BFS cannot generally guarantee the minimum cost.

Depending on the problem, other algorithms may be required, such as:

```text
0-1 BFS
Dijkstra
Bellman-Ford
```

So always check:

> **Are all moves equally expensive?**

---

# 24. Complexity of Grid BFS

For a normal `n × m` grid:

```text
Number of cells = n × m
```

Each cell has at most 4 neighbors.

Therefore:

```text
Time = O(n × m)
Space = O(n × m)
```

The constant `4` from the four directions is ignored in Big-O.

---

# 25. Complexity of State-Space BFS

Suppose the state is:

```text
(r,c,energy)
```

and:

```text
energy = 0 ... E
```

Then the number of possible states is approximately:

```text
n × m × (E + 1)
```

Each state has at most 4 transitions.

Therefore:

```text
Time = O(n × m × E)
Space = O(n × m × E)
```

This is a crucial difference from ordinary grid BFS.

The complexity depends on the **number of possible states**, not merely the number of grid cells.

---

# 26. A State Can Be Visited More Than Once Physically

A physical cell may be entered multiple times.

That is not necessarily a problem.

For example:

```text
(r,c,energy=2)
(r,c,energy=7)
```

are different states.

So:

```cpp
visited[r][c]
```

would incorrectly merge them.

But:

```cpp
visited[r][c][energy]
```

keeps them separate.

This is one of the central ideas of state-space BFS.

---

# 27. The Key Insight You Should Remember

Do **not** ask:

> "Have I visited this cell?"

Ask:

> **"Have I visited this exact state?"**

For simple BFS:

```text
State = position
```

so:

```cpp
visited[r][c]
```

For state-space BFS:

```text
State = position + additional information
```

so the additional information must also be represented in `visited`.

---

# 28. Common Mistakes

### Mistake 1: Using only position

```cpp
visited[r][c]
```

when energy/resources can differ.

### Mistake 2: Forgetting to update the state

For example, moving but forgetting:

```cpp
newEnergy = currEnergy - 1;
```

### Mistake 3: Checking the old state

Checking:

```cpp
visited[nr][nc][currEnergy]
```

instead of the new state.

### Mistake 4: Not marking the initial state

Always consider:

```cpp
visited[start] = true;
```

### Mistake 5: Treating all cells identically

Special cells may change the state:

```text
obstacle → cannot enter
reset → resource changes
target → goal condition
normal → state changes normally
```

### Mistake 6: Using BFS when edge costs differ

If moves have different costs, reconsider whether ordinary BFS is appropriate.

---

# 29. How to Approach a New Grid BFS Problem

When you see a new problem, use this checklist.

### Step 1 — Identify the position

Usually:

```text
(r,c)
```

### Step 2 — Identify what else changes

Ask:

```text
Energy?
Keys?
Remaining moves?
Collected objects?
Switch status?
Health?
```

### Step 3 — Define the complete state

For example:

```text
(r,c,energy)
```

### Step 4 — Define visited

Match it to the state:

```text
visited[r][c][energy]
```

### Step 5 — Define transitions

For every direction:

```text
up
down
left
right
```

calculate the next state.

### Step 6 — Handle special cells

Determine exactly how they modify the state.

### Step 7 — Determine the target

Ask:

```text
When can I stop BFS?
```

### Step 8 — Verify complexity

Calculate:

```text
number of possible states
×
number of transitions per state
```

This tells you whether the solution can fit within the constraints.

---

# 30. Final Mental Model

The most important progression is:

```text
Graph BFS
   ↓
Grid BFS
   ↓
Grid cell = node
   ↓
visited[r][c]
   ↓
What if position isn't enough?
   ↓
State-Space BFS
   ↓
State = position + relevant information
   ↓
visited must represent the complete state
```

The biggest lesson is:

> **BFS is not fundamentally about grids. BFS is about exploring states level by level.**

A grid is simply one way of representing those states.

Once additional information affects the future, that information becomes part of the state.

So instead of thinking:

```text
"I am at cell (r,c)"
```

think:

```text
"This is my complete situation:
(r,c,energy,...)
"
```

Then BFS explores those complete situations level by level.

That mental model is what allows you to move from simple Grid BFS problems to much harder **State-Space BFS** problems.
