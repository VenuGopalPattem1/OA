# Count Valid Sequences — 3D DP

## Problem Description

Given two integers `N` and `T`, count the number of sequences of length `N` using only:

```text
{-1, 0, 1, 2}
```

A sequence is valid if:

1. The final sum is less than or equal to `T`.

```text
finalSum <= T
```

2. If the current sum is divisible by `3`, then `2` cannot be selected as the next element.

```text
if (currentSum % 3 == 0)
    next != 2
```

Return the answer modulo:

```text
1,000,000,007
```

Since `-1` is allowed, the running sum can be negative.

For `N` elements:

```text
-N <= sum <= 2N
```

Therefore, when using the sum as a DP array index, an offset is required.

---

## Test Case 1

```text
Input:
N = 1
T = 1

Output:
3
```

Valid sequences:

```text
[-1]
[0]
[1]
```

`[2]` is invalid because the initial current sum is `0`, and:

```text
0 % 3 == 0
```

Therefore, `2` cannot be selected.

---

## Test Case 2

```text
Input:
N = 2
T = 1

Output:
9
```

For example:

```text
[0, 2]
```

is invalid because after choosing `0`:

```text
currentSum = 0
0 % 3 == 0
```

so the next value cannot be `2`.

---

## Test Case 3

```text
Input:
N = 3
T = 2

Output:
33
```

The DP tries all possible values `{-1, 0, 1, 2}` and rejects `2` whenever the current sum is divisible by `3`.

---

# DP Solution 1 — 2D Memoization

The extra `lastWas2` state is not necessary because the restriction depends only on the current sum.

### State

```text
dp[i][sum + offset]
```

where:

```text
i   = current index
sum = current running sum
```

Since the sum can be negative, use:

```text
offset = N
```

### Java Code

```java
class Solution {

    static final int MOD = 1_000_000_007;

    Integer[][] dp;

    int n;
    int t;
    int offset;

    public int countSequences(int n, int t) {

        this.n = n;
        this.t = t;

        offset = n;

        dp = new Integer[n + 1][3 * n + 1];

        return fun(0, 0);
    }

    public int fun(int i, int sum) {

        // All elements selected
        if (i == n) {
            return sum <= t ? 1 : 0;
        }

        int index = sum + offset;

        if (dp[i][index] != null) {
            return dp[i][index];
        }

        long ans = 0;

        // Try -1, 0, 1, 2
        for (int next = -1; next <= 2; next++) {

            // If current sum is divisible by 3,
            // we cannot choose 2.
            if (sum % 3 == 0 && next == 2) {
                continue;
            }

            ans = (ans + fun(i + 1, sum + next)) % MOD;
        }

        return dp[i][index] = (int) ans;
    }
}
```

### Complexity

```text
Time  : O(N²)
Space : O(N²)
```

---

# DP Solution 2 — 3D Memoization

The 3D state follows the state mentioned in the OA:

```text
dp[i][sum + offset][lastWas2]
```

where:

```text
lastWas2 = 1 → previous element was 2
lastWas2 = 0 → previous element was not 2
```

### Java Code

```java
class Solution {

    static final int MOD = 1_000_000_007;

    Integer[][][] dp;

    int n;
    int t;
    int offset;

    public int countSequences(int n, int t) {

        this.n = n;
        this.t = t;

        offset = n;

        dp = new Integer[n + 1][3 * n + 1][2];

        return fun(0, 0, 0);
    }

    public int fun(int i, int sum, int lastWas2) {

        // All elements selected
        if (i == n) {
            return sum <= t ? 1 : 0;
        }

        int index = sum + offset;

        if (dp[i][index][lastWas2] != null) {
            return dp[i][index][lastWas2];
        }

        long ans = 0;

        // Try -1, 0, 1, 2
        for (int next = -1; next <= 2; next++) {

            // Current sum divisible by 3
            // means we cannot choose 2.
            if (sum % 3 == 0 && next == 2) {
                continue;
            }

            int newSum = sum + next;

            int newLastWas2 = (next == 2) ? 1 : 0;

            ans = (ans +
                    fun(i + 1, newSum, newLastWas2))
                    % MOD;
        }

        return dp[i][index][lastWas2] = (int) ans;
    }
}
```

### Complexity

```text
Time  : O(N²)
Space : O(N²)
```

The third dimension contains only `2` states, so it is a constant factor.

---

# Why Do We Need an Offset?

The current sum can become negative because `-1` is allowed.

For example:

```text
N = 5

Sequence:
[-1, -1, -1, -1, -1]

Final sum:
-5
```

We cannot do:

```java
dp[i][-5]
```

because array indexes cannot be negative.

So use:

```text
offset = N
```

and store:

```text
dp[i][sum + offset]
```

For example, when `N = 5`:

```text
sum = -5
index = -5 + 5 = 0
```

and:

```text
sum = 0
index = 0 + 5 = 5
```

The maximum sum is `2N`, so the total number of possible sum indexes is:

```text
2N - (-N) + 1
= 3N + 1
```

Hence:

```java
new Integer[n + 1][3 * n + 1]
```

---

# DP Transition

At every position, try:

```text
-1
 0
 1
 2
```

Before choosing `next`, check:

```java
if (sum % 3 == 0 && next == 2)
    continue;
```

If the choice is valid:

```text
newSum = sum + next
```

and move to:

```text
fun(i + 1, newSum)
```

For the 3D version:

```text
fun(i + 1, newSum, next == 2 ? 1 : 0)
```

---

# Base Case

When:

```java
i == n
```

all `N` elements have been selected.

Now check the final sum:

```java
if (sum <= t)
    return 1;
```

Otherwise:

```java
return 0;
```

---

# Key DP Pattern

This is a:

```text
Counting DP + Running Sum + Negative Values
```

problem.

The important state is:

```text
dp[index][sum + offset]
```

or, following the OA's 3D approach:

```text
dp[index][sum + offset][lastWas2]
```

The main trick is:

```text
Negative sum
     ↓
Add OFFSET
     ↓
Use sum as array index
```

---

# Important Observation

The `lastWas2` dimension is technically not required for this exact condition because whether we can choose `2` depends only on:

```text
currentSum % 3
```

Therefore, the 2D version is simpler and is the preferred implementation.

However, if the OA specifically expects a 3D DP state, the 3D version can be used.

---

# Common Mistakes

### 1. Using sum directly as an array index

Wrong:

```java
dp[i][sum]
```

because `sum` can be negative.

Correct:

```java
dp[i][sum + offset]
```

### 2. Checking the condition after adding the value

The restriction is based on the **current sum before choosing the next value**.

Correct:

```java
if (sum % 3 == 0 && next == 2)
    continue;
```

### 3. Forgetting the final condition

At `i == n`, count the sequence only if:

```text
sum <= T
```

### 4. Forgetting modulo

Use:

```java
ans = (ans + value) % MOD;
```

where:

```text
MOD = 1,000,000,007
```

---

# Quick Revision

```text
Allowed values:
{-1, 0, 1, 2}

Condition:
currentSum % 3 == 0
        ↓
next cannot be 2

Final condition:
sum <= T

Minimum sum:
-N

Maximum sum:
2N

Offset:
N

2D State:
dp[i][sum + offset]

3D State:
dp[i][sum + offset][lastWas2]

2D Complexity:
O(N²) Time
O(N²) Space

3D Complexity:
O(N²) Time
O(N²) Space
```

---

# Similar Practice Problems

### 1. LeetCode 494 — Target Sum

https://leetcode.com/problems/target-sum/

Useful for learning DP with a running sum that can become negative.

### 2. LeetCode 377 — Combination Sum IV

https://leetcode.com/problems/combination-sum-iv/

Useful for counting different sequences using DP.

### 3. GeeksforGeeks — Count Ways to Obtain a Given Sum

https://www.geeksforgeeks.org/dsa/count-ways-to-obtain-given-sum/

Useful for practicing sum-based counting DP.

### 4. GeeksforGeeks — Number of Ways to Reach a Given Sum

https://www.geeksforgeeks.org/dsa/number-of-ways-to-reach-a-given-sum/

Useful for understanding DP states based on a running sum.
