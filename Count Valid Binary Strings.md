# Count Valid Binary Strings

## Problem Description

Given an integer `N`, count the number of binary strings of length `N` that satisfy both conditions:

1. No two consecutive `1`s are allowed.
2. `(count of 0s - count of 1s)` must be a multiple of `3`.

Valid differences are:

```text
0, 3, -3, 6, -6, ...

Return the total number of valid binary strings.

If no valid string exists, return 0.

Example

For:

N = 3

Possible strings without consecutive 1s:

000
001
010
100
101

Check:

000 -> 3 - 0 = 3  -> Valid
001 -> 2 - 1 = 1  -> Invalid
010 -> 2 - 1 = 1  -> Invalid
100 -> 2 - 1 = 1  -> Invalid
101 -> 1 - 2 = -1 -> Invalid

Therefore:

Answer = 1
Test Cases
Test Case 1
Input:
N = 3

Output:
1

Valid string:

000

Difference:

3 - 0 = 3
Test Case 2
Input:
N = 2

Possible strings without "11":

00
01
10

Check:

00 -> 2 - 0 = 2 -> Invalid
01 -> 1 - 1 = 0 -> Valid
10 -> 1 - 1 = 0 -> Valid

Output:

2
Test Case 3
Input:
N = 4

Possible strings without "11":

0000
0001
0010
0100
0101
1000
1001
1010

Valid strings:

0101 -> 2 - 2 = 0
1001 -> 2 - 2 = 0
1010 -> 2 - 2 = 0

Output:

3
Approach

Use Dynamic Programming with Memoization.

We need to track three things:

1. Current index
2. Previous character
3. Difference modulo 3

Define:

dp[index][prev][rem]

Where:

index = current position

prev = previous character
       0 -> previous character was 0
       1 -> previous character was 1

rem = (count0 - count1) % 3

We only need the remainder because we only care whether the final difference is divisible by 3.

Transition
Choose 0

Adding 0 increases:

count0 - count1

by 1.

Therefore:

newRem = (rem + 1) % 3

We can always choose 0.

Choose 1

Adding 1 decreases:

count0 - count1

by 1.

Therefore:

newRem = (rem - 1 + 3) % 3

We can choose 1 only when:

prev == 0

This prevents the substring "11".

Base Case

When:

index == N

the complete string has been created.

If:

rem == 0

then:

count0 - count1

is a multiple of 3.

Therefore:

return rem == 0 ? 1 : 0;
Java Memoization Code
import java.util.*;

class Solution {

    Long[][][] dp;

    public long countValidStrings(int N) {
        dp = new Long[N + 1][2][3];
        return fun(0, 0, 0, N);
    }

    public long fun(int index, int prev, int rem, int N) {

        if (index == N) {
            return rem == 0 ? 1 : 0;
        }

        if (dp[index][prev][rem] != null) {
            return dp[index][prev][rem];
        }

        long ans = 0;

        // Choose 0
        int newRem = (rem + 1) % 3;

        ans += fun(index + 1, 0, newRem, N);

        // Choose 1 only if previous character is 0
        if (prev == 0) {

            newRem = (rem - 1 + 3) % 3;

            ans += fun(index + 1, 1, newRem, N);
        }

        return dp[index][prev][rem] = ans;
    }
}
Dry Run

For:

N = 2

Initial call:

fun(0, 0, 0, 2)

Here:

index = 0
prev = 0
rem = 0

We can choose 0:

newRem = (0 + 1) % 3
       = 1

State:

fun(1, 0, 1, 2)

From here:

Choose 0:

newRem = (1 + 1) % 3
       = 2

String = 00

Difference = 2
Invalid

Choose 1:

newRem = (1 - 1 + 3) % 3
       = 0

String = 01

Difference = 0
Valid

Now choose 1 from the initial state:

newRem = (0 - 1 + 3) % 3
       = 2

State:

fun(1, 1, 2, 2)

Since:

prev = 1

we cannot choose another 1.

So we must choose 0:

String = 10

Difference = 0
Valid

Therefore the valid strings are:

01
10

Answer:

2
Complexity

Number of states:

N × 2 × 3

Each state has at most two transitions.

Therefore:

Time Complexity  = O(N)
Space Complexity = O(N)
Why Memoization?

Without memoization, the recursion can calculate the same state many times.

For example:

fun(index, prev, rem)

may be reached through different strings.

We store the result in:

dp[index][prev][rem]

and reuse it.

Therefore, instead of exploring all:

2^N

strings, we only calculate:

N × 2 × 3

states.

Key Interview Insight

The important observation is that we don't need the exact value of:

count0 - count1

We only need:

(count0 - count1) % 3

because the final difference only needs to be checked for divisibility by 3.

Therefore, our state contains:

Current Index
+
Previous Character
+
Difference % 3

This makes the problem a Dynamic Programming + State Tracking + Modular Arithmetic problem.

Edge Cases
N = 1

Possible strings:

0
1

Differences:

0 -> 1
1 -> -1

Neither is a multiple of 3.

Answer:

0
N = 2

Valid strings:

01
10

Answer:

2
N = 3

Only:

000

satisfies both conditions.

Answer:

1
Related LeetCode Problems
1. Climbing Stairs

LeetCode 70:

https://leetcode.com/problems/climbing-stairs/

2. Knight Dialer

LeetCode 935:

https://leetcode.com/problems/knight-dialer/

3. Dice Roll Simulation

LeetCode 1223:

https://leetcode.com/problems/dice-roll-simulation/

4. House Robber

LeetCode 198:

https://leetcode.com/problems/house-robber/

The exact problem is custom, but it combines ideas from counting DP, state tracking, and modular arithmetic.

Key Concepts
Dynamic Programming
Memoization
Recursion
State Tracking
Modular Arithmetic
Binary Strings
Counting DP
Avoiding Consecutive Characters
Final Complexity
Time  = O(N)
Space = O(N)
