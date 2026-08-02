# Minimum Sum of Pairwise Absolute Differences

## Problem Statement

You are given an **even-length** array `a` of size `N`.

You need to partition all elements into exactly `N/2` disjoint pairs.

A pair is **valid** only if:

- One element belongs to the **first half** of the original array (`0` to `N/2-1`).
- The other element belongs to the **second half** of the original array (`N/2` to `N-1`).

The cost of a pair is

```
|x - y|
```

where `x` and `y` are the two paired elements.

Your task is to find the **minimum possible total cost** across all valid pairings.

---

# Input Format

- First line contains an even integer `N`.
- Second line contains `N` space-separated integers.

---

# Output Format

Print a single integer representing the minimum total pairing cost.

---

# Constraints

```text
2 ≤ N ≤ 10^5
N is even
-10^9 ≤ a[i] ≤ 10^9
```

---

# Example 1

## Input

```text
4
1 4 2 5
```

## Output

```text
2
```

## Explanation

First Half

```
1 4
```

Second Half

```
2 5
```

Optimal pairing

```
1 ↔ 2 = 1
4 ↔ 5 = 1
```

Total

```
2
```

---

# Example 2

## Input

```text
6
1 10 20 2 9 18
```

## Output

```text
4
```

## Explanation

First Half

```
1 10 20
```

Second Half

```
2 9 18
```

Sort both halves.

```
Left  = [1,10,20]
Right = [2,9,18]
```

Pair corresponding elements.

```
1 ↔ 2  = 1
10 ↔ 9 = 1
20 ↔18 = 2
```

Total

```
4
```

---

# Example 3

## Input

```text
8
5 8 1 10 6 9 2 11
```

## Output

```text
4
```

## Explanation

Split

```
Left  = [5,8,1,10]
Right = [6,9,2,11]
```

Sort

```
Left  = [1,5,8,10]
Right = [2,6,9,11]
```

Pair

```
1 ↔ 2   = 1
5 ↔ 6   = 1
8 ↔ 9   = 1
10 ↔11  = 1
```

Total

```
4
```

---

# Observation

Suppose

```
Left

1 8

Right

3 10
```

Two possible pairings

### Pairing 1

```
1 ↔ 3
8 ↔10
```

Cost

```
2 + 2 = 4
```

---

### Pairing 2

```
1 ↔10
8 ↔3
```

Cost

```
9 + 5 = 14
```

Clearly,

**pairing sorted elements together always gives the minimum total absolute difference.**

---

# Greedy Algorithm

1. Split the array into two halves.
2. Sort the first half.
3. Sort the second half.
4. Pair the ith smallest element of the first half with the ith smallest element of the second half.
5. Add all absolute differences.

---

# Java Solution

```java
import java.util.*;

class Solution {

    static long minimumCost(int[] a) {

        int n = a.length;
        int m = n / 2;

        int[] left = new int[m];
        int[] right = new int[m];

        for (int i = 0; i < m; i++) {
            left[i] = a[i];
            right[i] = a[i + m];
        }

        Arrays.sort(left);
        Arrays.sort(right);

        long ans = 0;

        for (int i = 0; i < m; i++) {
            ans += Math.abs((long) left[i] - right[i]);
        }

        return ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        int[] a = new int[n];

        for (int i = 0; i < n; i++)
            a[i] = sc.nextInt();

        System.out.println(minimumCost(a));
    }
}
```

---

# Dry Run

Input

```text
8
5 8 1 10 6 9 2 11
```

Split

```
Left

5 8 1 10

Right

6 9 2 11
```

Sort

```
Left

1 5 8 10

Right

2 6 9 11
```

Pair

|Left|Right|Difference|
|---:|---:|---:|
|1|2|1|
|5|6|1|
|8|9|1|
|10|11|1|

Total

```
4
```

---

# Complexity

### Time

```
O(N log N)
```

Sorting dominates the complexity.

### Space

```
O(N)
```

---

# Related Problems

## 1. Assignment Problem (Minimum Weight Bipartite Matching)

Every element from the first half must be matched with exactly one element from the second half.

General solution:

- Hungarian Algorithm
- Time Complexity: `O(N³)`

Reference

https://en.wikipedia.org/wiki/Hungarian_algorithm

---

## 2. Minimum Weight Perfect Matching in Bipartite Graph

Graph interpretation of the same problem.

Each edge weight is

```
|a[i]-b[j]|
```

General solution

- Hungarian Algorithm

---

## 3. LeetCode 2616 – Minimize the Maximum Difference of Pairs

Although the objective is different (minimize the maximum difference instead of the sum), it also involves pairing elements based on absolute differences.

https://leetcode.com/problems/minimize-the-maximum-difference-of-pairs/

---

## 4. LeetCode 1200 – Minimum Absolute Difference

Finds pairs with the smallest absolute difference after sorting.

https://leetcode.com/problems/minimum-absolute-difference/

---

# Key Observation

For the cost function

```
|a - b|
```

the optimal strategy is

```
Sort both halves independently
```

and

```
Pair corresponding elements.
```

Cross pairings always produce an equal or larger total cost.

---

# Revision Notes

✅ Split into two halves.

✅ Sort each half independently.

✅ Pair corresponding elements.

✅ Sum the absolute differences.

**Time Complexity**

```
O(N log N)
```

**Space Complexity**

```
O(N)
```
