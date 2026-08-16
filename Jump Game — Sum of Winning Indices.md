# Jump Game — Sum of Winning Indices

## Problem Statement
You are given an integer array `nums` of length $N$, where `nums[i]` represents the maximum forward jump length from index $i$.

An index $i$ ($0 \le i < N$) is considered a **"winning index"** if you can eventually reach the last index ($N - 1$) starting from $i$.

**Objective:** Find and return the **sum of all winning indices**.

---

## Constraints
- $1 \le N \le 10^4$
- $0 \le \text{nums}[i] \le 10^5$

---

## Examples & Testcases

### Testcase 1
- **Input:**
  ```text
  5
  2 3 1 0 4
  ```
- **Output:**
  ```text
  5
  ```
- **Explanation:**
  - Index `4`: At the last index $\rightarrow$ **Winning**
  - Index `3`: Can jump $0$ steps $\rightarrow$ Cannot reach index 4
  - Index `2`: Can jump to index 3 $\rightarrow$ Cannot reach index 4
  - Index `1`: Can jump to index 2, 3, or 4 $\rightarrow$ **Winning** (direct jump to 4)
  - Index `0`: Can jump to index 1 or 2 $\rightarrow$ **Winning** (jump to 1, then to 4)
  - Sum of winning indices = $0 + 1 + 4 = 5$.

---

### Testcase 2
- **Input:**
  ```text
  5
  3 2 1 0 4
  ```
- **Output:**
  ```text
  4
  ```
- **Explanation:**
  - Only the last index (`4`) can reach the end.
  - From indices `0, 1, 2`, all paths get stuck at index `3` (where jump length is `0`).
  - Sum of winning indices = $4$.

---

### Testcase 3 (Single Element)
- **Input:**
  ```text
  1
  0
  ```
- **Output:**
  ```text
  0
  ```
- **Explanation:**
  - $N = 1$. The only index is `0`, which is already the destination.
  - Sum = $0$.

---

### Testcase 4 (All Reachable)
- **Input:**
  ```text
  4
  1 1 1 1
  ```
- **Output:**
  ```text
  6
  ```
- **Explanation:**
  - Every index can jump to the next index, easily reaching the end.
  - Winning indices: `0, 1, 2, 3`.
  - Sum = $0 + 1 + 2 + 3 = 6$.

---

## Java Solutions

### 1. Optimal Solution — Reverse Greedy Scan $\mathcal{O}(N)$

```java
import java.util.Scanner;

public class OptimalJumpGameWinningSum {

    public static long sumOfWinningIndices(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;

        // Last index is always a winning index
        long totalSum = n - 1;
        int target = n - 1;

        // Traverse backwards from n - 2 to 0
        for (int i = n - 2; i >= 0; i--) {
            // If index i can reach the nearest known winning target
            if (i + nums[i] >= target) {
                totalSum += i;
                target = i; // i becomes the new nearest target
            }
        }

        return totalSum;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        if (!sc.hasNextInt()) return;

        int n = sc.nextInt();
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = sc.nextInt();
        }

        System.out.println(sumOfWinningIndices(nums));
        sc.close();
    }
}
```

---

### 2. Top-Down DP / Frog Jump Style (Memoization) $\mathcal{O}(N^2)$

```java
import java.util.Arrays;
import java.util.Scanner;

public class FrogJumpWinningIndices {

    public static long sumOfWinningIndices(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;

        // dp[i]: -1 = unvisited, 1 = winning, 0 = not winning
        int[] dp = new int[n];
        Arrays.fill(dp, -1);

        long totalSum = 0;

        for (int i = 0; i < n; i++) {
            if (canJump(i, nums, dp, n) == 1) {
                totalSum += i;
            }
        }

        return totalSum;
    }

    private static int canJump(int index, int[] nums, int[] dp, int n) {
        if (index == n - 1) {
            return 1;
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int maxJump = Math.min(index + nums[index], n - 1);

        for (int next = index + 1; next <= maxJump; next++) {
            if (canJump(next, nums, dp, n) == 1) {
                return dp[index] = 1;
            }
        }

        return dp[index] = 0;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        if (!sc.hasNextInt()) return;

        int n = sc.nextInt();
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = sc.nextInt();
        }

        System.out.println(sumOfWinningIndices(nums));
        sc.close();
    }
}
```

---

## Complexity Analysis

| Approach | Time Complexity | Space Complexity |
| :--- | :--- | :--- |
| **Reverse Greedy (Optimal)** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ |
| **Frog Jump / Memoization** | $\mathcal{O}(N^2)$ | $\mathcal{O}(N)$ |
