# Q7. Count Binary Strings Length N with No K Consecutive 1s

## Source

**Source:** `SP_DSE_SP-Off Campus-Test-01-Aug-26-10 AM — Question 2`

---

## Problem Description

You are given two integers `N` and `K`.

You need to count the number of binary strings of length `N` such that:

1. The string does **not contain `K` consecutive `1`s**.
2. The **first and last characters are different**.

Return the number of valid binary strings modulo:

```text
1,000,000,007
```

### Example

If:

```text
K = 3
```

then a string cannot contain:

```text
111
```

So:

```text
01101   -> valid with respect to consecutive 1s
00111   -> invalid
11100   -> invalid
```

The first and last characters must also be different.

Therefore:

```text
00101   -> first = 0, last = 1 -> possible
10100   -> first = 1, last = 0 -> possible
10101   -> first = 1, last = 1 -> invalid
```

---

# Important Observation

We need to remember three things during recursion:

```text
position
first character
number of consecutive 1s at the end
```

So our DP state is:

```text
solve(pos, first, ones)
```

where:

- `pos` = current position we are filling
- `first` = first character of the string
- `ones` = number of consecutive `1`s currently at the end

---

# How the DP Works

At every position, we have two choices:

```text
Put 0
Put 1
```

## Choice 1 — Put `0`

A `0` breaks the consecutive sequence of `1`s.

Therefore:

```text
ones = 0
```

Transition:

```java
solve(pos + 1, first, 0)
```

---

## Choice 2 — Put `1`

We can add another `1` only if doing so does not create `K` consecutive `1`s.

Therefore:

```java
if (ones < K - 1)
```

then:

```java
solve(pos + 1, first, ones + 1)
```

### Why `ones < K - 1`?

Suppose:

```text
K = 3
```

The maximum allowed consecutive ones is:

```text
11
```

If:

```text
ones = 2
```

adding another `1` would create:

```text
111
```

which is forbidden.

---

# Base Case

When:

```java
pos == N
```

the string is complete.

We need the first and last characters to be different.

We don't explicitly store the last character.

Instead:

```text
ones == 0
```

means the last character is `0`.

And:

```text
ones > 0
```

means the last character is `1`.

So:

```java
int last = ones == 0 ? 0 : 1;
```

Then:

```java
return first != last ? 1 : 0;
```

---

# Simple Memoization Solution

```java
class Solution {

    static final int MOD = 1_000_000_007;

    int n, k;
    Integer[][][] dp;

    public int countStrings(int n, int k) {

        this.n = n;
        this.k = k;

        dp = new Integer[n][2][k];

        // Start with 0
        int ans = solve(1, 0, 0);

        // Start with 1
        if (k > 1) {
            ans = (ans + solve(1, 1, 1)) % MOD;
        }

        return ans;
    }

    int solve(int pos, int first, int ones) {

        // String is complete
        if (pos == n) {

            // ones == 0 -> last character is 0
            // ones > 0  -> last character is 1

            int last = ones == 0 ? 0 : 1;

            return first != last ? 1 : 0;
        }

        if (dp[pos][first][ones] != null) {
            return dp[pos][first][ones];
        }

        // Put 0
        int ans = solve(pos + 1, first, 0);

        // Put 1
        if (ones < k - 1) {
            ans = (ans + solve(pos + 1, first, ones + 1)) % MOD;
        }

        return dp[pos][first][ones] = ans;
    }
}
```

---

# Understanding the Parameters

The function:

```java
solve(pos, first, ones)
```

can be understood as:

```text
solve(
    where am I?       -> pos
    what was first?   -> first
    how many 1s now?  -> ones
)
```

For example, for the partial string:

```text
0011
```

we have:

```text
pos   = 4
first = 0
ones  = 2
```

Because:

- Position is `4`
- First character is `0`
- The string currently ends with two consecutive `1`s

---

# Test Case 1

## Input

```text
N = 3
K = 2
```

Since `K = 2`, we cannot have:

```text
11
```

All binary strings of length `3`:

```text
000
001
010
011
100
101
110
111
```

Remove strings containing `11`:

```text
000
001
010
100
```

Now apply:

```text
first != last
```

```text
000 -> 0 == 0 -> invalid
001 -> 0 != 1 -> valid
010 -> 0 == 0 -> invalid
100 -> 1 != 0 -> valid
```

Valid strings:

```text
001
100
```

### Output

```text
2
```

---

# Test Case 2

## Input

```text
N = 4
K = 3
```

We cannot have:

```text
111
```

The valid strings satisfying both conditions are:

```text
0001
0011
0101
1000
1010
1100
```

Check:

```text
0001 -> first 0, last 1 -> valid
0011 -> first 0, last 1 -> valid
0101 -> first 0, last 1 -> valid

1000 -> first 1, last 0 -> valid
1010 -> first 1, last 0 -> valid
1100 -> first 1, last 0 -> valid
```

### Output

```text
6
```

---

# Special Cases

## Case 1 — `N = 1`

For a string of length `1`, the first and last characters are the same position.

Therefore they can never be different.

```text
N = 1
answer = 0
```

The provided code naturally handles this because both the first and last character refer to the same character.

---

## Case 2 — `K = 1`

If:

```text
K = 1
```

then even a single `1` would be considered `1` consecutive `1`.

Therefore only:

```text
000...000
```

would be possible.

But its first and last characters are equal.

Therefore:

```text
answer = 0
```

The code avoids starting with `1` when `k <= 1`.

---

# Dry Run — N = 3, K = 2

Start with `0`:

```text
0
├── 0
│   ├── 0 -> 000 -> invalid
│   └── 1 -> 001 -> valid
│
└── 1
    └── 0 -> 010 -> invalid
```

Start with `1`:

```text
1
└── 0
    ├── 0 -> 100 -> valid
    └── 1 -> 101 -> invalid
```

So:

```text
answer = 2
```

---

# Why We Start With Two Calls

The first character has only two possibilities:

```text
0
1
```

So we start the recursion with:

```java
solve(1, 0, 0)
```

and:

```java
solve(1, 1, 1)
```

The first call means:

```text
String currently = "0"
first = 0
consecutive ones = 0
```

The second call means:

```text
String currently = "1"
first = 1
consecutive ones = 1
```

Then recursion builds the remaining `N - 1` characters.

---

# Why Memoization Works

Without memoization, the recursion explores many repeated states.

For example, different strings can reach the same state:

```text
(pos, first, ones)
```

Once we calculate:

```text
dp[pos][first][ones]
```

we store the answer.

If the same state appears again:

```java
if (dp[pos][first][ones] != null) {
    return dp[pos][first][ones];
}
```

we immediately return the stored result.

---

# Complexity

There are:

```text
N × 2 × K
```

possible states.

Each state has at most two transitions:

```text
put 0
put 1
```

Therefore:

```text
Time Complexity  = O(N × K)
Space Complexity = O(N × K)
```

The `2` for the first character is constant.

---

# Similar Problems

## 1. LeetCode 600 — Non-negative Integers without Consecutive Ones

A useful related problem for learning DP involving consecutive `1`s.

It is different from this problem because it works with the binary representation of integers, but the state idea is closely related.

https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/

---

## 2. GeeksforGeeks — Count Binary Strings With No Consecutive 1s

This is a simpler version where the main restriction is that consecutive `1`s are not allowed.

https://www.geeksforgeeks.org/problems/count-binary-strings-with-no-consecutive-1s/0

---

## 3. GeeksforGeeks — Consecutive 1's Not Allowed

Another good problem for practicing the basic DP pattern.

https://www.geeksforgeeks.org/problems/consecutive-1s-not-allowed1912/1

---

# Pattern to Remember

For this problem, remember:

```text
solve(pos, first, ones)
```

At every position:

```text
              current state
                   |
            ----------------
            |              |
           put 0          put 1
            |              |
       ones = 0      ones must be < K
                           |
                    ones = ones + 1
```

At the end:

```text
first != last
```

where:

```text
last = 0  if ones == 0
last = 1  if ones > 0
```

---

# One-Line Summary

> Use memoization with the state `(position, first character, consecutive 1s)`; try adding `0` or `1`, prevent the consecutive-ones count from reaching `K`, and at the end count only strings whose first and last characters are different.
