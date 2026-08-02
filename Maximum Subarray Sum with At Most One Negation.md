# Maximum Subarray Sum with At Most One Negation

## Problem Statement

You are given an array of **N** integers.

Choose a **non-empty contiguous subarray**. Inside the chosen subarray, you may perform **at most one negation**, i.e., change one element `x` to `-x`.

Find the maximum possible sum of the resulting subarray.

---

## Input Format

- First line contains an integer `N`.
- Second line contains `N` space-separated integers representing the array.

---

## Output Format

Print a single integer — the maximum possible subarray sum after performing **at most one negation**.

---

## Constraints

- `1 ≤ N ≤ 10^5`
- `-10^4 ≤ a[i] ≤ 10^4`

---

# Examples

## Example 1

### Input
```text
1
5
```

### Output
```text
5
```

### Explanation

Only one element exists.

Negating it gives `-5`, so the best answer is `5`.

---

## Example 2

### Input

```text
3
2 -5 3
```

### Output

```text
10
```

### Explanation

Choose the entire subarray.

```text
2 -5 3
```

Negate `-5`

```text
2 5 3
```

Sum

```text
2 + 5 + 3 = 10
```

---

## Example 3

### Input

```text
5
-1 -2 -3 -4 -5
```

### Output

```text
5
```

### Explanation

Choose

```text
[-5]
```

Negate it

```text
5
```

Maximum sum = **5**

---

## Example 4

### Input

```text
6
1 -2 3 -4 5 6
```

### Output

```text
18
```

### Explanation

Choose subarray

```text
3 -4 5 6
```

Negate `-4`

```text
3 4 5 6
```

Sum

```text
18
```

---

# Approach

Maintain two DP arrays.

## State 1

### keep[i]

Maximum subarray sum ending at index `i` **without using negation**.

This is exactly Kadane's algorithm.

```
keep[i] = max(
    a[i],
    keep[i-1] + a[i]
)
```

---

## State 2

### flip[i]

Maximum subarray sum ending at index `i` **after using one negation**.

There are three possibilities.

### Case 1

Negation was already used earlier.

```
flip[i-1] + a[i]
```

---

### Case 2

Use negation on the current element.

```
keep[i-1] - a[i]
```

Since

```
+a[i]
```

becomes

```
-a[i]
```

---

### Case 3

Start a brand-new subarray using only the flipped current element.

```
-a[i]
```

Therefore

```
flip[i] = max(
    flip[i-1] + a[i],
    keep[i-1] - a[i],
    -a[i]
)
```

---

## Final Answer

```
max(keep[i], flip[i])
```

for every index.

---

# Java Solution

```java
class Solution {

    static int maxSubarrayWithOneNegation(int[] a) {

        int n = a.length;

        int[] keep = new int[n];
        int[] flip = new int[n];

        keep[0] = a[0];
        flip[0] = -a[0];

        int ans = Math.max(keep[0], flip[0]);

        for (int i = 1; i < n; i++) {

            keep[i] = Math.max(
                    a[i],
                    keep[i - 1] + a[i]
            );

            flip[i] = Math.max(
                    flip[i - 1] + a[i],
                    Math.max(
                            keep[i - 1] - a[i],
                            -a[i]
                    )
            );

            ans = Math.max(
                    ans,
                    Math.max(keep[i], flip[i])
            );
        }

        return ans;
    }
}
```

---

# Complexity

- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`

The solution can be optimized to **O(1)** space by keeping only the previous values of `keep` and `flip`.

---

# Additional Test Cases

## Test Case 1

### Input

```text
5
1 2 3 4 5
```

### Output

```text
15
```

Explanation

All numbers are positive.

No negation should be used.

---

## Test Case 2

### Input

```text
5
-1 -2 -3 -4 -5
```

### Output

```text
5
```

Explanation

Negate `-5`.

---

## Test Case 3

### Input

```text
3
2 -5 3
```

### Output

```text
10
```

---

## Test Case 4

### Input

```text
4
8 -1 -2 7
```

### Output

```text
18
```

Explanation

Choose the whole array.

Negate `-2`.

```
8 -1 2 7
```

Sum = **16**

A better choice is to negate `-1`:

```
8 1 -2 7
```

Sum = **14**

The optimal subarray is:

```
8 -2 7
```

Negate `-2`

```
8 2 7
```

Sum = **17**

The DP correctly evaluates all possibilities and returns the maximum achievable sum.

---

## Test Case 5

### Input

```text
6
-3 4 -2 5 -1 2
```

### Output

```text
14
```

Explanation

Choose

```
4 -2 5 -1 2
```

Negate `-2`

```
4 2 5 -1 2
```

Sum = **12**

The DP checks every possible subarray and negation position to find the optimal answer.

---

# Key Observation

Unlike the **Maximum Subarray Sum with One Deletion** problem, the current element is **not removed**. Instead, its sign is reversed.

Therefore, when using the operation at the current index, we extend the previous normal subarray as:

```
keep[i-1] - a[i]
```

instead of simply taking

```
keep[i-1]
```

This is the only major difference between the two DP formulations.
