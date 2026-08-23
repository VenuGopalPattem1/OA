# Reduce Array Size to the Half — Range Variant

## Problem Description

Given an array `arr` of even length, choose a single contiguous range of integer values `[X, Y]` and remove every element whose value lies within that range (inclusive).

Find the minimum possible length of the chosen range:

`Y - X + 1`

such that after removing the elements in that range, at most half of the original elements remain.

In other words, the chosen range must remove at least `n / 2` elements.

### Example

```text
Input:
arr = [2,3,3,3,5,6]

Output:
1
```

Explanation:

The array has `6` elements, so we need to remove at least `3` elements.

Choose the range `[3,3]`.

There are three `3`s in the array, so all three are removed.

The length of the range is:

`3 - 3 + 1 = 1`

Therefore, the answer is `1`.

---

## Test Case 1

```text
Input:
arr = [2,3,3,3,5,6]

Output:
1
```

Explanation:

Choose `[3,3]`.

Elements removed:

```text
3, 3, 3
```

Number of removed elements = `3`.

Required = `6 / 2 = 3`.

Range length:

```text
3 - 3 + 1 = 1
```

Answer = `1`.

---

## Test Case 2

```text
Input:
arr = [1,2,4,5,6,7,8,9]

Output:
4
```

Explanation:

The array has `8` elements, so we need to remove at least `4`.

Choose:

```text
[4,7]
```

This removes:

```text
4,5,6,7
```

Number of removed elements = `4`.

Range length:

```text
7 - 4 + 1 = 4
```

Answer = `4`.

---

# Approach

We need to find a numeric range `[X,Y]` that contains at least `n/2` elements of the array.

The important point is that the removed values must be **contiguous in value**.

For example, if the values are:

```text
1 2 3 4 5 6
```

we can choose:

```text
[2,5]
```

but we cannot choose only `2` and `5`.

Therefore, simply selecting the elements with the largest frequencies using a PriorityQueue does not work.

We use:

1. Frequency Map
2. Sort distinct values
3. Two pointers / sliding window

---

## Step 1: Count Frequencies

For:

```text
arr = [2,3,3,3,5,6]
```

the frequencies are:

```text
2 -> 1
3 -> 3
5 -> 1
6 -> 1
```

---

## Step 2: Sort Distinct Values

```text
[2,3,5,6]
```

---

## Step 3: Use Two Pointers

Maintain a window from `left` to `right`.

`count` stores how many array elements are currently inside the range.

When:

```text
count >= n / 2
```

the current range is valid.

Now try to shrink the range from the left.

For every valid range:

```text
rangeLength = values[right] - values[left] + 1
```

Take the minimum.

---

# Java Code

```java
import java.util.*;

class Solution {

    public int minRange(int[] arr) {
        int n = arr.length;

        // Count frequency of every value
        Map<Integer, Integer> freq = new HashMap<>();

        for (int x : arr) {
            freq.put(x, freq.getOrDefault(x, 0) + 1);
        }

        // Store distinct values
        int[] values = new int[freq.size()];
        int idx = 0;

        for (int x : freq.keySet()) {
            values[idx++] = x;
        }

        // Sort distinct values
        Arrays.sort(values);

        int left = 0;
        int count = 0;
        int ans = Integer.MAX_VALUE;

        for (int right = 0; right < values.length; right++) {

            // Add frequency of current value
            count += freq.get(values[right]);

            // Current range removes at least half
            while (count >= n / 2) {

                int rangeLength =
                        values[right] - values[left] + 1;

                ans = Math.min(ans, rangeLength);

                // Remove left value from window
                count -= freq.get(values[left]);
                left++;
            }
        }

        return ans;
    }
}
```

---

# Dry Run

Consider:

```text
arr = [2,3,3,3,5,6]
```

`n = 6`

We need to remove:

```text
n / 2 = 3
```

Frequency table:

```text
2 -> 1
3 -> 3
5 -> 1
6 -> 1
```

Sorted values:

```text
[2,3,5,6]
```

### right = 0

Value = `2`

```text
count = 1
```

Not enough.

---

### right = 1

Value = `3`

```text
count = 1 + 3
      = 4
```

Now:

```text
count >= 3
```

Current range:

```text
[2,3]
```

Length:

```text
3 - 2 + 1 = 2
```

So:

```text
ans = 2
```

Now shrink from the left.

Remove value `2`:

```text
count = 4 - 1
      = 3
```

Current range:

```text
[3,3]
```

Length:

```text
3 - 3 + 1 = 1
```

So:

```text
ans = 1
```

Now remove `3`:

```text
count = 3 - 3
      = 0
```

Stop shrinking.

Therefore:

```text
Answer = 1
```

---

# Why Sliding Window Works

After sorting the distinct values:

```text
v[0] < v[1] < v[2] < ... < v[k-1]
```

If a range `[left,right]` contains at least `n/2` elements, then any larger range is also valid.

So we can:

- Move `right` forward to increase the number of removed elements.
- Once we have enough elements, move `left` forward to make the range smaller.
- Keep the minimum range length.

This gives an `O(k)` two-pointer step after sorting.

---

# Why PriorityQueue Is Not Enough

A PriorityQueue can find the values having the largest frequencies.

For example:

```text
1 -> 1
2 -> 1
3 -> 1
4 -> 1
5 -> 3
6 -> 1
```

A PriorityQueue would prefer:

```text
5 -> 3
```

But if we need more elements, it might select another high-frequency value.

The problem is that we are allowed to remove only values inside **one contiguous range**.

We cannot select:

```text
1 and 5
```

because `[1,5]` also contains `2,3,4`.

Therefore, frequency alone is not enough.

We need:

```text
frequency + sorted values + range
```

which is why the two-pointer approach is appropriate.

---

# Complexity

Let `k` be the number of distinct values.

Frequency counting:

```text
O(n)
```

Sorting distinct values:

```text
O(k log k)
```

Two-pointer traversal:

```text
O(k)
```

Overall:

```text
Time Complexity: O(n + k log k)
Space Complexity: O(k)
```

Since `k <= n`, this can also be written as:

```text
Time: O(n log n)
Space: O(n)
```

---

# Key Pattern

This problem is a combination of:

```text
Frequency Map
        +
Sort Distinct Values
        +
Two Pointers / Sliding Window
        +
Minimum Range
```

The main condition is:

```text
count >= n / 2
```

and the value we minimize is:

```text
values[right] - values[left] + 1
```
