# Q22. Number of Ways to Reach N with Step Set (Avoiding Broken Stairs)

**Source:** SP_DSE_SP-Off Campus-Test-01-Aug-26-02 PM — Question 2

## Problem Statement

You are starting at stair `0`, and you want to reach exactly stair `N`.

At every step, you can move by one of these step sizes:

```text
X, Y, or Z
```

However, there are `M` broken stairs that you are **strictly not allowed to step on**.

Find the number of valid **ordered step sequences** you can take to reach exactly stair `N` without ever landing on a broken stair.

Return the answer modulo:

```text
10^9 + 7
```

---

## Input Format

The available part of the input visible from the source is:

```text
The first line contains an integer N, denoting the target stair.

The second line contains an integer X, denoting step size X.

The third line contains an integer Y, denoting step size Y.

The fourth line contains an integer Z, denoting step size Z.

The fifth line contains an integer M, denoting the number of broken stairs.
```

**Note:** The remainder of the original input format was cut off in the source screenshot. The solution below assumes the next `M` values are the broken stair positions.

---

# Example 1

Suppose:

```text
N = 5
X = 1
Y = 2
Z = 3
Broken stairs = {4}
```

We need to reach:

```text
5
```

We cannot land on stair `4`.

Some valid sequences are:

```text
1 + 1 + 1 + 1 + 1
1 + 1 + 1 + 2
1 + 1 + 2 + 1
1 + 2 + 1 + 1
2 + 1 + 1 + 1
2 + 2 + 1
2 + 1 + 2
1 + 2 + 2
3 + 2
2 + 3
1 + 1 + 3
1 + 3 + 1
3 + 1 + 1
```

Any sequence that lands on stair `4` is invalid.

---

# Example 2

```text
N = 4
X = 1
Y = 2
Z = 3
Broken stairs = {2}
```

We cannot land on stair `2`.

Possible valid sequences include:

```text
1 + 1 + 1 + 1
1 + 3
3 + 1
```

Sequences such as:

```text
2 + 2
1 + 1 + 2
2 + 1 + 1
```

are invalid because they land on broken stair `2`.

Therefore the answer is:

```text
3
```

---

# DP Idea

Let:

```text
dp[i]
```

represent the number of ways to reach stair `i`.

We start from stair `0`.

There is exactly one way to be at stair `0`:

```text
dp[0] = 1
```

That represents:

```text
Take no steps.
```

---

# Transition

To reach stair `i`, the last step could have been:

```text
X
Y
Z
```

Therefore:

```text
dp[i] = dp[i-X] + dp[i-Y] + dp[i-Z]
```

But there is one important condition:

If stair `i` is broken, we cannot land there.

So:

```text
if i is broken:
    dp[i] = 0
```

Otherwise:

```text
dp[i] = dp[i-X] + dp[i-Y] + dp[i-Z]
```

Take modulo `10^9 + 7`.

---

# Memoization Approach

We can use recursion.

Define:

```text
fun(i)
```

as the number of ways to reach stair `i`.

For every stair:

1. If `i < 0`, return `0`.
2. If `i` is broken, return `0`.
3. If `i == 0`, return `1`.
4. Otherwise try the three previous positions:
   - `i - X`
   - `i - Y`
   - `i - Z`

So:

```text
fun(i)
=
fun(i-X)
+
fun(i-Y)
+
fun(i-Z)
```

---

# Java Memoization Code

```java
import java.util.*;

class Solution {

    static final long MOD = 1000000007;

    long[] dp;
    boolean[] broken;

    public long solve(int N, int X, int Y, int Z,
                      int M, int[] brokenStairs) {

        dp = new long[N + 1];
        Arrays.fill(dp, -1);

        broken = new boolean[N + 1];

        // Mark broken stairs
        for (int stair : brokenStairs) {
            if (stair >= 0 && stair <= N) {
                broken[stair] = true;
            }
        }

        return fun(N, X, Y, Z);
    }

    private long fun(int n, int X, int Y, int Z) {

        // Outside the staircase
        if (n < 0) {
            return 0;
        }

        // Cannot land on a broken stair
        if (broken[n]) {
            return 0;
        }

        // Starting stair
        if (n == 0) {
            return 1;
        }

        // Already calculated
        if (dp[n] != -1) {
            return dp[n];
        }

        long ans = 0;

        // Take step X
        ans = (ans + fun(n - X, X, Y, Z)) % MOD;

        // Take step Y
        ans = (ans + fun(n - Y, X, Y, Z)) % MOD;

        // Take step Z
        ans = (ans + fun(n - Z, X, Y, Z)) % MOD;

        dp[n] = ans;

        return ans;
    }
}
```

---

# Very Simple Explanation of the Code

Suppose:

```text
N = 5
X = 1
Y = 2
Z = 3
```

To reach stair `5`, the last step can be:

```text
1
2
3
```

Therefore:

```text
ways(5)
=
ways(4)
+
ways(3)
+
ways(2)
```

But if stair `4` is broken:

```text
ways(4) = 0
```

So:

```text
ways(5)
=
0
+
ways(3)
+
ways(2)
```

That is the main idea.

---

# Dry Run

Take:

```text
N = 5
X = 1
Y = 2
Z = 3
Broken = {4}
```

We want:

```text
fun(5)
```

To reach `5`, we can come from:

```text
5 - 1 = 4
5 - 2 = 3
5 - 3 = 2
```

Therefore:

```text
fun(5)
=
fun(4)
+
fun(3)
+
fun(2)
```

---

## fun(4)

Stair `4` is broken.

Therefore:

```text
fun(4) = 0
```

We do not calculate any further from stair `4`.

---

## fun(3)

Stair `3` is not broken.

Possible previous stairs:

```text
3 - 1 = 2
3 - 2 = 1
3 - 3 = 0
```

Therefore:

```text
fun(3)
=
fun(2)
+
fun(1)
+
fun(0)
```

---

## fun(2)

Previous stairs:

```text
2 - 1 = 1
2 - 2 = 0
2 - 3 = -1
```

Therefore:

```text
fun(2)
=
fun(1)
+
fun(0)
+
fun(-1)
```

`fun(-1)` returns:

```text
0
```

because we cannot go below stair `0`.

---

## fun(1)

Previous stairs:

```text
1 - 1 = 0
1 - 2 = -1
1 - 3 = -2
```

Therefore:

```text
fun(1)
=
fun(0)
+
fun(-1)
+
fun(-2)
```

We know:

```text
fun(0) = 1
fun(-1) = 0
fun(-2) = 0
```

So:

```text
fun(1) = 1
```

---

## fun(2)

We already know:

```text
fun(1) = 1
fun(0) = 1
fun(-1) = 0
```

Therefore:

```text
fun(2) = 1 + 1 + 0
        = 2
```

Ways:

```text
1 + 1
2
```

---

## fun(3)

We know:

```text
fun(2) = 2
fun(1) = 1
fun(0) = 1
```

Therefore:

```text
fun(3) = 2 + 1 + 1
        = 4
```

Ways:

```text
1 + 1 + 1
1 + 2
2 + 1
3
```

---

## fun(5)

Remember:

```text
fun(5)
=
fun(4)
+
fun(3)
+
fun(2)
```

We have:

```text
fun(4) = 0
fun(3) = 4
fun(2) = 2
```

Therefore:

```text
fun(5) = 0 + 4 + 2
        = 6
```

### Answer

```text
6
```

---

# Why Broken Stairs Are Easy to Handle

The important condition is:

```java
if (broken[n]) {
    return 0;
}
```

For example, if:

```text
broken = {4}
```

then:

```text
fun(4) = 0
```

This automatically removes every sequence that lands on `4`.

For example:

```text
1 + 3
```

to reach `4` is invalid.

So when another stair tries to use `fun(4)`, it gets:

```text
0
```

---

# Why Memoization Is Needed

Without memoization, the same stair can be calculated many times.

For example:

```text
fun(5)
    -> fun(4)
    -> fun(3)
        -> fun(2)
        -> fun(1)
        -> fun(0)
    -> fun(2)
```

Notice that:

```text
fun(2)
```

can be calculated more than once.

With:

```java
if (dp[n] != -1) {
    return dp[n];
}
```

we calculate each stair only once.

---

# Base Cases to Remember

There are three important cases.

## 1. Negative Stair

```java
if (n < 0) {
    return 0;
}
```

We cannot go below stair `0`.

---

## 2. Broken Stair

```java
if (broken[n]) {
    return 0;
}
```

We are not allowed to land there.

---

## 3. Stair 0

```java
if (n == 0) {
    return 1;
}
```

There is exactly one way to reach stair `0`:

```text
Take no steps.
```

---

# Important Formula

The main formula is:

```text
ways(n)
=
ways(n-X)
+
ways(n-Y)
+
ways(n-Z)
```

Except when `n` is broken:

```text
ways(n) = 0
```

---

# Complexity

There are `N` possible stairs.

Each stair has at most 3 transitions:

```text
X
Y
Z
```

Because of memoization, every stair is calculated only once.

Therefore:

```text
Time Complexity:  O(N)

Space Complexity: O(N)
```

---

# Final Summary

Remember these 4 things:

```text
1. dp[n] = number of ways to reach stair n

2. If stair n is broken:
      dp[n] = 0

3. Otherwise:
      dp[n] =
          dp[n-X]
        + dp[n-Y]
        + dp[n-Z]

4. dp[0] = 1
```

The key idea is:

```text
Broken stair -> return 0
Negative stair -> return 0
Stair 0 -> return 1
Otherwise -> try X, Y, Z
```
