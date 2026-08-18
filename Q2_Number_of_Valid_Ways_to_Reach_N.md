# Q2. Number of Valid Ways to Reach N

## Problem

You start at position `0` and want to reach exactly position `N`.

You are given:

-   `steps` --- allowed jump sizes
-   `broken[i]` --- whether position `i` is broken

From position `i`, you may move forward by any value in `steps`.

You cannot land on a broken position.

Different orders of jumps are considered different ways.

Return the number of valid ways to reach exactly `N`.

## Example 1

``` text
N = 4
steps = [1, 2]
broken = [false, false, false, false, false]

Answer = 5
```

The five ways are:

``` text
1 + 1 + 1 + 1
1 + 1 + 2
1 + 2 + 1
2 + 1 + 1
2 + 2
```

## Example 2

``` text
N = 4
steps = [1, 2]
broken = [false, false, true, false, false]

Answer = 1
```

Position `2` is broken.

The only valid path is:

``` text
0 -> 1 -> 3 -> 4
```

## Memoization Idea

Define:

``` text
fun(n) = number of ways to reach position n
```

For every allowed step:

``` text
fun(n - steps[i])
```

contributes to the answer.

Therefore:

``` text
fun(n) = sum(fun(n - step))
```

for all allowed steps.

## Base Cases

If `n < 0`:

``` text
return 0
```

because we overshot the destination.

If position `n` is broken:

``` text
return 0
```

because we cannot land there.

If:

``` text
n == 0
```

we have successfully reached the starting position, so there is exactly
one way.

## Java Memoization Solution

``` java
class Main {

    public static void main(String[] args) {

        int[] steps = {1, 2};
        int n = 4;

        boolean[] broken = {
            false, false, false, false, false
        };

        int[] dp = new int[n + 1];

        for(int i = 0; i <= n; i++) {
            dp[i] = -1;
        }

        System.out.println(fun(n, steps, dp, broken));
    }

    public static int fun(int n, int[] steps,
                          int[] dp, boolean[] broken) {

        if(n < 0) {
            return 0;
        }

        if(broken[n]) {
            return 0;
        }

        if(n == 0) {
            return 1;
        }

        if(dp[n] != -1) {
            return dp[n];
        }

        int ans = 0;

        for(int i = 0; i < steps.length; i++) {
            ans += fun(n - steps[i], steps, dp, broken);
        }

        return dp[n] = ans;
    }
}
```

## Complexity

Let:

``` text
N = destination
K = number of allowed steps
```

Then:

``` text
Time  : O(N * K)
Space : O(N)
```

## Key Pattern

This is a **1D counting DP** problem.

The standard pattern is:

``` java
if(invalid)
    return 0;

if(base)
    return 1;

if(dp[n] != -1)
    return dp[n];

for(each choice)
    ans += fun(nextState);

return dp[n] = ans;
```

Different jump orders count separately.
