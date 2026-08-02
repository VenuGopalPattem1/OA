# Minimum Increments to Make Array Strictly Increasing and Divisible by Index

## Problem Statement

You are given an array `a` of `N` integers.

Initially, the array may not satisfy the required conditions.

In one operation, you may **increase any element by 1**.

Your task is to make the array satisfy **both** of the following conditions:

1. The array must be **strictly increasing**.
2. Every element must be divisible by its **1-based index**.

Formally,

- `a[i] > a[i-1]` for every `i > 1`
- `a[i] % i == 0`

Find the **minimum number of increment operations** required.

---

# Input Format

- First line contains an integer `N`.
- Second line contains `N` space-separated integers.

---

# Output Format

Print the minimum number of increment operations.

---

# Constraints

```text
1 ≤ N ≤ 10^5
1 ≤ a[i] ≤ 10^9
```

---

# Example 1

## Input

```text
4
1 2 3 4
```

## Output

```text
0
```

### Explanation

The array already satisfies

- Strictly increasing ✅
- Divisible by index ✅

```
1 % 1 = 0
2 % 2 = 0
3 % 3 = 0
4 % 4 = 0
```

No operations are required.

---

# Example 2

## Input

```text
4
2 3 4 5
```

## Output

```text
6
```

### Explanation

Process from left to right.

|Index|Original|Final|Increment|
|---:|---:|---:|---:|
|1|2|2|0|
|2|3|4|1|
|3|4|6|2|
|4|5|8|3|

Final array

```text
2 4 6 8
```

Total increments

```text
0 + 1 + 2 + 3 = 6
```

---

# Example 3

## Input

```text
5
2 2 2 2 2
```

## Output

```text
20
```

### Explanation

Final array

```text
2 4 6 8 10
```

Increments

```
0 + 2 + 4 + 6 + 8 = 20
```

---

# Greedy Idea

Process the array from **left to right**.

At every position,

the current value must satisfy

- Greater than the previous value.
- Divisible by its index.

Always choose the **smallest possible valid value**.

---

# Algorithm

For every index `i (1-based)`:

```
need = max(originalValue, previousValue + 1)
```

If `need` is not divisible by `i`,

increase it until it becomes divisible.

That value becomes the new array element.

Add the increments to the answer.

---

# Simple Java Solution

```java
import java.util.*;

public class Main {

    static long minOperations(int[] a) {

        long ans = 0;
        long prev = 0;

        for (int i = 1; i <= a.length; i++) {

            long cur = Math.max(prev + 1, a[i - 1]);

            while (cur % i != 0) {
                cur++;
            }

            ans += cur - a[i - 1];

            prev = cur;
        }

        return ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        int[] a = new int[n];

        for (int i = 0; i < n; i++) {
            a[i] = sc.nextInt();
        }

        System.out.println(minOperations(a));
    }
}
```

---

# Optimized Java Solution (O(N))

Instead of increasing one by one, directly compute the next multiple.

```java
import java.util.*;

public class Main {

    static long minOperations(int[] a) {

        long ans = 0;
        long prev = 0;

        for (int i = 1; i <= a.length; i++) {

            long need = Math.max(prev + 1, a[i - 1]);

            long value;

            if (need % i == 0)
                value = need;
            else
                value = ((need + i - 1) / i) * i;

            ans += value - a[i - 1];

            prev = value;
        }

        return ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        int[] a = new int[n];

        for (int i = 0; i < n; i++)
            a[i] = sc.nextInt();

        System.out.println(minOperations(a));
    }
}
```

---

# Dry Run

Input

```text
4
2 3 4 5
```

|Index|Original|Need|Smallest Valid Multiple|Final Value|Increment|
|---:|---:|---:|---:|---:|---:|
|1|2|2|2|2|0|
|2|3|3|4|4|1|
|3|4|5|6|6|2|
|4|5|7|8|8|3|

Final array

```text
2 4 6 8
```

Answer

```text
6
```

---

# Complexity

### Simple Solution

- Time: `O(N²)` (worst case due to the `while` loop)
- Space: `O(1)`

### Optimized Solution

- Time: `O(N)`
- Space: `O(1)`

---

# Related Problems

## 1. LeetCode 1827 – Minimum Operations to Make the Array Increasing

**Link**

https://leetcode.com/problems/minimum-operations-to-make-the-array-increasing/

### Difference

LeetCode 1827 only requires the array to be **strictly increasing**.

This OA problem additionally requires

```
a[i] % i == 0
```

The greedy idea is the same:

- Process left to right.
- Choose the smallest valid value.

---

## 2. LeetCode 1187 – Make Array Strictly Increasing

**Link**

https://leetcode.com/problems/make-array-strictly-increasing/

This is a harder Dynamic Programming problem where you are allowed to replace elements using another array.

---

# Key Observation

At every index,

choose the **smallest possible value** that satisfies both constraints.

Choosing a larger value can never reduce the number of future increments.

Hence, a greedy left-to-right approach is optimal.

---

# Revision Notes

✅ Process from left to right.

✅ Current value must be

- Greater than previous.
- Divisible by index.

✅ Always choose the **smallest valid value**.

**Time Complexity**

- Simple: `O(N²)`
- Optimized: `O(N)`

**Space Complexity**

```
O(1)
```
