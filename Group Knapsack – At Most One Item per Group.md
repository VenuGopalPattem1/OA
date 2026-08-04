# Group Knapsack – At Most One Item per Group

## Problem Description

You are given **N** consecutive groups of items and a knapsack with a maximum weight capacity **W**.

Each group contains exactly **S** item slots. Some slots may contain **empty placeholder items**, represented by:

```text
weight = -1
value = -1
```

Each valid item has a **weight** and a **value**.

Your task is to maximize the total value of the selected items while satisfying the following constraints.

---

# Rules

* You may select **at most one item** from each group.
* If you select an item from **group `i`**, you **cannot** select any item from:

  * Group `i-1`
  * Group `i+1`
* The total weight of all selected items must not exceed **W**.
* Empty items (`-1, -1`) cannot be selected.
* You may also skip any group.

Return the **maximum total value**.

---

# Input Format

* First line contains an integer `N` (number of groups).
* Second line contains an integer `W` (maximum knapsack capacity).
* Third line contains an integer `S` (number of item slots in each group).
* Fourth line contains an integer `M`, where `M = N × S`.
* The next `M` lines contain:

```text
weight value
```

representing the items of each group.

---

# Output Format

Print a single integer representing the maximum total value.

---

# Constraints

```text
1 ≤ N ≤ 100
1 ≤ W ≤ 1000
1 ≤ S ≤ 20
M = N × S
```

---

# Test Case 1

### Input

```text
N = 3
W = 5
S = 2

Group 1
2 6
3 8

Group 2
2 5
4 9

Group 3
1 4
-1 -1
```

### Output

```text
12
```

### Explanation

Choose:

* Group 1 → `(3,8)`
* Group 3 → `(1,4)`

These groups are **not adjacent**.

Total weight:

```text
3 + 1 = 4
```

Total value:

```text
8 + 4 = 12
```

This is the maximum possible value.

---

# Test Case 2

### Input

```text
N = 4
W = 7
S = 2

Group 1
2 6
3 7

Group 2
4 10
-1 -1

Group 3
2 8
3 9

Group 4
1 5
5 12
```

### Output

```text
16
```

### Explanation

Possible valid selections:

| Groups  | Items         | Weight | Value |
| ------- | ------------- | -----: | ----: |
| G1      | (3,7)         |      3 |     7 |
| G2      | (4,10)        |      4 |    10 |
| G3      | (3,9)         |      3 |     9 |
| G4      | (5,12)        |      5 |    12 |
| G1 + G3 | (3,7), (3,9)  |      6 |    16 |
| G2 + G4 | (4,10), (1,5) |      5 |    15 |

Invalid selections:

* G2 + G3 (adjacent groups)
* G3 + G4 (adjacent groups)
* G1 + G4 using `(5,12)` exceeds capacity.

The maximum value is:

```text
16
```

---

# Approach

This problem combines two classic DP problems:

* **0/1 Knapsack** (weight constraint)
* **House Robber** (cannot choose adjacent groups)

For every group, we have two choices.

---

## Choice 1: Skip the Current Group

Move to the next group.

```text
solve(group + 1, capacity)
```

---

## Choice 2: Pick One Item

Try every valid item in the current group.

If the item fits in the remaining capacity:

* Add its value.
* Since adjacent groups cannot be selected, move to `group + 2`.

```text
item.value + solve(group + 2, capacity - item.weight)
```

Take the maximum of all possible choices.

---

# DP State

```text
dp[group][capacity]
```

Where:

* `group` → Current group being processed.
* `capacity` → Remaining knapsack capacity.

The state stores:

> Maximum value obtainable starting from the current group with the given remaining capacity.

---

# State Transition

```text
solve(group, capacity)

=

max(

Skip current group,

Choose Item 1,

Choose Item 2,

...

Choose Item S

)
```

---

# Base Case

When all groups have been processed:

```java
if(group >= n)
    return 0;
```

No more value can be obtained.

---

# Memoization Solution (Java)

```java
class Solution {

    static class Item {
        int weight;
        int value;

        Item(int w, int v) {
            weight = w;
            value = v;
        }
    }

    Integer[][] dp;
    Item[][] groups;
    int n, s;

    public int maxValue(Item[][] groups, int W) {

        this.groups = groups;
        this.n = groups.length;
        this.s = groups[0].length;

        dp = new Integer[n][W + 1];

        return solve(0, W);
    }

    private int solve(int group, int capacity) {

        if (group >= n)
            return 0;

        if (dp[group][capacity] != null)
            return dp[group][capacity];

        // Skip current group
        int ans = solve(group + 1, capacity);

        // Choose one item from current group
        for (int i = 0; i < s; i++) {

            Item item = groups[group][i];

            if (item.weight == -1)
                continue;

            if (item.weight <= capacity) {

                ans = Math.max(ans,
                        item.value +
                        solve(group + 2,
                              capacity - item.weight));
            }
        }

        return dp[group][capacity] = ans;
    }
}
```

---

# Dry Run

Suppose:

```text
W = 5

Group 1
(2,6)
(3,8)

Group 2
(2,5)
(4,9)

Group 3
(1,4)
```

Start from:

```text
solve(0,5)
```

Choices:

### Skip Group 1

```text
solve(1,5)
```

---

### Pick (2,6)

Remaining capacity:

```text
5 - 2 = 3
```

Move to Group 3:

```text
6 + solve(2,3)
```

---

### Pick (3,8)

Remaining capacity:

```text
5 - 3 = 2
```

Move to Group 3:

```text
8 + solve(2,2)
```

Take the maximum of these three choices.

---

# Why `group + 2`?

If we select an item from the current group,

```text
Group i
```

then

```text
Group i-1 ❌

Group i ✅

Group i+1 ❌
```

The next possible group is

```text
Group i+2
```

Hence,

```java
solve(group + 2, ...)
```

---

# Why Don't We Need a Previous Group Variable?

Some may think we need:

```text
(group, capacity, previousSelected)
```

We don't.

Whenever we choose a group, we immediately jump to:

```text
group + 2
```

Thus, the adjacency rule is automatically enforced.

---

# Complexity Analysis

### Time Complexity

There are:

* `N` groups
* `W` capacities

For every state, we try at most `S` items.

```text
O(N × W × S)
```

---

### Space Complexity

DP table:

```text
O(N × W)
```

Recursion stack:

```text
O(N)
```

Overall:

```text
O(N × W)
```

---

# Similar Problems

* LeetCode 198 – House Robber
* LeetCode 213 – House Robber II
* LeetCode 416 – Partition Equal Subset Sum
* LeetCode 474 – Ones and Zeroes
* GeeksforGeeks – 0/1 Knapsack
* Group Knapsack Variants

---

# Concepts Used

* Dynamic Programming
* Memoization
* 0/1 Knapsack
* House Robber
* Group Knapsack
* State Transition
* Recursion
* Capacity Optimization
