# Sorted Vowel String Count

## Problem

Given a length `n`, count the number of strings that:

- Use only the vowels `a, e, i, o, u`.
- Are in non-decreasing order.
- The frequency of `a`, `i`, and `u` must be **odd or zero**.
- The frequency of `e` and `o` must be **even**.
- A vowel can have frequency `0`.

### Examples

```text
n = 1 → 3
a, i, u

n = 2 → 5
ai, au, ee, iu, oo

n = 3 → 10
aaa, iii, uuu, aiu, aee,
eei, eeu, aoo, ioo, oou
```

## Backtracking + Memoization Code

```java
import java.util.*;

class Java {

    static HashMap<String, Integer> dp;

    public static int countStrings(int n) {

        dp = new HashMap<>();

        int[] cnt = new int[5];

        return fun(0, n, 0, cnt);
    }

    public static int fun(int pos, int n, int last, int[] cnt) {

        // String completed
        if (pos == n) {

            if ((cnt[0] == 0 || cnt[0] % 2 == 1) &&
                cnt[1] % 2 == 0 &&
                (cnt[2] == 0 || cnt[2] % 2 == 1) &&
                cnt[3] % 2 == 0 &&
                (cnt[4] == 0 || cnt[4] % 2 == 1)) {

                return 1;
            }

            return 0;
        }

        String key = pos + "," + last + "," +
                     cnt[0] + "," + cnt[1] + "," +
                     cnt[2] + "," + cnt[3] + "," + cnt[4];

        if (dp.containsKey(key)) {
            return dp.get(key);
        }

        int ans = 0;

        // Choose next vowel
        for (int i = last; i < 5; i++) {

            // choose
            cnt[i]++;

            ans += fun(pos + 1, n, i, cnt);

            // undo
            cnt[i]--;
        }

        dp.put(key, ans);

        return ans;
    }

    public static void main(String[] args) {

        System.out.println(countStrings(1)); // 3
        System.out.println(countStrings(2)); // 5
        System.out.println(countStrings(3)); // 10
    }
}
```

## Important Backtracking Pattern

The main pattern is:

```java
cnt[i]++;       // choose

fun(...);       // explore

cnt[i]--;       // undo
```

This means:

```text
Choose
  ↓
Explore all possibilities
  ↓
Undo the choice
  ↓
Try the next choice
```

## Why `cnt[i]++` and `cnt[i]--`?

Suppose we choose `a`.

Before choosing:

```text
cnt = [0, 0, 0, 0, 0]
```

After:

```java
cnt[0]++;
```

we have:

```text
cnt = [1, 0, 0, 0, 0]
```

After exploring all strings that start with this choice, we undo it:

```java
cnt[0]--;
```

and return to:

```text
cnt = [0, 0, 0, 0, 0]
```

This lets us try another vowel without carrying the previous choice into the next branch.

## Why `i = last`?

The loop is:

```java
for (int i = last; i < 5; i++)
```

This guarantees that the string remains sorted.

The vowel order is:

```text
a → e → i → o → u
```

If we have already selected `i`, we cannot select `a` or `e` afterward.

For example:

```text
aiu   ✓
aee   ✓
eeu   ✓
ioo   ✓
```

But:

```text
eai   ✗
uia   ✗
oie   ✗
```

are never generated.

## Frequency Conditions

The five counters are:

```text
cnt[0] → a
cnt[1] → e
cnt[2] → i
cnt[3] → o
cnt[4] → u
```

Valid conditions:

```text
a → 0 or odd
e → even
i → 0 or odd
o → even
u → 0 or odd
```

For example:

```text
cnt = [1, 2, 0, 0, 0]
```

represents:

```text
aee
```

and is valid.

Another:

```text
cnt = [1, 0, 1, 0, 1]
```

represents:

```text
aiu
```

and is valid.

## Key Observation

Because the string must be sorted, once the frequencies are known, the string is uniquely determined.

For example:

```text
a = 1
e = 2
i = 1
o = 0
u = 1
```

produces exactly:

```text
aeeiu
```

So we do not need to consider permutations of the same frequencies.

## Related Problem

**LeetCode 1641 — Count Sorted Vowel Strings**

https://leetcode.com/problems/count-sorted-vowel-strings/


# Related Practice Problems

Focus on:

- Backtracking
- Non-decreasing strings
- Frequency / count DP
- Parity constraints

## Closest Practice Problems

### 1. LeetCode 1079 — Letter Tile Possibilities

Very relevant for understanding how frequency/counts determine the number of strings.

[LeetCode 1079](https://leetcode.com/problems/letter-tile-possibilities/)

### 2. LeetCode 1641 — Count Sorted Vowel Strings ⭐

This is **extremely close** to your problem because it asks for non-decreasing strings made from vowels.

[LeetCode 1641](https://leetcode.com/problems/count-sorted-vowel-strings/)

### 3. LeetCode 22 — Generate Parentheses

Good for learning the `choose → recurse → undo` backtracking pattern.

[LeetCode 22](https://leetcode.com/problems/generate-parentheses/)

### 4. LeetCode 78 — Subsets

Basic backtracking practice.

[LeetCode 78](https://leetcode.com/problems/subsets/)

### 5. LeetCode 90 — Subsets II

Useful for understanding duplicates and frequency-based choices.

[LeetCode 90](https://leetcode.com/problems/subsets-ii/)

### 6. LeetCode 47 — Permutations II

Good practice for handling frequencies instead of blindly generating permutations.

[LeetCode 47](https://leetcode.com/problems/permutations-ii/)
