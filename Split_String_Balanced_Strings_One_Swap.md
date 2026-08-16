# Split a String into Balanced Strings (with One Optional Swap)

## Problem

You are given a string `s` of even length `N` containing only the characters `L` and `R`.

The string is globally balanced, meaning it contains an equal number of `L` and `R`.

A substring is called **balanced** if it contains an equal number of `L` and `R`.

You need to split the string into the **maximum possible number of contiguous balanced substrings**.

Before splitting, you may perform **at most one swap of adjacent characters**.

You may also choose not to perform any swap.

---

## Example

```text
s = "LLRR"
```

Without a swap:

```text
LLRR
```

There is only one balanced substring.

Now swap the middle two characters:

```text
LLRR -> LRLR
```

Now we can split:

```text
LR | LR
```

Both substrings are balanced.

Therefore:

```text
Answer = 2
```

---

## Approach

We use a simple brute-force approach.

### Step 1: Count balanced substrings

We use a variable called `balance`.

```text
L = +1
R = -1
```

Whenever `balance == 0`, the current prefix contains an equal number of `L` and `R`.

For example:

```text
LRLR
```

Prefix balances:

```text
L  R  L  R
1  0  1  0
```

There are two positions where the balance becomes `0`.

Therefore:

```text
LR | LR
```

gives `2` balanced substrings.

### Step 2: Try every adjacent swap

There are `N - 1` possible adjacent pairs.

For every position `i`:

1. Swap `s[i]` and `s[i+1]`.
2. Count the balanced substrings.
3. Update the maximum answer.
4. Swap them back.

We also check the original string without performing any swap.

---

## Java Solution

```java
class Solution {

    public int maxBalancedSubstrings(String s) {

        int ans = countBalanced(s);

        char[] arr = s.toCharArray();

        // Try every adjacent swap
        for (int i = 0; i < arr.length - 1; i++) {

            // Swap
            char temp = arr[i];
            arr[i] = arr[i + 1];
            arr[i + 1] = temp;

            // Count balanced substrings
            ans = Math.max(ans, countBalanced(new String(arr)));

            // Swap back
            temp = arr[i];
            arr[i] = arr[i + 1];
            arr[i + 1] = temp;
        }

        return ans;
    }

    private int countBalanced(String s) {

        int balance = 0;
        int count = 0;

        for (int i = 0; i < s.length(); i++) {

            if (s.charAt(i) == 'L') {
                balance++;
            } else {
                balance--;
            }

            if (balance == 0) {
                count++;
            }
        }

        return count;
    }
}
```

---

## Dry Run

### Test Case 1

```text
Input:
s = "LLRR"
```

Initially:

```text
LLRR
```

Balanced substring count:

```text
1
```

Try swapping positions `1` and `2`:

```text
LLRR -> LRLR
```

Now:

```text
LR | LR
```

Number of balanced substrings:

```text
2
```

Therefore:

```text
Output = 2
```

---

## Test Case 2

```text
Input:
s = "LRRL"
```

Without swapping:

```text
LR | RL
```

So:

```text
count = 2
```

Trying the possible adjacent swaps does not produce more than `2`.

Therefore:

```text
Output = 2
```

---

## Test Cases

### Test Case 1

```text
Input:
LLRR

Output:
2
```

### Test Case 2

```text
Input:
LRRL

Output:
2
```

---

## Complexity

There are `N - 1` possible adjacent swaps.

For every swap, we scan the complete string to count balanced substrings.

```text
Time Complexity: O(N²)
Space Complexity: O(N)
```

The `O(N)` extra space comes from converting the string into a character array.

---

## Key Idea

The easiest way to remember the solution is:

```text
1. Count without swap.
2. Try every adjacent swap.
3. Count after each swap.
4. Take the maximum.
```

The helper function uses:

```text
L = +1
R = -1
```

Whenever:

```text
balance == 0
```

we have found one balanced substring.
