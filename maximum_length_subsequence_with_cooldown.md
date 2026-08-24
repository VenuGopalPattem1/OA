# Maximum Length Subsequence with Cooldown

## Problem

Given an array `nums[]` and an integer `target`, choose a subsequence
whose sum is exactly equal to `target`.

There is a cooldown rule:

If `nums[i]` is selected, the next selectable index becomes:

``` text
nextIndex = i + (nums[i] % 4) + 1
```

Therefore, all indices between `i` and `nextIndex - 1` are skipped.

Find the **maximum possible length** of a valid subsequence.

Return `-1` if no valid subsequence exists.

------------------------------------------------------------------------

## Example

``` text
nums = [2, 3, 4, 5]
target = 7
```

Choose:

``` text
2 + 5 = 7
```

After selecting `2` at index `0`:

``` text
nextIndex = 0 + (2 % 4) + 1
          = 3
```

So index `3` can be selected.

The selected elements are:

``` text
2, 5
```

Therefore:

``` text
Answer = 2
```

------------------------------------------------------------------------

## Approach: Memoization DP

We use:

``` text
DP(index, sum)
```

where:

-   `index` = current index we are considering
-   `sum` = sum collected so far
-   return value = maximum number of elements that can still be selected
    to reach `target`

At every index, we have two choices.

### Choice 1: Skip

Do not select `nums[index]`.

Move to the next index:

``` text
fun(index + 1, sum)
```

### Choice 2: Take

Select `nums[index]` if it does not make the sum greater than `target`.

``` text
nextIndex = index + (nums[index] % 4) + 1
```

Then:

``` text
1 + fun(nextIndex, sum + nums[index])
```

Take the maximum of the two choices.

------------------------------------------------------------------------

## Base Cases

### Target reached

If:

``` text
sum == target
```

we have successfully formed the target.

Return:

``` text
0
```

because no more elements are needed.

### Array exhausted

If:

``` text
index >= nums.length
```

and the target has not been reached, this path is invalid.

Return a very negative value such as:

``` text
-1000000
```

This makes it easy to ignore impossible paths when taking the maximum.

------------------------------------------------------------------------

## Java Memoization Code

``` java
import java.util.*;

class Solution {

    Integer[][] dp;

    public int maximumLengthSubsequence(int[] nums, int target) {

        int n = nums.length;

        dp = new Integer[n + 1][target + 1];

        int ans = fun(0, 0, nums, target);

        return ans < 0 ? -1 : ans;
    }

    public int fun(int i, int sum, int[] nums, int target) {

        // Target achieved
        if (sum == target) {
            return 0;
        }

        // No elements left
        if (i >= nums.length) {
            return -1000000;
        }

        // Already calculated
        if (dp[i][sum] != null) {
            return dp[i][sum];
        }

        // Option 1: Skip current element
        int skip = fun(i + 1, sum, nums, target);

        // Option 2: Take current element
        int take = -1000000;

        if (sum + nums[i] <= target) {

            int next = i + (nums[i] % 4) + 1;

            int result = fun(
                next,
                sum + nums[i],
                nums,
                target
            );

            if (result >= 0) {
                take = 1 + result;
            }
        }

        return dp[i][sum] = Math.max(skip, take);
    }
}
```

------------------------------------------------------------------------

## Why `dp[index][sum]` is Enough

The important information about our current situation is:

``` text
index
sum
```

We do not need to remember the complete subsequence.

Once we know the current index and current sum, the future choices are
completely determined by these two values.

Therefore:

``` text
dp[index][sum]
```

is sufficient.

------------------------------------------------------------------------

## Dry Run

Consider:

``` text
nums = [2, 3, 4, 5]
target = 7
```

Start:

``` text
fun(0, 0)
```

At index `0`, `nums[0] = 2`.

### Skip 2

``` text
fun(1, 0)
```

### Take 2

New sum:

``` text
0 + 2 = 2
```

Cooldown:

``` text
next = 0 + (2 % 4) + 1
     = 3
```

So:

``` text
fun(3, 2)
```

At index `3`:

``` text
nums[3] = 5
```

Take it:

``` text
sum = 2 + 5
    = 7
```

Target reached.

Return:

``` text
0
```

The previous `take` adds `1`:

``` text
1 + 0 = 1
```

And the `take 2` from the first state adds another `1`:

``` text
1 + 1 = 2
```

Therefore:

``` text
Answer = 2
```

------------------------------------------------------------------------

## Test Cases

### Test Case 1

``` text
nums = [2, 3, 4, 5]
target = 7
```

Valid subsequence:

``` text
2 + 5 = 7
```

Answer:

``` text
2
```

------------------------------------------------------------------------

### Test Case 2

``` text
nums = [1, 2, 3, 4, 5]
target = 6
```

Choose:

``` text
1 + 5 = 6
```

For `1` at index `0`:

``` text
next = 0 + (1 % 4) + 1
     = 2
```

Index `4` can be selected.

Answer:

``` text
2
```

------------------------------------------------------------------------

### Test Case 3

``` text
nums = [5, 5, 5]
target = 6
```

There is no subsequence whose sum is `6`.

Answer:

``` text
-1
```

------------------------------------------------------------------------

## Complexity

There are at most:

``` text
N * target
```

different states.

Each state performs constant work.

Therefore:

``` text
Time Complexity  = O(N * target)
Space Complexity = O(N * target)
```

For:

``` text
N <= 1000
target <= 1000
```

this approach is efficient.

------------------------------------------------------------------------

## Important Pattern

This problem is a combination of:

``` text
1. Subsequence DP
2. Exact target sum DP
3. Maximum length DP
4. Variable index jump / cooldown
```

The general pattern is:

``` text
fun(i, sum)

    skip:
        fun(i + 1, sum)

    take:
        next = i + cooldown(nums[i])
        1 + fun(next, sum + nums[i])
```

This pattern is useful for many custom OA/assessment DP questions.

------------------------------------------------------------------------

## Related Problems

### 1. LeetCode 2915

**Length of the Longest Subsequence That Sums to Target**

Closest to this problem because it uses:

``` text
index + sum
```

and asks for the maximum subsequence length.

https://leetcode.com/problems/length-of-the-longest-subsequence-that-sums-to-target/

### 2. LeetCode 416

**Partition Equal Subset Sum**

Useful for learning:

``` text
index + sum
```

subset-sum DP.

https://leetcode.com/problems/partition-equal-subset-sum/

### 3. LeetCode 198

**House Robber**

Useful for understanding:

``` text
take / skip
```

DP with restricted next indices.

https://leetcode.com/problems/house-robber/

### 4. LeetCode 740

**Delete and Earn**

Another useful take/skip DP problem.

https://leetcode.com/problems/delete-and-earn/

------------------------------------------------------------------------

## Recommended Practice Order

For this exact pattern, practice:

``` text
2915 → 416 → 198 → 740
```

Then return to this problem and implement:

``` text
DP(index, sum)
```

with the additional cooldown jump:

``` text
index + (nums[index] % 4) + 1
```
