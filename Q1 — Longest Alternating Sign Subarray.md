# Q1 — Longest Alternating Sign Subarray

## Problem Description

You are given two integer arrays `A` and `B`, both of size `N`.

Construct a new array `C` such that for every index `i`, you must choose exactly one value:

```text
C[i] = A[i]
```

or

```text
C[i] = B[i]
```

Your task is to maximize the length of the **longest contiguous subarray** of `C` in which consecutive elements have alternating signs.

Valid patterns are:

```text
+ - + - + ...
```

or:

```text
- + - + - ...
```

It is guaranteed that no element in `A` or `B` is zero.

---

## Test Case 1

```text
Input:
A = [1, 2, -3, 4, -5]
B = [-1, -2, 3, -4, 5]

Output:
5
```

Choose:

```text
C = [1, -2, 3, -4, 5]
```

Signs:

```text
+ - + - +
```

The entire array is alternating.

---

## Test Case 2

```text
Input:
A = [1, 2, 3, 4]
B = [-1, -2, -3, -4]

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

Therefore:

```text
Answer = 4
```

---

## Test Case 3

```text
Input:
A = [1, 3, 5, -2]
B = [2, 4, -1, 6]

Output:
3
```

Choose:

```text
C = [1, 3, -1, 6]
```

The longest alternating part is:

```text
3, -1, 6
```

Signs:

```text
+ - +
```

Therefore:

```text
Answer = 3
```

---

## DP Idea

At every index we have two choices:

```text
dpA[i] = longest alternating subarray ending at i
         when we choose A[i]

dpB[i] = longest alternating subarray ending at i
         when we choose B[i]
```

Every single element is itself a valid alternating subarray of length `1`.

Therefore:

```java
Arrays.fill(dpA, 1);
Arrays.fill(dpB, 1);
```

To extend the previous alternating subarray, the current value and the previous selected value must have opposite signs.

For `A[i]`, check both:

```text
A[i - 1]
B[i - 1]
```

For `B[i]`, check both:

```text
A[i - 1]
B[i - 1]
```

---

## Java Tabulation Code

```java
import java.util.*;

class Solution {

    public int maxLength(int[] A, int[] B) {

        int n = A.length;

        int[] dpA = new int[n];
        int[] dpB = new int[n];

        // Every element alone is a valid subarray of length 1
        Arrays.fill(dpA, 1);
        Arrays.fill(dpB, 1);

        int ans = 1;

        for (int i = 1; i < n; i++) {

            // Choose A[i]

            if (opposite(A[i], A[i - 1])) {
                dpA[i] = Math.max(
                    dpA[i],
                    dpA[i - 1] + 1
                );
            }

            if (opposite(A[i], B[i - 1])) {
                dpA[i] = Math.max(
                    dpA[i],
                    dpB[i - 1] + 1
                );
            }

            // Choose B[i]

            if (opposite(B[i], A[i - 1])) {
                dpB[i] = Math.max(
                    dpB[i],
                    dpA[i - 1] + 1
                );
            }

            if (opposite(B[i], B[i - 1])) {
                dpB[i] = Math.max(
                    dpB[i],
                    dpB[i - 1] + 1
                );
            }

            ans = Math.max(
                ans,
                Math.max(dpA[i], dpB[i])
            );
        }

        return ans;
    }

    private boolean opposite(int a, int b) {

        return (a > 0 && b < 0) ||
               (a < 0 && b > 0);
    }
}
```

---

## Complexity

```text
Time  : O(N)
Space : O(N)
```

---

## Similar Practice Problems

### 1. LeetCode 978 — Longest Turbulent Subarray

https://leetcode.com/problems/longest-turbulent-subarray/

### 2. LeetCode 376 — Wiggle Subsequence

https://leetcode.com/problems/wiggle-subsequence/

### 3. GeeksforGeeks — Longest Alternating Subarray

https://www.geeksforgeeks.org/dsa/longest-alternating-subarray/
