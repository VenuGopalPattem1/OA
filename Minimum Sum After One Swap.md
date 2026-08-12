# Minimum Sum After One Swap

## Problem Description

You are given an integer array `A` of size `N` and two integers `K` and `D`.

You must select exactly `K` elements whose indices form an arithmetic progression with common difference `D`.

If the first selected index is `start`, the selected indices are:

```text
start, start + D, start + 2D, ..., start + (K - 1) * D
```

After selecting the elements, you may perform at most one swap between one selected element and one non-selected element.

Your goal is to minimize the sum of the selected elements after the swap.

---

## Greedy Idea

For every possible starting index:

1. Select the `K` elements using step `D`.
2. Calculate their sum.
3. Find the largest selected element.
4. Find the smallest non-selected element.
5. Try swapping them.
6. Keep the minimum answer.

The important formula is:

```text
newSum = currentSum - largestSelected + smallestNotSelected
```

We also consider the sum without performing a swap.

---

## Java Solution

```java
class Solution {

    public long minimumSum(int[] A, int K, int D) {

        int n = A.length;
        long ans = Long.MAX_VALUE;

        // Try every possible starting index
        for (int start = 0;
             start + (K - 1) * D < n;
             start++) {

            long sum = 0;
            int maxSelected = Integer.MIN_VALUE;

            boolean[] selected = new boolean[n];

            // Select K elements
            for (int j = 0; j < K; j++) {

                int index = start + j * D;

                selected[index] = true;

                sum += A[index];

                maxSelected = Math.max(
                    maxSelected,
                    A[index]
                );
            }

            // Find smallest non-selected element
            int minNonSelected = Integer.MAX_VALUE;

            for (int i = 0; i < n; i++) {

                if (!selected[i]) {
                    minNonSelected =
                        Math.min(minNonSelected, A[i]);
                }
            }

            // Don't perform a swap
            ans = Math.min(ans, sum);

            // Perform one swap
            if (minNonSelected != Integer.MAX_VALUE) {

                long newSum =
                    sum - maxSelected + minNonSelected;

                ans = Math.min(ans, newSum);
            }
        }

        return ans;
    }
}
```

---

## Dry Run

### Input

```text
A = [5, 100, 4, 3, 2]
K = 2
D = 2
```

### Start = 0

Selected indices:

```text
0, 2
```

Selected values:

```text
5, 4
```

Current sum:

```text
9
```

Largest selected:

```text
5
```

Smallest non-selected:

```text
2
```

After swap:

```text
newSum = 9 - 5 + 2
       = 6
```

### Start = 1

Selected indices:

```text
1, 3
```

Selected values:

```text
100, 3
```

Current sum:

```text
103
```

Largest selected:

```text
100
```

Smallest non-selected:

```text
2
```

After swap:

```text
newSum = 103 - 100 + 2
       = 5
```

Therefore:

```text
Answer = 5
```

---

## Test Cases

### Test Case 1

```text
A = [10, 2, 8, 1, 5, 3]
K = 3
D = 2

Output = 6
```

### Test Case 2

```text
A = [5, 100, 4, 3, 2]
K = 2
D = 2

Output = 5
```

### Test Case 3

```text
A = [1, 2, 3, 4, 5]
K = 3
D = 1

Output = 6
```

---

## Complexity

```text
Time Complexity: O(N
