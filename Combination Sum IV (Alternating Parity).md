# Combination Sum IV (Alternating Parity)

## Problem Description
You are given an array of distinct positive integers `nums` and an integer `target`.

Return the number of ordered combinations (sequences/permutations) of `nums` that sum up to `target` such that **no two adjacent elements in the combination have the same parity** (i.e., numbers must strictly alternate between even and odd).

* Different orderings count as distinct combinations.
* An element from `nums` can be reused multiple times.
* Return the answer modulo $10^9 + 7$.

---

## Example Walkthrough

### Example 1
* **Input:** `nums = [1, 2, 3]`, `target = 4`
* **All standard combinations summing to 4:**
  * `[1, 1, 1, 1]` $\rightarrow$ Parities: `[O, O, O, O]` (Invalid: consecutive odds)
  * `[1, 1, 2]`, `[1, 2, 1]`, `[2, 1, 1]` $\rightarrow$
    * `[1, 1, 2]` $\rightarrow$ `[O, O, E]` (Invalid)
    * `[1, 2, 1]` $\rightarrow$ `[O, E, O]` (**Valid**)
    * `[2, 1, 1]` $\rightarrow$ `[E, O, O]` (Invalid)
  * `[2, 2]` $\rightarrow$ `[E, E]` (Invalid: consecutive evens)
  * `[1, 3]`, `[3, 1]` $\rightarrow$ `[O, O]` (Invalid: consecutive odds)
* **Valid combinations:** `[1, 2, 1]`
* **Output:** `1`

### Example 2
* **Input:** `nums = [2, 4, 6]`, `target = 6`
* **Valid single-element combinations:** `[6]`
* **Output:** `1`

---

## Intuition & DP State

This problem is a variation of Unbounded Knapsack / Coin Change / Combination Sum IV. Because the choice of the next element depends on the parity of the previous element, we must track the last placed parity in our state.

### State Definition
* `dp[s][0]`: Number of valid sequences that sum to $s$ where the **last element is even**.
* `dp[s][1]`: Number of valid sequences that sum to $s$ where the **last element is odd**.

### Transitions
For a current sum $s$ and each number $x \in nums$ ($x \le s$):
* If $x$ is **even** ($x \pmod 2 == 0$):
  $$dp[s][0] = (dp[s][0] + dp[s - x][1]) \pmod{10^9 + 7}$$
  *(An even element can only be appended after an odd element)*
* If $x$ is **odd** ($x \pmod 2 == 1$):
  $$dp[s][1] = (dp[s][1] + dp[s - x][0]) \pmod{10^9 + 7}$$
  *(An odd element can only be appended after an even element)*

---

## 1. Top-Down Approach (Memoization)

```java
import java.util.Arrays;

public class SolutionMemo {
    private static final int MOD = 1_000_000_007;
    private static Integer[][] memo;

    public static int combinationSum4Alternating(int[] nums, int target) {
        // memo[remTarget][lastParity]
        // lastParity: 0 = even, 1 = odd, 2 = start/none
        memo = new Integer[target + 1][3];
        return solve(target, 2, nums);
    }

    private static int solve(int remTarget, int lastParity, int[] nums) {
        // Base case: exact sum reached
        if (remTarget == 0) {
            return lastParity != 2 ? 1 : 0;
        }

        if (memo[remTarget][lastParity] != null) {
            return memo[remTarget][lastParity];
        }

        long totalWays = 0;

        for (int x : nums) {
            if (x <= remTarget) {
                int curParity = x % 2; // 0 for even, 1 for odd

                // Allowed if it is the first element OR if parity alternates
                if (lastParity == 2 || curParity != lastParity) {
                    totalWays = (totalWays + solve(remTarget - x, curParity, nums)) % MOD;
                }
            }
        }

        return memo[remTarget][lastParity] = (int) totalWays;
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        int target = 4;
        System.out.println("Result: " + combinationSum4Alternating(nums, target)); // Output: 1
    }
}
```

---

## 2. Bottom-Up Approach (Tabulation)

```java
import java.util.Arrays;

public class SolutionTabulation {
    private static final int MOD = 1_000_000_007;

    public static int combinationSum4Alternating(int[] nums, int target) {
        if (target <= 0) return 0;

        // dp[sum][0] -> count of valid sequences ending with an even number
        // dp[sum][1] -> count of valid sequences ending with an odd number
        int[][] dp = new int[target + 1][2];

        // Base cases: single-element starting sequences
        for (int x : nums) {
            if (x <= target) {
                if (x % 2 == 0) {
                    dp[x][0] = (dp[x][0] + 1) % MOD;
                } else {
                    dp[x][1] = (dp[x][1] + 1) % MOD;
                }
            }
        }

        // Fill DP table
        for (int s = 1; s <= target; s++) {
            for (int x : nums) {
                if (s - x > 0) {
                    if (x % 2 == 0) {
                        dp[s][0] = (dp[s][0] + dp[s - x][1]) % MOD;
                    } else {
                        dp[s][1] = (dp[s][1] + dp[s - x][0]) % MOD;
                    }
                }
            }
        }

        return (dp[target][0] + dp[target][1]) % MOD;
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        int target = 4;
        System.out.println("Result: " + combinationSum4Alternating(nums, target)); // Output: 1
    }
}
```

---

## Complexity Analysis

| Metric | Complexity | Explanation |
|---|---|---|
| **Time Complexity** | $O(\text{target} \times |nums|)$ | Outer loop runs $\text{target}$ times, inner loop iterates through all elements of $nums$. |
| **Space Complexity** | $O(\text{target})$ | DP table / memo cache takes $3 \times (\text{target} + 1)$ entries. |

---

## Key Edge Cases & Considerations

1. **Single Element Sequences:**
   * A single number (e.g., `[4]` for target 4) has no adjacent elements, so it naturally satisfies the alternating parity rule.
2. **All Even or All Odd Arrays:**
   * If all numbers are of the same parity, sequences of length $\ge 2$ cannot alternate. Only single-element answers can contribute.
3. **Unreachable Targets:**
   * If $\text{target} < \min(nums)$, the algorithm returns `0`.
4. **Large Inputs / Modulo:**
   * Using `long` during intermediate summations and taking `% 1_000_000_007` avoids integer overflow.
