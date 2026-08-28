# Ninja and his Friends

## Problem Description

You are given an `R x C` grid where each cell contains chocolates.

- Alice starts at `(0, 0)`.
- Bob starts at `(0, C - 1)`.
- Both move to the next row at every step.
- From `(i, j)`, each can move to `(i+1, j-1)`, `(i+1, j)`, or `(i+1, j+1)`.
- They must stay inside the grid.
- If both land on the same cell, count its chocolates only once.
- Find the maximum chocolates they can collect.

## Example

Input:

```text
grid = [
    [2, 3, 1, 2],
    [3, 4, 2, 2],
    [5, 6, 3, 5]
]
```

Output:

```text
21
```

One optimal path:

```text
Alice: (0,0) -> (1,1) -> (2,1)
       2          4          6 = 12

Bob:   (0,3) -> (1,3) -> (2,3)
       2          2          5 = 9
```

Total = `21`.

## DP Idea

At first it looks like a 4D DP:

```text
dp[rowAlice][colAlice][rowBob][colBob]
```

But Alice and Bob are always on the same row, so:

```text
rowAlice == rowBob
```

Therefore we only need:

```text
dp[row][colAlice][colBob]
```

State:

```text
fun(i, j1, j2)
```

- `i` = current row
- `j1` = Alice's column
- `j2` = Bob's column

## Why Two Loops?

Each person has 3 choices:

```text
-1 = left
 0 = same column
+1 = right
```

Therefore there are:

```text
3 x 3 = 9
```

combinations.

The double loop simply tries all combinations:

```java
for (int d1 = -1; d1 <= 1; d1++) {
    for (int d2 = -1; d2 <= 1; d2++) {
        ...
    }
}
```

It does not make the DP 4D. The DP is still 3D.

## Memoization Code

```java
import java.util.*;

class Solution {

    int[][][] dp;

    public int maximumChocolates(int[][] grid) {

        int n = grid.length;
        int m = grid[0].length;

        dp = new int[n][m][m];

        for (int[][] x : dp) {
            for (int[] y : x) {
                Arrays.fill(y, -1);
            }
        }

        return fun(0, 0, m - 1, grid);
    }

    int fun(int i, int j1, int j2, int[][] grid) {

        int n = grid.length;
        int m = grid[0].length;

        if (j1 < 0 || j1 >= m ||
            j2 < 0 || j2 >= m) {
            return Integer.MIN_VALUE;
        }

        if (i == n - 1) {

            if (j1 == j2) {
                return grid[i][j1];
            }

            return grid[i][j1] + grid[i][j2];
        }

        if (dp[i][j1][j2] != -1) {
            return dp[i][j1][j2];
        }

        int chocolates;

        if (j1 == j2) {
            chocolates = grid[i][j1];
        } else {
            chocolates = grid[i][j1] + grid[i][j2];
        }

        int ans = Integer.MIN_VALUE;

        for (int d1 = -1; d1 <= 1; d1++) {

            for (int d2 = -1; d2 <= 1; d2++) {

                int next = fun(
                    i + 1,
                    j1 + d1,
                    j2 + d2,
                    grid
                );

                ans = Math.max(ans, next);
            }
        }

        return dp[i][j1][j2] = chocolates + ans;
    }
}
```

## Same Cell

If:

```java
j1 == j2
```

both are on the same cell, so count it once:

```java
chocolates = grid[i][j1];
```

Otherwise:

```java
chocolates = grid[i][j1] + grid[i][j2];
```

## Complexity

There are `R * C * C` states.

Each state tries at most 9 transitions.

```text
Time  = O(R * C^2)
Space = O(R * C^2)
```

Recursion uses an additional `O(R)` stack space.

## Key Point

Remember:

```text
4D:
(rowAlice, colAlice, rowBob, colBob)

Because rowAlice == rowBob:

3D:
(row, colAlice, colBob)
```

The two loops are only for the `3 x 3 = 9` possible movement combinations.
