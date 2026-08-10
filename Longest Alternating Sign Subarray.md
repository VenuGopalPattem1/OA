# Infosys OA Q1 — Longest Alternating Sign Subarray

## Problem

You are given two integer arrays `A` and `B`, both of size `N`.

You must construct a new array `C` of size `N`.

For every index `i`, you must choose exactly one value:

```text
C[i] = A[i]
```

or

```text
C[i] = B[i]
```

Your task is to **maximize the length of the longest contiguous subarray of `C`** in which consecutive elements have alternating signs.

The signs must follow either:

```text
Positive, Negative, Positive, Negative, ...
```

or:

```text
Negative, Positive, Negative, Positive, ...
```

It is guaranteed that no element in either `A` or `B` is zero.

---

# Example

Suppose:

```text
A = [1, -2, 3, -4]
B = [-1, 2, -3, 4]
```

We can choose:

```text
C = [1, -2, 3, -4]
```

The signs are:

```text
+  -  +  -
```

They alternate at every position.

Therefore, the longest alternating subarray has length:

```text
4
```

---

# What Does Contiguous Mean?

A contiguous subarray contains elements next to each other.

For:

```text
C = [1, -2, 3, -4, 5]
```

These are contiguous subarrays:

```text
[1, -2]
[-2, 3]
[3, -4]
[-4, 5]
[1, -2, 3]
[1, -2, 3, -4]
```

But:

```text
[1, 3, 5]
```

is NOT contiguous because elements were skipped.

---

# What Does Alternating Sign Mean?

Consecutive elements must have opposite signs.

For example:

```text
[1, -2, 3, -4]
```

has signs:

```text
+ - + -
```

This is alternating.

Similarly:

```text
[-1, 2, -3, 4]
```

has signs:

```text
- + - +
```

This is also alternating.

But:

```text
[1, 2, -3, 4]
```

has signs:

```text
+ + - +
```

The first two elements have the same sign, so the whole sequence is not alternating.

---

# Important Observation

We don't care about the actual magnitude of the values.

We only care about whether a value is:

```text
Positive
```

or:

```text
Negative
```

For example:

```text
100
```

and:

```text
5
```

are both positive.

Similarly:

```text
-100
```

and:

```text
-2
```

are both negative.

Therefore, the important operation is checking:

```java
A[i] > 0
```

or:

```java
A[i] < 0
```

---

# Test Case 1

```text
N = 4

A = [1, -2, 3, -4]
B = [-1, 2, -3, 4]

Output:
4
```

Choose:

```text
C = [1, -2, 3, -4]
```

Signs:

```text
+ - + -
```

The entire array is alternating.

Therefore:

```text
Answer = 4
```

---

# Test Case 2

```text
N = 5

A = [1, 2, -3, 4, 5]
B = [-1, -2, 3, -4, -5]

Output:
5
```

Choose:

```text
C = [-1, 2, -3, 4, -5]
```

Signs:

```text
- + - + -
```

The entire array alternates.

Therefore:

```text
Answer = 5
```

---

# Test Case 3

```text
N = 6

A = [1, 2, 3, -4, -5, 6]
B = [-1, -2, -3, 4, 5, -6]

Output:
6
```

Choose:

```text
C = [1, -2, 3, -4, 5, -6]
```

Signs:

```text
+ - + - + -
```

Therefore:

```text
Answer = 6
```

---

# DP Approach

This problem is a **2D Dynamic Programming / Choice DP** problem.

At every index, we have two choices:

```text
A[i]
B[i]
```

Therefore, we need to remember which choice we made at the previous index.

Define:

```text
dp[i][0]
```

as:

> Maximum length of an alternating subarray ending at index `i` when we choose `A[i]`.

And:

```text
dp[i][1]
```

as:

> Maximum length of an alternating subarray ending at index `i` when we choose `B[i]`.

---

# Why Do We Need Two States?

Consider:

```text
A[i] = 5
B[i] = -5
```

At position `i`, we have two possible signs:

```text
A[i] → positive
B[i] → negative
```

The previous choice affects whether the current choice can extend the alternating sequence.

Therefore, we need to know whether the previous element was selected from:

```text
A[i-1]
```

or:

```text
B[i-1]
```

That's why we use:

```text
dp[i][0]
dp[i][1]
```

---

# State Definition

```text
dp[i][0]
```

means:

```text
Choose A[i]
and find the maximum alternating length ending at i.
```

```text
dp[i][1]
```

means:

```text
Choose B[i]
and find the maximum alternating length ending at i.
```

---

# Base Case

At index `0`, there is only one element.

Regardless of whether we choose `A[0]` or `B[0]`, the length is `1`.

Therefore:

```java
dp[0][0] = 1;
dp[0][1] = 1;
```

---

# Transition

Suppose we want to calculate:

```text
dp[i][0]
```

That means we choose:

```text
A[i]
```

We can extend the previous alternating subarray if:

```text
sign(A[i]) != sign(A[i-1])
```

Then:

```text
dp[i][0] = dp[i-1][0] + 1
```

We can also come from:

```text
B[i-1]
```

if:

```text
sign(A[i]) != sign(B[i-1])
```

Then:

```text
dp[i][0] = dp[i-1][1] + 1
```

So:

```text
dp[i][0] =
    max(
        dp[i-1][0] + 1,
        dp[i-1][1] + 1
    )
```

but only when the corresponding signs are different.

---

# Transition for B[i]

Similarly:

```text
dp[i][1]
```

means we choose:

```text
B[i]
```

We can come from `A[i-1]` if:

```text
sign(B[i]) != sign(A[i-1])
```

or from `B[i-1]` if:

```text
sign(B[i]) != sign(B[i-1])
```

---

# Simple Sign Function

Because there are no zero values, we can simply use:

```java
A[i] > 0
```

to determine whether the value is positive.

A convenient method is:

```java
boolean opposite(int x, int y) {
    return (x > 0 && y < 0) ||
           (x < 0 && y > 0);
}
```

This returns `true` when the two numbers have opposite signs.

---

# Complete Java Solution

```java
class Solution {

    public int longestAlternating(int[] A, int[] B) {

        int n = A.length;

        int[][] dp = new int[n][2];

        // At index 0, either choice gives length 1
        dp[0][0] = 1;
        dp[0][1] = 1;

        int ans = 1;

        for (int i = 1; i < n; i++) {

            // Choose A[i]
            dp[i][0] = 1;

            if (opposite(A[i], A[i - 1])) {
                dp[i][0] = Math.max(
                    dp[i][0],
                    dp[i - 1][0] + 1
                );
            }

            if (opposite(A[i], B[i - 1])) {
                dp[i][0] = Math.max(
                    dp[i][0],
                    dp[i - 1][1] + 1
                );
            }


            // Choose B[i]
            dp[i][1] = 1;

            if (opposite(B[i], A[i - 1])) {
                dp[i][1] = Math.max(
                    dp[i][1],
                    dp[i - 1][0] + 1
                );
            }

            if (opposite(B[i], B[i - 1])) {
                dp[i][1] = Math.max(
                    dp[i][1],
                    dp[i - 1][1] + 1
                );
            }

            ans = Math.max(
                ans,
                Math.max(dp[i][0], dp[i][1])
            );
        }

        return ans;
    }

    private boolean opposite(int x, int y) {

        return (x > 0 && y < 0) ||
               (x < 0 && y > 0);
    }
}
```

---

# Understanding the Code

Consider:

```text
A = [1, -2, 3]
B = [-1, 2, -3]
```

At index `0`:

```text
A[0] = 1
B[0] = -1
```

We can choose either.

Therefore:

```text
dp[0][0] = 1
dp[0][1] = 1
```

---

## Index 1

We have:

```text
A[1] = -2
B[1] = 2
```

Suppose we choose:

```text
A[1] = -2
```

We can come from:

```text
A[0] = 1
```

because:

```text
+ -> -
```

So:

```text
dp[1][0] = dp[0][0] + 1
         = 2
```

We can also come from:

```text
B[0] = -1
```

but:

```text
- -> -
```

is not alternating.

So we cannot extend from `B[0]`.

---

# Index 2

Now:

```text
A[2] = 3
B[2] = -3
```

If we choose:

```text
A[2] = 3
```

we can come from:

```text
A[1] = -2
```

because:

```text
- -> +
```

Therefore:

```text
dp[2][0] = dp[1][0] + 1
         = 3
```

So we have:

```text
[1, -2, 3]
```

with signs:

```text
+ - +
```

Answer becomes:

```text
3
```

---

# Visualizing the DP

For:

```text
A = [1, -2, 3]
B = [-1, 2, -3]
```

The states can look like:

```text
             Index 0       Index 1       Index 2

Choose A       1              2              3
                \             /
                 \           /
                  \         /
Choose B       1              2              3
```

The important point is:

> We can transition from the previous `A` or previous `B` only when their signs are opposite to the current selected value.

---

# Why Is This a Subarray Problem?

A subarray must be contiguous.

Suppose:

```text
C = [+, -, -, +, -]
```

The longest alternating subarray is:

```text
[+, -]
```

or:

```text
[+, -]
```

at the end.

We cannot skip the repeated `-`.

This is why when the signs don't alternate, we reset the current length to:

```text
1
```

That's why the transition starts with:

```java
dp[i][0] = 1;
dp[i][1] = 1;
```

The current element itself always forms a valid subarray of length `1`.

---

# Why We Take Maximum?

At every index there may be multiple ways to reach the current choice.

For example:

```text
A[i]
```

could come from:

```text
A[i-1]
```

or:

```text
B[i-1]
```

If both are valid, we take the larger one:

```java
Math.max(...)
```

because we want the longest alternating subarray.

---

# Complexity

There are `N` positions.

At every position, we calculate only two states:

```text
dp[i][0]
dp[i][1]
```

Each state performs a constant number of operations.

Therefore:

```text
Time Complexity = O(N)
```

The DP table contains:

```text
N × 2
```

values.

Therefore:

```text
Space Complexity = O(N)
```

---

# Space Optimized Solution

We actually only need the previous index.

We don't need the entire DP table.

So we can store:

```text
prevA
prevB
```

instead of:

```text
dp[n][2]
```

This reduces space from:

```text
O(N)
```

to:

```text
O(1)
```

---

# O(1) Space Java Solution

```java
class Solution {

    public int longestAlternating(int[] A, int[] B) {

        int n = A.length;

        int prevA = 1;
        int prevB = 1;

        int ans = 1;

        for (int i = 1; i < n; i++) {

            int currA = 1;
            int currB = 1;

            // Choose A[i]

            if (opposite(A[i], A[i - 1])) {
                currA = Math.max(currA, prevA + 1);
            }

            if (opposite(A[i], B[i - 1])) {
                currA = Math.max(currA, prevB + 1);
            }


            // Choose B[i]

            if (opposite(B[i], A[i - 1])) {
                currB = Math.max(currB, prevA + 1);
            }

            if (opposite(B[i], B[i - 1])) {
                currB = Math.max(currB, prevB + 1);
            }


            // Move current to previous
            prevA = currA;
            prevB = currB;

            ans = Math.max(
                ans,
                Math.max(currA, currB)
            );
        }

        return ans;
    }

    private boolean opposite(int x, int y) {

        return (x > 0 && y < 0) ||
               (x < 0 && y > 0);
    }
}
```

Complexity:

```text
Time  = O(N)
Space = O(1)
```

---

# Important Difference: Subarray vs Subsequence

This is a common interview trap.

### Subarray

Must be contiguous.

```text
[1, -2, 3, -4]
```

You cannot skip elements.

### Subsequence

Does not need to be contiguous.

For:

```text
[1, 2, -3, 4, -5]
```

you could select:

```text
[1, -3, 4, -5]
```

if allowed.

This problem specifically asks for a **contiguous subarray**, so we must not skip indices.

---

# Common Mistakes

## Mistake 1 — Checking values instead of signs

Wrong:

```java
if (A[i] != A[i - 1])
```

Different values do not necessarily mean different signs.

For example:

```text
5 and 10
```

are different but both positive.

Correct:

```java
opposite(A[i], A[i - 1])
```

---

## Mistake 2 — Forgetting both choices

At every position:

```text
C[i] = A[i]
```

or:

```text
C[i] = B[i]
```

We must consider both.

That's why we have:

```text
dp[i][0]
dp[i][1]
```

---

## Mistake 3 — Only comparing A with A and B with B

You must also consider:

```text
A[i] <- B[i-1]
```

and:

```text
B[i] <- A[i-1]
```

There are four possible transitions:

```text
A[i-1] -> A[i]
A[i-1] -> B[i]

B[i-1] -> A[i]
B[i-1] -> B[i]
```

---

# Four Possible Transitions

At every index:

```text
Previous        Current

A[i-1]   --->   A[i]
A[i-1]   --->   B[i]

B[i-1]   --->   A[i]
B[i-1]   --->   B[i]
```

A transition is allowed if:

```text
sign(previous) != sign(current)
```

---

# DP State to Remember

The most important part of this problem is:

```text
dp[i][choice]
```

where:

```text
choice = 0 → choose A[i]
choice = 1 → choose B[i]
```

This is a common pattern for problems where:

> At every index, choose one of two values and maximize/minimize something based on the previous choice.

---

# General Template for Similar Problems

Whenever you see:

```text
For every i:
choose A[i] or B[i]
```

think:

```text
dp[i][0]
dp[i][1]
```

Then ask:

```text
Can I transition from previous A?
Can I transition from previous B?
```

For this problem:

```text
Transition allowed if signs are opposite.
```

---

# Interview Explanation

A concise interview explanation:

> "At each index I have two choices, A[i] or B[i]. Since whether the current element can extend the alternating subarray depends on the sign of the previous selected element, I maintain two DP states. `dp[i][0]` is the maximum alternating subarray length ending at `i` when choosing `A[i]`, and `dp[i][1]` is the same when choosing `B[i]`. For each state I check both possible choices from the previous index and extend only when their signs are opposite. If neither transition is possible, the current element starts a new subarray of length 1. The answer is the maximum value across all states."

---

# Quick Revision

```text
Problem:
Choose A[i] or B[i].

Goal:
Longest contiguous subarray with alternating signs.

State:
dp[i][0] = choose A[i]
dp[i][1] = choose B[i]

Base:
dp[0][0] = dp[0][1] = 1

Transition:
Current sign must be opposite to previous sign.

Four transitions:
A -> A
A -> B
B -> A
B -> B

If valid:
current = previous + 1

Otherwise:
current = 1

Answer:
max(dp[i][0], dp[i][1])

Time:
O(N)

Space:
O(N)

Optimized Space:
O(1)
```

---

# Similar Practice Problems

### 1. LeetCode — Longest Alternating Subarray

[LeetCode 2765 — Longest Alternating Subarray](https://leetcode.com/problems/longest-alternating-subarray/?utm_source=chatgpt.com)

Useful for understanding the basic alternating contiguous-subarray pattern.

### 2. LeetCode — Wiggle Subsequence

[LeetCode 376 — Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/?utm_source=chatgpt.com)

Useful for understanding alternating DP, although it is a subsequence rather than a subarray.

### 3. GeeksforGeeks — Longest Alternating Subarray

[GeeksforGeeks — Longest Alternating Subarray](https://www.geeksforgeeks.org/dsa/longest-alternating-subarray-prefix-even-odd-elements/?utm_source=chatgpt.com)

Good practice for alternating subarray problems.

### 4. GeeksforGeeks — Longest Alternating Subsequence

[GeeksforGeeks — Longest Alternating Subsequence](https://www.geeksforgeeks.org/dsa/longest-alternating-subsequence/?utm_source=chatgpt.com)

Useful for learning the broader alternating DP pattern.

---

# Final Recommended Solution

For the Infosys OA, use the **2D DP solution first** because it is easier to understand and explain:

```java
class Solution {

    public int longestAlternating(int[] A, int[] B) {

        int n = A.length;

        int[][] dp = new int[n][2];

        dp[0][0] = 1;
        dp[0][1] = 1;

        int ans = 1;

        for (int i = 1; i < n; i++) {

            dp[i][0] = 1;
            dp[i][1] = 1;

            if (opposite(A[i], A[i - 1])) {
                dp[i][0] = Math.max(dp[i][0],
                                    dp[i - 1][0] + 1);
            }

            if (opposite(A[i], B[i - 1])) {
                dp[i][0] = Math.max(dp[i][0],
                                    dp[i - 1][1] + 1);
            }

            if (opposite(B[i], A[i - 1])) {
                dp[i][1] = Math.max(dp[i][1],
                                    dp[i - 1][0] + 1);
            }

            if (opposite(B[i], B[i - 1])) {
                dp[i][1] = Math.max(dp[i][1],
                                    dp[i - 1][1] + 1);
            }

            ans = Math.max(ans,
                    Math.max(dp[i][0], dp[i][1]));
        }

        return ans;
    }

    private boolean opposite(int x, int y) {
        return (x > 0 && y < 0) ||
               (x < 0 && y > 0);
    }
}
```

**Core idea to remember:**

```text
2 choices at every index
        ↓
dp[i][2]
        ↓
check opposite sign
        ↓
extend previous alternating subarray
        ↓
take maximum
```
