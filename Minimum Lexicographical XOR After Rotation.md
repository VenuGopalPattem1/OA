# Minimum Lexicographical XOR After Rotation

## Problem Description

You are given an integer array `A` of size `N`.

For every possible rotation `k` from `0` to `N - 1`:

1. Rotate the array to the left by `k` positions.
2. For every index `i`, perform:
   
   `rotated[i] XOR i`

3. This creates a new array `cur`.
4. Compare all such arrays lexicographically.
5. Return the `k` value that produces the lexicographically smallest array.

If two rotations produce the same array, return the smaller `k`.

---

## What is Lexicographical Order?

Two arrays are compared from left to right.

The first position where they differ decides the result.

For example:

```text
[1, 5, 7] < [2, 0, 0]
```

because `1 < 2`.

Similarly:

```text
[1, 3, 5] < [1, 4, 0]
```

because the first elements are equal, but `3 < 4`.

---

## Rotation

For a left rotation by `k`:

```text
rotated[i] = A[(i + k) % N]
```

Then:

```text
cur[i] = rotated[i] ^ i
```

So directly:

```text
cur[i] = A[(i + k) % N] ^ i
```

---

## Example 1

```text
A = [3, 1, 2]
```

### k = 0

```text
Rotated = [3, 1, 2]

XOR with indexes:

3 ^ 0 = 3
1 ^ 1 = 0
2 ^ 2 = 0

Result = [3, 0, 0]
```

### k = 1

```text
Rotated = [1, 2, 3]

1 ^ 0 = 1
2 ^ 1 = 3
3 ^ 2 = 1

Result = [1, 3, 1]
```

### k = 2

```text
Rotated = [2, 3, 1]

2 ^ 0 = 2
3 ^ 1 = 2
1 ^ 2 = 3

Result = [2, 2, 3]
```

Compare:

```text
[3, 0, 0]
[1, 3, 1]
[2, 2, 3]
```

The lexicographically smallest array is:

```text
[1, 3, 1]
```

Therefore:

```text
Answer = 1
```

---

## Example 2

```text
A = [1, 2, 3]
```

### k = 0

```text
[1, 2, 3]

1 ^ 0 = 1
2 ^ 1 = 3
3 ^ 2 = 1

Result = [1, 3, 1]
```

### k = 1

```text
[2, 3, 1]

2 ^ 0 = 2
3 ^ 1 = 2
1 ^ 2 = 3

Result = [2, 2, 3]
```

### k = 2

```text
[3, 1, 2]

3 ^ 0 = 3
1 ^ 1 = 0
2 ^ 2 = 0

Result = [3, 0, 0]
```

Therefore:

```text
[1, 3, 1] < [2, 2, 3] < [3, 0, 0]
```

So:

```text
Answer = 0
```

---

## Simple Java Solution

```java
class Solution {

    public int findK(int[] a) {

        int n = a.length;
        int bestK = 0;

        int[] best = new int[n];

        // k = 0
        for (int i = 0; i < n; i++) {
            best[i] = a[i] ^ i;
        }

        // Try every rotation
        for (int k = 1; k < n; k++) {

            int[] cur = new int[n];

            // Create rotated XOR array
            for (int i = 0; i < n; i++) {
                cur[i] = a[(i + k) % n] ^ i;
            }

            // Check lexicographically
            for (int i = 0; i < n; i++) {

                if (cur[i] < best[i]) {

                    bestK = k;
                    best = cur;
                    break;

                } else if (cur[i] > best[i]) {

                    break;
                }
            }
        }

        return bestK;
    }
}
```

## How the Code Works

First, calculate the result for `k = 0` and store it in `best`.

```text
best[i] = a[i] ^ i
```

Then try every other rotation.

For each `k`:

```text
cur[i] = a[(i + k) % n] ^ i
```

Now compare `cur` and `best` from left to right.

If:

```text
cur[i] < best[i]
```

then `cur` is lexicographically smaller, so update:

```text
bestK = k
best = cur
```

If:

```text
cur[i] > best[i]
```

then the current rotation cannot be better, so stop comparing it.

---

## Test Cases

### Test Case 1

```text
Input:
A = [3, 1, 2]

Output:
1
```

### Test Case 2

```text
Input:
A = [1, 2, 3]

Output:
0
```

### Test Case 3

```text
Input:
A = [0, 1, 2, 3]

Output:
0
```

For `k = 0`:

```text
[0 ^ 0, 1 ^ 1, 2 ^ 2, 3 ^ 3]

= [0, 0, 0, 0]
```

Since the first element is already `0`, no other rotation can produce a smaller first element.

---

## Complexity

There are `N` possible rotations.

For each rotation:

- Create the XOR array: `O(N)`
- Compare with the best array: `O(N)`

Therefore:

```text
Time Complexity: O(N²)

Space Complexity: O(N)
```

---

## Key Formula

For rotation `k`:

```text
cur[i] = A[(i + k) % N] ^ i
```

The answer is the `k` whose `cur` array is lexicographically smallest.
