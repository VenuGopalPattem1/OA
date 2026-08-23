# Beauty of Range — Strictly Increasing Beauty Partitions

## Problem Description

Given an array of integers `arr`, the beauty of a continuous range `[l, r]` is the length of the Longest Increasing Subsequence (LIS) inside that range.

Partition the complete array into continuous, non-empty subarrays such that the beauties of the subarrays are strictly increasing.

Return the number of valid partitions.

## Test Case 1

```text
Input:
arr = [1,2,3]

Output:
2
```

Valid partitions:

```text
[1,2,3]
[1] [2,3]
```

Their beauties are:

```text
3
1 < 2
```

## Test Case 2

```text
Input:
arr = [3,1,2]

Output:
2
```

Valid partitions:

```text
[3,1,2]       -> beauty 2
[3] [1,2]     -> 1 < 2
```

## Test Case 3

```text
Input:
arr = [3,2,1]

Output:
1
```

Every non-empty range has LIS length `1`, so only the whole array as one partition is valid.

# Simple Memoization Approach

Define:

```text
fun(i, prevBeauty)
```

where:

- `i` = starting index of the next partition
- `prevBeauty` = beauty of the previous partition

For every possible ending index `j`, consider:

```text
arr[i...j]
```

Find its LIS length.

If:

```text
currentBeauty > prevBeauty
```

then choose this partition and continue with:

```text
fun(j + 1, currentBeauty)
```

# Java Code

```java
import java.util.*;

class Solution {

    int n;
    Long[][] dp;

    public long countPartitions(int[] arr) {

        n = arr.length;

        // dp[i][prevBeauty]
        dp = new Long[n + 1][n + 1];

        return fun(0, 0, arr);
    }

    // i = starting index of current partition
    // prevBeauty = beauty of previous partition
    public long fun(int i, int prevBeauty, int[] arr) {

        if (i == n) {
            return 1;
        }

        if (dp[i][prevBeauty] != null) {
            return dp[i][prevBeauty];
        }

        long ans = 0;

        for (int j = i; j < n; j++) {

            int currentBeauty = getLIS(arr, i, j);

            if (currentBeauty > prevBeauty) {
                ans += fun(j + 1, currentBeauty, arr);
            }
        }

        return dp[i][prevBeauty] = ans;
    }

    // LIS length of arr[l...r]
    public int getLIS(int[] arr, int l, int r) {

        ArrayList<Integer> tails = new ArrayList<>();

        for (int i = l; i <= r; i++) {

            int x = arr[i];

            // Lower bound:
            // first index where tails[index] >= x

            int left = 0;
            int right = tails.size() - 1;

            int pos = tails.size();

            while (left <= right) {

                int mid = left + (right - left) / 2;

                if (tails.get(mid) >= x) {
                    pos = mid;
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }

            if (pos == tails.size()) {
                tails.add(x);
            } else {
                tails.set(pos, x);
            }
        }

        return tails.size();
    }
}
```

# Lower Bound

The binary search finds the first index where:

```text
tails[index] >= x
```

using:

```java
int pos = tails.size();

while (left <= right) {

    int mid = left + (right - left) / 2;

    if (tails.get(mid) >= x) {
        pos = mid;
        right = mid - 1;
    } else {
        left = mid + 1;
    }
}
```

At the end:

```text
pos = first index where tails[pos] >= x
```

If no such position exists, `pos == tails.size()`, so we add `x`.

Otherwise:

```java
tails.set(pos, x);
```

# How `getLIS()` Works

For:

```text
arr = [3,1,2,5]
```

the LIS is:

```text
1,2,5
```

so:

```text
getLIS(arr, 0, 3) = 3
```

The `tails` array is used to maintain the smallest possible ending value for increasing subsequences of different lengths.

Example:

```text
3 -> [3]
1 -> [1]
2 -> [1,2]
5 -> [1,2,5]
```

Therefore:

```text
tails.size() = 3
```

# DP Meaning

```text
dp[i][prevBeauty]
```

means:

```text
number of valid ways to partition arr[i...n-1]
when the previous partition has beauty prevBeauty
```

The transition is:

```text
choose arr[i...j]
        |
        v
find LIS / beauty
        |
        v
beauty > prevBeauty ?
        |
       YES
        |
        v
fun(j + 1, beauty)
```

# Complexity

This is the simple solution for understanding the DP:

```text
Time:  approximately O(n^3 log n)
Space: O(n^2)
```

It is not the optimized solution for large constraints of the 1800-rated version.

# Key Idea

Remember the problem as:

```text
Partition array
     ↓
Find LIS of each part
     ↓
beauty must strictly increase
     ↓
DP state = (current index, previous beauty)
```
