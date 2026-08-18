# Q3. Longest Valid Subsequence with Increasing Sum

## Problem

You are given an array `nums` of positive integers and an integer `K`.

Find the maximum length subsequence satisfying all of these conditions:

1.  Elements must remain in their original order.
2.  Selected values must be strictly increasing.
3.  The sum of selected values must be at most `K`.

Return the maximum possible length.

## Example 1

``` text
nums = [3, 1, 2, 5, 4]
K = 8

Answer = 3
```

One valid subsequence is:

``` text
1 -> 2 -> 5
```

Its sum is:

``` text
1 + 2 + 5 = 8
```

and its length is `3`.

## Example 2

``` text
nums = [4, 3, 2, 1]
K = 10

Answer = 1
```

There is no increasing subsequence of length `2`.

## Memoization State

A state needs three pieces of information:

``` text
fun(i, prevIndex, sum)
```

where:

-   `i` = current index
-   `prevIndex` = index of the previously selected element
-   `sum` = current sum

Use `prevIndex = -1` when no element has been selected yet.

## Choices

At index `i`, there are two choices.

### 1. Skip

``` text
fun(i + 1, prevIndex, sum)
```

### 2. Take

We can take `nums[i]` if:

``` text
prevIndex == -1
```

or:

``` text
nums[i] > nums[prevIndex]
```

and:

``` text
sum + nums[i] <= K
```

Then:

``` text
1 + fun(i + 1, i, sum + nums[i])
```

The answer is:

``` text
max(skip, take)
```

## Java Memoization Solution

A full 3D array is too large for large `N` and `K`, so a `HashMap` is
used to store only states that are actually visited.

``` java
import java.util.*;

class Main {

    static Map<String, Integer> dp = new HashMap<>();

    public static void main(String[] args) {

        int[] nums = {3, 1, 2, 5, 4};
        int k = 8;
        int n = nums.length;

        int ans = fun(0, -1, 0, n, nums, k);

        System.out.println(ans);
    }

    public static int fun(int i, int prevIndex, int sum,
                          int n, int[] nums, int k) {

        if(i == n) {
            return 0;
        }

        String key = i + "," + prevIndex + "," + sum;

        if(dp.containsKey(key)) {
            return dp.get(key);
        }

        // Skip nums[i]
        int skip = fun(
            i + 1,
            prevIndex,
            sum,
            n,
            nums,
            k
        );

        // Take nums[i]
        int take = 0;

        if((prevIndex == -1 ||
            nums[i] > nums[prevIndex]) &&
            sum + nums[i] <= k) {

            take = 1 + fun(
                i + 1,
                i,
                sum + nums[i],
                n,
                nums,
                k
            );
        }

        int ans = Math.max(skip, take);

        dp.put(key, ans);

        return ans;
    }
}
```

## Why `prevIndex` instead of `prevValue`?

Do not use:

``` text
prev = previous value
```

as an array index.

Instead use:

``` text
prevIndex = index of previous selected element
```

Then compare:

``` java
nums[i] > nums[prevIndex]
```

The special value:

``` text
prevIndex = -1
```

means that no element has been selected yet.

## Why Sum Must Be Part of the State

These two states can have different answers:

``` text
fun(i, prevIndex, 3)
fun(i, prevIndex, 7)
```

when:

``` text
K = 8
```

The first state has more remaining capacity than the second.

Therefore, `sum` must be included in the memoization key.

## Complexity

The theoretical number of possible states is approximately:

``` text
O(N * N * K)
```

because the state contains:

``` text
i
prevIndex
sum
```

HashMap memoization stores only states that are actually reached.

The worst-case time and memory can still be large, so this solution is
mainly useful for understanding and implementing the **top-down
memoization state**.

## Key Pattern

This is a **3-parameter DP**:

``` text
fun(index, previous choice, current resource)
```

The recurrence is:

``` text
skip = fun(i + 1, prevIndex, sum)

take = 1 + fun(i + 1, i, sum + nums[i])

answer = max(skip, take)
```
