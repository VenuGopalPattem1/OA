# Q11. Quantum Plasma Resonance

## Source

`SP-Off Campus-Test-02-Aug-26-10 AM`

---

# 1. Problem Description

You are configuring a linear sequence of exactly `N` plasma nodes.

Each node must be assigned an intensity from `1` to `F`.

So the sequence is:

```text
I[1], I[2], ..., I[N]
```

Normally, total power is:

```text
I[1] + I[2] + ... + I[N]
```

Exactly one Fusion Coil must be installed between two adjacent nodes.

The two nodes directly next to the coil are multiplied instead of added. The other `N - 2` nodes are added normally.

The final power must be exactly `T`.

Return the total number of valid configurations modulo:

```text
1,000,000,007
```

A configuration is different if either the intensity sequence is different or the Fusion Coil position is different.

---

# 2. Fusion Coil Example

For:

```text
N = 4
```

and:

```text
[2, 3, 4, 1]
```

if the coil is between node 2 and node 3:

```text
2 + (3 * 4) + 1 = 15
```

There are `N - 1` possible coil positions.

For four nodes:

```text
(1 * 2) + 3 + 4
1 + (2 * 3) + 4
1 + 2 + (3 * 4)
```

---

# 3. Important Understanding

This is **not a subset problem**.

If:

```text
F = 3
```

every node independently chooses one value:

```text
1, 2, or 3
```

Repetition is allowed.

For example:

```text
[2, 1, 3, 2]
[2, 2, 2, 2]
[1, 3, 1, 3]
```

are all possible sequences.

Only **one adjacent pair** is multiplied.

For:

```text
[a, b, c, d, e]
```

with the coil between `c` and `d`:

```text
a + b + (c * d) + e
```

---

# 4. Mathematical Form

If the coil is between positions `i` and `i + 1`:

```text
I[1] + ... + I[i-1]
+ (I[i] * I[i+1])
+ I[i+2] + ... + I[N]
= T
```

Every intensity satisfies:

```text
1 <= I[j] <= F
```

---

# 5. Key Observation

For one fixed coil position:

```text
a * b + sum of remaining N-2 values = T
```

So:

1. Choose `a` and `b`.
2. Calculate `a * b`.
3. The remaining `N - 2` nodes must have sum `T - a*b`.
4. Count those ordered sequences using DP.
5. There are `N - 1` possible coil positions.

Therefore:

```text
answer = waysForOnePosition * (N - 1)
```

---

# 6. Memoization State

For the normal nodes use:

```text
solve(pos, remainingSum)
```

Meaning:

> Number of ways to fill the remaining normal positions so that their total is exactly `remainingSum`.

At each position, choose:

```text
1, 2, ..., F
```

Transition:

```text
solve(pos, sum)
=
solve(pos+1, sum-1)
+ solve(pos+1, sum-2)
+ ...
+ solve(pos+1, sum-F)
```

Base case:

```java
if (remainingNodes == 0)
    return sum == 0 ? 1 : 0;
```

---

# 7. Simple Memoization Solution

```java
class Solution {

    static final int MOD = 1_000_000_007;

    int n, f;
    Integer[][] dp;

    public int countConfigurations(int N, int F, int T) {

        n = N;
        f = F;

        long ways = 0;

        // Choose the two nodes multiplied by the coil
        for (int a = 1; a <= f; a++) {

            for (int b = 1; b <= f; b++) {

                int product = a * b;

                if (product > T) {
                    continue;
                }

                int remaining = T - product;

                // dp[pos][sum]
                dp = new Integer[n - 1][T + 1];

                ways += solve(0, n - 2, remaining);
                ways %= MOD;
            }
        }

        // N - 1 possible positions for the coil
        return (int) (ways * (n - 1) % MOD);
    }

    int solve(int pos, int remainingNodes, int sum) {

        // All normal nodes are filled
        if (remainingNodes == 0) {
            return sum == 0 ? 1 : 0;
        }

        if (sum < 0) {
            return 0;
        }

        if (dp[pos][sum] != null) {
            return dp[pos][sum];
        }

        long ans = 0;

        // Pick an intensity for this node
        for (int x = 1; x <= f; x++) {

            if (x > sum) {
                break;
            }

            ans += solve(
                pos + 1,
                remainingNodes - 1,
                sum - x
            );

            ans %= MOD;
        }

        return dp[pos][sum] = (int) ans;
    }
}
```

---

# 8. How the Recursion Works

Suppose:

```text
N = 4
F = 3
```

There are:

```text
N - 2 = 2
```

normal nodes.

Suppose the multiplied pair gives:

```text
a * b = 3
```

and:

```text
T = 7
```

Then the remaining two nodes must sum to:

```text
7 - 3 = 4
```

So we calculate:

```text
solve(0, 2, 4)
```

The first normal node can be `1`, `2`, or `3`.

If we choose `1`, the next node must make sum `3`:

```text
[1, 3]
```

If we choose `2`:

```text
[2, 2]
```

If we choose `3`:

```text
[3, 1]
```

So there are:

```text
3 ways
```

---

# 9. Test Case 1

## Input

```text
N = 3
F = 2
T = 4
```

Each node can be `1` or `2`.

There are:

```text
N - 1 = 2
```

possible coil positions.

### Coil between node 1 and node 2

Equation:

```text
I1 * I2 + I3 = 4
```

Try the four pairs:

```text
1 * 1 + I3 = 4  -> I3 = 3 -> invalid
1 * 2 + I3 = 4  -> I3 = 2 -> valid
2 * 1 + I3 = 4  -> I3 = 2 -> valid
2 * 2 + I3 = 4  -> I3 = 0 -> invalid
```

So:

```text
2 ways
```

### Coil between node 2 and node 3

Equation:

```text
I1 + I2 * I3 = 4
```

Valid sequences:

```text
[2, 1, 2]
[2, 2, 1]
```

Again:

```text
2 ways
```

### Output

```text
2 + 2 = 4
```

```text
Output: 4
```

---

# 10. Test Case 2

## Input

```text
N = 4
F = 2
T = 5
```

There are:

```text
N - 1 = 3
```

possible coil positions.

For one fixed position:

```text
a * b + x + y = 5
```

### Product = 1

Only:

```text
a = 1, b = 1
```

Then:

```text
x + y = 4
```

Only:

```text
(2, 2)
```

works.

So:

```text
1 way
```

### Product = 2

This happens with:

```text
(1, 2)
(2, 1)
```

Then:

```text
x + y = 3
```

Possible:

```text
(1, 2)
(2, 1)
```

Therefore:

```text
2 * 2 = 4 ways
```

### Product = 4

Only:

```text
(2, 2)
```

Then:

```text
x + y = 1
```

Impossible because the minimum is `2`.

So:

```text
0 ways
```

For one coil position:

```text
1 + 4 + 0 = 5
```

There are `3` coil positions:

```text
5 * 3 = 15
```

```text
Output: 15
```

---

# 11. Why Multiply by `N - 1`?

For `N` nodes there are exactly `N - 1` gaps:

```text
1 | 2 | 3 | ... | N
```

The coil can be placed in any gap:

```text
between 1 and 2
between 2 and 3
...
between N-1 and N
```

So:

```text
number of coil positions = N - 1
```

For this problem, every fixed position has the same number of intensity assignments because every node has the same allowed range `1..F`.

Therefore:

```text
final answer = ways for one position * (N - 1)
```

---

# 12. Why This Is Not Pick / Don't Pick

A normal subset DP has:

```text
pick
don't pick
```

Q11 is different.

Every node must receive an intensity.

If `F = 3`, each node has:

```text
3 choices:
1
2
3
```

So the recursion looks like:

```text
Node
├── choose 1
├── choose 2
└── choose 3
```

not:

```text
Element
├── Pick
└── Don't Pick
```

The natural state is:

```text
solve(pos, remainingSum)
```

---

# 13. Similar Problems

## LeetCode 1155 — Number of Dice Rolls With Target Sum

This is the closest practice problem.

Each die chooses a value from `1..k`, and we count ordered ways to reach an exact target sum. The DP state is based on position/dice count and sum.

Official problem:
https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/

LeetCode describes it as counting ways for `n` dice with faces `1..k` to reach a target, modulo `1e9+7`. citeturn0search0

## LeetCode 377 — Combination Sum IV

Useful for understanding ordered sequence counting. Different orders are counted separately.

Official problem:
https://leetcode.com/problems/combination-sum-iv/

For example, with `[1,2,3]` and target `4`, sequences such as `(1,2,1)` and `(2,1,1)` are counted separately. citeturn0search4

---

# 14. Recommended Practice

```text
LeetCode 1155
      ↓
solve(position, sum)
      ↓
LeetCode 377
      ↓
ordered counting
      ↓
Q11
      ↓
add multiplied pair
      ↓
multiply by N - 1
```

---

# 15. Complexity

For the provided memo solution:

Number of DP states is approximately:

```text
O(N * T)
```

Each state tries up to `F` intensities.

Therefore:

```text
Time = O(N * T * F + F^2)
Space = O(N * T)
```

The `F^2` term comes from trying every possible multiplied pair `(a,b)`.

---

# 16. Important Note

The supplied problem statement does not include the constraints for `N`, `F`, and `T`.

The above solution is the **simple memoization version** requested and is useful for understanding the DP.

If `N`, `F`, or `T` is very large, this can be optimized further.

---

# 17. Final Mental Model

```text
N positions
    ↓
Each position chooses 1..F
    ↓
Choose one adjacent pair
    ↓
Multiply that pair
    ↓
Add the remaining N-2 values
    ↓
Total must equal T
    ↓
Count configurations
    ↓
Account for N-1 possible coil positions
```

The key equation is:

```text
a * b + sum(remaining N-2 values) = T
```

and the key DP is:

```text
solve(pos, remainingSum)
```

---

# 18. One-Line Summary

> Choose an intensity from `1..F` for every node, choose one adjacent pair to multiply, add the remaining `N-2` intensities, count ordered configurations whose total is exactly `T`, and account for all `N-1` possible Fusion Coil positions.
