# Count Strings with No "12" Substring

## Problem Description

Given three integers `N`, `M`, and `R`, construct strings of length exactly `N` using only the digits `{1, 2, 3}`.

A string is valid if:
1. It does not contain `"12"` as a substring.
2. Interpreted as a base-10 integer, it leaves remainder `R` when divided by `M`.

## Constraints

```text
1 <= N <= 10^18
1 <= M <= 20
0 <= R <= M - 1
```

## Test Case 1

```text
Input:
N = 2
M = 3
R = 1

Output:
3
```

Valid strings:

```text
13
22
31
```

## Test Case 2

```text
Input:
N = 3
M = 5
R = 2

Output:
5
```

## Memoization Approach

The DP state is:

```text
dp[pos][prev][rem]
```

Where:

- `pos` = current position
- `prev` = previous digit (`0`, `1`, `2`, or `3`)
- `rem` = current number modulo `M`

`prev = 0` means there is no previous digit.

### Transition

Try digits `1`, `2`, and `3`.

If the previous digit is `1`, we cannot choose `2` because that creates `"12"`.

The new remainder is:

```java
newRem = (rem * 10 + d) % M;
```

The new previous digit is simply:

```java
newPrev = d;
```

## Java Memoization Code

```java
class Solution {

    static Integer[][][] dp;
    static int n, M, R;

    public static int countStrings(int N, int mod, int r) {

        n = N;
        M = mod;
        R = r;

        dp = new Integer[n][4][M];

        return fun(0, 0, 0);
    }

    public static int fun(int pos, int prev, int rem) {

        if (pos == n) {
            return rem == R ? 1 : 0;
        }

        if (dp[pos][prev][rem] != null) {
            return dp[pos][prev][rem];
        }

        int ans = 0;

        for (int d = 1; d <= 3; d++) {

            // Cannot form "12"
            if (prev == 1 && d == 2) {
                continue;
            }

            int newRem = (rem * 10 + d) % M;

            int newPrev = d;

            ans += fun(pos + 1, newPrev, newRem);
        }

        return dp[pos][prev][rem] = ans;
    }

    public static void main(String[] args) {

        System.out.println(countStrings(2, 3, 1)); // 3
        System.out.println(countStrings(3, 5, 2)); // 5
    }
}
```

## Important Observation

If:

```text
prev = 1
```

and we choose:

```text
d = 2
```

then we create:

```text
12
```

which is invalid.

Therefore:

```java
if (prev == 1 && d == 2)
    continue;
```

## Remainder Formula

When appending digit `d`:

```text
new number = old number * 10 + d
```

Therefore:

```java
newRem = (rem * 10 + d) % M;
```

We never need to construct the complete number.

## Complexity

```text
Time  = O(N * M)
Space = O(N * M)
```

This memoization version is useful for learning the recurrence, but because `N` can be `10^18`, the actual maximum-constraint solution needs matrix exponentiation.

## Related Problem

### LeetCode 1397 — Find All Good Strings

A closely related DP problem involving counting strings while avoiding a forbidden substring.

[LeetCode 1397](https://leetcode.com/problems/find-all-good-strings/)
