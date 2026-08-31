# Maximum Sum with Constraints — DP Memoization

## 1. Problem Statement

Given an integer array `A` and two integers `K` and `D`, select **exactly `K` indices** such that:

1. Consecutive selected elements have **different parity**.
   - Odd must be followed by even.
   - Even must be followed by odd.
2. Consecutive selected indices must be at a distance of **at most `D`**.
3. Maximize the sum of the selected elements.

If it is impossible to select exactly `K` valid elements, return `0`.

---

## 2. Understanding the Constraints

Suppose the selected indices are:

```text
i1 < i2 < i3 < ... < iK
```

Then the index condition is:

```text
i2 - i1 <= D
i3 - i2 <= D
...
iK - i(K-1) <= D
```

The parity condition is:

```text
A[i1] % 2 != A[i2] % 2
A[i2] % 2 != A[i3] % 2
...
```

Finally, we want:

```text
A[i1] + A[i2] + ... + A[iK]
```

to be as large as possible.

---

# 3. Example 1

```text
A = [5, 2, 7, 4, 9]
K = 3
D = 2
```

Select:

```text
5, 2, 7
```

Their indices are:

```text
0, 1, 2
```

### Index condition

```text
1 - 0 = 1 <= 2
2 - 1 = 1 <= 2
```

Valid.

### Parity condition

```text
5 -> Odd
2 -> Even
7 -> Odd
```

So:

```text
Odd -> Even -> Odd
```

Valid.

### Sum

```text
5 + 2 + 7 = 14
```

Therefore:

```text
Answer = 14
```

---

# 4. Example 2

```text
A = [2, 4, 6, 8]
K = 3
D = 2
```

Every value is even:

```text
Even -> Even -> Even
```

Consecutive selected elements must have different parity.

Therefore, selecting 3 valid elements is impossible.

```text
Answer = 0
```

---

# 5. Example 3

Consider:

```text
A = [10, 3, 8, 5, 12]
K = 3
D = 2
```

Possible selection:

```text
10 -> 3 -> 8
```

Indices:

```text
0 -> 1 -> 2
```

Parity:

```text
Even -> Odd -> Even
```

Sum:

```text
10 + 3 + 8 = 21
```

Another possible selection:

```text
3 -> 8 -> 5
```

Sum:

```text
3 + 8 + 5 = 16
```

So the better answer is:

```text
21
```

---

# 6. Brute Force Idea

A simple approach would be to try every possible selection of `K` indices.

For example:

```text
Choose K indices
        |
        v
Check index distance
        |
        v
Check parity
        |
        v
Calculate sum
        |
        v
Take maximum
```

The number of possible combinations is very large.

Approximately:

```text
C(N, K)
```

Therefore, brute force is not practical for large `N`.

We need Dynamic Programming.

---

# 7. Why Dynamic Programming?

Notice that after selecting an element, the future choices only depend on:

1. How many elements we have selected.
2. Which index was selected last.

We do **not** need to remember the entire sequence of previously selected indices.

For example:

```text
5 -> 2
```

and another path that also ends at:

```text
5 -> 2
```

has exactly the same future possibilities.

Therefore, both paths can share the same DP state.

This is the overlapping-subproblems property of Dynamic Programming.

---

# 8. DP State

Define:

```text
solve(selected, last)
```

where:

```text
selected = number of elements selected so far
last = index of the last selected element
```

Meaning:

> `solve(selected, last)` returns the maximum sum obtainable when `selected` elements have already been selected and `last` is the index of the most recently selected element.

---

# 9. Memoization Table

We store each state in:

```text
dp[selected][last]
```

For example:

```text
dp[2][3]
```

means:

> We have selected 2 elements and the last selected element is at index 3. What is the maximum sum we can obtain?

Because `selected` ranges from `1` to `K` and `last` ranges from `0` to `N-1`, the number of states is:

```text
K * N
```

---

# 10. Base Case

If:

```text
selected == K
```

we have already selected exactly the required number of elements.

Therefore:

```java
if (selected == K) {
    return A[last];
}
```

Why return only `A[last]`?

Because the previous recursive calls already add the values of the previous selected elements.

For example:

```text
solve(1, 0)
    |
    v
solve(2, 1)
    |
    v
solve(3, 2)
```

For:

```text
A = [5, 2, 7]
```

the base case returns:

```text
A[2] = 7
```

Then the previous calls add:

```text
2 + 7
```

and then:

```text
5 + 2 + 7
```

---

# 11. Finding the Next Index

Suppose:

```text
last = 3
D = 2
```

The next selected index can be:

```text
4
5
```

because:

```text
4 - 3 = 1 <= 2
5 - 3 = 2 <= 2
```

Therefore:

```java
int start = last + 1;
int end = Math.min(n - 1, last + D);
```

Then:

```java
for (int next = start; next <= end; next++) {
```

checks every possible next index.

---

# 12. Checking Parity

We need:

```text
Odd -> Even
Even -> Odd
```

In Java:

```java
(A[last] & 1)
```

returns:

```text
0 -> Even
1 -> Odd
```

Therefore:

```java
if ((A[last] & 1) != (A[next] & 1))
```

means:

```text
A[last] and A[next] have different parity
```

### Example

```text
A[last] = 5
A[next] = 2
```

Then:

```text
5 & 1 = 1
2 & 1 = 0
```

Since:

```text
1 != 0
```

the transition is valid.

---

# 13. Recursive Transition

If `next` is valid:

```java
long result = solve(selected + 1, next);
```

We have selected one more element.

The total sum is:

```java
A[last] + result
```

So:

```java
ans = Math.max(ans, A[last] + result);
```

We try every valid `next` and take the maximum.

---

# 14. Memoization

Before calculating a state:

```java
if (dp[selected][last] != null) {
    return dp[selected][last];
}
```

If the state was already calculated, return the stored result.

After calculating:

```java
dp[selected][last] = ans;
```

This is memoization.

### Without memoization

The same state can be calculated repeatedly:

```text
solve(2, 3)
solve(2, 3)
solve(2, 3)
...
```

### With memoization

It is calculated once:

```text
solve(2, 3)
    |
    v
dp[2][3] = answer
```

Every later call simply returns:

```text
dp[2][3]
```

---

# 15. Complete Java Memoization Code

```java
class Solution {

    int n, K, D;
    int[] A;
    Long[][] dp;

    public long maxSum(int[] arr, int k, int d) {

        A = arr;
        n = A.length;
        K = k;
        D = d;

        dp = new Long[n][K + 1];

        long ans = Long.MIN_VALUE;

        for (int i = 0; i < n; i++) {
            ans = Math.max(ans, solve(1, i));
        }

        return ans == Long.MIN_VALUE ? 0 : ans;
    }

    long solve(int k, int i) {

        if (k == K) {
            return A[i];
        }

        if (dp[i][k] != null) {
            return dp[i][k];
        }

        long ans = Long.MIN_VALUE;

        for (int j = i + 1; j <= Math.min(n - 1, i + D); j++) {

            if ((A[i] & 1) != (A[j] & 1)) {

                long res = solve(k + 1, j);

                if (res != Long.MIN_VALUE) {
                    ans = Math.max(ans, A[i] + res);
                }
            }
        }

        return dp[i][k] = ans;
    }
}
```

---

# 16. Dry Run

Use:

```text
A = [5, 2, 7, 4, 9]
K = 3
D = 2
```

Start with index `0`:

```text
solve(1, 0)
```

Current value:

```text
A[0] = 5
```

Possible next indices:

```text
1, 2
```

Choose index `1`:

```text
5 -> 2
```

Different parity:

```text
Odd -> Even
```

So call:

```text
solve(2, 1)
```

From index `1`, possible next indices:

```text
2, 3
```

Choose index `2`:

```text
2 -> 7
```

Different parity:

```text
Even -> Odd
```

So call:

```text
solve(3, 2)
```

Now:

```text
selected == K
```

Therefore:

```text
solve(3, 2) = 7
```

Return to:

```text
solve(2, 1)
```

Calculate:

```text
2 + 7 = 9
```

Therefore:

```text
solve(2, 1) = 9
```

Return to:

```text
solve(1, 0)
```

Calculate:

```text
5 + 9 = 14
```

Therefore:

```text
solve(1, 0) = 14
```

---

# 17. Recursion Tree

For the path above:

```text
solve(1, 0)
      |
      | choose index 1
      v
solve(2, 1)
      |
      | choose index 2
      v
solve(3, 2)
      |
      v
     7
```

Values are added while returning:

```text
solve(3, 2) = 7

solve(2, 1) = 2 + 7
            = 9

solve(1, 0) = 5 + 9
            = 14
```

---

# 18. Why Do We Try Every First Index?

The first selected index has no previous element.

Therefore, any index can potentially be the first selected index.

```java
for (int i = 0; i < n; i++) {
    ans = Math.max(ans, solve(1, i));
}
```

For:

```text
A = [5, 2, 7, 4, 9]
```

we try:

```text
solve(1, 0)
solve(1, 1)
solve(1, 2)
solve(1, 3)
solve(1, 4)
```

and take the maximum valid result.

---

# 19. Why Is `Long` Used for DP?

The answer is a sum of multiple array elements.

If the array values or `K` are large, the sum may exceed the range of Java's `int`.

Therefore, using:

```java
long
```

is safer.

The memo table uses:

```java
Long[][]
```

instead of:

```java
long[][]
```

because `null` is useful to represent:

> This state has not been calculated yet.

---

# 20. Why Use `Long.MIN_VALUE`?

We need to distinguish between:

```text
valid answer = negative number
```

and:

```text
impossible state
```

For example, an array could contain:

```text
[-5, -2, -7]
```

So we cannot use `0` to represent an impossible state.

Instead:

```java
long ans = Long.MIN_VALUE;
```

means:

```text
No valid selection found yet.
```

---

# 21. Why `Long.MIN_VALUE / 4` Is Sometimes Used

If we use:

```java
Long.MIN_VALUE
```

and then add a value to it, overflow can occur.

For example:

```java
Long.MIN_VALUE + A[last]
```

could overflow.

One safe alternative is:

```java
long NEG = Long.MIN_VALUE / 4;
```

In the implementation above, we avoid adding `Long.MIN_VALUE` by checking:

```java
if (result != Long.MIN_VALUE)
```

before adding.

---

# 22. Complexity

There are:

```text
K * N
```

possible states.

For every state, we may check up to:

```text
D
```

next indices.

Therefore:

```text
Time Complexity = O(K * N * D)
```

Memoization reduces repeated recursion, but each state still examines up to `D` transitions.

### Space

The memo table contains:

```text
K * N
```

entries.

Therefore:

```text
DP Space = O(K * N)
```

The recursion depth is at most:

```text
K
```

So total auxiliary space is:

```text
O(K * N + K)
```

which is generally written as:

```text
O(K * N)
```

---

# 23. Top-Down vs Bottom-Up

This solution is **Top-Down DP**.

### Top-Down

```text
Start with the answer
        |
        v
solve(...)
        |
        v
recursively solve smaller states
        |
        v
memoize results
```

### Bottom-Up

Would calculate:

```text
dp[1]
dp[2]
dp[3]
...
dp[K]
```

iteratively.

The current solution uses recursion + memoization because it is easier to understand the choices.

---

# 24. Common Mistakes

## Mistake 1: Selecting any next index

Incorrect:

```java
for (int next = last + 1; next < n; next++)
```

This ignores `D`.

Correct:

```java
int end = Math.min(n - 1, last + D);

for (int next = last + 1; next <= end; next++)
```

---

## Mistake 2: Checking same parity

Incorrect:

```java
if ((A[last] & 1) == (A[next] & 1))
```

This allows:

```text
Odd -> Odd
Even -> Even
```

We need different parity.

Correct:

```java
if ((A[last] & 1) != (A[next] & 1))
```

---

## Mistake 3: Stopping at `selected > K`

The important base case is:

```java
if (selected == K)
```

because the problem requires **exactly** `K` selections.

---

## Mistake 4: Using `0` for impossible states

This is wrong if negative values are possible.

For example:

```text
A = [-5, -2, -7]
```

A valid answer could be negative.

Use:

```java
Long.MIN_VALUE
```

to represent impossible states.

---

## Mistake 5: Forgetting the memo check

Without:

```java
if (dp[selected][last] != null) {
    return dp[selected][last];
}
```

the recursion becomes much slower because the same states are recalculated.

---

# 25. Interview Explanation

A concise interview explanation:

> I use top-down dynamic programming with memoization. The state is `(selected, last)`, where `selected` represents how many elements have already been chosen and `last` is the index of the most recently chosen element. From `last`, I try every next index within distance `D` and only allow it when the current and next values have different parity. The transition adds the current value to the best result from the next state. When `selected == K`, we return the last value. Each state is memoized, giving `O(KND)` time and `O(KN)` space.

---

# 26. Core Pattern to Remember

This problem follows the general pattern:

```text
State:
    (number selected, last index)

Choices:
    next index within D

Condition:
    different parity

Transition:
    current value + best future result

Base:
    selected == K

Memo:
    dp[selected][last]

Answer:
    maximum over every possible first index
```

The most important line is:

```java
ans = Math.max(ans, A[last] + solve(selected + 1, next));
```

It means:

> Take the current element and add the best possible sum after choosing a valid next element.

---

# 27. Useful Links

- [GeeksforGeeks — Dynamic Programming](https://www.geeksforgeeks.org/dynamic-programming/)
- [GeeksforGeeks — Introduction to Dynamic Programming](https://www.geeksforgeeks.org/solve-dynamic-programming-problem/)
- [LeetCode — Dynamic Programming Problems](https://leetcode.com/tag/dynamic-programming/)
- [LeetCode — Maximum Subsequence Sum With Constraints](https://leetcode.com/problemset/)

---

# 28. Final Summary

For this problem:

```text
A = array
K = exact number of elements
D = maximum distance between consecutive selected indices
```

We define:

```text
solve(selected, last)
```

The recursion:

```text
solve(selected, last)
        |
        +-- try next = last + 1 ... last + D
        |
        +-- check different parity
        |
        +-- solve(selected + 1, next)
        |
        +-- add A[last]
        |
        +-- take maximum
        |
        +-- store in dp[selected][last]
```

Final answer:

```text
max(
    solve(1, 0),
    solve(1, 1),
    ...
    solve(1, N-1)
)
```

If no valid selection of exactly `K` elements exists:

```text
0
```

## Complexity

```text
Time:  O(K * N * D)
Space: O(K * N)
```
