# Paint Fence with Fatigue Cost

## Problem Description

You are given **N** fence posts and **K** available colors.

Painting the **i-th** post with the **j-th** color costs `cost[i][j]`.

Additionally, each color has a **fatigue penalty** `fatigue[j]`. Whenever **two adjacent fence posts** are painted with the same color, the corresponding fatigue penalty is added **once** for that adjacent pair.

Your task is to paint every fence post such that the **total cost is minimized**.

The total cost consists of:

* Painting cost of every post.
* Fatigue penalties for adjacent posts painted with the same color.

### Rules

* Every post must be painted with exactly one color.
* Two consecutive posts **may** have the same color.
* **Three consecutive posts cannot have the same color.**
* If two adjacent posts have the same color `c`, add `fatigue[c]` once for that pair.

Return the **minimum possible total painting cost**.

---

## Input Format

* The first line contains two integers `N` and `K`.
* The next `N` lines each contain `K` integers where `cost[i][j]` represents the cost of painting the `i-th` post with the `j-th` color.
* The last line contains `K` integers representing the fatigue penalty of each color.

---

## Output Format

Print a single integer representing the minimum total painting cost.

---

## Example

### Input

```text
3 2

1 5
2 3
4 1

2 4
```

### Output

```text
6
```

### Explanation

Choose the coloring:

```text
0 0 1
```

Painting cost:

```text
1 + 2 + 1 = 4
```

Fatigue:

* Posts 1 and 2 have the same color (0), so add fatigue of color 0.

```text
+2
```

Total cost:

```text
4 + 2 = 6
```

The coloring

```text
0 0 0
```

is invalid because three consecutive posts cannot have the same color.

---

# Memoization Solution (Top-Down DP)

## Approach

We solve the problem using **Dynamic Programming with Memoization**.

For every fence post, we try all possible colors while keeping track of:

* Which post we are currently painting.
* The color used for the previous post.
* How many consecutive posts have been painted with that color.

This information is enough to:

* Prevent three consecutive posts with the same color.
* Apply fatigue penalties correctly.
* Compute the minimum total cost.

---

## DP State

```text
dp[index][prevColor][count]
```

Where:

* `index` → Current fence post.
* `prevColor` → Color of the previous post.
* `count` → Number of consecutive posts painted with `prevColor`.

Possible values of `count`:

* `0` → No previous color (initial state)
* `1` → One consecutive occurrence
* `2` → Two consecutive occurrences

---

## State Transition

### Case 1: Paint with a Different Color

* Add only the painting cost.
* Reset consecutive count to `1`.

```text
cost[index][color] + solve(index + 1, color, 1)
```

---

### Case 2: Paint with the Same Color

Allowed only when:

```text
count < 2
```

Then,

* Add painting cost.
* Add fatigue penalty.
* Increase consecutive count.

```text
cost[index][color]
+ fatigue[color]
+ solve(index + 1, color, count + 1)
```

If `count == 2`, this choice is invalid because it would create three consecutive posts with the same color.

---

## Base Case

When all fence posts have been painted:

```java
if(index == n)
    return 0;
```

---

## Memoization

Before solving any state:

```java
if(dp[index][prevColor][count] != null)
    return dp[index][prevColor][count];
```

This avoids recomputing already solved subproblems.

---

## Java Code

```java
class Solution {

    int[][] cost;
    int[] fatigue;
    int n, k;
    Integer[][][] dp;

    public int minimumCost(int[][] cost, int[] fatigue) {

        this.cost = cost;
        this.fatigue = fatigue;

        n = cost.length;
        k = cost[0].length;

        // k represents "no previous color"
        dp = new Integer[n][k + 1][3];

        return solve(0, k, 0);
    }

    int solve(int idx, int prevColor, int cnt) {

        if (idx == n)
            return 0;

        if (dp[idx][prevColor][cnt] != null)
            return dp[idx][prevColor][cnt];

        int ans = Integer.MAX_VALUE;

        for (int color = 0; color < k; color++) {

            int currCost = cost[idx][color];

            if (prevColor == k || color != prevColor) {

                ans = Math.min(ans,
                        currCost + solve(idx + 1, color, 1));

            } else {

                if (cnt < 2) {

                    ans = Math.min(ans,
                            currCost + fatigue[color]
                                    + solve(idx + 1, color, cnt + 1));
                }
            }
        }

        return dp[idx][prevColor][cnt] = ans;
    }
}
```

---

## Complexity Analysis

### Time Complexity

There are:

* `N` fence posts
* `K + 1` possible previous colors
* `3` possible consecutive counts

Each state tries all `K` colors.

**Time Complexity**

```text
O(N × K²)
```

### Space Complexity

DP table:

```text
O(N × K)
```

Recursion stack:

```text
O(N)
```

Overall:

```text
O(N × K)
```

---

# Related Problems

If you enjoyed this problem, try these similar Dynamic Programming problems:

1. **LeetCode 276 – Paint Fence**

   * Count the number of valid ways to paint fence posts with no three consecutive posts having the same color.

2. **LeetCode 256 – Paint House**

   * Minimize the painting cost such that adjacent houses have different colors.

3. **LeetCode 265 – Paint House II**

   * Optimized version of Paint House with `K` colors.

4. **LeetCode 746 – Min Cost Climbing Stairs**

   * Minimize cumulative cost using dynamic programming.

5. **LeetCode 198 – House Robber**

   * Classic DP problem involving decisions based on previous states.

6. **LeetCode 740 – Delete and Earn**

   * Dynamic programming with state transitions similar to House Robber.

7. **Ninja's Training (Coding Ninjas)**

   * DP with previous-state tracking and multiple choices.

---

## Concepts Used

* Dynamic Programming
* Memoization (Top-Down DP)
* Multi-dimensional DP
* State Transition
* Previous State Tracking
* Cost Minimization
* Recursion with Memoization
* Constraint Handling
