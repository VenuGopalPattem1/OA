# Count Subsequences of Size K Whose GCD Is 1

## Problem Statement

Given an integer array `a` of size `n` and an integer `k`, count the number of subsequences of exactly size `k` whose GCD is `1`.

A subsequence is formed by selecting elements while maintaining their original order.

The selected elements do not need to be contiguous.

---

## Example

### Input

```text
a = [2, 3, 4]
k = 2
```

All subsequences of size `2` are:

```text
[2, 3] → GCD = 1 ✓
[2, 4] → GCD = 2 ✗
[3, 4] → GCD = 1 ✓
```

Therefore:

```text
Answer = 2
```

---

# Approach

We use **Recursion + Memoization (DP)**.

While constructing the subsequence, we maintain the GCD of the elements selected so far.

At every index, we have two choices:

1. Take the current element.
2. Skip the current element.

If the current GCD is `g` and we take `a[i]`, the new GCD becomes:

```text
newGcd = gcd(g, a[i])
```

Initially, the GCD is `0`.

This works because:

```text
gcd(0, x) = x
```

---

# DP State

We use:

```text
dp[i][k][gcd]
```

Meaning:

> Number of ways to select exactly `k` elements from index `i` onward, when the GCD of the elements already selected is `gcd`.

The GCD must be part of the DP state.

For example:

```text
fun(2, i, k)
```

and

```text
fun(3, i, k)
```

cannot share the same DP value because their current GCDs are different.

Therefore:

```text
dp[i][k]
```

is not sufficient.

We need:

```text
dp[i][k][gcd]
```

---

# Base Cases

## Case 1: Exactly K elements selected

When:

```java
k == 0
```

we have selected exactly `k` elements.

Now we check the GCD.

```java
if (k == 0) {
    return gcd == 1 ? 1 : 0;
}
```

If the GCD is `1`, this is a valid subsequence.

Otherwise, it is not counted.

---

## Case 2: No elements left

If:

```java
i == a.length
```

and we still need elements, it is impossible to form a subsequence.

So:

```java
if (i == a.length) {
    return 0;
}
```

---

# Recurrence

At index `i`, we have two choices.

## 1. Take the current element

```java
int newGcd = gcd(gcd, a[i]);

int take = fun(newGcd, i + 1, k - 1, a);
```

When we take `a[i]`:

- `k` decreases by `1`
- `i` moves to the next index
- GCD is updated

---

## 2. Skip the current element

```java
int skip = fun(gcd, i + 1, k, a);
```

When we skip `a[i]`:

- `k` remains the same
- `i` moves forward
- GCD remains unchanged

---

Therefore:

```text
answer = take + skip
```

---

# Java Code

```java
import java.util.*;

class Java {

    static Integer[][][] dp;

    public static void main(String[] args) {

        int[] a = {2, 3, 4};
        int n = a.length;
        int k = 2;

        dp = new Integer[n][k + 1][1001];

        System.out.println(fun(0, 0, k, a));
    }

    public static int fun(int gcd, int i, int k, int[] a) {

        // Selected exactly k elements
        if (k == 0) {
            return gcd == 1 ? 1 : 0;
        }

        // No elements left
        if (i == a.length) {
            return 0;
        }

        // Not enough elements remaining
        if (a.length - i < k) {
            return 0;
        }

        // Already calculated
        if (dp[i][k][gcd] != null) {
            return dp[i][k][gcd];
        }

        // Take current element
        int newGcd = gcd(gcd, a[i]);

        int take = fun(newGcd, i + 1, k - 1, a);

        // Skip current element
        int skip = fun(gcd, i + 1, k, a);

        return dp[i][k][gcd] = take + skip;
    }

    public static int gcd(int a, int b) {

        if (a == 0) {
            return b;
        }

        return gcd(b % a, a);
    }
}
```

---

# Dry Run

Consider:

```text
a = [2, 3, 4]
k = 2
```

Initially:

```text
fun(0, 0, 2)
```

Here:

```text
gcd = 0
i = 0
k = 2
```

---

## Take 2

We calculate:

```text
gcd(0, 2) = 2
```

So we call:

```text
fun(2, 1, 1)
```

Now we need to select one more element.

### Take 3

```text
gcd(2, 3) = 1
```

Call:

```text
fun(1, 2, 0)
```

Since:

```text
k == 0
```

and:

```text
gcd == 1
```

we return:

```text
1
```

So:

```text
[2, 3]
```

is counted.

---

## Take 2, Skip 3, Take 4

Starting with:

```text
gcd = 2
```

Take `4`:

```text
gcd(2, 4) = 2
```

The final GCD is `2`, so:

```text
[2, 4]
```

is not counted.

---

## Skip 2

We move to:

```text
fun(0, 1, 2)
```

Take `3`:

```text
gcd(0, 3) = 3
```

Then take `4`:

```text
gcd(3, 4) = 1
```

Therefore:

```text
[3, 4]
```

is counted.

---

## Final Answer

The valid subsequences are:

```text
[2, 3]
[3, 4]
```

Therefore:

```text
Answer = 2
```

---

# Why Do We Need GCD in the DP State?

Suppose we only use:

```text
dp[i][k]
```

This is incorrect.

Consider two states:

```text
fun(2, i, k)
fun(3, i, k)
```

Both have the same `i` and `k`, but different current GCDs.

Suppose the next element is `6`.

For the first state:

```text
gcd(2, 6) = 2
```

For the second state:

```text
gcd(3, 6) = 3
```

The future answers can therefore be different.

So GCD must be included:

```text
dp[i][k][gcd]
```

---

# Why Do We Start GCD With 0?

Initially, no element has been selected.

We use:

```text
gcd = 0
```

because:

```text
gcd(0, x) = x
```

For example:

```text
gcd(0, 2) = 2
gcd(2, 3) = 1
```

Therefore, after selecting:

```text
[2, 3]
```

the GCD becomes:

```text
1
```

---

# Optional Pruning

We can use:

```java
if (a.length - i < k) {
    return 0;
}
```

This is **not required for correctness**.

It is only an optimization.

It checks whether enough elements remain to form a subsequence of size `k`.

For example:

```text
n = 5
i = 4
k = 2
```

Only one element remains.

But we need two elements.

Therefore:

```text
a.length - i < k
1 < 2
```

So we can immediately return `0`.

Without this condition, the recursion will eventually reach:

```java
if (i == a.length) {
    return 0;
}
```

and still produce the correct answer.

---

# Time Complexity

Let:

- `n` = number of elements
- `k` = required subsequence size
- `M` = maximum possible GCD/value

Number of DP states:

```text
O(n × k × M)
```

Each state performs constant work.

Therefore:

```text
Time Complexity = O(n × k × M)
```

---

# Space Complexity

The DP array is:

```text
Integer[n][k + 1][M + 1]
```

Therefore:

```text
Space Complexity = O(n × k × M)
```

---

# Important Constraint Note

The code uses:

```java
dp = new Integer[n][k + 1][1001];
```

This assumes:

```text
a[i] <= 1000
```

because the GCD can never be greater than the values in the array.

If:

```text
a[i] <= 10^9
```

then a DP array with `10^9` GCD states is not possible.

For large values, a `HashMap`-based GCD DP is preferable.

---

# Interview Explanation

A simple way to explain the solution in an interview:

> I use recursion with memoization. At every index, I either take or skip the current element. Since the final condition depends on the GCD of all selected elements, I maintain the current GCD as part of the state. Therefore, the DP state is `dp[i][k][gcd]`, where `i` is the current index, `k` is the number of elements still required, and `gcd` is the GCD of the elements selected so far. When `k` becomes zero, I return `1` only if the GCD is `1`.

The main recurrence is:

```text
take = fun(gcd(g, a[i]), i + 1, k - 1)
skip = fun(g, i + 1, k)

answer = take + skip
```

The base condition is:

```text
k == 0 → gcd == 1 ? 1 : 0
```

---

# Final Code

```java
import java.util.*;

class Java {

    static Integer[][][] dp;

    public static void main(String[] args) {

        int[] a = {2, 3, 4};
        int n = a.length;
        int k = 2;

        dp = new Integer[n][k + 1][1001];

        System.out.println(fun(0, 0, k, a));
    }

    public static int fun(int gcd, int i, int k, int[] a) {

        if (k == 0) {
            return gcd == 1 ? 1 : 0;
        }

        if (i == a.length) {
            return 0;
        }

        if (a.length - i < k) {
            return 0;
        }

        if (dp[i][k][gcd] != null) {
            return dp[i][k][gcd];
        }

        // Take
        int newGcd = gcd(gcd, a[i]);

        int take = fun(newGcd, i + 1, k - 1, a);

        // Skip
        int skip = fun(gcd, i + 1, k, a);

        return dp[i][k][gcd] = take + skip;
    }

    public static int gcd(int a, int b) {

        if (a == 0) {
            return b;
        }

        return gcd(b % a, a);
    }
}
```
