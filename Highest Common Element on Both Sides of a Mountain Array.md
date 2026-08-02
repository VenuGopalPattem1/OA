# Highest Common Element on Both Sides of a Mountain Array

## Problem

A mountain array is an array that first strictly increases to a single peak and then strictly decreases.

Find the **largest element** that appears on **both sides** of the peak.

If no such element exists, return `-1`.

---

## Example

### Input

```text
7
1 2 4 7 4 2 1
```

### Output

```text
4
```

### Explanation

Increasing side

```
1 2 4
```

Decreasing side

```
4 2 1
```

Common elements

```
1 2 4
```

Largest = **4**

---

## Approach

1. Find the peak index.
2. Store all elements before the peak in a HashSet.
3. Traverse the decreasing side.
4. If an element exists in the set, update the answer.

---

## Java Code

```java
import java.util.*;

class Solution {

    static int highestCommonElement(int[] a) {

        int n = a.length;

        int peak = 0;
        for (int i = 1; i < n - 1; i++) {
            if (a[i] > a[i - 1] && a[i] > a[i + 1]) {
                peak = i;
                break;
            }
        }

        HashSet<Integer> set = new HashSet<>();

        for (int i = 0; i < peak; i++) {
            set.add(a[i]);
        }

        int ans = -1;

        for (int i = peak + 1; i < n; i++) {
            if (set.contains(a[i])) {
                ans = Math.max(ans, a[i]);
            }
        }

        return ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        int[] a = new int[n];

        for (int i = 0; i < n; i++)
            a[i] = sc.nextInt();

        System.out.println(highestCommonElement(a));
    }
}
```

## Algorithm

```
Find Peak

Insert left-side elements into HashSet

Traverse right side

If element exists in HashSet

    answer = max(answer, element)
```

---

## Complexity

Time

```
O(N)
```

Space

```
O(N)
```

---

## Similar Problems

- LeetCode 852 – Peak Index in a Mountain Array
- LeetCode 1095 – Find in Mountain Array

---

## Concepts

- Arrays
- HashSet
- Peak Finding
- Greedy Traversal
