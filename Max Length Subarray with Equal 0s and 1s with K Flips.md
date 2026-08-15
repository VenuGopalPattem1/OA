# Max Length Subarray with Equal 0s and 1s with K Flips

## Problem Description
Given a binary string `s` and an integer `k`, find the maximum length of a subarray where the number of `0`s and `1`s can be made equal by performing at most `k` flips. 

A single flip allows you to change a `0` to a `1` or a `1` to a `0`.

---

## Explanation of Logic

1. **The Counting Gap**: Every time you flip a character inside a window, you decrease the count of one digit by `1` and increase the other by `1`. This changes the absolute difference between `0`s and `1`s by exactly **2**.
2. **The Flip Condition**: For a window to be validly balanced using flips:
   * The absolute difference between the counts of `0`s and `1`s must be an **even number**.
   * The total operations needed (`difference / 2`) must not exceed `k`.
3. **Sliding Window**: We use two pointers (`left` and `right`) to scan the string. We expand `right` to increase our window and shrink `left` whenever the window violates our flip condition.

---

## Complexity Analysis

* **Time Complexity**: **O(N)** – Each character is visited by the `right` pointer once and the `left` pointer at most once.
* **Space Complexity**: **O(1)** – No extra memory is allocated; we only track counter variables.

---

## Java Implementation

```java
public class Solution {
    public static int maxLengthSubarray(String s, int k) {
        int maxLength = 0;
        int zeros = 0;
        int ones = 0;
        int left = 0;

        // Expand the right boundary of the window
        for (int right = 0; right < s.length(); right++) {
            if (s.charAt(right) == '0') {
                zeros++;
            } else {
                ones++;
            }

            // Shrink the window from the left if it is invalid
            while (Math.abs(zeros - ones) % 2 != 0 || Math.abs(zeros - ones) / 2 > k) {
                if (s.charAt(left) == '0') {
                    zeros--;
                } else {
                    ones--;
                }
                left++;
            }

            // Record maximum length if valid
            if (Math.abs(zeros - ones) / 2 <= k && Math.abs(zeros - ones) % 2 == 0) {
                maxLength = Math.max(maxLength, right - left + 1);
            }
        }

        return maxLength;
    }

    public static void main(String[] args) {
        String testStr = "0100110";
        int k = 1;
        System.out.println("Maximum Subarray Length: " + maxLengthSubarray(testStr, k)); 
    }
}
```
