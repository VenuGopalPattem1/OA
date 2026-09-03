# Q21. Number of Compositions of N with Parts in {1,...,K}

**Source:** SP_DSE_SP-Off Campus-Test-01-Aug-26-02 PM — Question 2

## Problem Statement

You are given two integers `N` and `K`.

A valid sequence is an **ordered sequence** of positive integers where:

- Each part is between `1` and `K`.
- The sum of all parts is exactly `N`.
- The value of a valid sequence is its **length**.

Find the **sum of values (lengths) over all valid sequences**.

Since the answer can be large, return it modulo:

```text
10^9 + 7
```

## Input Format

The first line contains an integer `N`.

The second line contains an integer `K`.

## Constraints

```text
1 <= N <= 10^5
1 <= K <= N
```

## Example 1

### Input

```text
N = 3
K = 2
```

Allowed parts:

```text
1, 2
```

Valid sequences:

```text
[1,1,1] -> length = 3
[1,2]   -> length = 2
[2,1]   -> length = 2
```

Sum of lengths:

```text
3 + 2 + 2 = 7
```

### Output

```text
7
```

## Example 2

### Input

```text
N = 4
K = 2
```

Valid sequences:

```text
[1,1,1,1] -> length = 4
[1,1,2]   -> length = 3
[1,2,1]   -> length = 3
[2,1,1]   -> length = 3
[2,2]     -> length = 2
```

Sum:

```text
4 + 3 + 3 + 3 + 2 = 15
```

### Output

```text
15
```

# Understanding the Problem

We are not just finding the **number of ways** to make `N`.

We need:

```text
Sum of lengths of all ways
```

For example:

```text
N = 3
K = 2
```

Ways:

```text
[1,1,1] -> length 3
[1,2]   -> length 2
[2,1]   -> length 2
```

Number of ways:

```text
3
```

Required answer:

```text
3 + 2 + 2 = 7
```

# DP Idea

We need two values:

```text
count[n]
```

= Number of valid sequences whose sum is `n`.

And:

```text
sumLen[n]
```

= Sum of lengths of all valid sequences whose sum is `n`.

## Base Case

For sum `0`, there is one empty sequence.

```text
count[0] = 1
sumLen[0] = 0
```

The empty sequence has length `0`.

# Transition

Suppose we are calculating the answer for `n`.

The last number can be:

```text
1, 2, 3, ..., K
```

Suppose we choose `x` as the last number.

Before adding `x`, we need to make:

```text
n - x
```

So we use:

```text
count[n-x]
sumLen[n-x]
```

## Count Transition

Every sequence that makes `n-x` can append `x`.

Therefore:

```text
count[n] += count[n-x]
```

## Length Transition

Suppose an old sequence has length `L`.

After adding `x`, its length becomes:

```text
L + 1
```

Therefore:

```text
sumLen[n] += sumLen[n-x] + count[n-x]
```

Why do we add `count[n-x]`?

Because **every old sequence gets one extra element**.

# Memoization Solution

```java
class Solution {

    static final long MOD = 1000000007;

    Long[] count;
    Long[] sumLength;
    int K;

    public int solve(int N, int K) {

        this.K = K;

        count = new Long[N + 1];
        sumLength = new Long[N + 1];

        fun(N);

        return (int)(sumLength[N] % MOD);
    }

    void fun(int n) {

        if (n == 0) {
            count[0] = 1L;
            sumLength[0] = 0L;
            return;
        }

        if (count[n] != null) {
            return;
        }

        long cnt = 0;
        long len = 0;

        for (int x = 1; x <= K && x <= n; x++) {

            fun(n - x);

            cnt = (cnt + count[n - x]) % MOD;

            len = (len
                    + sumLength[n - x]
                    + count[n - x]) % MOD;
        }

        count[n] = cnt;
        sumLength[n] = len;
    }
}
```

# Simple Explanation of the Code

For every `n`, we try all possible last parts:

```text
1, 2, ..., K
```

For example, if:

```text
n = 3
K = 2
```

we try:

```text
last part = 1
last part = 2
```

### Last part = 1

We need to make:

```text
3 - 1 = 2
```

So use the answers for `2`.

### Last part = 2

We need to make:

```text
3 - 2 = 1
```

So use the answers for `1`.

This gives all sequences of sum `3`.

# Dry Run

Take:

```text
N = 3
K = 2
```

## fun(0)

Base case:

```text
count = 1
sumLen = 0
```

This represents the empty sequence.

## fun(1)

Only part `1` is possible.

```text
[1]
```

Therefore:

```text
count[1] = 1
sumLen[1] = 1
```

## fun(2)

Possible last parts:

```text
1
2
```

### Last part = 1

Use `fun(1)`:

```text
count = 1
sumLen = 1
```

After adding one element:

```text
sumLen = 1 + 1 = 2
```

Sequence:

```text
[1,1]
```

### Last part = 2

Use `fun(0)`:

```text
count = 1
sumLen = 0
```

After adding one element:

```text
sumLen = 0 + 1 = 1
```

Sequence:

```text
[2]
```

Therefore:

```text
count[2] = 2
sumLen[2] = 3
```

## fun(3)

Possible last parts:

```text
1
2
```

### Last part = 1

Use `fun(2)`:

```text
count = 2
sumLen = 3
```

After adding `1`:

```text
sumLen = 3 + 2 = 5
```

Sequences:

```text
[1,1,1] -> 3
[2,1]   -> 2
```

Total:

```text
5
```

### Last part = 2

Use `fun(1)`:

```text
count = 1
sumLen = 1
```

After adding `2`:

```text
sumLen = 1 + 1 = 2
```

Sequence:

```text
[1,2] -> 2
```

Therefore:

```text
sumLen[3] = 5 + 2 = 7
```

Final answer:

```text
7
```

# Why Memoization Is Needed

Without memoization, the same values are calculated many times.

For example:

```text
fun(3)
  -> fun(2)
      -> fun(1)
      -> fun(0)
  -> fun(1)
```

`fun(1)` can be calculated repeatedly.

With:

```java
if (dp[n] != null) {
    return dp[n];
}
```

we calculate each `n` only once.

# Complexity

There are `N` states.

For every state, we try up to `K` possible parts.

Therefore:

```text
Time:  O(N * K)
Space: O(N)
```

The memoization solution is much better than plain recursion because each state is calculated only once.

# Important Formula

Remember these two formulas:

```text
count[n] += count[n-x]
```

and:

```text
sumLen[n] += sumLen[n-x] + count[n-x]
```

The second formula is the most important:

```text
sumLen[n-x]
```

= old total lengths

```text
count[n-x]
```

= every sequence gets `+1` length when we append the new part.

# Final Example

For:

```text
N = 3
K = 2
```

All valid sequences:

```text
[1,1,1] -> 3
[1,2]   -> 2
[2,1]   -> 2
```

Answer:

```text
3 + 2 + 2 = 7
```

So:

```text
Output = 7
```
