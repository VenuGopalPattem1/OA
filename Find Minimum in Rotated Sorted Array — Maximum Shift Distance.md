# Find Minimum in Rotated Sorted Array — Maximum Shift Distance

## Problem Description
An ascending sorted array of distinct integers `nums` ($0$-indexed) has been circularly rotated to the right by some unknown number of steps $k$.

For any element `nums[i]`:
- **Target Index:** The index it would occupy if the array was sorted in strictly ascending order.
- **Shift Distance:** The minimum number of circular shifts (left or right) needed to move it from its current index $i$ to its target index within array bounds.

**Objective:** Find the **maximum shift distance** among all elements in the array.

---

## Key Observation
Because the original array is strictly ascending and rotated circularly:
1. Every element has been shifted by the exact same offset $k$, where $k$ is the index of the minimum element.
2. The minimum circular shift distance to return any element back to its sorted position is:
   $$\text{shift distance} = \min(k, N - k)$$
3. Since this value is identical for every element, the maximum shift distance across all elements is simply $\min(k, N - k)$.

---

## Solutions in Java

### 1. Optimal Solution — Binary Search $\mathcal{O}(\log N)$
Using the standard `low <= high` template with `low = mid + 1` and `high = mid - 1`:

```java
import java.util.Scanner;

public class MaxShiftDistanceBinarySearch {

    public static int findMaxShiftDistance(int[] nums) {
        int n = nums.length;
        if (n <= 1) return 0;

        // If array is already fully sorted
        if (nums[0] < nums[n - 1]) {
            return 0;
        }

        int low = 0;
        int high = n - 1;
        int minIdx = 0;

        while (low <= high) {
            int mid = low + (high - low) / 2;

            // Check if mid is the pivot point
            if (mid > 0 && nums[mid] < nums[mid - 1]) {
                minIdx = mid;
                break;
            }

            // Check if mid + 1 is the pivot point
            if (mid < n - 1 && nums[mid] > nums[mid + 1]) {
                minIdx = mid + 1;
                break;
            }

            // Decide search direction
            if (nums[mid] >= nums[0]) {
                low = mid + 1; // Pivot lies in right half
            } else {
                high = mid - 1; // Pivot lies in left half
            }
        }

        int k = minIdx;
        return Math.min(k, n - k);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        if (!sc.hasNextInt()) return;

        int n = sc.nextInt();
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = sc.nextInt();
        }

        System.out.println(findMaxShiftDistance(nums));
        sc.close();
    }
}
```

---

### 2. Simple Solution — Linear Scan $\mathcal{O}(N)$

```java
import java.util.Scanner;

public class SimpleMaxShiftDistance {

    public static int findMaxShiftDistance(int[] nums) {
        int n = nums.length;
        if (n <= 1) return 0;

        // Find index of the minimum element (k)
        int minIdx = 0;
        for (int i = 1; i < n; i++) {
            if (nums[i] < nums[i - 1]) {
                minIdx = i;
                break;
            }
        }

        return Math.min(minIdx, n - minIdx);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        if (!sc.hasNextInt()) return;

        int n = sc.nextInt();
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = sc.nextInt();
        }

        System.out.println(findMaxShiftDistance(nums));
        sc.close();
    }
}
```

---

## Complexity Analysis

| Approach | Time Complexity | Space Complexity |
| :--- | :--- | :--- |
| **Binary Search** | $\mathcal{O}(\log N)$ | $\mathcal{O}(1)$ |
| **Linear Scan** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ |
