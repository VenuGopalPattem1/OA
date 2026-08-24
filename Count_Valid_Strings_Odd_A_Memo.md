# Count Valid Strings with Odd Number of `a` — Memoization

## Problem

Given the alphabet `{a, b, c, d}` and an integer `N`, count valid strings of length `N`.

Forbidden adjacent pairs:

```text
ab, ba, bc, cb, cd, dc
```

The total number of `a` characters must be odd.

Return the answer modulo `1_000_000_007`.

## Examples

### Test Case 1
```text
Input:
1

Output:
1
```

### Test Case 2
```text
Input:
2

Output:
4
```

Valid strings:
```text
ac
ad
ca
da
```

### Test Case 3
```text
Input:
3

Output:
11
```

### Test Case 4
```text
Input:
4

Output:
29
```

## DP State

We use:

```text
fun(i, last, parity)
```

- `i` = current position
- `last` = previous character (`a=0, b=1, c=2, d=3`)
- `parity = 0` → even number of `a`s
- `parity = 1` → odd number of `a`s

When we add `a`, parity changes:

```java
newParity = parity ^ 1;
```

When we add `b`, `c`, or `d`, parity stays the same.

Two consecutive characters are invalid when:

```java
Math.abs(last - ch) == 1
```

## Java Memoization Code

```java
import java.util.*;

class Solution {

    static final long MOD = 1_000_000_007L;

    Long[][][] dp;

    public int countStrings(int n) {

        dp = new Long[n][4][2];

        long ans = 0;

        // Choose the first character
        for (int ch = 0; ch < 4; ch++) {

            // a = 0 -> odd number of a's
            // b,c,d -> even number of a's
            int parity = (ch == 0) ? 1 : 0;

            ans = (ans + fun(1, ch, parity, n)) % MOD;
        }

        return (int) ans;
    }

    // i      = current position
    // last   = previous character
    // parity = 0 -> even number of a's
    //          1 -> odd number of a's
    public long fun(int i, int last, int parity, int n) {

        // String completed
        if (i == n) {
            return parity == 1 ? 1 : 0;
        }

        // Already calculated
        if (dp[i][last][parity] != null) {
            return dp[i][last][parity];
        }

        long ans = 0;

        // Try every next character
        for (int ch = 0; ch < 4; ch++) {

            // Forbidden pairs:
            // ab, ba, bc, cb, cd, dc
            if (Math.abs(last - ch) == 1) {
                continue;
            }

            int newParity = parity;

            // ch == 0 means 'a'
            if (ch == 0) {
                newParity ^= 1;
            }

            ans = (ans + fun(
                    i + 1,
                    ch,
                    newParity,
                    n
            )) % MOD;
        }

        return dp[i][last][parity] = ans;
    }
}
```

## Why Parity Is Needed

The problem only cares whether the number of `a`s is odd.

```text
0 = even
1 = odd
```

For example:

```text
"a"   -> 1 a -> odd  -> parity 1
"aa"  -> 2 a -> even -> parity 0
"aaa" -> 3 a -> odd  -> parity 1
```

Therefore, at the end:

```java
if (parity == 1)
```

the string is counted.

## Complexity

There are `N * 4 * 2` states and 4 transitions per state.

```text
Time  = O(N)
Space = O(N)
```

The key pattern is:

```text
String DP
    +
Previous Character
    +
Parity (odd/even)
    +
Memoization
```
