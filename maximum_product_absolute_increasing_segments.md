# Q2. Maximum Product of Strictly Increasing-by-Absolute-Value Segments

## Problem

Given an array of integers, divide it into contiguous segments.

A segment continues only while:

```text
abs(nums[i]) > abs(nums[i - 1])
```

Whenever this condition fails, start a new segment.

Compute the product of every segment and return the maximum product among all segments.

---

## Example

```text
nums = [-2, 5, -7, 3]
```

Absolute values:

```text
2, 5, 7, 3
```

Since:

```text
2 < 5 < 7
```

the first segment is:

```text
[-2, 5, -7]
```

Then:

```text
abs(3) > abs(-7)
3 > 7
```

is false, so a new segment starts:

```text
[3]
```

Products:

```text
(-2) * 5 * (-7) = 70
3 = 3
```

Therefore:

```text
Answer = 70
```

---

# Test Case 1

```text
nums = [2, 3, 5, 1, 4]
```

Absolute values:

```text
2, 3, 5, 1, 4
```

Segments:

```text
[2, 3, 5]
[1, 4]
```

Products:

```text
2 * 3 * 5 = 30
1 * 4 = 4
```

Answer:

```text
30
```

---

# Test Case 2

```text
nums = [-2, 3, -5, 4, -6]
```

Absolute values:

```text
2, 3, 5, 4, 6
```

Segments:

```text
[-2, 3, -5]
[4, -6]
```

Products:

```text
(-2) * 3 * (-5) = 30
4 * (-6) = -24
```

Answer:

```text
30
```

---

# Approach

The segment boundaries are completely determined by:

```text
abs(nums[i]) > abs(nums[i - 1])
```

Therefore, we do not need to try every possible subarray.

Maintain:

```text
currentProduct
answer
```

For every element:

### Case 1: Absolute value increases

If:

```text
abs(nums[i]) > abs(nums[i - 1])
```

then the current segment continues:

```text
currentProduct *= nums[i]
```

### Case 2: Condition fails

Otherwise, the previous segment ends and a new segment starts:

```text
currentProduct = nums[i]
```

After either case:

```text
answer = max(answer, currentProduct)
```

---

# Java Code

```java
class Solution {

    public long maxProduct(int[] nums) {

        int n = nums.length;

        long currentProduct = nums[0];
        long ans = nums[0];

        for (int i = 1; i < n; i++) {

            if (Math.abs((long) nums[i]) >
                Math.abs((long) nums[i - 1])) {

                // Continue current segment
                currentProduct *= nums[i];

            } else {

                // Start a new segment
                currentProduct = nums[i];
            }

            ans = Math.max(ans, currentProduct);
        }

        return ans;
    }
}
```

---

# Dry Run

Consider:

```text
nums = [-2, 3, -5, 4, -6]
```

Start:

```text
currentProduct = -2
answer = -2
```

## i = 1

```text
abs(3) > abs(-2)
3 > 2
```

True.

Continue the segment:

```text
currentProduct = -2 * 3
               = -6
```

```text
answer = max(-2, -6)
       = -2
```

Current segment:

```text
[-2, 3]
```

---

## i = 2

```text
abs(-5) > abs(3)
5 > 3
```

True.

Continue:

```text
currentProduct = -6 * -5
               = 30
```

```text
answer = 30
```

Current segment:

```text
[-2, 3, -5]
```

---

## i = 3

```text
abs(4) > abs(-5)
4 > 5
```

False.

Start a new segment:

```text
currentProduct = 4
```

---

## i = 4

```text
abs(-6) > abs(4)
6 > 4
```

True.

Continue:

```text
currentProduct = 4 * -6
               = -24
```

Final answer:

```text
30
```

---

# Why This Is Different From Maximum Product Subarray

In standard Maximum Product Subarray, we can choose any subarray.

Because negative numbers can turn a minimum product into a maximum product, we usually maintain:

```text
maxProduct
minProduct
```

For this problem, we cannot choose arbitrary boundaries.

The rule itself determines the segments:

```text
if abs(nums[i]) > abs(nums[i - 1])
    continue
else
    restart
```

Therefore, we only need:

```text
currentProduct
answer
```

---

# Why Use long?

Products can become larger than the range of Java `int`.

For example:

```text
1000 * 999 * 998 * ...
```

can overflow an `int`.

So use:

```java
long currentProduct;
long ans;
```

Also cast before `Math.abs`:

```java
Math.abs((long) nums[i])
```

This avoids the special overflow issue of `Math.abs(Integer.MIN_VALUE)`.

---

# Complexity

We scan the array only once.

```text
Time Complexity  = O(N)
Space Complexity = O(1)
```

---

# Core Pattern

This is a one-pass segment/Kadane-style problem.

```text
if abs(nums[i]) > abs(nums[i - 1])
        |
        v
continue current segment
        |
        currentProduct *= nums[i]

otherwise
        |
        v
start new segment
        |
        currentProduct = nums[i]
```

The main idea is:

```text
currentProduct = product of current valid segment
answer = maximum product seen so far
```

---

# Related Problems

## 1. LeetCode 152 — Maximum Product Subarray

The closest problem for understanding maximum product and negative numbers.

https://leetcode.com/problems/maximum-product-subarray/

## 2. LeetCode 53 — Maximum Subarray

Classic Kadane's algorithm.

https://leetcode.com/problems/maximum-subarray/

## 3. LeetCode 674 — Longest Continuous Increasing Subsequence

Useful for understanding continuous segments controlled by an increasing condition.

https://leetcode.com/problems/longest-continuous-increasing-subsequence/

## 4. LeetCode 978 — Longest Turbulent Subarray

Useful for conditions involving adjacent elements.

https://leetcode.com/problems/longest-turbulent-subarray/

---

# Recommended Practice Order

```text
53. Maximum Subarray
        ↓
152. Maximum Product Subarray
        ↓
674. Longest Continuous Increasing Subsequence
        ↓
978. Longest Turbulent Subarray
        ↓
This Custom Problem
```

The key difference from LeetCode 152 is that here the segment boundaries are fixed by:

```text
abs(nums[i]) > abs(nums[i - 1])
```

so a simple `O(N)` scan is enough.
