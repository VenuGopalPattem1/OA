# Digit String Decoding with Even Two-Digit Rule

## Problem Statement

You are given a string `S` consisting only of digits (`0-9`).

Find the number of different ways to decode the entire string.

The decoding rules are:

1. A single digit from **1 to 9** can be decoded individually.
2. The digit **0 cannot be decoded alone**.
3. Two consecutive digits can be decoded together **only if**:
   - The number formed is between **10 and 26** (inclusive).
   - The number is **even**.

Return the total number of valid decodings.

---

## Input Format

- First line contains a string `S`.

---

## Output Format

Print the number of different valid decodings.

---

## Constraints

```text
1 ≤ |S| ≤ 10^5
S consists only of digits ('0'–'9')
```

---

# Example 1

## Input

```text
12
```

## Output

```text
2
```

## Explanation

Possible decodings

```text
1 | 2
```

```text
12
```

`12` is between `10` and `26` and is **even**.

Hence both are valid.

Answer = **2**

---

# Example 2

## Input

```text
1234
```

## Output

```text
2
```

## Explanation

Valid

```text
1 | 2 | 3 | 4
```

```text
12 | 3 | 4
```

Invalid

```text
1 | 23 | 4
```

because `23` is **odd**.

Invalid

```text
1 | 2 | 34
```

because `34 > 26`.

Answer = **2**

---

# Greedy / DP Observation

This is almost identical to **LeetCode 91 – Decode Ways**.

The only modification is:

A two-digit number is valid only if

```text
10 ≤ value ≤ 26
```

and

```text
value is even
```

---

# DP State

Let

```
dp[i]
```

be the number of ways to decode the substring starting from index `i`.

---

## Transition

### Take one digit

Possible only if

```
s[i] != '0'
```

```
dp[i] += dp[i+1]
```

---

### Take two digits

Possible only if

```
10 ≤ value ≤ 26
```

and

```
value % 2 == 0
```

Then

```
dp[i] += dp[i+2]
```

---

# Memoization Solution

```java
import java.util.*;

class Solution {

    public int numDecodings(String s) {

        int n = s.length();

        int[] dp = new int[n];

        Arrays.fill(dp, -1);

        return solve(s, 0, dp);
    }

    private int solve(String s, int i, int[] dp) {

        if (i == s.length())
            return 1;

        if (s.charAt(i) == '0')
            return 0;

        if (dp[i] != -1)
            return dp[i];

        int ans = solve(s, i + 1, dp);

        if (i < s.length() - 1) {

            int value =
                    (s.charAt(i) - '0') * 10 +
                    (s.charAt(i + 1) - '0');

            if (value >= 10 &&
                value <= 26 &&
                value % 2 == 0) {

                ans += solve(s, i + 2, dp);
            }
        }

        return dp[i] = ans;
    }
}
```

---

# Dry Run

Input

```text
226
```

At index 0

```
2
```

Take one digit

```
2 | 26
```

Take two digits

```
22 | 6
```

Both

```
22
```

and

```
26
```

are

- between 10 and 26
- even

Valid decodings

```text
2 | 2 | 6
22 | 6
2 | 26
```

Answer

```text
3
```

---

# Complexity

### Time

```
O(N)
```

Each index is computed only once.

### Space

```
O(N)
```

Memoization array + recursion stack.

---

# Related Problems

## 1. LeetCode 91 – Decode Ways ⭐⭐⭐⭐⭐

**Link**

https://leetcode.com/problems/decode-ways/

### Difference

**LeetCode 91**

Two-digit numbers are valid if

```
10 ≤ value ≤ 26
```

Examples

```
10
11
12
...
26
```

---

**This OA Problem**

Two-digit numbers are valid only if

```
10 ≤ value ≤ 26
```

and

```
value is even
```

Valid

```
10
12
14
16
18
20
22
24
26
```

Invalid

```
11
13
15
17
19
21
23
25
```

Everything else is exactly the same as LeetCode 91.

---

## 2. LeetCode 639 – Decode Ways II

**Link**

https://leetcode.com/problems/decode-ways-ii/

A harder version where the string may also contain the wildcard character `'*'`.

---

# Key Observation

This is simply **Decode Ways** with one extra condition:

Instead of checking

```java
10 <= value && value <= 26
```

check

```java
10 <= value &&
value <= 26 &&
value % 2 == 0
```

No other part of the DP changes.

---

# Revision Notes

✅ `0` cannot be decoded alone.

✅ One digit (`1–9`) is always valid.

✅ Two digits are valid only if

```
10 ≤ value ≤ 26
```

and

```
value is even
```

DP

```
dp[i]
```

= number of ways to decode the suffix starting at index `i`.

**Time Complexity**

```
O(N)
```

**Space Complexity**

```
O(N)
```
