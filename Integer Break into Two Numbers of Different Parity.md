# Problem Analysis: Integer Break into Two Numbers of Different Parity

---

## 1. Problem Statement

Given a positive integer $n$, partition it into **exactly two positive integers** ($a$ and $b$) such that:
1. $a + b = n$
2. $a \ge 1$ and $b \ge 1$
3. $a$ and $b$ have **different parities** (one is even, one is odd).
4. The product $a \times b$ is **maximized**.

If no such partition exists, return `-1`.

---

## 2. Top-Down Dynamic Programming (Recursion + Memoization)

### Intuition & Formulation
* **State:** `fun(n)` computes the maximum product achievable by breaking integer $n$ into two valid positive integers.
* **Base Case:** 
  * If $n < 3$, return `-1` (no two positive integers with different parities sum to less than 3).
* **Transitions:** 
  * Try every split point $i \in [1, n - 1]$.
  * Set $a = i$ and $b = n - i$.
  * Verify different parity: `(a % 2) != (b % 2)`.
  * If valid, track the maximum product: `ans = max(ans, a * b)`.
* **Memoization Table:** `Integer[] dp` or `Long[] dp` caches computed results to avoid redundant work.

---

### Java Memoization Code

```java
class Solution {
    private Integer[] dp;

    public int integerBreak(int n) {
        if (n < 3) {
            return -1;
        }
        dp = new Integer[n + 1];
        return fun(n);
    }

    public int fun(int n) {
        // Base case: impossible to partition numbers smaller than 3
        if (n < 3) {
            return -1;
        }

        // Return memoized result if already computed
        if (dp[n] != null) {
            return dp[n];
        }

        int ans = -1;

        // Try every split i and (n - i)
        for (int i = 1; i < n; i++) {
            int first = i;
            int second = n - i;

            // Parity check: one must be odd and the other even
            if ((first % 2) != (second % 2)) {
                ans = Math.max(ans, first * second);
            }
        }

        return dp[n] = ans;
    }
}
