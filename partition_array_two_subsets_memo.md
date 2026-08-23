# Partition Array into Two Subsets — Groww OA

## Problem Description

You are given an array `arr` of `N` positive integers and an integer `K`.

Your task is to divide the array into **two non-empty subsets** such that the sum of elements in **both subsets is strictly greater than `K`**.

Return `"YES"` if such a partition is possible; otherwise return `"NO"`.

### Constraints

- `1 <= N <= 10^5`
- `1 <= K <= 10^5`
- `1 <= arr[i] <= 10^5`
- Both subsets must be non-empty.

---

## Examples

### Example 1

```text
arr = [4, 6, 7, 3]
K = 8
```

One valid partition is:

```text
Subset 1 = [4, 6] -> sum = 10
Subset 2 = [7, 3] -> sum = 10
```

Both sums are greater than `8`.

```text
Output: YES
```

### Example 2

```text
arr = [2, 2, 2, 2]
K = 3
```

Partition:

```text
Subset 1 = [2, 2] -> sum = 4
Subset 2 = [2, 2] -> sum = 4
```

```text
Output: YES
```

### Example 3

```text
arr = [1, 2, 3]
K = 3
```

Total:

```text
1 + 2 + 3 = 6
```

Both subsets need to have sum greater than `3`.

The best possible split is:

```text
[1]       -> 1
[2, 3]    -> 5
```

The first subset is not greater than `3`.

```text
Output: NO
```

### Example 4

```text
arr = [5, 1, 1, 1, 1, 5]
K = 6
```

Partition:

```text
Subset 1 = [5, 1, 1] -> 7
Subset 2 = [5, 1, 1] -> 7
```

```text
Output: YES
```

---

# Main Observation

Let the total sum of the array be:

```text
total = sum(arr)
```

Suppose one subset has sum:

```text
sum1
```

Then the other subset automatically has:

```text
sum2 = total - sum1
```

The requirement is:

```text
sum1 > K
sum2 > K
```

Substitute:

```text
sum1 > K
total - sum1 > K
```

Rearrange the second condition:

```text
total - sum1 > K

total - K > sum1

sum1 < total - K
```

Therefore, both requirements are equivalent to:

```text
K < sum1 < total - K
```

Since all values are integers, this means:

```text
K + 1 <= sum1 <= total - K - 1
```

So the problem becomes:

> Find a non-empty subset whose sum lies strictly between `K` and `total - K`.

---

# Important Initial Check

Before running DP:

```java
if (total <= 2 * K) {
    return "NO";
}
```

Why?

Both subsets need to be greater than `K`.

Therefore their combined sum must be:

```text
> K + K
```

So:

```text
total > 2 * K
```

If:

```text
total <= 2 * K
```

there is no possible partition.

---

# Memoization Solution

```java
class Solution {

    Boolean[][] dp;

    public String canPartition(int[] arr, int K) {

        int n = arr.length;

        int total = 0;

        for (int x : arr) {
            total += x;
        }

        // Both subsets must have sum > K
        if (total <= 2 * K) {
            return "NO";
        }

        dp = new Boolean[n][total + 1];

        return fun(0, 0, arr, K, total) ? "YES" : "NO";
    }

    boolean fun(int i, int sum, int[] arr, int K, int total) {

        // We found a subset whose sum satisfies
        // both subset requirements.
        if (sum > K && sum < total - K) {
            return true;
        }

        // Sum is already too large.
        // The other subset would have sum <= K.
        if (sum >= total - K) {
            return false;
        }

        // No elements left
        if (i == arr.length) {
            return false;
        }

        if (dp[i][sum] != null) {
            return dp[i][sum];
        }

        // Take arr[i] into subset 1
        boolean take = fun(
            i + 1,
            sum + arr[i],
            arr,
            K,
            total
        );

        // Do not take arr[i] into subset 1.
        // It automatically belongs to subset 2.
        boolean skip = fun(
            i + 1,
            sum,
            arr,
            K,
            total
        );

        return dp[i][sum] = take || skip;
    }
}
```

---

# Dry Run

Consider:

```text
arr = [4, 6, 7, 3]
K = 8
```

First calculate:

```text
total = 4 + 6 + 7 + 3
      = 20
```

We need:

```text
subset1 > 8
subset2 > 8
```

Since:

```text
subset2 = 20 - subset1
```

we need:

```text
subset1 > 8
20 - subset1 > 8
```

Therefore:

```text
8 < subset1 < 12
```

So valid subset sums are:

```text
9, 10, 11
```

---

## Recursion

Start:

```text
fun(0, 0)
```

At index `0`:

```text
arr[0] = 4
```

Take it:

```text
fun(1, 4)
```

At index `1`:

```text
arr[1] = 6
```

Take it:

```text
fun(2, 10)
```

Now:

```text
sum = 10
K = 8
total - K = 12
```

Check:

```text
10 > 8       YES
10 < 12      YES
```

Therefore:

```text
return true
```

The chosen subset is:

```text
Subset 1 = [4, 6]
sum1 = 10
```

The remaining elements form:

```text
Subset 2 = [7, 3]
sum2 = 10
```

Both satisfy:

```text
10 > 8
10 > 8
```

Therefore:

```text
Output = YES
```

---

# Why the Condition Works

This is the most important part.

Suppose:

```text
total = 20
K = 8
sum1 = 10
```

Then:

```text
sum2 = total - sum1
     = 20 - 10
     = 10
```

Our condition checks:

```java
sum > K && sum < total - K
```

Substitute:

```text
10 > 8       -> true
10 < 20 - 8  -> true
```

Therefore both subsets are valid.

---

## Invalid Case

Suppose:

```text
sum1 = 13
```

Then:

```text
sum2 = 20 - 13
     = 7
```

Check:

```text
13 > 8       -> true
13 < 12      -> false
```

So the condition rejects it.

And correctly:

```text
sum2 = 7
7 > 8 -> false
```

---

# Why We Can Use Only One Subset Sum

We do not need to explicitly build both subsets.

Once we choose a subset with sum `sum1`:

```text
sum2 = total - sum1
```

So checking:

```java
sum > K && sum < total - K
```

simultaneously guarantees:

```text
sum1 > K
```

and:

```text
sum2 > K
```

This is the key transformation:

```text
Original:

sum1 > K
sum2 > K

        ↓

sum2 = total - sum1

        ↓

sum1 > K
total - sum1 > K

        ↓

K < sum1 < total - K
```

---

# Take / Skip Meaning

The recursion is standard subset-sum DP.

At every element:

```text
                arr[i]
                /    \
             TAKE    SKIP
              /        \
      subset 1      subset 2
```

### Take

```java
fun(i + 1, sum + arr[i], ...)
```

The current element goes into the first subset.

### Skip

```java
fun(i + 1, sum, ...)
```

The current element does not go into the first subset.

Because every array element must belong to one of the two subsets, a skipped element automatically belongs to the second subset.

---

# Memoization State

```java
dp[i][sum]
```

means:

> Starting from index `i`, can we choose some elements so that the first subset eventually gets a valid sum?

There are two dimensions:

```text
i   = current index
sum = current sum of subset 1
```

If the same `(i, sum)` occurs again, we already know the answer and can reuse it.

---

# Complexity

If the total sum is `S`:

```text
States = O(N * S)

Time  = O(N * S)
Space = O(N * S)
```

The important improvement is that we stop immediately when:

```java
sum > K && sum < total - K
```

or:

```java
sum >= total - K
```

---

# Key Pattern to Remember

This problem is a variation of **Subset Sum / Take-or-Skip DP**.

Remember this transformation:

```text
Two subsets:

sum1 > K
sum2 > K

sum1 + sum2 = total

        ↓

K < sum1 < total - K
```

Then solve:

```text
Find a subset sum inside this range
```

using:

```text
dp[index][sum]
```

The most important condition is:

```java
if (sum > K && sum < total - K) {
    return true;
}
```
