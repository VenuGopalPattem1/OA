# Count Strings of Length N

## Problem

Count the number of strings of length `N` using the characters

```
A
B
C
```

such that

- No two consecutive `A`s appear.
- The substring `CB` never appears.

Return the total number of valid strings.

---

## Example 1

### Input

```text
1
```

### Output

```text
3
```

Valid strings

```
A
B
C
```

---

## Example 2

### Input

```text
2
```

### Output

```text
7
```

Valid strings

```
AB
AC
BA
BB
BC
CA
CC
```

Invalid strings

```
AA
CB
```
## Java Code (Memoization)

```java
import java.util.*;

class Solution {

    long[][] dp;

    public long countStrings(int n) {

        dp = new long[n + 1][4];

        for (long[] row : dp)
            Arrays.fill(row, -1);

        return solve(0, 3, n);
    }

    long solve(int pos, int last, int n) {

        if (pos == n)
            return 1;

        if (dp[pos][last] != -1)
            return dp[pos][last];

        long ans = 0;

        // Place A
        if (last != 0)
            ans += solve(pos + 1, 0, n);

        // Place B
        if (last != 2)
            ans += solve(pos + 1, 1, n);

        // Place C
        ans += solve(pos + 1, 2, n);

        return dp[pos][last] = ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        Solution obj = new Solution();

        System.out.println(obj.countStrings(n));
    }
}
```
---

## DP State

```
dp[pos][last]
```

where

- `pos` = current position
- `last` = previous character

---

## Transition

Choose

### A

Allowed if previous character is not `A`.

### B

Allowed if previous character is not `C`.

### C

Always allowed.

---

## Complexity

Time

```
O(N)
```

Space

```
O(N)
```

---

## Similar Problems

- LeetCode 552 – Student Attendance Record II
- LeetCode 91 – Decode Ways (similar state-based DP)
- GeeksforGeeks – Count Strings with Given Constraints

---

## Concepts

- Dynamic Programming
- Memoization
- State Machine DP
- String DP
