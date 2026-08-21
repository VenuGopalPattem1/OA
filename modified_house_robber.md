# Q2. Modified House Robber

## Problem

You are given an integer array `profit[]`, where `profit[i]` represents the profit obtainable from robbing the `i`-th house.

You cannot rob two adjacent houses.

Additionally, if two houses are robbed consecutively in your chosen sequence, the absolute difference between their profits must be at least `D`.

For two consecutively robbed houses `j` and `i`:

```text
i - j >= 2
```

and:

```text
|profit[i] - profit[j]| >= D
```

Find the maximum total profit that can be obtained.

## Example 1

```text
profit = [5, 10, 4, 12]
D = 6

Output = 17
```

Choose houses `0` and `3`:

```text
5 + 12 = 17
```

They are non-adjacent and:

```text
|12 - 5| = 7 >= 6
```

## Example 2

```text
profit = [5, 10, 4]
D = 6

Output = 10
```

Houses `0` and `2` are non-adjacent, but:

```text
|5 - 4| = 1 < 6
```

So they cannot both be robbed. The best choice is house `1`:

```text
Answer = 10
```

## Memoization Idea

Define:

```text
fun(i, prev)
```

where:

- `i` = current house
- `prev` = previously robbed house

Initially no house has been robbed, so use:

```text
prev = N
```

The initial call is:

```text
fun(0, N)
```

## Choices

### Do Not Rob Current House

```text
fun(i + 1, prev)
```

### Rob Current House

If there is no previous house:

```text
prev == N
```

we can rob the current house directly.

Otherwise, both conditions must hold:

```text
i - prev >= 2
```

and:

```text
Math.abs(a[i] - a[prev]) >= d
```

Then:

```text
a[i] + fun(i + 1, i)
```

## Base Case

When all houses are processed:

```text
i == N
```

return `0`.

## Java Memoization Solution

```java
class Solution {

    Integer[][] dp;

    public int rob(int[] a, int d) {

        int n = a.length;

        dp = new Integer[n + 1][n + 1];

        return fun(0, n, a, d);
    }

    public int fun(int i, int prev, int[] a, int d) {

        if(i == a.length) {
            return 0;
        }

        if(dp[i][prev] != null) {
            return dp[i][prev];
        }

        // Don't rob current house
        int notTake = fun(i + 1, prev, a, d);

        // Rob current house
        int take = 0;

        if(prev == a.length ||
           (i - prev >= 2 &&
            Math.abs(a[i] - a[prev]) >= d)) {

            take = a[i] + fun(i + 1, i, a, d);
        }

        return dp[i][prev] = Math.max(take, notTake);
    }
}
```

## Why `prev = N`?

Valid array indices are:

```text
0 to N - 1
```

So `N` is used as a special value meaning:

```text
No previous house has been robbed.
```

Therefore the first house can be selected without checking the difference condition.

## Complexity

```text
Time  : O(N²)
Space : O(N²)
```

## Key Pattern

This is a **2D memoization DP**:

```text
fun(currentIndex, previousRobbedIndex)
```

At every index:

```text
Take
or
Not Take
```

The take condition is:

```java
prev == N ||
(i - prev >= 2 && Math.abs(a[i] - a[prev]) >= d)
```
