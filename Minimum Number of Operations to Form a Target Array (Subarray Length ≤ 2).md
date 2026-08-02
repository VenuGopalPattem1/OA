# Minimum Number of Operations to Form a Target Array (Subarray Length ≤ 2)

## Problem Statement

You are given an array of `N` elements, initially filled with zeros.

Your goal is to transform it into the given `target` array.

In one operation, you may choose **any contiguous subarray of length 1 or 2** and increment every element in that subarray by **1**.

Determine the **minimum number of operations** required to obtain the target array exactly.

---

## Input Format

- The first line contains an integer `N`, representing the size of the array.
- The second line contains `N` space-separated integers representing the target array.

---

## Output Format

Print a single integer representing the minimum number of operations.

---

## Constraints

```text
1 ≤ N ≤ 10^5
1 ≤ target[i] ≤ 10^4
```

---

# Example 1

## Input

```text
5
1 2 2 1 1
```

## Output

```text
4
```

## Explanation

Initially

```text
[0,0,0,0,0]
```

Operations

```text
Increment [0,1]
→ [1,1,0,0,0]

Increment [1,2]
→ [1,2,1,0,0]

Increment [2,3]
→ [1,2,2,1,0]

Increment [4]
→ [1,2,2,1,1]
```

Minimum operations = **4**

---

# Example 2

## Input

```text
4
2 2 2 2
```

## Output

```text
4
```

## Explanation

Operations

```text
Increment [0,1]
Increment [0,1]

Current
[2,2,0,0]

Increment [2,3]
Increment [2,3]

Current
[2,2,2,2]
```

Minimum operations = **4**

---

# Example 3

## Input

```text
5
3 1 2 2 1
```

## Output

```text
5
```

---

# Greedy Idea

Process the array from **left to right**.

Maintain another array `curr[]` representing the values constructed so far.

At every index:

```
need = target[i] - curr[i]
```

If

```
need > 0
```

perform `need` operations.

If there is a next element, use a subarray of length **2**.

Otherwise, use a subarray of length **1**.

This is always optimal because one operation on two adjacent elements helps build both positions simultaneously.

---

# Algorithm

```
curr[] = all zeros
answer = 0

for every index i

    need = target[i] - curr[i]

    if need <= 0
        continue

    answer += need

    curr[i] += need

    if i+1 exists
        curr[i+1] += need
```

---

# Java Solution

```java
import java.util.*;

class Solution {

    static long minimumOperations(int[] target) {

        int n = target.length;

        int[] curr = new int[n];

        long ans = 0;

        for (int i = 0; i < n; i++) {

            int need = target[i] - curr[i];

            if (need <= 0)
                continue;

            ans += need;

            curr[i] += need;

            if (i + 1 < n)
                curr[i + 1] += need;
        }

        return ans;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();

        int[] target = new int[n];

        for (int i = 0; i < n; i++)
            target[i] = sc.nextInt();

        System.out.println(minimumOperations(target));
    }
}
```

---

# Dry Run

Input

```text
5
1 2 2 1 1
```

Initially

```text
curr = [0,0,0,0,0]
```

|Index|Target|Current|Need|Operation|Current Array|Answer|
|---:|---:|---:|---:|---|---|---:|
|0|1|0|1|Increment [0,1]|[1,1,0,0,0]|1|
|1|2|1|1|Increment [1,2]|[1,2,1,0,0]|2|
|2|2|1|1|Increment [2,3]|[1,2,2,1,0]|3|
|3|1|1|0|None|[1,2,2,1,0]|3|
|4|1|0|1|Increment [4]|[1,2,2,1,1]|4|

Answer

```text
4
```

---

# Complexity

### Time

```text
O(N)
```

### Space

```text
O(N)
```

Can be optimized to **O(1)**.

---

# Similar Problems

Although this exact problem is not publicly available, it is closely related to:

- **LeetCode 1526 – Minimum Number of Increments on Subarrays to Form a Target Array**
  - Difference:
    - LeetCode allows incrementing **any length subarray**.
    - This problem allows incrementing only **subarrays of length 1 or 2**.

---

# Key Observation

Always prefer using a **length-2 subarray** whenever possible because a single operation contributes to **two adjacent elements** simultaneously.

Only use a **length-1 subarray** for the last element or when necessary.

---

# Revision Notes

✅ Initial array is all zeros.

✅ One operation increments a contiguous subarray of length **1 or 2**.

✅ Process from left to right.

✅ Compute

```text
need = target[i] - curr[i]
```

✅ If `need > 0`

- Increase answer by `need`.
- Update current index.
- Also update the next index if it exists.

**Time Complexity:** `O(N)`

**Space Complexity:** `O(N)`
