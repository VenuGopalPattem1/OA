# Best Time to Buy and Sell Stock with Variable Cooldown

## Problem Description
You are given an integer array `prices` where `prices[i]` represents the price of a given stock on day `i`.

You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times) under the following constraints:

1. **Single Stock Constraint:** You cannot hold more than one stock at a time (you must sell the stock before you can buy again).
2. **Variable Cooldown Rule:** If you buy the stock on day $b$ and sell it on day $s$ ($0 \le b < s < N$), the holding duration is:
   $$\text{Holding Duration} = s - b + 1 \text{ days}$$
   After selling on day $s$, you enter a cooldown period whose duration is equal to your holding duration ($s - b + 1$ days). During this cooldown, you cannot buy any stock.
3. **Next Buy Day:** The earliest day you can buy again after selling on day $s$ is:
   $$\text{Next Buy Day} = (s + 1) + (s - b + 1) = 2s - b + 2$$

---

## Objective
Find and return the **maximum total profit** you can achieve across all valid, non-overlapping transactions. If no profitable transactions can be made, return `0`.

---

## Example Walkthrough

### Example 1
**Input:** `prices = [1, 3, 2, 8, 4, 9]`  
**Output:** `7`

**Explanation:**
- **Option 1:** Buy on day `0` (`prices[0] = 1`), sell on day `3` (`prices[3] = 8`).
  - Profit: $8 - 1 = 7$.
  - Holding duration: $3 - 0 + 1 = 4$ days.
  - Cooldown: $4$ days (days 4, 5, 6, 7).
  - Next buy day: Day `8` (out of bounds).
  - Total Profit = **7**.

- **Option 2:** Buy on day `0` (`prices[0] = 1`), sell on day `1` (`prices[1] = 3`).
  - Profit: $3 - 1 = 2$.
  - Holding duration: $1 - 0 + 1 = 2$ days.
  - Cooldown: $2$ days (days 2, 3).
  - Next buy day: Day `4`.
  - Buy on day `4` (`prices[4] = 4`), sell on day `5` (`prices[5] = 9`). Profit = $9 - 4 = 5$.
  - Total Profit: $2 + 5 =$ **7**.

---

## Java Solutions

### 1. Top-Down Dynamic Programming (Recursive Memoization with Buy/Sell State)

```java
import java.util.Arrays;

public class Solution {

    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n < 2) return 0;

        // dp[day][buy][buyDay]
        // day: 0 to n - 1
        // buy: 0 (ready to buy), 1 (holding stock)
        // buyDay: 0 to n (day when stock was bought; n represents not holding)
        int[][][] dp = new int[n][2][n + 1];
        for (int[][] matrix : dp) {
            for (int[] row : matrix) {
                Arrays.fill(row, -1);
            }
        }

        return solve(0, 0, n, dp, prices, n);
    }

    private int solve(int day, int buy, int n, int[][][] dp, int[] prices, int buyDay) {
        // Base case: Past the last day
        if (day >= n) {
            return 0;
        }

        if (dp[day][buy][buyDay] != -1) {
            return dp[day][buy][buyDay];
        }

        if (buy == 0) {
            // Option 1: Buy stock today (records buyDay = day, state transitions to buy = 1)
            int take = -prices[day] + solve(day + 1, 1, n, dp, prices, day);
            // Option 2: Skip buying today
            int notTake = solve(day + 1, 0, n, dp, prices, n);
            dp[day][buy][buyDay] = Math.max(take, notTake);
        } else {
            // Selling on 'day' when bought on 'buyDay':
            // Next available buy day = (day + 1) + (day - buyDay + 1) = 2 * day - buyDay + 2
            int nextBuyDay = 2 * day - buyDay + 2;

            // Option 1: Sell stock today and jump past cooldown
            int sell = prices[day] + solve(nextBuyDay, 0, n, dp, prices, n);
            // Option 2: Keep holding the stock
            int hold = solve(day + 1, 1, n, dp, prices, buyDay);
            dp[day][buy][buyDay] = Math.max(sell, hold);
        }

        return dp[day][buy][buyDay];
    }
}
```

---

### 2. 1D Top-Down Memoization (Decision at Buy Day)

```java
import java.util.Arrays;

public class Solution1DMemo {

    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n < 2) return 0;

        int[] memo = new int[n];
        Arrays.fill(memo, -1);

        return solve(0, prices, memo);
    }

    private int solve(int day, int[] prices, int[] memo) {
        int n = prices.length;
        if (day >= n - 1) {
            return 0;
        }

        if (memo[day] != -1) {
            return memo[day];
        }

        // Choice 1: Skip buying on this day
        int maxProfit = solve(day + 1, prices, memo);

        // Choice 2: Buy on 'day' and pick every valid future sell day 's'
        for (int s = day + 1; s < n; s++) {
            if (prices[s] > prices[day]) {
                int tradeProfit = prices[s] - prices[day];
                int nextBuyDay = 2 * s - day + 2;
                int futureProfit = solve(nextBuyDay, prices, memo);
                maxProfit = Math.max(maxProfit, tradeProfit + futureProfit);
            }
        }

        return memo[day] = maxProfit;
    }
}
```

---

## Complexity Analysis

| Approach | Time Complexity | Space Complexity |
| :--- | :--- | :--- |
| **3D State DP (`[day][buy][buyDay]`)** | $\mathcal{O}(N^2)$ | $\mathcal{O}(N^2)$ |
| **1D DP (`memo[day]`)** | $\mathcal{O}(N^2)$ | $\mathcal{O}(N)$ |
