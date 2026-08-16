# Clipping Points in an Array - Brute Force Solution

## Problem Statement
An internal element is a **clipping point** if it is strictly greater than both its adjacent neighbours (a peak) or strictly smaller than both its adjacent neighbours (a valley).

Given an array `arr`, determine the minimum number of clipping points remaining after removing exactly one element from the array.

---

## Brute Force Approach
The brute-force approach simulates removing every element one by one at each index `i` from `0` to `n - 1`. For each removal:
1. Construct the resulting array of size `n - 1`.
2. Iterate through all internal elements of the new array (indices `1` to `n - 3`).
3. Count the number of peaks and valleys.
4. Track and return the minimum count observed across all candidate removals.

---

## Java Implementation

```java
public class BruteForceClippingPoints {

    /**
     * Finds the minimum number of clipping points remaining after removing one element.
     *
     * @param arr the input array of integers
     * @return minimum remaining clipping points
     */
    public static int minClippingPointsBruteForce(int[] arr) {
        int n = arr.length;
        // If the array has <= 3 elements, removing 1 leaves <= 2 elements,
        // which contains no internal elements (thus 0 clipping points).
        if (n <= 3) {
            return 0;
        }

        int minClips = Integer.MAX_VALUE;

        // Try removing every element at index i
        for (int i = 0; i < n; i++) {
            // Build the new array without arr[i]
            int[] temp = new int[n - 1];
            int idx = 0;
            for (int j = 0; j < n; j++) {
                if (j != i) {
                    temp[idx++] = arr[j];
                }
            }

            // Count clipping points in the new array
            int currentClips = 0;
            for (int k = 1; k < temp.length - 1; k++) {
                if ((temp[k] > temp[k - 1] && temp[k] > temp[k + 1]) ||
                    (temp[k] < temp[k - 1] && temp[k] < temp[k + 1])) {
                    currentClips++;
                }
            }

            minClips = Math.min(minClips, currentClips);
        }

        return minClips;
    }

    public static void main(String[] args) {
        int[] arr = {1, 5, 2, 4, 3};
        int result = minClippingPointsBruteForce(arr);
        System.out.println("Original array: [1, 5, 2, 4, 3]");
        System.out.println("Minimum remaining clipping points: " + result); // Output: 1
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** $\mathcal{O}(N^2)$  
  - Outer loop runs $N$ times (testing removal at each index).
  - Inside the loop, constructing the array takes $\mathcal{O}(N)$ and scanning for clipping points takes $\mathcal{O}(N)$.
  - Total time: $N \times \mathcal{O}(N) = \mathcal{O}(N^2)$.

- **Space Complexity:** $\mathcal{O}(N)$  
  - Allocates a temporary array of size $N - 1$ per iteration.
