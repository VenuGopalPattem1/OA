# Q1. Minimum Cost to Reach N

## Problem

You are standing at position `0` and want to reach exactly position `N`.

You are given an integer array `cost` of length `N + 1`, where `cost[i]`
is the cost of landing on position `i`.

From position `i`, you can move:

-   `i + 1`
-   `i + 2`

You do not pay the cost of position `0`.

Find the minimum total cost required to reach exactly `N`.

## Example

``` text
cost = [0, 3, 2, 6, 1, 4]
N = 5

Optimal path:
0 -> 2 -> 4 -> 5

Cost = 2 + 1 + 4 = 7

Answer = 7
```

## Memoization Idea

Define:

``` text
fun(i) = minimum cost to reach N starting from position i
```

At every position we have two choices:

``` text
i -> i + 1
i -> i + 2
```

Therefore:

``` text
fun(i) = cost[i] + min(fun(i + 1), fun(i + 2))
```

Position `0` has cost `0`, so the same recurrence can be used for every
position.

## Base Cases

If we reach exactly `N`:

``` text
i == N
```

the cost is `0`.

If we go beyond `N`:

``` text
i > N
```

that path is invalid, so return a very large value.

## Java Memoization Solution

``` java
class Main {

    public static void main(String[] args) {

        int[] cost = {0, 3, 2, 6, 1, 4};
        int n = cost.length;

        int[] dp = new int[n];

        for(int i = 0; i < n; i++) {
            dp[i] = -1;
        }

        System.out.println(fun(0, n, cost, dp));
    }

    public static int fun(int i, int n, int[] cost, int[] dp) {

        if(i >= n) {
            return Integer.MAX_VALUE / 2;
        }

        if(i == n - 1) {
            return cost[i];
        }

        if(dp[i] != -1) {
            return dp[i];
        }

        int x = cost[i] + fun(i + 1, n, cost, dp);
        int y = cost[i] + fun(i + 2, n, cost, dp);

        return dp[i] = Math.min(x, y);
    }
}
```

## Complexity

``` text
Time  : O(N)
Space : O(N)
```

## Key Pattern

This is a simple **1D top-down DP** problem.

``` text
dp[i] = answer from index i
```

The important part is handling overshooting correctly.
