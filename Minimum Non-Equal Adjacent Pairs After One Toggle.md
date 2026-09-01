# Minimum Non-Equal Adjacent Pairs After One Toggle

## Problem Description

You are given a string `s` consisting of only two characters:

- `A`
- `C`

You must toggle exactly one character in the string.

Toggle means:

- `A → C`
- `C → A`

After toggling one character, count the number of adjacent pairs whose characters are different.

Return the minimum possible number of non-equal adjacent pairs.

## Example

### Input

AACA

Try toggling every position:

- Toggle index 0: `CACA`
  - `CA` → different
  - `AC` → different
  - `CA` → different
  - Count = 3

- Toggle index 1: `ACCA`
  - `AC` → different
  - `CC` → equal
  - `CA` → different
  - Count = 2

- Toggle index 2: `AAAA`
  - `AA` → equal
  - `AA` → equal
  - `AA` → equal
  - Count = 0

- Toggle index 3: `AACC`
  - `AA` → equal
  - `AC` → different
  - `CC` → equal
  - Count = 1

Therefore:

Answer = `0`

## Brute Force Approach

The simplest approach is to try toggling every character.

For every index:

1. Toggle the character.
2. Traverse the complete string.
3. Count the adjacent pairs where `arr[j] != arr[j + 1]`.
4. Update the minimum answer.
5. Restore the original character.

Since we try `n` positions and scan `n` characters for every position, the time complexity is `O(n²)`.

## Java Code

class Solution {

    public static int minPairs(String s) {

        char[] arr = s.toCharArray();
        int n = arr.length;

        int ans = Integer.MAX_VALUE;

        // Try toggling every character
        for (int i = 0; i < n; i++) {

            // Toggle
            if (arr[i] == 'A')
                arr[i] = 'C';
            else
                arr[i] = 'A';

            // Count non-equal adjacent pairs
            int count = 0;

            for (int j = 0; j < n - 1; j++) {

                if (arr[j] != arr[j + 1]) {
                    count++;
                }
            }

            // Update minimum
            ans = Math.min(ans, count);

            // Restore original character
            if (arr[i] == 'A')
                arr[i] = 'C';
            else
                arr[i] = 'A';
        }

        return ans;
    }
}

## Dry Run

For:

s = "AACA"

Initial:

A A C A
0 1 2 3

### Toggle index 0

A → C

New string:

C A C A

Adjacent pairs:

CA → different
AC → different
CA → different

Count = 3

### Toggle index 1

A → C

New string:

A C C A

Adjacent pairs:

AC → different
CC → equal
CA → different

Count = 2

### Toggle index 2

C → A

New string:

A A A A

Adjacent pairs:

AA → equal
AA → equal
AA → equal

Count = 0

### Toggle index 3

A → C

New string:

A A C C

Adjacent pairs:

AA → equal
AC → different
CC → equal

Count = 1

## Final Result

Toggle index 0 → 3
Toggle index 1 → 2
Toggle index 2 → 0
Toggle index 3 → 1

Answer = min(3, 2, 0, 1)

Answer = 0

## Complexity

Time Complexity: `O(n²)`

Space Complexity: `O(n)`

The `O(n)` space is used for the character array.

## Brute Force Pattern to Remember

For every index:

1. Toggle character
2. Count all adjacent unequal pairs
3. Update minimum
4. Restore character

## Optimized Observation

When we toggle `s[i]`, only two adjacent pairs can change:

- `(i - 1, i)`
- `(i, i + 1)`

All other adjacent pairs remain unchanged.

Therefore, instead of counting the entire string after every toggle, we can update the count using only these two pairs.

This gives:

Time Complexity: `O(n)`

Space Complexity: `O(1)`
