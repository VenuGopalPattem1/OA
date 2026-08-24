# Stock Trading with Settlement Delay

## Problem Description

You are given an integer array `prices[]`, where `prices[i]` is the stock price on day `i`.

You may perform **at most one buy-sell transaction**.

If you buy on day `i`, the settlement delay is:

```text
(prices[i] % 10) + 1
```

Therefore, you can sell only on a day satisfying:

```text
j >= i + (prices[i] % 10) + 1
```

Profit:

```text
prices[j] - prices[i]
```

Return the maximum possible profit, or `0` if no positive profit is possible.

---

## Example 1

```text
prices = [3, 8, 2, 10, 6, 15]
```

Buy on day `2`:

```text
price = 2
delay = (2 % 10) + 1 = 3
earliest sell day = 2 + 3 = 5
```

Sell on day `5`:

```text
profit = 15 - 2 = 13
```

Answer:

```text
13
```

---

## Example 2

```text
prices = [10, 5, 4, 3, 2]
```

No valid transaction produces positive profit.

Answer:

```text
0
```

---

# DP Idea

Use the standard Buy/Sell DP:

```text
fun(i, state)
```

where:

```text
state = 0 -> have not bought yet
state = 1 -> have bought and can sell
```

### State 0

Choices:

```text
SKIP
BUY
```

If we buy on day `i`:

```text
delay = (prices[i] % 10) + 1
sellDay = i + delay
```

Transition:

```text
-prices[i] + fun(sellDay, 1)
```

### State 1

Choices:

```text
SKIP
SELL
```

Selling gives:

```text
prices[i]
```

---

# Memoization Code

```java
import java.util.*;

class Solution {

    public int maxProfit(int[] a) {

        int n = a.length;

        int[][] dp = new int[n][2];

        for (int[] x : dp) {
            Arrays.fill(x, -1);
        }

        return fun(dp, 0, n, a, 0);
    }

    // day = 0 -> can buy
    // day = 1 -> already bought, can sell
    int fun(int[][] dp, int day, int n, int[] a, int i) {

        if (i >= n) {
            return 0;
        }

        if (dp[i][day] != -1) {
            return dp[i][day];
        }

        if (day == 0) {

            // Skip buying today
            int skip = fun(dp, 0, n, a, i + 1);

            // Buy today
            int delay = (a[i] % 10) + 1;
            int sellDay = i + delay;

            int buy = 0;

            if (sellDay < n) {
                buy = -a[i] + fun(
                    dp, 1, n, a, sellDay
                );
            }

            dp[i][day] = Math.max(buy, skip);

        } else {

            // Sell today
            int sell = a[i];

            // Wait
            int skip = fun(dp, 1, n, a, i + 1);

            dp[i][day] = Math.max(sell, skip);
        }

        return dp[i][day];
    }
}
```

---

# Tabulation Code

```java
class Solution {

    public int maxProfit(int[] a) {

        int n = a.length;

        int[][] dp = new int[n + 1][2];

        for (int i = n - 1; i >= 0; i--) {

            // State 1: already bought
            int sell = a[i];
            int skipSell = dp[i + 1][1];

            dp[i][1] = Math.max(
                sell,
                skipSell
            );

            // State 0: have not bought
            int skipBuy = dp[i + 1][0];

            int delay = (a[i] % 10) + 1;
            int sellDay = i + delay;

            int buy = 0;

            if (sellDay < n) {
                buy = -a[i] + dp[sellDay][1];
            }

            dp[i][0] = Math.max(
                buy,
                skipBuy
            );
        }

        return dp[0][0];
    }
}
```

---

# Why Tabulation Goes Right to Left

The recurrence uses:

```text
dp[i + 1]
```

and:

```text
dp[sellDay]
```

Both indices are greater than `i`.

Therefore calculate:

```text
N-1
N-2
...
1
0
```

The final answer is:

```text
dp[0][0]
```

---

# Dry Run of the Important Transition

For:

```text
prices = [3, 8, 2, 10, 6, 15]
```

At `i = 2`:

```text
a[i] = 2
```

Delay:

```text
(2 % 10) + 1 = 3
```

Earliest selling day:

```text
2 + 3 = 5
```

Therefore:

```text
buy = -2 + dp[5][1]
```

At day `5`:

```text
dp[5][1] = 15
```

So:

```text
buy = -2 + 15
     = 13
```

Hence the answer is at least `13`.

---

# Complexity

### Memoization

```text
Time Complexity  = O(N)
Space Complexity = O(N)
```

There are only `2N` states.

### Tabulation

```text
Time Complexity  = O(N)
Space Complexity = O(N)
```

---

# Core Pattern

```text
fun(i, 0)
    |
    |-- SKIP
    |     fun(i + 1, 0)
    |
    |-- BUY
          -a[i] + fun(i + delay, 1)


fun(i, 1)
    |
    |-- SKIP
    |     fun(i + 1, 1)
    |
    |-- SELL
          +a[i]
```

where:

```text
delay = (a[i] % 10) + 1
```

The important DP state is:

```text
DP(day, 0/1)
```

The special part is the **variable jump after buying**.
