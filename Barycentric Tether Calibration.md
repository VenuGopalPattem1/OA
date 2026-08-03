# Barycentric Tether Calibration

## Problem Description

You are the flight director of the **Aegis Deep Space Orbital Platform**.

The platform has **N** deployed satellites, where the `i-th` satellite has a structural mass `M[i]` (in tons).

To establish communication relays, you must connect **pairs of distinct satellites** using carbon nanotube tethers.

For a tether to remain stable, the pair must produce an **Effective Anchor Mass** equal to **S**.

The Effective Anchor Mass of two satellites with masses **A** and **B** is defined as:

```text
Effective Anchor Mass = 2 × min(A, B) + max(A, B)
```

Your task is to determine the number of **unordered pairs of distinct satellites** whose Effective Anchor Mass is exactly **S**.

---

# Rules

* Each pair consists of two different satellites.
* `(i, j)` and `(j, i)` represent the **same pair**.
* If two satellites have the same mass, they can still form a valid pair if:

```text
3 × mass = S
```

---

# Input Format

* The first line contains two integers:

```text
N S
```

* The second line contains `N` integers representing the masses of the satellites.

---

# Output Format

Print the total number of valid unordered pairs.

---

# Constraints

```text
1 ≤ N ≤ 2 × 10^5
1 ≤ M[i], S ≤ 10^9
```

---

# Test Case 1

### Input

```text
6 10

2 6 4 4 1 8
```

### Output

```text
2
```

### Explanation

Valid pairs are:

* (2,6) → 2×2+6 = 10
* (1,8) → 2×1+8 = 10

Hence the answer is **2**.

---

# Test Case 2

### Input

```text
5 9

3 3 3 5 2
```

### Output

```text
3
```

### Explanation

Since

```text
2×3+3 = 9
```

Every pair of satellites having mass `3` is valid.

There are three such satellites.

Number of pairs:

```text
3C2 = 3
```

Hence the answer is **3**.

---

# Test Case 3

### Input

```text
7 10

2 6 4 4 1 8 2
```

### Output

```text
3
```

### Explanation

Valid pairs are

```text
(2,6)
(2,6)
(1,8)
```

There are two satellites with mass `2`, so both can pair with the satellite of mass `6`.

Hence the answer is **3**.

---

# Brute Force Approach

Try every pair of satellites.

For each pair:

1. Compute

```text
2 × min(A,B) + max(A,B)
```

2. If it equals `S`, increment the answer.

---

# Brute Force Code

```java
class Solution {

    public int countPairs(int[] mass, int S) {

        int n = mass.length;
        int ans = 0;

        for(int i = 0; i < n; i++) {

            for(int j = i + 1; j < n; j++) {

                int small = Math.min(mass[i], mass[j]);
                int large = Math.max(mass[i], mass[j]);

                if(2 * small + large == S)
                    ans++;
            }
        }

        return ans;
    }
}
```

---

## Complexity

**Time**

```text
O(N²)
```

**Space**

```text
O(1)
```

---

# Optimal Approach

Instead of checking every pair, store the frequency of every mass.

Suppose the smaller mass is `a`.

From

```text
2 × a + b = S
```

we can compute

```text
b = S − 2 × a
```

For every unique mass:

* Find its corresponding partner.
* If both exist, count the pairs.
* If both masses are equal, use the combination formula.

---

# Algorithm

```text
Create a frequency map.

For every unique mass a

    b = S − 2*a

    If b < a
        Skip

    If b does not exist
        Skip

    If a == b

        Add freq(a) choose 2

    Else

        Add freq(a) × freq(b)
```

---

# Java Code (Optimal)

```java
import java.util.*;

class Solution {

    public long countPairs(int[] mass, int S) {

        HashMap<Integer,Integer> map = new HashMap<>();

        for(int x : mass)
            map.put(x, map.getOrDefault(x,0)+1);

        long ans = 0;

        for(int a : map.keySet()) {

            int b = S - 2 * a;

            if(b < a)
                continue;

            if(!map.containsKey(b))
                continue;

            if(a == b) {

                long f = map.get(a);
                ans += f * (f - 1) / 2;

            } else {

                ans += 1L * map.get(a) * map.get(b);
            }
        }

        return ans;
    }
}
```

---

# Dry Run

Consider

```text
mass = [2,6,4,4,1,8,2]

S = 10
```

Frequency map:

| Mass | Frequency |
| ---- | --------: |
| 1    |         1 |
| 2    |         2 |
| 4    |         2 |
| 6    |         1 |
| 8    |         1 |

Initially

```text
ans = 0
```

### a = 1

```text
b = 10 − 2×1 = 8
```

Both exist.

```text
ans += 1 × 1 = 1
```

Current answer

```text
1
```

---

### a = 2

```text
b = 10 − 4 = 6
```

Both exist.

```text
ans += 2 × 1 = 2
```

Current answer

```text
3
```

---

### a = 4

```text
b = 2
```

Since

```text
b < a
```

skip.

This prevents counting `(2,4)` after `(4,2)`.

---

### a = 6

```text
b = -2
```

Skip.

---

### a = 8

```text
b = -6
```

Skip.

Final answer

```text
3
```

---

# Why do we use

```java
if(b < a)
    continue;
```

Suppose

```text
a = 2

b = 6
```

The pair `(2,6)` is counted.

Later, if we processed `(6,2)`, it would represent the same unordered pair.

The condition

```text
b < a
```

ensures that each unordered pair is counted exactly once.

---

# Why do we use

```java
f × (f − 1) / 2
```

Suppose

```text
mass = [3,3,3]

S = 9
```

Since

```text
2×3+3 = 9
```

every pair is valid.

Frequency

```text
f = 3
```

Number of unordered pairs

```text
3C2

= 3 × 2 / 2

= 3
```

Hence

```java
f * (f - 1) / 2
```

is used.

---

# Complexity Analysis

### Time Complexity

Building the frequency map

```text
O(N)
```

Iterating through unique masses

```text
O(N)
```

Overall

```text
O(N)
```

---

### Space Complexity

Frequency map

```text
O(N)
```

---

# Related Problems

* Two Sum
* 3Sum
* LeetCode 167 – Two Sum II
* LeetCode 1 – Two Sum
* GeeksforGeeks – Count Pairs with Given Sum
* GeeksforGeeks – Count Equal Pairs
* Frequency Map Based Pair Counting

---

# Concepts Used

* HashMap
* Frequency Counting
* Greedy Observation
* Pair Counting
* Combinatorics (`nC2`)
* Time Optimization
