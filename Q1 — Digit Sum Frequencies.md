# Q1 — Digit Sum Frequencies

## Problem Description

Given an array `A` of size `N`, calculate the digit sum of every element.

The **digit sum** of a number is the sum of all its digits.

For every digit sum `s`, calculate its frequency:

```text
c[s] = number of elements whose digit sum is s
```

Then calculate:

```text
Answer = Σ (A[i] * c[digitSum(A[i])])
```

Return the answer modulo:

```text
10^9 + 7
```

### Example

```text
A = [12, 23, 15]
```

Digit sums:

```text
12 → 1 + 2 = 3
23 → 2 + 3 = 5
15 → 1 + 5 = 6
```

Frequencies:

```text
c[3] = 1
c[5] = 1
c[6] = 1
```

Therefore:

```text
Answer = 12 * 1 + 23 * 1 + 15 * 1
       = 50
```

---

## Test Case 1

```text
Input:
A = [12, 23, 15]

Output:
50
```

Explanation:

```text
12 → digit sum = 3 → frequency = 1
23 → digit sum = 5 → frequency = 1
15 → digit sum = 6 → frequency = 1

Answer = 12*1 + 23*1 + 15*1
       = 50
```

---

## Test Case 2

```text
Input:
A = [12, 21, 30, 14]

Output:
203
```

Digit sums:

```text
12 → 3
21 → 3
30 → 3
14 → 5
```

Frequencies:

```text
c[3] = 3
c[5] = 1
```

Therefore:

```text
Answer = 12*3 + 21*3 + 30*3 + 14*1
       = 36 + 63 + 90 + 14
       = 203
```

---

# Solution 1 — HashMap

### Idea

Use a `HashMap` where:

```text
key   = digit sum
value = frequency
```

First calculate the frequency of every digit sum.

Then iterate through the array again and calculate:

```text
A[i] * frequency[digitSum(A[i])]
```

### Java Code

```java
class Solution {

    static final long MOD = 1_000_000_007;

    public long solve(int[] A) {

        HashMap<Integer, Integer> freq = new HashMap<>();

        // Calculate frequency of each digit sum
        for (int x : A) {

            int sum = digitSum(x);

            freq.put(
                sum,
                freq.getOrDefault(sum, 0) + 1
            );
        }

        long ans = 0;

        // Calculate answer
        for (int x : A) {

            int sum = digitSum(x);

            ans = (ans + (long) x * freq.get(sum)) % MOD;
        }

        return ans;
    }

    private int digitSum(int x) {

        int sum = 0;

        while (x > 0) {
            sum += x % 10;
            x /= 10;
        }

        return sum;
    }
}
```

### Complexity

```text
Time  : O(N * D)
Space : O(D)
```

Where `D` is the number of digits in each number.

---

# Solution 2 — Frequency Array

If the maximum possible digit sum is known, we can use an array instead of a `HashMap`.

For example, if every number has at most 10 digits:

```text
Maximum digit sum = 9 * 10 = 90
```

So:

```java
int[] freq = new int[91];
```

### Java Code

```java
class Solution {

    static final long MOD = 1_000_000_007;

    public long solve(int[] A) {

        int[] freq = new int[91];

        // Calculate frequency
        for (int x : A) {

            int sum = digitSum(x);

            freq[sum]++;
        }

        long ans = 0;

        // Calculate answer
        for (int x : A) {

            int sum = digitSum(x);

            ans = (ans + (long) x * freq[sum]) % MOD;
        }

        return ans;
    }

    private int digitSum(int x) {

        int sum = 0;

        while (x > 0) {
            sum += x % 10;
            x /= 10;
        }

        return sum;
    }
}
```

### Complexity

```text
Time  : O(N * D)
Space : O(D)
```

---

# Key Idea

We do not need to compare every pair of elements.

First calculate:

```text
A[i]
  ↓
digitSum(A[i])
  ↓
frequency[digitSum]
  ↓
A[i] * frequency[digitSum]
  ↓
Add to answer
```

The main pattern is:

```text
Calculate digit sum
        ↓
Count frequency
        ↓
Use frequency for every element
        ↓
Calculate final sum
```

---

# Important Edge Cases

### Single Element

```text
A = [123]
```

Digit sum:

```text
1 + 2 + 3 = 6
```

Frequency:

```text
c[6] = 1
```

Answer:

```text
123
```

### Same Digit Sum

```text
A = [12, 21, 30]
```

All have digit sum `3`.

Therefore:

```text
c[3] = 3
```

Answer:

```text
12*3 + 21*3 + 30*3
= 189
```

---

# Similar Practice Problems

1. GeeksforGeeks — Sum of Digits
   https://www.geeksforgeeks.org/dsa/program-for-sum-of-the-digits-of-a-number/

2. LeetCode 258 — Add Digits
   https://leetcode.com/problems/add-digits/

3. LeetCode 1748 — Sum of Unique Elements
   https://leetcode.com/problems/sum-of-unique-elements/

4. LeetCode 1941 — Check if All Characters Have Equal Number of Occurrences
   https://leetcode.com/problems/check-if-all-characters-have-equal-number-of-occurrences/

---

# Quick Revision

```text
Input:
Array A

Step 1:
Calculate digitSum(A[i])

Step 2:
Store frequency of each digit sum

Step 3:
For every A[i]:

answer += A[i] * frequency[digitSum(A[i])]

Step 4:
Return answer % (10^9 + 7)

Approaches:
1. HashMap
2. Frequency Array

Time:
O(N * D)

Space:
O(D)
```
