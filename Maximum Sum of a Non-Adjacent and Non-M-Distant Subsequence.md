# Maximum Sum of a Non-Adjacent and Non-M-Distant Subsequence

## Problem Description

You are given an array of **N positive integers** and an integer **M**.

Your task is to find the **maximum possible sum of a subsequence** such that the following conditions are satisfied:

1. **No two selected elements are adjacent.**

   * If you select the element at index `i`, you cannot select the elements at indices `i-1` or `i+1`.

2. **No two selected elements are exactly `M` indices apart.**

   * If you select the element at index `i`, you cannot select the elements at indices `i-M` or `i+M`.

The selected elements **do not need to be contiguous**. Your objective is to maximize the sum of the selected elements while satisfying both constraints.

---

# Input Format

* The first line contains an integer `N`, representing the size of the array.
* The second line contains `N` space-separated integers representing the array elements.
* The third line contains an integer `M`.

---

# Output Format

Print a single integer representing the maximum possible sum.

---

# Constraints

```text
1 ≤ N ≤ 10^5
1 ≤ M ≤ N
1 ≤ A[i] ≤ 10^9
```

---

# Example 1

### Input

```text
N = 6

Array = [5, 1, 8, 2, 10, 6]

M = 2
```

### Output

```text
15
```

### Explanation

Possible valid selections:

* `{5, 10}` → Sum = **15** ✅
* `{8, 6}` → Sum = **14** ✅
* `{5, 8}` ❌ (distance = 2)
* `{8, 10}` ❌ (adjacent)

Hence, the maximum possible sum is **15**.

---

# Example 2

### Input

```text
N = 8

Array = [5, 1, 9, 2, 8, 3, 7, 4]

M = 3
```

### Output

```text
16
```

### Explanation

Choose the elements:

```text
9 + 7 = 16
```

Selected indices (1-based):

```text
3 and 7
```

Difference:

```text
7 − 3 = 4
```

* Not adjacent ✔
* Not exactly `M (=3)` apart ✔

Hence, the maximum sum is **16**.

---

# Observations

This problem is similar to the **House Robber** problem, but with an additional restriction.

Unlike House Robber, selecting an element does **not** determine a fixed next index because:

* Adjacent indices are forbidden.
* Indices exactly `M` positions away are also forbidden.
* Other indices may still be valid choices.

Therefore, a simple recurrence like:

```text
take = arr[i] + solve(i + 2)
```

is **not sufficient**.

---

# Approach

A valid solution must ensure that:

* Adjacent elements are never selected.
* Elements exactly `M` indices apart are never selected.

Depending on the constraints, the problem can be solved using:

* Backtracking
* Memoization with additional state
* Bitmask Dynamic Programming (when `M` is small)
* Graph-based Dynamic Programming

A simple one-dimensional DP (`dp[i]`) is **not enough**, because the decision at index `i` depends on whether the element at index `i-M` has already been selected.

---

# Complexity

The complexity depends on the chosen approach:

| Approach               | Time Complexity  | Space Complexity |
| ---------------------- | ---------------- | ---------------- |
| Backtracking           | O(2^N)           | O(N)             |
| Memoization with state | Depends on state | Depends on state |
| Bitmask DP             | O(N × 2^M)       | O(2^M)           |

---

# Brute Force Solution (Java)

```java
class Solution {

    int ans = 0;

    public int maxSum(int[] arr, int m) {

        boolean[] chosen = new boolean[arr.length];

        dfs(0, 0, arr, m, chosen);

        return ans;
    }

    private void dfs(int index, int sum, int[] arr, int m, boolean[] chosen) {

        if (index == arr.length) {
            ans = Math.max(ans, sum);
            return;
        }

        // Skip current element
        dfs(index + 1, sum, arr, m, chosen);

        // Check whether current element can be selected
        boolean canTake = true;

        // Adjacent restriction
        if (index - 1 >= 0 && chosen[index - 1])
            canTake = false;

        // M-distance restriction
        if (index - m >= 0 && chosen[index - m])
            canTake = false;

        if (canTake) {
            chosen[index] = true;
            dfs(index + 1, sum + arr[index], arr, m, chosen);
            chosen[index] = false;
        }
    }

    public static void main(String[] args) {

        int[] arr = {5, 1, 8, 2, 10, 6};
        int m = 2;

        Solution obj = new Solution();

        System.out.println(obj.maxSum(arr, m));
    }
}
```

---

## Complexity Analysis

### Time Complexity

```
O(2^N)
```

Each element has two choices: take or skip.

### Space Complexity

```
O(N)
```

- Recursion stack
- Boolean array storing selected indices

# Similar Problems

* LeetCode 198 – House Robber
* LeetCode 213 – House Robber II
* GeeksforGeeks – Max Sum without Adjacents
* GeeksforGeeks – Stickler Thief
* Maximum Weight Independent Set in Graphs

---

# Concepts Used

* Dynamic Programming
* Backtracking
* Bitmask DP
* State Compression
* House Robber Pattern
* Graph DP
* Constraint-Based Subsequence Selection
