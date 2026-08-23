# Tug of War Captain

## Problem

There are `N` kids with strengths `s[i]`.

The **first kid is always the captain**.

The captain's strength becomes double:

```text
captain strength = 2 * s[0]
```

The remaining kids may join in any order.

When a kid joins:

- If one side has smaller total strength, the kid MUST join that side.
- If both sides have equal strength, the kid may choose either side.

Find the minimum possible final absolute difference between the two sides.

## Example

```text
Input:
N = 3
s = [1, 2, 3]

First kid is captain.

Strengths become:
[2, 2, 3]

Choose order:
2 -> Left
3 -> Right
2 -> Left

Left  = 4
Right = 3

Answer = 1
```

## Test Case 1

```text
Input:
4
4 2 1 4

Output:
1
```

One valid order:

```text
Captain = 4 -> strength 8

8 -> Left
4 -> Right
2 -> Right
1 -> Right

Left  = 8
Right = 7

Answer = 1
```

## Test Case 2

```text
Input:
3
1 2 3

Output:
1
```

Captain:

```text
1 -> 2
```

Order:

```text
2 -> Left
3 -> Right
2 -> Left
```

Final:

```text
Left  = 4
Right = 3

Answer = 1
```

## Test Case 3

```text
Input:
3
3 3 3

Output:
0
```

Captain:

```text
3 -> 6
```

Order:

```text
6 -> Left
3 -> Right
3 -> Right
```

Final:

```text
Left  = 6
Right = 6

Answer = 0
```

# Java Code

```java
import java.util.*;

class Solution {

    public long minDifference(int[] s) {

        int n = s.length;

        // First kid is always the captain
        long left = 2L * s[0];
        long right = 0;

        // Remaining kids
        Integer[] a = new Integer[n - 1];

        for (int i = 1; i < n; i++) {
            a[i - 1] = s[i];
        }

        /*
         * Process larger strengths first.
         * This is the standard greedy load-balancing order.
         */
        Arrays.sort(a, Collections.reverseOrder());

        for (int x : a) {

            // Kid must join the smaller side
            if (left <= right) {
                left += x;
            } else {
                right += x;
            }
        }

        return Math.abs(left - right);
    }
}
```

# Important Note

The code above follows the intended interpretation where:

```text
1. First kid is fixed as captain.
2. Captain is doubled.
3. Remaining kids can be ordered.
4. Each kid joins the currently smaller side.
```

The descending-order greedy is the simple approach to try for this formulation.

## Related Practice

### LeetCode 1049 — Last Stone Weight II

Useful for learning the two-group balancing idea.

https://leetcode.com/problems/last-stone-weight-ii/

### LeetCode 416 — Partition Equal Subset Sum

Classic two-subset balancing problem.

https://leetcode.com/problems/partition-equal-subset-sum/

### LeetCode 2035 — Partition Array Into Two Arrays to Minimize Sum Difference

Harder partition/balancing problem.

https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/

### LeetCode 1723 — Find Minimum Time to Finish All Jobs

Useful for load-balancing ideas.

https://leetcode.com/problems/find-minimum-time-to-finish-all-jobs/

## Key Pattern

Think:

```text
First kid
   |
   v
Double strength
   |
   v
Sort remaining strengths descending
   |
   v
Take each kid
   |
   v
Put into currently smaller side
   |
   v
Minimum final difference
```
