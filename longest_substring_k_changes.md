# Longest Substring with Only One Repeated Character After K Changes

## Problem Description

You are given a string `s` and an integer `k`.

You can change at most `k` characters in the string.

Your goal is to find the **length of the longest continuous substring** that can be converted into a substring containing only one repeated character.

### Example 1

```text
s = "AABABBA"
k = 1
```

Consider the substring:

```text
A A B A
```

There are 3 `A`s and 1 `B`.

We can change the `B` to `A`:

```text
A A A A
```

Only 1 change is required, so the substring length is `4`.

Therefore, the answer is:

```text
4
```

### Example 2

```text
s = "ABBB"
k = 1
```

We can change:

```text
A -> B
```

giving:

```text
BBBB
```

Only 1 change is needed.

Answer:

```text
4
```

## Approach: Sliding Window

For every window `[left...right]`, count the frequency of each character.

Let:

```text
maxFreq = frequency of the most frequent character in the window
```

If the window has length:

```text
windowLength = right - left + 1
```

then the number of characters we need to change is:

```text
changes = windowLength - maxFreq
```

Why?

Suppose the window is:

```text
A A B A B
```

Frequency:

```text
A = 3
B = 2
```

If we decide to make everything `A`, we keep the 3 `A`s and change the 2 `B`s.

So:

```text
changes = 5 - 3 = 2
```

The window is valid if:

```text
changes <= k
```

If:

```text
windowLength - maxFreq > k
```

the window is invalid, so we move `left` forward until it becomes valid again.

## Java Code

```java
class Solution {
    public int characterReplacement(String s, int k) {

        int[] freq = new int[26];

        int left = 0;
        int maxFreq = 0;
        int ans = 0;

        for (int right = 0; right < s.length(); right++) {

            int ch = s.charAt(right) - 'A';

            freq[ch]++;

            maxFreq = Math.max(maxFreq, freq[ch]);

            while ((right - left + 1) - maxFreq > k) {

                freq[s.charAt(left) - 'A']--;

                left++;
            }

            ans = Math.max(ans, right - left + 1);
        }

        return ans;
    }
}
```

## Dry Run

For:

```text
s = "AABABBA"
k = 1
```

Consider:

```text
A A B A
```

Frequency:

```text
A = 3
B = 1
```

Window length:

```text
4
```

Changes required:

```text
4 - 3 = 1
```

Since:

```text
1 <= k
```

the window is valid.

So:

```text
answer = 4
```

Now consider:

```text
A A B A B
```

Frequency:

```text
A = 3
B = 2
```

Window length:

```text
5
```

Changes required:

```text
5 - 3 = 2
```

But:

```text
2 > k
```

so the window is invalid.

We move `left` forward and continue.

## Key Formula

Remember this:

```text
changes needed = window length - highest frequency character
```

If:

```text
changes needed <= k
```

the window is valid.

Otherwise:

```text
move left forward
```

## Complexity

- **Time:** `O(n)`
- **Space:** `O(26)` = `O(1)`

## Important Observation

We do not actually perform the character replacements.

We only calculate how many replacements **would be required**.

That is why the sliding-window solution is efficient.
