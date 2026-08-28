# DP / Binary Search Problems — Problems and Java Solutions

## 1. Best Time to Buy and Sell Stock With Transaction Fee + Parity Restriction

### Problem Description

Given an array of daily stock prices and a fixed transaction fee, find the maximum profit you can achieve.

You may complete as many transactions as you like, but:

- You must sell the stock before buying again.
- The transaction fee is paid once for every completed buy-sell transaction.
- You can sell a stock only if the selling price has a different parity (odd/even) from the price at which you bought it.

### DP Idea

Use:

```text
fun(i, state)
```

where:

- `i` = current day
- `state = 0` = we are allowed to buy
- `state = 1` = we are holding a stock and can sell

For the parity restriction, when selling, check:

```java
(a[i] % 2) != (buyPrice % 2)
```

### Java Memoization Code

```java
import java.util.*;

class Solution {

    int[][][] dp;

    public long maxProfit(int[] a, int fee) {
        int n = a.length;

        // state: 0 = can buy, 1 = holding stock
        // buy parity: 0 = even, 1 = odd
        dp = new int[n][2][2];

        for (int[][] x : dp) {
            for (int[] y : x) {
                Arrays.fill(y, Integer.MIN_VALUE);
            }
        }

        return fun(0, 0, 0, a, fee);
    }

    long fun(int i, int state, int buyParity,
             int[] a, int fee) {

        if (i == a.length) {
            return 0;
        }

        if (dp[i][state][buyParity] != Integer.MIN_VALUE) {
            return dp[i][state][buyParity];
        }

        long ans;

        if (state == 0) {

            // Buy
            long buy = -a[i] + fun(i + 1, 1,
                    a[i] % 2, a, fee);

            // Don't buy
            long skip = fun(i + 1, 0,
                    buyParity, a, fee);

            ans = Math.max(buy, skip);

        } else {

            // Don't sell
            long hold = fun(i + 1, 1,
                    buyParity, a, fee);

            long sell = Long.MIN_VALUE;

            // Sell only if parity is different
            if ((a[i] % 2 + 2) % 2 !=
                (buyParity + 2) % 2) {

                sell = a[i] - fee +
                       fun(i + 1, 0,
                           buyParity, a, fee);
            }

            ans = Math.max(hold, sell);
        }

        return dp[i][state][buyParity] = (int) ans;
    }
}
```

> Note: If the input constraints allow large profits, use `long` DP storage instead of `int`.

---

# 2. Minimum Cost For Tickets With Loyalty Tokens

## Problem Description

You are given a sorted array `days` containing the days of the year on which you need to travel.

Three types of passes are available:

- A 1-day pass covers one day and gives `0` loyalty tokens.
- A 7-day pass covers 7 consecutive days and gives `1` loyalty token.
- A 30-day pass covers 30 consecutive days and gives `2` loyalty tokens.

A loyalty token can be used on a future travel day as a free 1-day pass.

Find the minimum cost required to cover all travel days.

### Memoization Idea

Use the day number as the state because the days are between `1` and `365`.

`tokens` tells us how many free 1-day passes are available.

At every travel day, we can:

1. Use a token if available.
2. Buy a 1-day pass.
3. Buy a 7-day pass and gain one token.
4. Buy a 30-day pass and gain two tokens.

### Java Memoization Code Using a 365-Day Boolean Array

```java
import java.util.*;

class Solution {

    int[][] dp;
    boolean[] travel;
    int[] costs;

    public int mincostTickets(int[] days, int[] costs) {

        this.costs = costs;

        travel = new boolean[366];

        for (int d : days) {
            travel[d] = true;
        }

        dp = new int[366][100];

        for (int[] x : dp) {
            Arrays.fill(x, -1);
        }

        return fun(1, 0);
    }

    int fun(int day, int tokens) {

        if (day > 365) {
            return 0;
        }

        if (dp[day][tokens] != -1) {
            return dp[day][tokens];
        }

        // No travel today
        if (!travel[day]) {
            return dp[day][tokens] = fun(day + 1, tokens);
        }

        // Use token
        int useToken = Integer.MAX_VALUE;

        if (tokens > 0) {
            useToken = fun(day + 1, tokens - 1);
        }

        // Buy 1-day pass
        int one = costs[0] + fun(day + 1, tokens);

        // Buy 7-day pass -> get 1 token
        int seven = costs[1] + fun(day + 7, tokens + 1);

        // Buy 30-day pass -> get 2 tokens
        int thirty = costs[2] + fun(day + 30, tokens + 2);

        return dp[day][tokens] =
                Math.min(useToken,
                    Math.min(one,
                        Math.min(seven, thirty)));
    }
}
```

---

# 3. Maximum Number of Transitions in a Subsequence

## Problem Description

You are given an array where each number represents a color.

Choose a subsequence such that the number of transitions between consecutive selected colors is maximized.

A transition happens when two consecutive selected elements have different colors.

For example:

```text
[1, 2, 2, 3]
```

Choosing:

```text
[1, 2, 3]
```

gives two transitions:

```text
1 -> 2
2 -> 3
```

There is no need for a separate "same color" transition because selecting the same color does not increase the answer. It can simply be skipped.

### Memoization Code

```java
import java.util.*;

class Solution {

    int[][] dp;

    public int maxTransitions(int[] a) {

        int n = a.length;

        dp = new int[n][n + 1];

        for (int[] x : dp) {
            Arrays.fill(x, -1);
        }

        return fun(0, -1, a);
    }

    int fun(int i, int prev, int[] a) {

        if (i == a.length) {
            return 0;
        }

        if (dp[i][prev + 1] != -1) {
            return dp[i][prev + 1];
        }

        // Don't take current element
        int notTake = fun(i + 1, prev, a);

        int take = 0;

        if (prev == -1) {

            // First selected element creates no transition
            take = fun(i + 1, i, a);

        } else if (a[i] != a[prev]) {

            // Different color creates one transition
            take = 1 + fun(i + 1, i, a);
        }

        return dp[i][prev + 1] =
                Math.max(take, notTake);
    }
}
```

### Complexity

```text
Time:  O(N^2)
Space: O(N^2)
```

---

# 4. Maximum Length Subsequence With a Value-Range Restriction

## Problem Description

You are given an array.

Find the maximum length of a subsequence such that if you pick an element `a[i]`, you cannot later pick an element `a[j]` when:

```text
a[i] / 2 <= a[j] <= a[i] * 2
```

In other words, after choosing a value, the next selected value must be outside the range from half of the previous value to twice the previous value.

The exact inequality should be adjusted according to the original problem statement if it specifies strict or non-strict boundaries.

### Memoization Code

```java
import java.util.*;

class Solution {

    int[][] dp;

    public int maxLength(int[] a) {

        int n = a.length;

        dp = new int[n][n + 1];

        for (int[] x : dp) {
            Arrays.fill(x, -1);
        }

        return fun(0, -1, a);
    }

    int fun(int i, int prev, int[] a) {

        if (i == a.length) {
            return 0;
        }

        if (dp[i][prev + 1] != -1) {
            return dp[i][prev + 1];
        }

        // Don't take
        int notTake = fun(i + 1, prev, a);

        int take = 0;

        if (prev == -1 ||
            a[i] < a[prev] / 2.0 ||
            a[i] > a[prev] * 2.0) {

            take = 1 + fun(i + 1, i, a);
        }

        return dp[i][prev + 1] =
                Math.max(take, notTake);
    }
}
```

### Complexity

```text
Time:  O(N^2)
Space: O(N^2)
```

---

# 5. Maximum Product Subarray — Strictly Increasing Absolute Values

## Problem Description

Given an integer array `nums`, return the maximum product of a non-empty contiguous subarray such that the absolute values of its elements are strictly increasing.

A subarray is valid when:

```text
|nums[i]| < |nums[i + 1]|
```

for every pair of adjacent elements.

Example:

```text
[2, -3, 4]
```

is valid because:

```text
2 < 3 < 4
```

For:

```text
[2, -3, 4, -5]
```

the whole array is valid and:

```text
2 * (-3) * 4 * (-5) = 120
```

### Important Idea

We need both:

- `max` = maximum product ending at the current position
- `min` = minimum product ending at the current position

This is necessary because multiplying a negative number by the minimum negative product can produce a large positive product.

### Java Code

```java
class Solution {

    public long maxProduct(int[] a) {

        long max = a[0];
        long min = a[0];
        long ans = a[0];

        for (int i = 1; i < a.length; i++) {

            long x = a[i];

            if (Math.abs(x) >
                Math.abs((long) a[i - 1])) {

                long oldMax = max;
                long oldMin = min;

                max = Math.max(x,
                        Math.max(oldMax * x,
                                 oldMin * x));

                min = Math.min(x,
                        Math.min(oldMax * x,
                                 oldMin * x));

            } else {

                // Cannot extend the previous subarray
                max = x;
                min = x;
            }

            ans = Math.max(ans, max);
        }

        return ans;
    }
}
```

### Example

```text
Input:
4
2 -3 4 -5

Absolute values:
2 < 3 < 4 < 5

Product:
2 * -3 * 4 * -5 = 120

Output:
120
```

### Complexity

```text
Time:  O(N)
Space: O(1)
```

---

# 6. Minimum Time to Make Rope Colorful / Diffuser

## Problem Description

You are given a string `colors` and an array `neededTime`.

Each character represents the color of a lamp/balloon.

For two adjacent elements with the same color, one of them must be diffused/removed so that no two same-color elements remain adjacent.

`neededTime[i]` is the cost of removing/diffusing element `i`.

For every consecutive group of the same color, keep the element with the largest cost and remove all the others. This minimizes the total cost.

### Example

```text
colors = "aaa"
neededTime = [10, 1, 10]
```

Keep one `10` and remove:

```text
1 + 10 = 11
```

Answer:

```text
11
```

### Simple Java Code

```java
class Solution {

    public int minCost(String colors, int[] neededTime) {

        int ans = 0;

        for (int i = 1; i < colors.length(); i++) {

            if (colors.charAt(i) ==
                colors.charAt(i - 1)) {

                // Remove the cheaper one
                ans += Math.min(neededTime[i],
                                neededTime[i - 1]);

                // Keep the expensive one
                neededTime[i] =
                    Math.max(neededTime[i],
                             neededTime[i - 1]);
            }
        }

        return ans;
    }
}
```

### Why keep the expensive one?

If two same-color elements have costs:

```text
10, 3
```

we must remove one.

Removing `3` costs less than removing `10`.

Therefore:

```text
keep 10
remove 3
```

For a group, this is equivalent to keeping the maximum cost and removing all other costs.

### Complexity

```text
Time:  O(N)
Space: O(1)
```

---

# 7. Capacity to Ship Packages Within D Days — Alternating Capacity

## Problem Description

You have `N` packages that must be shipped in the given order within `days` days.

The ship capacity alternates:

- Odd-numbered day: capacity = `C`
- Even-numbered day: capacity = `C / 2`

If the next package does not fit into the current day's remaining capacity, that package waits for the next day. The current day can therefore finish without shipping that package.

Find the minimum `C`.

### Example

```text
N = 4
days = 4
weights = [4, 3, 7, 2]
```

For:

```text
C = 7
```

we get:

```text
Day 1: capacity 7 -> 4, 3
Day 2: capacity 3 -> empty because 7 does not fit
Day 3: capacity 7 -> 7
Day 4: capacity 3 -> 2
```

Therefore:

```text
Answer = 7
```

### Simple Helper Function

```java
static boolean can(int[] a, int days, int k) {

    int i = 0;

    for (int d = 1;
         d <= days && i < a.length;
         d++) {

        int cap = (d % 2 == 1) ? k : k / 2;
        int sum = 0;

        while (i < a.length &&
               sum + a[i] <= cap) {

            sum += a[i];
            i++;
        }
    }

    return i == a.length;
}
```

The important condition is:

```java
sum + a[i] <= cap
```

or equivalently, when the package doesn't fit:

```java
sum + a[i] > cap
```

then we stop the current day and move to the next day.

### Full Binary Search Code

```java
import java.util.*;

class Main {

    static boolean can(int[] a, int days, int k) {

        int i = 0;

        for (int d = 1;
             d <= days && i < a.length;
             d++) {

            int cap = (d % 2 == 1) ? k : k / 2;
            int sum = 0;

            while (i < a.length &&
                   sum + a[i] <= cap) {

                sum += a[i];
                i++;
            }
        }

        return i == a.length;
    }

    static int solve(int[] a, int days) {

        int low = 0;
        int high = 0;

        for (int x : a) {
            low = Math.max(low, x);
            high += x;
        }

        // Make sure the upper bound is large enough
        // even for even-day capacity C/2.
        high = Math.max(high, 2 * low);

        int ans = high;

        while (low <= high) {

            int mid = low + (high - low) / 2;

            if (can(a, days, mid)) {

                ans = mid;
                high = mid - 1;

            } else {

                low = mid + 1;
            }
        }

        return ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        int days = sc.nextInt();

        int[] a = new int[n];

        for (int i = 0; i < n; i++) {
            a[i] = sc.nextInt();
        }

        System.out.println(solve(a, days));
    }
}
```

### Complexity

```text
check(): O(N)
Binary Search: O(log(sum(weights)))

Total: O(N log(sum(weights)))
Space: O(1)
```

---

# 8. Quantum Core Parity

## Problem Description

You have `N` nodes. Each node can contain one of:

```text
0, 1, 2
```

Rules:

1. Two consecutive nodes cannot both be `0`.
2. The normal energy is the sum of all node values.
3. If `1` is immediately followed by `2`, an additional `5` energy is added.
4. Count how many valid configurations have even total energy.
5. Return the answer modulo `10^9 + 7`.

Examples:

```text
N = 1 -> 2
N = 2 -> 4
N = 3 -> 11
```

### Why store parity instead of the full sum?

We only need to know whether the final energy is even or odd.

Represent:

```text
0 = even
1 = odd
```

Adding an even number does not change parity.

Adding an odd number flips parity.

Therefore:

```java
parity ^= (x % 2);
```

The special `+5` also flips parity because `5` is odd:

```java
if (prev == 1 && x == 2) {
    parity ^= 1;
}
```

### Memoization Code

```java
import java.util.*;

class Main {

    static final long MOD = 1000000007;

    static long[][][] dp;

    static long fun(int i, int prev,
                    int parity, int n) {

        if (i == n) {

            // Count only even-energy configurations
            return parity == 0 ? 1 : 0;
        }

        if (dp[i][prev][parity] != -1) {
            return dp[i][prev][parity];
        }

        long ans = 0;

        for (int x = 0; x <= 2; x++) {

            // No consecutive 0s
            if (prev == 0 && x == 0) {
                continue;
            }

            int newParity = parity;

            // Add x to energy
            newParity ^= (x % 2);

            // 1 followed by 2 adds extra 5.
            // 5 is odd, so flip parity.
            if (prev == 1 && x == 2) {
                newParity ^= 1;
            }

            ans = (ans +
                   fun(i + 1, x,
                       newParity, n)) % MOD;
        }

        return dp[i][prev][parity] = ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        // prev has 3 possible values:
        // 0, 1, 2.
        //
        // We use prev = 2 initially as a dummy
        // "no previous value" state.
        dp = new long[n][3][2];

        for (long[][] x : dp) {
            for (long[] y : x) {
                Arrays.fill(y, -1);
            }
        }

        System.out.println(
            fun(0, 2, 0, n)
        );
    }
}
```

### DP State

```text
fun(i, prev, parity)
```

where:

- `i` = current position
- `prev` = previous value
- `parity = 0` = current energy is even
- `parity = 1` = current energy is odd

### Complexity

There are:

```text
N × 3 × 2
```

states and at most `3` choices for each state.

Therefore:

```text
Time:  O(N)
Space: O(N)
```

---

# Quick Pattern Summary

| Problem | Main Technique |
|---|---|
| Stock Buy/Sell + Fee + Parity | DP on day + buy/sell state |
| Minimum Cost Tickets | Memoization on day |
| Maximum Color Transitions | Subsequence DP |
| Maximum Length Value-Range Subsequence | Subsequence DP |
| Maximum Product Increasing Absolute Values | DP with max/min product |
| Rope Colorful / Diffuser | Greedy |
| Alternating Shipping Capacity | Binary Search + Simulation |
| Quantum Core Parity | Memoization + Parity DP |

## Important DP Patterns

### Buy / Sell DP

```java
fun(day, state)
```

### Subsequence DP

```java
fun(i, prev)
```

At each element:

```java
take
notTake
```

### Parity DP

```java
fun(i, prev, parity)
```

Only `even/odd` is stored instead of the complete sum.

### Binary Search on Answer

```text
low = minimum possible answer
high = maximum possible answer

while (low <= high) {
    mid = ...
    if (can(mid))
        search left
    else
        search right
}
```

### Greedy Adjacent-Duplicate Pattern

When two adjacent same-color elements occur:

```java
ans += Math.min(a[i], a[i - 1]);

a[i] = Math.max(a[i], a[i - 1]);
```

This keeps the expensive element and removes the cheaper one.
