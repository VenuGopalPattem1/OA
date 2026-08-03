# No Adjacent Equal Sum Sequences

## Problem Description

You are given a positive integer **N**.

Your task is to find the number of **ordered sequences** whose elements are chosen from the set:

```text
{1, 3, 4}
```

such that:

* The sum of all elements is exactly **N**.
* No two adjacent elements in the sequence are equal.

Since the answer can be very large, return it modulo:

```text
10^9 + 7
```

---

# What is an Ordered Sequence?

An **ordered sequence** means the **position of the numbers matters**.

For example,

```text
1 3
```

and

```text
3 1
```

are considered **different sequences** because the numbers appear in a different order.

### Example

For

```text
N = 4
```

The valid sequences are

```text
4
1 3
3 1
```

Answer:

```text
3
```

---

# Rules

* Every element must be one of **1, 3, or 4**.
* The total sum must equal **N**.
* Order of elements matters.
* No two consecutive elements can be equal.

---

# Input Format

The first line contains an integer:

```text
N
```

---

# Output Format

Print the number of valid ordered sequences modulo **10^9 + 7**.

---

# Constraints

```text
1 ≤ N ≤ 10^5
```

---

# Sample Test Cases

## Test Case 1

### Input

```text
4
```

### Output

```text
3
```

### Explanation

The valid ordered sequences are:

```text
4
1 3
3 1
```

The sequence

```text
1 1 1 1
```

is invalid because two adjacent elements are equal.

---

## Test Case 2

### Input

```text
5
```

### Output

```text
3
```

### Explanation

The valid ordered sequences are:

```text
1 4
4 1
1 3 1
```

The following sequences are invalid:

```text
1 1 3
3 1 1
```

because they contain adjacent equal elements.

---

## Test Case 3

### Input

```text
7
```

### Output

```text
4
```

### Explanation

The valid ordered sequences are:

```text
3 4
4 3
3 1 3
1 4 1
```

Every sequence:

* Uses only numbers from `{1,3,4}`.
* Sums to `7`.
* Has no adjacent equal elements.

---

# Approach

This is a **Dynamic Programming** problem.

While building a sequence, we need to know:

1. How much sum is still remaining.
2. What was the previously chosen number.

The previous number is required to ensure that we never place the same number consecutively.

---

# DP State

```text
dp[remaining][previous]
```

Where

* `remaining` = remaining sum
* `previous` =

  * `0` → no previous number
  * `1` → previous number is 1
  * `2` → previous number is 3
  * `3` → previous number is 4

---

# State Transition

From every state we try three choices.

### Choose 1

Allowed only if

```text
previous != 1
```

Transition

```text
solve(remaining-1,1)
```

---

### Choose 3

Allowed only if

```text
previous != 2
```

Transition

```text
solve(remaining-3,2)
```

---

### Choose 4

Allowed only if

```text
previous != 3
```

Transition

```text
solve(remaining-4,3)
```

---

# Base Case

When the remaining sum becomes zero,

```java
if(rem == 0)
    return 1;
```

A valid sequence has been formed.

---

# Memoization

If a state has already been computed,

```java
if(dp[rem][prev] != null)
    return dp[rem][prev];
```

return the stored answer instead of recomputing it.

---

# Java Memoization Solution

```java
class Solution {

    static final int MOD = 1000000007;
    Integer[][] dp;

    public int countWays(int N) {

        dp = new Integer[N + 1][4];

        return solve(N, 0);
    }

    private int solve(int rem, int prev) {

        if (rem == 0)
            return 1;

        if (dp[rem][prev] != null)
            return dp[rem][prev];

        long ans = 0;

        // Choose 1
        if (rem >= 1 && prev != 1)
            ans += solve(rem - 1, 1);

        // Choose 3
        if (rem >= 3 && prev != 2)
            ans += solve(rem - 3, 2);

        // Choose 4
        if (rem >= 4 && prev != 3)
            ans += solve(rem - 4, 3);

        return dp[rem][prev] = (int)(ans % MOD);
    }
}
```

---

# Complexity Analysis

### Time Complexity

There are

* `N + 1` values of remaining sum.
* `4` previous states.

Each state tries at most three transitions.

```text
Time Complexity = O(N)
```

---

### Space Complexity

DP table

```text
O(N × 4)
```

which simplifies to

```text
O(N)
```

Recursion stack

```text
O(N)
```

---

# Why do we need the Previous Number?

Suppose we only store

```text
dp[remaining]
```

After choosing

```text
1
```

the DP cannot determine whether choosing another

```text
1
```

is allowed.

It would incorrectly count sequences like

```text
1 1 3
```

By storing the previous number, we can reject transitions that repeat the last element.

---

# Why does this satisfy the "Ordered Sequence" condition?

The recursion builds the sequence from **left to right**.

For example, when `N = 4`:

```text
Choose 1 → Choose 3  => 1 3
Choose 3 → Choose 1  => 3 1
```

These are different recursion paths, so both are counted separately.

Thus,

```text
1 3 ≠ 3 1
```

which satisfies the ordered sequence requirement.

---

# Related Problems

* LeetCode 377 — Combination Sum IV
* LeetCode 70 — Climbing Stairs
* LeetCode 552 — Student Attendance Record II
* LeetCode 1220 — Count Vowels Permutation
* LeetCode 2466 — Count Ways To Build Good Strings
* GeeksforGeeks — Count Ways to Express N as Sum of 1, 3 and 4
* GeeksforGeeks — Count Ways to Reach the Nth Stair

---

# Concepts Used

* Dynamic Programming
* Memoization (Top-Down DP)
* State Transition
* Previous State Tracking
* Ordered Sequence Counting
* Modulo Arithmetic
* Recursion
