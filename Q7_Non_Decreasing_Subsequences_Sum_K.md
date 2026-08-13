# Q7. Count Non-Decreasing Subsequences With Sum <= K

## Problem Description

Given an array `arr` and an integer `K`, count the total number of **non-decreasing subsequences** whose sum is `<= K`.

A subsequence keeps the original order, but elements do not need to be contiguous.

A subsequence is non-decreasing if:

`a1 <= a2 <= a3 <= ...`

The empty subsequence is not counted.

## Test Case 1

```text
Input:
arr = [1, 2, 3]
K = 4

Output:
5
```

Valid subsequences:

```text
[1]      -> 1
[2]      -> 2
[3]      -> 3
[1,2]    -> 3
[1,3]    -> 4
```

Invalid:

```text
[2,3]    -> 5
[1,2,3]  -> 6
```

## Test Case 2

```text
Input:
arr = [2, 1, 2, 3]
K = 5

Output:
9
```

The repeated values are at different indices, so they represent different subsequences.

## Approach - Memoization

At every index we have two choices:

1. Skip the current element.
2. Take the current element if:
   - it keeps the subsequence non-decreasing
   - the new sum is `<= K`

Define:

`fun(i, prev, sum)`

where:

- `i` = current index.
- `prev` = index of the previously selected element.
- `sum` = current subsequence sum.
- `prev = n` means no element has been selected yet.

We can take `arr[i]` when:

```text
prev == n || arr[i] >= arr[prev]
```

and:

```text
sum + arr[i] <= K
```

When we take an element, the current selection itself forms one valid subsequence, so we add `1`, then recursively try to extend it.

## Memoization Code

```java
class Solution {
    Integer[][][] dp;
    int[] arr;
    int n;
    int k;

    public int countSubsequences(int[] arr, int k) {
        this.arr = arr;
        this.n = arr.length;
        this.k = k;

        dp = new Integer[n][n + 1][k + 1];

        // n means no previous element is selected
        return fun(0, n, 0);
    }

    public int fun(int i, int prev, int sum) {
        if (i == n) {
            return 0;
        }

        if (dp[i][prev][sum] != null) {
            return dp[i][prev][sum];
        }

        // Skip current element
        int ans = fun(i + 1, prev, sum);

        // Take current element
        if ((prev == n || arr[i] >= arr[prev])
                && sum + arr[i] <= k) {

            // Current selection itself is a valid subsequence
            ans += 1;

            // Try extending the subsequence
            ans += fun(i + 1, i, sum + arr[i]);
        }

        return dp[i][prev][sum] = ans;
    }
}
```

## How the DP Works

For:

```text
arr = [1, 2, 3]
K = 4
```

Start:

```text
fun(0, n, 0)
```

At `1`, we can skip it or take it.

If we take `1`:

```text
fun(1, 0, 1)
```

Now `2` can be taken because:

```text
2 >= 1
1 + 2 <= 4
```

So `[1,2]` is counted.

`3` cannot be added after `[1,2]` because:

```text
1 + 2 + 3 = 6 > 4
```

The recursion similarly counts every other valid subsequence.

## Important Points

The problem has three conditions:

1. Elements must maintain their original order.
2. Selected values must be non-decreasing.
3. The total sum must be `<= K`.

The DP therefore needs `i`, `prev`, and `sum`.

The empty subsequence is not counted.

## Complexity

There are:

```text
N × (N + 1) × (K + 1)
```

possible states.

Each state does `O(1)` work.

```text
Time Complexity: O(N² × K)
Space Complexity: O(N² × K)
```

## Similar Practice Problems

1. LeetCode 491 - Non-decreasing Subsequences  
https://leetcode.com/problems/non-decreasing-subsequences/

2. LeetCode 300 - Longest Increasing Subsequence  
https://leetcode.com/problems/longest-increasing-subsequence/

3. GeeksforGeeks - Count Increasing Subsequences  
https://www.geeksforgeeks.org/dsa/count-number-increasing-subsequences-size-k/

4. Similar discussion containing this exact Infosys-style question  
https://www.reddit.com/r/codeforces/comments/1uzm8jg/i_was_asked_this_problem_for_infosys_and_i_know/

## Key Pattern

```text
fun(i, prev, sum)

        /          \
     skip          take
      |              |
   i + 1       if valid
                     |
                  count 1
                     |
                  continue
```

The main idea is:

**Count every subsequence that is non-decreasing and has sum <= K.**
