# Reduce to Zero

## Problem Description

You are given a positive integer `N`.

Your task is to reduce `N` to **0** using the minimum number of operations.

The following operations are allowed:

1. **Subtract 1**

   * You may subtract `1` from the current value at any time.

2. **Divide by 3**

   * Allowed only if:

     * The current value is divisible by `3`.
     * The **previous operation was NOT a division**.

3. **Divide by 4**

   * Allowed only if:

     * The current value is divisible by `4`.
     * The **previous operation was NOT a division**.

---

# Important Rule

Two division operations **cannot** be performed consecutively.

If you perform a division, the **next operation must be a subtraction** before another division can be performed.

Your goal is to reduce `N` to **0** using the **minimum number of operations**.

---

# Input Format

* The first line contains an integer `N`.

---

# Output Format

Print a single integer representing the minimum number of operations required.

---

# Constraints

```text
1 ≤ N ≤ 10^6
```

---

# Test Case 1

### Input

```text
N = 12
```

### Output

```text
4
```

### Explanation

One optimal sequence is:

```text
12 → 4   (/3)
4  → 3   (-1)
3  → 1   (/3)
1  → 0   (-1)
```

Operations:

```text
/3, -1, /3, -1
```

Total operations = **4**

Notice that every division is followed by a subtraction.

---

# Test Case 2

### Input

```text
N = 16
```

### Output

```text
4
```

### Explanation

One optimal sequence is:

```text
16 → 4   (/4)
4  → 3   (-1)
3  → 1   (/3)
1  → 0   (-1)
```

Operations:

```text
/4, -1, /3, -1
```

Total operations = **4**

Two divisions are never consecutive.

---

# Test Case 3

### Input

```text
N = 18
```

### Output

```text
5
```

### Explanation

One optimal sequence is:

```text
18 → 6   (/3)
6  → 5   (-1)
5  → 4   (-1)
4  → 1   (/4)
1  → 0   (-1)
```

Operations:

```text
/3, -1, -1, /4, -1
```

Total operations = **5**

---

# Brute Force Approach

At every number, try all possible operations.

* Subtract `1`
* Divide by `3` (if allowed)
* Divide by `4` (if allowed)

Explore every possible sequence and return the minimum.

Since many states repeat, this approach is inefficient.

---

# Dynamic Programming Approach (Memoization)

This problem is similar to **Minimum Steps to Reduce a Number**, but with one additional rule:

> Two division operations cannot occur consecutively.

Therefore, besides the current number, we also need to know whether the **previous operation was a division**.

---

# DP State

```text
dp[n][prevDiv]
```

Where:

* `n` → Current number.
* `prevDiv`

  * `0` → Previous operation was **not** a division.
  * `1` → Previous operation **was** a division.

The state stores:

> Minimum operations required to reduce `n` to `0`.

---

# State Transition

For every state:

### Choice 1

Subtract one.

```text
1 + solve(n-1,0)
```

After subtraction, division becomes allowed again.

---

### Choice 2

Divide by 3

Allowed only if

```text
prevDiv == 0

and

n % 3 == 0
```

Transition:

```text
1 + solve(n/3,1)
```

---

### Choice 3

Divide by 4

Allowed only if

```text
prevDiv == 0

and

n % 4 == 0
```

Transition:

```text
1 + solve(n/4,1)
```

Take the minimum of all valid choices.

---

# Base Case

When

```java
n == 0
```

No more operations are required.

Return

```java
0
```

---

# Memoization Code (Java)

```java
class Solution {

    Integer[][] dp;

    public int minOperations(int n) {

        dp = new Integer[n + 1][2];

        return solve(n, 0);
    }

    private int solve(int n, int prevDiv) {

        if (n == 0)
            return 0;

        if (dp[n][prevDiv] != null)
            return dp[n][prevDiv];

        int ans = Integer.MAX_VALUE;

        // Subtract 1
        ans = Math.min(ans, 1 + solve(n - 1, 0));

        // Divide by 3
        if (prevDiv == 0 && n % 3 == 0) {
            ans = Math.min(ans,
                    1 + solve(n / 3, 1));
        }

        // Divide by 4
        if (prevDiv == 0 && n % 4 == 0) {
            ans = Math.min(ans,
                    1 + solve(n / 4, 1));
        }

        return dp[n][prevDiv] = ans;
    }
}
```

---

# Dry Run

Suppose

```text
N = 12
```

Start:

```text
solve(12,0)
```

Choices:

### Subtract

```text
12 → 11

1 + solve(11,0)
```

---

### Divide by 3

```text
12 → 4

1 + solve(4,1)
```

Since the previous operation is now a division,

another division is **not allowed**.

Only subtraction:

```text
4 → 3

solve(3,0)
```

Now division becomes available again.

```text
3 → 1

solve(1,1)
```

Again,

only subtraction:

```text
1 → 0
```

Sequence:

```text
12 → 4 → 3 → 1 → 0
```

Operations:

```text
/3, -1, /3, -1
```

Total operations = **4**

---

# Why Do We Need `prevDiv`?

Without storing `prevDiv`, the DP cannot distinguish between these two situations:

### Case 1

```text
12 → 4

(previous operation = division)
```

From `4`, division is **not allowed**.

---

### Case 2

```text
5 → 4

(previous operation = subtraction)
```

From `4`, division **is allowed**.

Both cases reach the same number (`4`), but the allowed operations are different.

Therefore,

```text
dp[4]
```

is **not sufficient**.

We must store:

```text
dp[4][0]

dp[4][1]
```

These represent different states.

---

# Complexity Analysis

### Time Complexity

There are

* `N` possible numbers.
* `2` possible previous-operation states.

Each state considers at most three transitions.

```text
O(N)
```

---

### Space Complexity

DP table:

```text
O(2 × N)
```

which simplifies to

```text
O(N)
```

Recursion stack:

```text
O(N)
```

Overall:

```text
O(N)
```

---

# Similar Problems

* LeetCode 397 – Integer Replacement
* LeetCode 1553 – Minimum Number of Days to Eat N Oranges
* LeetCode 1342 – Number of Steps to Reduce a Number to Zero
* Minimum Steps to Reduce a Number to One (Dynamic Programming)
* GeeksforGeeks – Minimum Operations to Reduce N

---

# Concepts Used

* Dynamic Programming
* Memoization
* State Representation
* Recursion
* Minimum Operations
* Decision Making DP
* State Transition
