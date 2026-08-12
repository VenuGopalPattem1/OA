# Q2 — Minimum Cost to Reach the Nth Step

## Problem Description

You are standing at step `0` and want to reach exactly step `N`.

You are given three integers:

```text
A = cost of taking a 1-step jump
B = cost of taking a 2-step jump
C = cost of taking a 3-step jump
```

At every step, you can jump:

```text
1 step
2 steps
3 steps
```

There is one restriction:

> If you take a `2-step` or `3-step` jump, then your **next jump must be a 1-step jump**.

After taking the mandatory `1-step` jump, you can again choose `1`, `2`, or `3`.

Find the **minimum total cost** required to reach exactly step `N`.

---

## Understanding the Restriction

If you take a `2-step` jump:

```text
2 → 1
```

is valid.

But:

```text
2 → 2
2 → 3
```

are invalid.

Similarly, after a `3-step` jump:

```text
3 → 1
```

is valid, but:

```text
3 → 2
3 → 3
```

are invalid.

After the mandatory `1-step` jump, all jump sizes are available again.

---

## Test Case 1

```text
Input:
N = 4
A = 2
B = 3
C = 7

Output:
7
```

Possible paths:

```text
1 + 1 + 1 + 1
Cost = 2 + 2 + 2 + 2 = 8
```

```text
2 + 1 + 1
Cost = 3 + 2 + 2 = 7
```

```text
3 + 1
Cost = 7 + 2 = 9
```

Therefore:

```text
Answer = 7
```

---

## Test Case 2

```text
Input:
N = 5
A = 2
B = 5
C = 6

Output:
10
```

One optimal path is:

```text
1 + 1 + 3
```

Cost:

```text
2 + 2 + 6 = 10
```

The `3-step` jump is followed by no jump because we have reached exactly `N`, so the restriction does not cause a problem.

Therefore:

```text
Answer = 10
```

---

## Test Case 3

```text
Input:
N = 6
A = 3
B = 4
C = 5

Output:
12
```

A valid minimum-cost path is:

```text
2 + 1 + 3
```

Costs:

```text
2-step = 4
1-step = 3
3-step = 5
```

Total:

```text
4 + 3 + 5 = 12
```

The first `2-step` is followed by the mandatory `1-step`, after which another `3-step` is allowed.

Therefore:

```text
Answer = 12
```

---

# DP Idea

We need to remember whether the next jump is restricted.

Define:

```text
dp[i][0]
```

as:

> Minimum cost to reach step `i` when we can choose a `1`, `2`, or `3` step jump next.

And:

```text
dp[i][1]
```

as:

> Minimum cost to reach step `i` when the next jump must be `1`.

Why do we need this state?

Because knowing only the current step is not enough.

For example, if we are at step `4`:

```text
4, state 0
```

we can choose:

```text
1, 2, or 3
```

but:

```text
4, state 1
```

allows only:

```text
1
```

---

# State Transitions

### State 0

The next jump can be `1`, `2`, or `3`.

If we take a `1-step` jump:

```text
i → i + 1
```

cost:

```text
+A
```

and the next state is:

```text
0
```

because after a `1-step` jump there is no restriction.

If we take a `2-step` jump:

```text
i → i + 2
```

cost:

```text
+B
```

and the next state is:

```text
1
```

because the next jump must be `1`.

If we take a `3-step` jump:

```text
i → i + 3
```

cost:

```text
+C
```

and the next state is:

```text
1
```

---

### State 1

The previous jump was `2` or `3`, so the next jump must be `1`.

Therefore:

```text
i → i + 1
```

with cost:

```text
+A
```

and the new state becomes:

```text
0
```

---

# Memoization Solution

```java
import java.util.*;

class Solution {

    int[][] dp;

    int N;
    int A;
    int B;
    int C;

    int INF = Integer.MAX_VALUE / 2;

    public int minCost(int N, int A, int B, int C) {

        this.N = N;
        this.A = A;
        this.B = B;
        this.C = C;

        /*
            dp[i][0] -> at step i, next jump can be 1, 2, or 3

            dp[i][1] -> at step i, next jump MUST be 1
        */
        dp = new int[N + 1][2];

        for (int i = 0; i <= N; i++) {
            Arrays.fill(dp[i], -1);
        }

        return fun(0, 0);
    }

    public int fun(int i, int state) {

        // Reached exactly N
        if (i == N) {
            return 0;
        }

        // Cannot go beyond N
        if (i > N) {
            return INF;
        }

        if (dp[i][state] != -1) {
            return dp[i][state];
        }

        int ans = INF;

        // Previous jump was 2 or 3,
        // so next jump MUST be 1.
        if (state == 1) {

            if (i + 1 <= N) {

                ans = A + fun(i + 1, 0);
            }

        } else {

            // State 0:
            // We can choose 1, 2, or 3.

            // Take 1 step
            if (i + 1 <= N) {

                ans = Math.min(
                    ans,
                    A + fun(i + 1, 0)
                );
            }

            // Take 2 steps
            if (i + 2 <= N) {

                ans = Math.min(
                    ans,
                    B + fun(i + 2, 1)
                );
            }

            // Take 3 steps
            if (i + 3 <= N) {

                ans = Math.min(
                    ans,
                    C + fun(i + 3, 1)
                );
            }
        }

        return dp[i][state] = ans;
    }
}
```

---

# Dry Run

For:

```text
N = 5
A = 2
B = 5
C = 6
```

We start with:

```text
fun(0, 0)
```

At step `0`, state `0` means we can choose anything.

Possible choices:

```text
1-step → fun(1, 0) + 2
2-step → fun(2, 1) + 5
3-step → fun(3, 1) + 6
```

Suppose we choose `2-step`:

```text
0 → 2
```

Now we are at:

```text
fun(2, 1)
```

State `1` means the next jump MUST be `1`.

So we can only do:

```text
2 → 3
```

with cost `2`.

After that:

```text
fun(3, 0)
```

Now the restriction is removed and we can choose `1`, `2`, or `3` again.

This is exactly how the DP enforces the rule.

---

# Important DP Pattern

The important idea is:

```text
dp[position][state]
```

where the state remembers whether the next jump is restricted.

The transitions are:

```text
State 0:

1-step → State 0
2-step → State 1
3-step → State 1


State 1:

1-step → State 0
```

So we never accidentally allow:

```text
2 → 2
2 → 3
3 → 2
3 → 3
```

---

# Base Cases

When:

```text
i == N
```

we have reached exactly the required step, so:

```java
return 0;
```

No additional cost is required.

When:

```text
i > N
```

we have gone beyond the target, so that path is invalid:

```java
return INF;
```

---

# Complexity

There are only:

```text
N × 2
```

DP states.

Each state has at most 3 transitions.

Therefore:

```text
Time Complexity  = O(N)
Space Complexity = O(N)
```

The recursion stack also requires `O(N)` space.

---

# Similar Practice Problems

### 1. LeetCode 746 — Min Cost Climbing Stairs

https://leetcode.com/problems/min-cost-climbing-stairs/

### 2. GeeksforGeeks — Minimum Cost to Reach the Top

https://www.geeksforgeeks.org/dsa/minimum-cost-to-reach-the-top-of-the-floor-by-climbing-stairs/

### 3. GeeksforGeeks — Count Ways to Reach Nth Stair

https://www.geeksforgeeks.org/dsa/count-ways-reach-nth-stair/

### 4. GeeksforGeeks — Minimal Cost / K-Step Jump

https://www.geeksforgeeks.org/problems/minimal-cost/1

---

# Quick Revision

```text
Allowed jumps:
1, 2, 3

Costs:
1-step → A
2-step → B
3-step → C

Restriction:
After 2 or 3 → next MUST be 1

DP:
dp[i][0] → any jump allowed
dp[i][1] → only 1-step allowed

Transitions:

state 0:
1 → 0
2 → 1
3 → 1

state 1:
1 → 0

Base:
i == N → 0
i > N  → INF

Time:
O(N)

Space:
O(N)
```
