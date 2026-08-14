# Count Valid Strings Using 0, 1, 2 — 3D DP Memoization

## Problem Description

Given an integer `N`, count the number of strings of length `N` that can be formed using only the characters:

```text
0, 1, 2
```

The string must satisfy all of the following conditions:

### Condition 1 — `"00"` is not allowed

The substring:

```text
00
```

must never occur.

Examples:

```text
1001  ❌
0001  ❌
1010  ✓
```

---

### Condition 2 — `"111"` is not allowed

Three consecutive `1`s cannot occur.

```text
111
```

is invalid.

Examples:

```text
01110  ❌
111   ❌
1101  ✓
1011  ✓
```

---

### Condition 3 — Every `2` must be surrounded by `1`

Whenever a `2` occurs, it must have a `1` immediately before and immediately after it.

Valid:

```text
121
```

Invalid:

```text
210   ❌
012   ❌
120   ❌
202   ❌
```

Therefore, a `2` can only appear as part of:

```text
121
```

---

## Important Note

Since `2` must be surrounded by `1`, a `2` cannot be:

* The first character
* The last character
* Immediately after anything other than `1`
* Immediately before anything other than `1`

---

# Example

For:

```text
N = 3
```

Some valid strings are:

```text
010
011
101
110
121
```

So:

```text
Answer = 5
```

---

# Test Case 1

```text
Input:
N = 1

Output:
2
```

Possible strings:

```text
0
1
```

`2` cannot be used because it must be surrounded by `1`.

Therefore:

```text
Answer = 2
```

---

# Test Case 2

```text
Input:
N = 3

Output:
5
```

Valid strings:

```text
010
011
101
110
121
```

Therefore:

```text
Answer = 5
```

---

# Test Case 3

```text
Input:
N = 4

Output:
10
```

Valid strings include:

```text
0101
0110
0111
0121
1010
1011
1101
1110
1201
1210
```

Therefore:

```text
Answer = 10
```

---

# Dynamic Programming Approach

This problem can be solved using **3D DP with memoization**.

The important observation is:

> To decide whether we can add the next character, we need to know the previous two characters.

Why?

### To detect `"00"`

We only need the previous character:

```text
previous = 0
current = 0
```

This creates:

```text
00
```

which is invalid.

### To detect `"111"`

We need the previous two characters:

```text
previousPrevious = 1
previous = 1
current = 1
```

This creates:

```text
111
```

which is invalid.

### To handle `2`

If the previous character is `2`, the current character must be `1`.

Example:

```text
... 1 2 ?
```

The `?` must be:

```text
1
```

Therefore, we need both recent characters in our state.

---

# DP State

Define:

```text
dp[i][pp][p]
```

where:

```text
i  = current index
pp = previous previous character
p  = previous character
```

It represents:

> Number of valid ways to construct the string from index `i` onward when the last two characters are `pp` and `p`.

---

# Why 3 Dimensions?

There are three pieces of information:

```text
index
previous previous character
previous character
```

Therefore:

```text
dp[i][pp][p]
```

---

# Example of a State

Suppose the current partial string is:

```text
... 1 2
```

Then:

```text
pp = 1
p  = 2
```

We call:

```text
fun(i, 1, 2)
```

Because the previous character is `2`, the next character must be `1`.

---

# Character Choices

At every position we try:

```text
0
1
2
```

So:

```java
for (int cur = 0; cur <= 2; cur++)
```

For every candidate `cur`, we check whether it is valid.

---

# Transition Rules

## Rule 1 — Prevent `"00"`

```java
if (p == 0 && cur == 0)
    continue;
```

If the previous character is `0`, we cannot add another `0`.

---

## Rule 2 — Prevent `"111"`

```java
if (pp == 1 && p == 1 && cur == 1)
    continue;
```

If the last two characters are:

```text
11
```

we cannot add another `1`.

---

## Rule 3 — `2` must have `1` before it

When adding `2`:

```java
if (cur == 2 && p != 1)
    continue;
```

For example:

```text
02
```

is invalid.

```text
12
```

is allowed so far.

---

## Rule 4 — `2` must have `1` after it

If the previous character is `2`:

```java
if (p == 2 && cur != 1)
    continue;
```

For example:

```text
120
```

is invalid.

```text
121
```

is valid.

---

# Base Case

When:

```text
i == n
```

the entire string has been constructed.

But we must make sure the string does not end with `2`.

Why?

Because the last `2` would not have a `1` after it.

Therefore:

```java
if (i == n) {
    if (p == 2)
        return 0;

    return 1;
}
```

---

# Handling the First Character

The first character can only be:

```text
0
1
```

It cannot be `2`, because `2` needs a `1` before it.

We don't have a previous character at the beginning.

So we use:

```text
3
```

as a special value meaning:

```text
"No previous character"
```

Therefore:

```java
fun(1, 3, 0)
fun(1, 3, 1)
```

---

# Complete Memoization Code

class Solution {

    static final int MOD = 1_000_000_007;

    Integer[][][] dp;
    int n;

    public int countStrings(int n) {

        this.n = n;

        // 0, 1, 2 = actual characters
        // 3 = no character
        dp = new Integer[n + 1][4][4];

        // Start with an empty string
        // i  = 0  -> no character placed yet
        // pp = 3  -> no previous-previous character
        // p  = 3  -> no previous character
        return fun(0, 3, 3);
    }

    /*
        i  = current index to fill
        pp = previous previous character
        p  = previous character
    */
    public int fun(int i, int pp, int p) {

        // Entire string is constructed
        if (i == n) {

            // String cannot end with 2
            if (p == 2) {
                return 0;
            }

            return 1;
        }

        // Already calculated
        if (dp[i][pp][p] != null) {
            return dp[i][pp][p];
        }

        long ans = 0;

        // Try current character: 0, 1, or 2
        for (int cur = 0; cur <= 2; cur++) {

            // "00" is not allowed
            if (p == 0 && cur == 0) {
                continue;
            }

            // "111" is not allowed
            if (pp == 1 && p == 1 && cur == 1) {
                continue;
            }

            // 2 must have 1 before it
            if (cur == 2 && p != 1) {
                continue;
            }

            // 2 must be followed by 1
            if (p == 2 && cur != 1) {
                continue;
            }

            // Valid transition
            ans = (ans + fun(i + 1, p, cur)) % MOD;
        }

        return dp[i][pp][p] = (int) ans;
    }
}

---

# Dry Run

Consider:

```text
N = 3
```

We start with:

```text
fun(1, 3, 0)
```

and:

```text
fun(1, 3, 1)
```

---

## Start With `0`

Partial string:

```text
0
```

Now we try:

```text
0
1
2
```

### Try `0`

We have:

```text
00
```

Invalid.

So:

```java
if (p == 0 && cur == 0)
    continue;
```

---

### Try `1`

We get:

```text
01
```

Valid.

Continue with:

```text
fun(2, 0, 1)
```

---

### Try `2`

We have:

```text
02
```

Invalid because `2` must have `1` before it.

So:

```java
if (cur == 2 && p != 1)
    continue;
```

---

# Start With `1`

Partial string:

```text
1
```

Try:

```text
0
1
2
```

All are initially possible.

### `10`

Valid.

### `11`

Valid so far.

### `12`

Valid because `2` has `1` before it.

But after `2`, the next character must be `1`.

So:

```text
121
```

is valid.

---

# Why Memoization Helps

Without memoization, the same state can be reached through many different prefixes.

For example:

```text
fun(i, 1, 2)
```

might be reached through:

```text
...12
```

from many different prefixes.

But the answer from:

```text
fun(i, 1, 2)
```

is always the same.

So we calculate it once:

```java
dp[i][1][2]
```

and reuse it.

---

# Complexity

There are:

```text
N
```

possible values of `i`.

For each `i`:

```text
4
```

possible values of `pp`.

For each `pp`:

```text
4
```

possible values of `p`.

And for each state we try:

```text
3
```

possible current characters.

Therefore:

```text
Time Complexity = O(N × 4 × 4 × 3)
```

which simplifies to:

```text
O(N)
```

Space:

```text
O(N × 4 × 4)
```

which simplifies to:

```text
O(N)
```

---

# Important DP Pattern

This problem demonstrates an important string-DP pattern:

> When the validity of the next character depends on the previous two characters, store the previous two characters in the DP state.

The general form is:

```text
dp[index][previousPrevious][previous]
```

---

# Common Mistakes

## 1. Using only `dp[i][previous]`

This is not enough because we need to detect:

```text
111
```

To know whether adding another `1` creates `111`, we need the previous two characters.

---

## 2. Forgetting the ending `2`

A string like:

```text
12
```

must be rejected if every `2` needs to be surrounded by `1`.

Therefore:

```java
if (i == n && p == 2)
    return 0;
```

---

## 3. Allowing `2` as the first character

Wrong:

```text
2...
```

A `2` needs a `1` before it.

So the first character can only be:

```text
0 or 1
```

---

## 4. Allowing `2` after `0`

```text
02
```

is invalid.

The character before `2` must be `1`.

---

## 5. Forgetting modulo

The number of strings grows exponentially.

Always use:

```java
ans = (ans + value) % MOD;
```

with:

```java
static final int MOD = 1_000_000_007;
```

---

# Quick Revision

```text
Characters:
0, 1, 2

Rules:
"00" → not allowed
"111" → not allowed
2 → must be surrounded by 1

DP:
dp[i][pp][p]

pp:
previous previous character

p:
previous character

Try:
cur = 0, 1, 2

Reject:
p == 0 && cur == 0

pp == 1 && p == 1 && cur == 1

cur == 2 && p != 1

p == 2 && cur != 1

Base:
i == n

If p == 2:
return 0

Otherwise:
return 1

Complexity:
Time  = O(N)
Space = O(N)
```

---

# Similar Practice Problems

### 1. LeetCode 600 — Non-negative Integers Without Consecutive Ones

[LeetCode 600 — Non-negative Integers without Consecutive Ones](https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/?utm_source=chatgpt.com)

Useful for learning DP with restrictions on consecutive characters.

### 2. GeeksforGeeks — Count Binary Strings Without Consecutive 1s

[GeeksforGeeks — Count Binary Strings Without Consecutive 1s](https://www.geeksforgeeks.org/dsa/count-number-binary-strings-without-consecutive-1s/?utm_source=chatgpt.com)

Good practice for counting strings with character restrictions.

### 3. GeeksforGeeks — Count Strings Without Consecutive Same Characters

[GeeksforGeeks — Count Strings Without Consecutive Same Characters](https://www.geeksforgeeks.org/dsa/count-number-of-strings-formed-using-a-b-c-having-no-consecutive-same-characters/?utm_source=chatgpt.com)

Useful for understanding previous-character DP.

### 4. LeetCode 91 — Decode Ways

[LeetCode 91 — Decode Ways](https://leetcode.com/problems/decode-ways/?utm_source=chatgpt.com)

Good practice for DP where recent characters affect valid transitions.

---

# Final Takeaway

The main idea is:

```text
              current
                 ↓
       dp[i][previous2][previous]
                 ↓
        try 0 / 1 / 2
                 ↓
        check all rules
                 ↓
       fun(i + 1, previous, current)
```

The most important thing to remember for this problem is:

```text
If a string rule depends on the last TWO characters,
keep the last TWO characters in the DP state.
```

So the key state is:

```text
dp[index][previousPrevious][previous]
```
