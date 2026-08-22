# Q3. TriCore Yield Optimization

## Problem

You are given an integer array `a[]`, where `a[i]` represents the amount of minerals available in the `i`-th asteroid.

You must partition the **entire array** into contiguous groups.

Every group must contain **at least 3 elements**.

The profit of a group is its **third-smallest element**.

For example:

```text
Group = [8, 2, 5, 1, 10]

Sorted = [1, 2, 5, 8, 10]

Third-smallest = 5
```

Find the **maximum total profit** obtainable by partitioning the entire array into valid groups.

If it is impossible, return `-1`.

## Example 1

```text
a = [8, 2, 5, 1, 10, 6]

Output = 18
```

One optimal partition is:

```text
[8, 2, 5] | [1, 10, 6]
```

Their third-smallest values are:

```text
8 and 10
```

Therefore:

```text
8 + 10 = 18
```

## Example 2

```text
a = [5, 1, 3, 2]

Output = 3
```

Use the entire array as one group:

```text
[5, 1, 3, 2]
```

Sorted:

```text
[1, 2, 3, 5]
```

Third-smallest:

```text
3
```

Therefore:

```text
Answer = 3
```

## Memoization Idea

Define:

```text
fun(i) = maximum profit for partitioning a[i...n-1]
```

From index `i`, try every possible ending index `j`.

The current group is:

```text
a[i...j]
```

The group is valid only when:

```text
j - i + 1 >= 3
```

A group can have any length from `3` to `N`.

While extending the group, maintain the three smallest values:

```text
first  = smallest
second = second-smallest
third  = third-smallest
```

Then `third` is the profit of the current group.

The transition is:

```text
fun(i) = max(third + fun(j + 1))
```

## Base Cases

If all elements are used:

```text
i == n
```

return:

```text
0
```

If fewer than 3 elements remain, a valid group cannot be formed:

```text
n - i < 3
```

return `-INF`.

## Java Memoization Code

```java
class Solution {

    Integer[] dp;

    public int maxProfit(int[] a) {

        int n = a.length;

        dp = new Integer[n];

        int ans = fun(0, a);

        return ans == Integer.MIN_VALUE ? -1 : ans;
    }

    public int fun(int i, int[] a) {

        int n = a.length;

        if(i == n) {
            return 0;
        }

        if(n - i < 3) {
            return Integer.MIN_VALUE;
        }

        if(dp[i] != null) {
            return dp[i];
        }

        int first = Integer.MAX_VALUE;
        int second = Integer.MAX_VALUE;
        int third = Integer.MAX_VALUE;

        int ans = Integer.MIN_VALUE;

        for(int j = i; j < n; j++) {

            int x = a[j];

            // Maintain the three smallest values
            if(x <= first) {
                third = second;
                second = first;
                first = x;
            }
            else if(x <= second) {
                third = second;
                second = x;
            }
            else if(x < third) {
                third = x;
            }

            // Current group must contain at least 3 elements
            if(j - i + 1 >= 3) {

                int next = fun(j + 1, a);

                if(next != Integer.MIN_VALUE) {
                    ans = Math.max(ans, third + next);
                }
            }
        }

        return dp[i] = ans;
    }
}
```

## Complexity

```text
Time  : O(N²)
Space : O(N)
```

## Key Pattern

This is a:

```text
Partition DP + Memoization
```

The structure is similar to **LeetCode 1043 - Partition Array for Maximum Sum**.

The difference is that here:

```text
Group length = at least 3
Group profit = third-smallest element
```

instead of:

```text
Group length = 1 to k
Group profit = length × maximum element
```
