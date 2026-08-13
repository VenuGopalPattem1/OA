# Q2. Minimize the Maximum Score of K Subarrays

## Problem Description

You are given a string `s` and an integer `K`.

The score of a subarray is the number of unique/distinct characters
present in that subarray.

You have to divide the string into exactly `K` contiguous, non-empty
subarrays.

Your goal is to minimize the maximum score among all `K` subarrays.

In simple words:

-   Divide the string into `K` parts.
-   Find the number of unique characters in each part.
-   Take the maximum of those scores.
-   Minimize that maximum value.

This is a Binary Search on Answer problem.

## Test Case 1

Input: `s = "abcabc"` `K = 3`

Partition: `"ab" | "ca" | "bc"`

Scores: `"ab" = 2` `"ca" = 2` `"bc" = 2`

Maximum score = `2`

Output: `2`

## Test Case 2

Input: `s = "aabcb"` `K = 2`

Partition: `"aa" | "bcb"`

Scores: `"aa" = 1` `"bcb" = 2`

Maximum score = `2`

Output: `2`

## Test Case 3

Input: `s = "abcdef"` `K = 3`

Partition: `"ab" | "cd" | "ef"`

Scores: `"ab" = 2` `"cd" = 2` `"ef" = 2`

Maximum score = `2`

Output: `2`

## Approach

We need to find the minimum possible maximum score.

Suppose `mid` is the maximum number of unique characters allowed in one
partition.

We check whether we can divide the string into at most `K` partitions
such that every partition has at most `mid` unique characters.

For the check, greedily build the current partition. Keep track of its
unique characters. If adding a character makes the unique count greater
than `mid`, start a new partition.

If the required number of partitions becomes greater than `K`, `mid` is
not possible.

If the required number of partitions is less than or equal to `K`, `mid`
is possible.

Since a smaller maximum score is better, binary search works as follows:

-   If `mid` is possible, store it as the answer and search left.
-   If `mid` is not possible, search right.

Therefore, we are finding the minimum possible value of the maximum
partition score.

## Final Solution

``` java
class Solution {
    public int minMaxScore(String s, int k) {
        int max = 26;

        int l = 1, h = max, ans = 0;

        while (l <= h) {
            int mid = (l + h) / 2;

            if (fun(s, mid, k)) {
                ans = mid;
                h = mid - 1;
            } else {
                l = mid + 1;
            }
        }

        return ans;
    }

    public boolean fun(String s, int mid, int k) {
        int parts = 1;
        boolean[] seen = new boolean[26];
        int unique = 0;

        for (char c : s.toCharArray()) {
            int x = c - 'a';

            if (!seen[x]) {
                seen[x] = true;
                unique++;
            }

            if (unique > mid) {
                parts++;

                if (parts > k) {
                    return false;
                }

                seen = new boolean[26];
                seen[x] = true;
                unique = 1;
            }
        }

        return parts <= k;
    }
}
```

## Complexity

Let `N` be the length of the string.

Each feasibility check takes `O(N)`.

For lowercase English characters, binary search has at most `26`
possible answers, so:

Time Complexity: `O(N log 26)`, which is effectively `O(N)`.

Space Complexity: `O(26)`, which is `O(1)`.

For a general character set with `D` possible distinct characters:

Time Complexity: `O(N log D)`

Space Complexity: `O(D)`

## Key Idea

The important thing to remember is:

We are NOT simply finding the maximum score.

We are finding the MINIMUM possible MAXIMUM score.

`mid` = maximum unique characters allowed in one partition.

`fun()` = checks whether the string can be divided into `K` partitions
under that limit.

If `fun()` returns true: - `mid` works - try a smaller value

If `fun()` returns false: - `mid` does not work - try a larger value

## Similar Problems

1.  LeetCode 410 - Split Array Largest Sum
    https://leetcode.com/problems/split-array-largest-sum/

2.  LeetCode 1011 - Capacity To Ship Packages Within D Days
    https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/

3.  LeetCode 1231 - Divide Chocolate
    https://leetcode.com/problems/divide-chocolate/

4.  LeetCode 774 - Minimize Max Distance to Gas Station
    https://leetcode.com/problems/minimize-max-distance-to-gas-station/
