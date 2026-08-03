# Maximum Position-Weighted Sum of K Items

## Problem Description

You are given a **1-indexed** array `a` of `N` integers, where the indices range from **1** to **N**.

The **weight** of the element at index `i` is defined as:

```text
weight(i) = a[i] × i
```

Your task is to select **exactly `K` elements** from the array such that:

* No two selected elements have the same remainder when their **1-based indices** are divided by `K`.

That is, if two selected indices are `i` and `j`, then:

```text
i % K ≠ j % K
```

Since there are exactly **K possible remainders** (`0, 1, 2, ..., K-1`) and you must choose exactly **K elements**, you must select **one element from each remainder group**.

Return the **maximum possible sum of weights** modulo **10⁹ + 7**.

---

# Rules

* Compute the weight of every element as:

```text
weight = value × index
```

* Select exactly `K` elements.
* No two selected indices may have the same remainder when divided by `K`.
* Maximize the total weight.

---

# Input Format

* The first line contains two integers:

```text
N K
```

* The second line contains `N` integers representing the array.

---

# Output Format

Print the maximum position-weighted sum modulo **10⁹ + 7**.

---

# Constraints

```text
1 ≤ K ≤ N ≤ 2 × 10^5
1 ≤ a[i] ≤ 10^9
```

---

# Test Case 1

### Input

```text
6 3

2 5 1 4 3 6
```

### Output

```text
67
```

### Explanation

Compute the weight of every element.

| Index | Value | Weight | Index % 3 |
| ----: | ----: | -----: | --------: |
|     1 |     2 |      2 |         1 |
|     2 |     5 |     10 |         2 |
|     3 |     1 |      3 |         0 |
|     4 |     4 |     16 |         1 |
|     5 |     3 |     15 |         2 |
|     6 |     6 |     36 |         0 |

Choose the maximum weight from each remainder group.

* Remainder **0** → 36
* Remainder **1** → 16
* Remainder **2** → 15

Maximum sum:

```text
36 + 16 + 15 = 67
```

---

# Test Case 2

### Input

```text
8 4

3 7 2 5 8 1 6 4
```

### Output

```text
128
```

### Explanation

Weights:

| Index | Value | Weight | Index % 4 |
| ----: | ----: | -----: | --------: |
|     1 |     3 |      3 |         1 |
|     2 |     7 |     14 |         2 |
|     3 |     2 |      6 |         3 |
|     4 |     5 |     20 |         0 |
|     5 |     8 |     40 |         1 |
|     6 |     1 |      6 |         2 |
|     7 |     6 |     42 |         3 |
|     8 |     4 |     32 |         0 |

Choose the maximum weight from every remainder group.

* Remainder **0** → 32
* Remainder **1** → 40
* Remainder **2** → 14
* Remainder **3** → 42

Maximum sum:

```text
32 + 40 + 14 + 42 = 128
```

---

# Approach

This problem can be solved greedily.

### Observation

The only restriction is:

* Two selected elements cannot belong to the same remainder group.

Since exactly `K` elements must be selected and there are exactly `K` remainder groups, we **must choose exactly one element from each group**.

Therefore:

1. Compute the weight of every element.
2. Group the weights according to:

```text
index % K
```

3. Select the maximum weight from each group.
4. Add all selected weights.

---

# Algorithm

```text
Create K remainder groups

For every index

    Compute weight = value × index

    Store the maximum weight for that remainder

Finally,

Answer = Sum of all maximum weights
```

---

# Java Solution

```java
class Solution {

    static final long MOD = 1000000007L;

    public long maximumWeightedSum(int[] a, int K) {

        int n = a.length;

        long[] best = new long[K];

        for (int i = 0; i < K; i++)
            best[i] = Long.MIN_VALUE;

        for (int i = 1; i <= n; i++) {

            long weight = 1L * a[i - 1] * i;

            int rem = i % K;

            best[rem] = Math.max(best[rem], weight);
        }

        long ans = 0;

        for (int i = 0; i < K; i++) {
            ans = (ans + best[i]) % MOD;
        }

        return ans;
    }
}
```

---

# Dry Example

Suppose

```text
Array = [2,5,1,4,3,6]

K = 3
```

Weights:

```text
2
10
3
16
15
36
```

Groups:

```text
Remainder 0 → 3, 36

Remainder 1 → 2, 16

Remainder 2 → 10, 15
```

Choose the maximum from each group:

```text
36

16

15
```

Answer:

```text
67
```

---

# Complexity Analysis

### Time Complexity

Each element is processed exactly once.

```text
O(N)
```

### Space Complexity

We store one maximum value for each remainder group.

```text
O(K)
```

---

# Why does this Greedy approach work?

Each remainder group is **independent**.

Choosing an element from one group does not affect the choices in any other group.

Since we must pick exactly one element from every group, the optimal decision is simply to choose the **maximum weight** available in each group.

---

# Related Problems

* LeetCode 347 — Top K Frequent Elements
* LeetCode 215 — Kth Largest Element in an Array
* LeetCode 769 — Max Chunks To Make Sorted
* GeeksforGeeks — Maximum Sum Subarray
* GeeksforGeeks — Group Array Elements Based on Modulo
* GeeksforGeeks — Maximum Sum of Distinct Groups

---

# Concepts Used

* Greedy Algorithm
* Modulo Arithmetic
* Array Traversal
* Grouping by Remainder
* Maximum Element in Each Group
* Time Optimization
