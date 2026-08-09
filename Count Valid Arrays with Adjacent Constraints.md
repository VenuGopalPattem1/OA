# Count Valid Arrays with Adjacent Constraints

## Problem

Given two integers `N` and `K`, count the number of arrays of length `N` such that:

* Every element is in the range `[1, K]`.
* Repetition of elements is allowed.
* For every pair of consecutive elements `a[i]` and `a[i+1]`, **at least one** of the following conditions must be satisfied:

### Conditions

1. The absolute difference between consecutive elements is at most `1`.

```text
|a[i] - a[i+1]| <= 1
```

2. The previous element is twice the next element.

```text
a[i] = 2 * a[i+1]
```

3. The next element is twice the previous element.

```text
a[i+1] = 2 * a[i]
```

Return the number of valid arrays modulo:

```text
10^9 + 7
```

---

# Understanding the Problem

Suppose:

```text
N = 3
K = 3
```

Every element must be one of:

```text
1, 2, 3
```

Examples of valid arrays:

```text
[1, 1, 1]
[1, 2, 1]
[2, 1, 2]
[2, 3, 2]
[1, 2, 3]
```

because consecutive elements satisfy at least one of the conditions.

---

# What Does Repetition Allowed Mean?

Repetition means the same value can appear multiple times.

For example:

```text
[1, 1, 1]
[2, 2, 2]
[3, 3, 3]
```

are allowed if their consecutive elements satisfy the conditions.

For example:

```text
[2, 2]
```

is valid because:

```text
|2 - 2| = 0 <= 1
```

Therefore, we do NOT require all elements to be different.

---

# Important Observation

The validity of the next element depends only on the **previous element**.

Suppose the previous element is:

```text
last = 2
```

We try every possible next value:

```text
j = 1 ... K
```

A transition from `last` to `j` is valid if:

```text
|last - j| <= 1
```

OR

```text
last == 2 * j
```

OR

```text
j == 2 * last
```

Therefore:

```java
if (Math.abs(last - j) <= 1 ||
    last == 2 * j ||
    j == 2 * last)
```

---

# Examples of Valid Transitions

## Difference Condition

```text
1 -> 1
1 -> 2
2 -> 1
2 -> 2
2 -> 3
3 -> 2
3 -> 3
```

because their difference is at most `1`.

---

## Multiplication Condition

The following are also valid:

```text
2 -> 1
1 -> 2

4 -> 2
2 -> 4

6 -> 3
3 -> 6
```

because one value is twice the other.

---

# Example 1

```text
N = 2
K = 2
```

Possible arrays:

```text
[1,1]
[1,2]
[2,1]
[2,2]
```

All are valid.

Therefore:

```text
Answer = 4
```

---

# Example 2

```text
N = 2
K = 3
```

Possible pairs are:

```text
[1,1]
[1,2]

[2,1]
[2,2]
[2,3]

[3,2]
[3,3]
```

Therefore:

```text
Answer = 7
```

---

# Example 3

```text
N = 3
K = 2
```

Since every pair of values from `{1,2}` satisfies the difference condition or the doubling condition, every possible array is valid.

There are:

```text
2^3 = 8
```

arrays.

Therefore:

```text
Answer = 8
```

---

# Dynamic Programming Approach

This problem can be solved using **Top-Down Dynamic Programming (Memoization)**.

## DP State

Define:

```text
dp[i][last]
```

as:

> The number of valid arrays that can be formed from position `i` to `N-1`, assuming the previous element is `last`.

---

# Why Do We Need `last`?

Consider:

```text
[2, ...]
```

The possible next elements depend on `2`.

If:

```text
last = 2
```

we need to determine which `j` values satisfy:

```text
|2-j| <= 1
```

or:

```text
2 = 2*j
```

or:

```text
j = 2*2
```

Therefore, the previous value is necessary information for making the next decision.

---

# Recurrence

For every possible next value:

```text
j = 1 ... K
```

check:

```text
|last - j| <= 1
```

OR:

```text
last = 2*j
```

OR:

```text
j = 2*last
```

If valid:

```text
dp[i][last] += dp[i+1][j]
```

Therefore:

```text
dp[i][last] =
    sum(dp[i+1][j])
```

for all valid `j`.

---

# Base Case

When we have reached the end of the array:

```java
if (i == n) {
    return 1;
}
```

Why return `1`?

Because we successfully constructed one complete valid array.

For example:

```text
[1,2,1]
```

when `i == n`, this represents one valid construction.

---

# Why We Cannot Start With `fun(0, 1)`

A common mistake is:

```java
return fun(0, 1);
```

This assumes that the first element is already `1`.

But the first element can be any value from:

```text
1 ... K
```

Therefore, we must try every possible first element.

```java
for (int first = 1; first <= k; first++) {
    ans += fun(1, first);
}
```

Now every possible first element is considered.

---

# Complete Java Solution

```java
class Solution {

    Integer[][] dp;

    int n;
    int k;

    static final int MOD = 1_000_000_007;

    public int count(int n, int k) {

        this.n = n;
        this.k = k;

        dp = new Integer[n][k + 1];

        long ans = 0;

        // Choose the first element
        for (int first = 1; first <= k; first++) {

            ans = (ans + fun(1, first)) % MOD;
        }

        return (int) ans;
    }

    public int fun(int i, int last) {

        // Complete array constructed
        if (i == n) {
            return 1;
        }

        // Already calculated
        if (dp[i][last] != null) {
            return dp[i][last];
        }

        long ans = 0;

        // Try every possible next value
        for (int j = 1; j <= k; j++) {

            if (Math.abs(last - j) <= 1 ||
                last == 2 * j ||
                j == 2 * last) {

                ans = (ans + fun(i + 1, j)) % MOD;
            }
        }

        return dp[i][last] = (int) ans;
    }
}
```

---

# Dry Run

Consider:

```text
N = 3
K = 3
```

We first choose the first element.

```text
first = 1
first = 2
first = 3
```

Consider:

```text
first = 2
```

We call:

```text
fun(1, 2)
```

Now try:

```text
j = 1
j = 2
j = 3
```

Check `2 -> j`.

### `j = 1`

```text
|2 - 1| = 1
```

Valid.

So:

```text
fun(2,1)
```

### `j = 2`

```text
|2 - 2| = 0
```

Valid.

So:

```text
fun(2,2)
```

### `j = 3`

```text
|2 - 3| = 1
```

Valid.

So:

```text
fun(2,3)
```

Therefore:

```text
fun(1,2)
=
fun(2,1)
+
fun(2,2)
+
fun(2,3)
```

The same process continues until:

```text
i == n
```

---

# Memoization

Without memoization, the same states are calculated repeatedly.

For example:

```text
fun(2,2)
```

could be reached through many different arrays.

Instead of calculating it every time, we store:

```text
dp[2][2]
```

and reuse it.

This changes the complexity significantly.

---

# Time Complexity

There are:

```text
N * K
```

possible states:

```text
dp[i][last]
```

For each state, we try:

```text
K
```

possible next values.

Therefore:

```text
Time Complexity = O(N * K^2)
```

---

# Space Complexity

The DP table contains:

```text
N * K
```

states.

Therefore:

```text
Space Complexity = O(N * K)
```

The recursion stack requires:

```text
O(N)
```

additional space.

Overall:

```text
Space = O(N * K)
```

---

# Why Modulo Is Required

The number of possible arrays can become extremely large.

For example, even without restrictions, the number of arrays of length `N` with values from `1...K` is:

```text
K^N
```

This can become enormous.

Therefore, the problem asks for:

```text
answer % 1,000,000,007
```

We use:

```java
static final int MOD = 1_000_000_007;
```

and:

```java
ans = (ans + fun(i + 1, j)) % MOD;
```

Using `long` for the temporary sum is safer:

```java
long ans = 0;
```

---

# Common Mistakes

## Mistake 1: Incorrect `Math.abs`

Wrong:

```java
Math.abs(last - j <= 1)
```

Correct:

```java
Math.abs(last - j) <= 1
```

`Math.abs()` must receive a number, not a boolean expression.

---

## Mistake 2: Starting from 1

Wrong:

```java
return fun(0, 1);
```

This forces the first element to be `1`.

Correct:

```java
for (int first = 1; first <= k; first++) {
    ans += fun(1, first);
}
```

---

## Mistake 3: Forgetting modulo

Wrong:

```java
ans += fun(i + 1, j);
```

Correct:

```java
ans = (ans + fun(i + 1, j)) % MOD;
```

---

## Mistake 4: Using `int` for large intermediate sums

Prefer:

```java
long ans = 0;
```

and cast only when storing the final modulo result:

```java
return dp[i][last] = (int) ans;
```

---

# Alternative DP Interpretation

You can also think of the problem as a **graph DP**.

Each value:

```text
1, 2, 3, ..., K
```

is a node.

There is an edge:

```text
x -> y
```

if:

```text
|x-y| <= 1
```

or:

```text
x = 2*y
```

or:

```text
y = 2*x
```

Then the problem becomes:

> Count the number of paths of length `N-1` in this graph.

The DP state:

```text
dp[i][x]
```

means:

> Number of valid paths of length `i` ending at value `x`.

This is a useful way to recognize similar problems in coding interviews.

---

# Bottom-Up DP Idea

The same problem can be solved iteratively.

Let:

```text
dp[last]
```

represent the number of valid arrays ending with `last`.

Initially:

```text
dp[j] = 1
```

for every:

```text
j = 1 ... K
```

because any value can be the first element.

Then for each remaining position:

```text
newDp[j] = sum(dp[last])
```

for all `last` that can transition to `j`.

Conceptually:

```java
for (int len = 1; len < n; len++) {

    for (int last = 1; last <= k; last++) {

        for (int next = 1; next <= k; next++) {

            if (Math.abs(last - next) <= 1 ||
                last == 2 * next ||
                next == 2 * last) {

                newDp[next] += dp[last];
            }
        }
    }

    dp = newDp;
}
```

This is also:

```text
O(N * K^2)
```

but avoids recursion.

---

# Pattern to Remember

This problem belongs to the pattern:

```text
COUNT ARRAYS
+
CONSTRAINT BETWEEN ADJACENT ELEMENTS
+
DP ON (POSITION, PREVIOUS VALUE)
```

Whenever you see something like:

```text
Choose N elements
Each element is between 1 and K
The next element depends on the previous element
Count all valid arrays
```

think about:

```text
dp[position][previous]
```

---

# Similar Problems

### 1. Count arrays with adjacent difference at most 1

GeeksforGeeks:

https://www.geeksforgeeks.org/dsa/count-of-arrays-of-size-n-having-absolute-difference-between-adjacent-elements-at-most-1/

This is very close to the first condition of this problem.

---

### 2. Count arrays where adjacent elements divide each other

GeeksforGeeks:

https://www.geeksforgeeks.org/dsa/count-arrays-adjacent-elements-one-divide-another/

This is useful for understanding DP where the transition between consecutive values is based on a mathematical relationship.

---

### 3. Codeforces Problemset

https://codeforces.com/problemset

Search for:

```text
count arrays DP
adjacent elements DP
```

---

### 4. Codeforces – Again Counting Arrays

Easy:

https://codeforces.com/problemset/problem/1967/E1

Hard:

https://codeforces.com/problemset/problem/1967/E2

These are useful for advanced array-counting DP practice.

---

# Interview Explanation

If the interviewer asks you to explain the solution, you can say:

> "The validity of an array depends only on the previous element when choosing the next element. So I define a DP state `dp[i][last]`, representing the number of valid ways to construct the array from position `i` onward when the previous value is `last`. For every state, I try every value from `1` to `K` and check whether the transition satisfies at least one of the three conditions. If it does, I add the result of the next state. I memoize these states to avoid recomputation. Since there are `N*K` states and each state tries `K` transitions, the time complexity is `O(N*K^2)` and space complexity is `O(N*K)`."

---

# Key Takeaways

```text
DP State:
dp[i][last]

Base Case:
i == n -> return 1

Transition:
Try j from 1 to K

Valid transition:
|last-j| <= 1
OR
last == 2*j
OR
j == 2*last

First element:
Try every value from 1 to K

Modulo:
1,000,000,007

Time:
O(N*K^2)

Space:
O(N*K)
```

---

# Final Code

```java
class Solution {

    Integer[][] dp;

    int n;
    int k;

    static final int MOD = 1_000_000_007;

    public int count(int n, int k) {

        this.n = n;
        this.k = k;

        dp = new Integer[n][k + 1];

        long ans = 0;

        for (int first = 1; first <= k; first++) {
            ans = (ans + fun(1, first)) % MOD;
        }

        return (int) ans;
    }

    public int fun(int i, int last) {

        if (i == n) {
            return 1;
        }

        if (dp[i][last] != null) {
            return dp[i][last];
        }

        long ans = 0;

        for (int j = 1; j <= k; j++) {

            if (Math.abs(last - j) <= 1 ||
                last == 2 * j ||
                j == 2 * last) {

                ans = (ans + fun(i + 1, j)) % MOD;
            }
        }

        return dp[i][last] = (int) ans;
    }
}
```

## Complexity

```text
Time Complexity:  O(N * K^2)
Space Complexity: O(N * K)
```

## Core DP Pattern

```text
dp[position][previous_element]
```

**Remember this pattern:**
When the next choice depends only on the previous element, **DP on `(index, previous value)`** is usually the first approach to consider.
