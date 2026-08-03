# Maximum Savings via K Fixed Discount Coupons

## Problem Description

You are given an array of **N** items, where the `i-th` item has a price `P[i]`.

You also have **K** identical discount coupons. Each coupon provides a **fixed discount** of `D` on a single item.

The actual discount received on an item cannot exceed its price. Therefore, the effective discount for an item is:

```text
min(P[i], D)
```

Each item can receive **at most one coupon**.

According to the store policy, you must choose **exactly one contiguous subarray (window) of length `W`**. Within the selected window, you may apply your coupons to **at most `K` items** to maximize your total savings.

Return the **maximum possible savings** over all valid windows.

Since the answer can be large, return it modulo **10⁹ + 7**.

---

# Rules

* Choose exactly one contiguous subarray of size `W`.
* Each coupon can be applied to only one item.
* Each item can receive at most one coupon.
* The discount on an item is `min(price, D)`.
* Apply coupons to **at most K items** inside the chosen window.
* Maximize the total savings.

---

# Input Format

* First line contains four integers:

```text
N W K D
```

* Second line contains `N` integers representing the prices of the items.

---

# Output Format

Print a single integer representing the maximum possible savings modulo `10⁹ + 7`.

---

# Constraints

```text
1 ≤ W ≤ N ≤ 2 × 10^5
1 ≤ K ≤ W
1 ≤ P[i], D ≤ 10^9
```

---

# Example

### Input

```text
6 3 2 5

2 8 4 7 3 6
```

### Output

```text
10
```

### Explanation

Possible windows of size `3` are:

```text
[2,8,4]
[8,4,7]
[4,7,3]
[7,3,6]
```

For every item, compute

```text
discount = min(price, D)
```

Discount values become

```text
2 5 4 5 3 5
```

For each window, choose the **largest two discounts**.

| Window  | Discounts | Best 2 | Savings |
| ------- | --------- | ------ | ------: |
| [2,8,4] | 2 5 4     | 5+4    |       9 |
| [8,4,7] | 5 4 5     | 5+5    |      10 |
| [4,7,3] | 4 5 3     | 5+4    |       9 |
| [7,3,6] | 5 3 5     | 5+5    |      10 |

Maximum savings = **10**

---

# Test Case 1

### Input

```text
5 2 1 10

3 8 15 6 2
```

### Output

```text
10
```

### Explanation

Discounts become

```text
3 8 10 6 2
```

Possible windows:

```text
[3,8]   -> 8
[8,10]  -> 10
[10,6]  -> 10
[6,2]   -> 6
```

The maximum saving is **10**.

---

# Test Case 2

### Input

```text
5 3 2 4

6 1 5 2 7
```

### Output

```text
8
```

### Explanation

Discount values are

```text
4 1 4 2 4
```

Possible windows:

```text
[4,1,4] -> 4 + 4 = 8
[1,4,2] -> 4 + 2 = 6
[4,2,4] -> 4 + 4 = 8
```

Hence, the maximum savings is **8**.

---

# Brute Force Approach

For every window of size `W`:

1. Compute the discount for every item:

```text
discount = min(price, D)
```

2. Store the discounts of the current window in a temporary array.

3. Sort the discounts.

4. Select the largest `K` discounts.

5. Compute the total savings for the current window.

6. Return the maximum savings among all windows.

---

# Algorithm

```text
For every window of size W

    Compute discounts

    Sort the discounts

    Pick the largest K discounts

    Update the answer
```

---

# Java Brute Force Solution

```java
import java.util.*;

class Solution {

    static final int MOD = 1000000007;

    public int maximumSavings(int[] price, int W, int K, int D) {

        int n = price.length;
        long ans = 0;

        // Try every window
        for (int start = 0; start <= n - W; start++) {

            int[] discount = new int[W];

            // Compute discounts for the current window
            for (int i = 0; i < W; i++) {
                discount[i] = Math.min(price[start + i], D);
            }

            // Sort discounts
            Arrays.sort(discount);

            // Pick the largest K discounts
            long curr = 0;
            int coupons = K;

            for (int i = W - 1; i >= 0 && coupons > 0; i--) {
                curr += discount[i];
                coupons--;
            }

            ans = Math.max(ans, curr);
        }

        return (int)(ans % MOD);
    }
}
```

---

# Complexity Analysis

### Time Complexity

There are

```text
N - W + 1
```

windows.

For every window:

* Computing discounts takes `O(W)`.
* Sorting takes `O(W log W)`.
* Selecting the largest `K` discounts takes `O(K)`.

Overall,

```text
O((N - W + 1) × W log W)
≈ O(N × W log W)
```

---

### Space Complexity

Temporary discount array:

```text
O(W)
```

---

# Related Problems

* LeetCode 239 – Sliding Window Maximum
* LeetCode 480 – Sliding Window Median
* LeetCode 215 – Kth Largest Element in an Array
* LeetCode 347 – Top K Frequent Elements
* LeetCode 1005 – Maximize Sum of Array After K Negations
* GeeksforGeeks – Maximum Sum Subarray of Size K
* GeeksforGeeks – K Largest Elements in an Array

---

# Concepts Used

* Sliding Window
* Greedy Selection
* Sorting
* Array Processing
* Top-K Elements
* Brute Force
