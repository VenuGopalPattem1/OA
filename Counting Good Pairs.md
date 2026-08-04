# Counting Good Pairs

## Problem Description

You are given an integer array `nums` of size `N` and two integers `M` and `D`.

Your task is to count the number of **good pairs** of indices `(i, j)` such that:

* `i < j`
* `(nums[i] + nums[j]) % M == 0`
* `(nums[i] - nums[j]) % D == 0`

Return the total number of good pairs.

---

# Input Format

* First line contains an integer `N`, representing the size of the array.
* Second line contains `N` space-separated integers representing the array `nums`.
* Third line contains an integer `M`.
* Fourth line contains an integer `D`.

---

# Output Format

Print a single integer representing the number of good pairs.

---

# Constraints

```text
1 ≤ N ≤ 10^5
1 ≤ nums[i] ≤ 10^9
1 ≤ M, D ≤ 10^9
```

---

# Test Case 1

### Input

```text
N = 5

nums = [2, 4, 6, 8, 10]

M = 4

D = 2
```

### Output

```text
4
```

### Explanation

Valid pairs are:

| Pair   | Sum | Sum % 4 | Difference | Difference % 2 |
| ------ | --: | ------: | ---------: | -------------: |
| (2,6)  |   8 |       0 |         -4 |              0 |
| (2,10) |  12 |       0 |         -8 |              0 |
| (4,8)  |  12 |       0 |         -4 |              0 |
| (6,10) |  16 |       0 |         -4 |              0 |

Total good pairs = **4**

---

# Test Case 2

### Input

```text
N = 6

nums = [1, 3, 5, 7, 9, 11]

M = 4

D = 2
```

### Output

```text
6
```

### Explanation

All numbers are odd, so every difference is divisible by `2`.

Valid pairs satisfying both conditions are:

* (1,3)
* (1,11)
* (3,9)
* (5,7)
* (5,11)
* (7,9)

Hence, the answer is **6**.

---

# Test Case 3

### Input

```text
N = 5

nums = [2, 5, 8, 11, 14]

M = 3

D = 3
```

### Output

```text
10
```

### Explanation

Every number has remainder `2` when divided by `3`.

Therefore:

* Every difference is divisible by `3`.
* Every sum is also divisible by `3`.

Hence every pair is valid.

Total pairs:

```text
5C2 = 10
```

---

# Brute Force Approach

Try every pair `(i, j)` where `i < j`.

For each pair:

1. Check

```text
(nums[i] + nums[j]) % M == 0
```

2. Check

```text
(nums[i] - nums[j]) % D == 0
```

3. If both conditions are true, increment the answer.

---

# Brute Force Code

```java
class Solution {

    public int countGoodPairs(int[] nums, int M, int D) {

        int n = nums.length;
        int ans = 0;

        for (int i = 0; i < n; i++) {

            for (int j = i + 1; j < n; j++) {

                if ((nums[i] + nums[j]) % M == 0 &&
                    (nums[i] - nums[j]) % D == 0) {

                    ans++;
                }
            }
        }

        return ans;
    }
}
```

---

## Complexity

**Time Complexity**

```text
O(N²)
```

**Space Complexity**

```text
O(1)
```

---

# Optimal Approach

Instead of checking every pair, observe the conditions.

## Observation 1

For

```text
(nums[i] + nums[j]) % M == 0
```

If

```text
nums[i] % M = r
```

then

```text
nums[j] % M = (M - r) % M
```

---

## Observation 2

For

```text
(nums[i] - nums[j]) % D == 0
```

both numbers must have the **same remainder modulo `D`**.

```text
nums[i] % D == nums[j] % D
```

---

## Idea

While traversing the array:

For every number,

Compute

```text
remM = num % M

remD = num % D
```

The current number needs a previous number with:

```text
needM = (M - remM) % M

needD = remD
```

Store every previously seen pair:

```text
(remM, remD)
```

inside a HashMap.

Whenever the required pair already exists, add its frequency to the answer.

---

# Algorithm

```text
Create a HashMap

For every number

    remM = num % M

    remD = num % D

    needM = (M - remM) % M

    Answer += frequency of (needM, remD)

    Store (remM, remD)

Return answer
```

---

# Optimal Java Code

```java
import java.util.HashMap;
import java.util.Map;

class Solution {

    public long countGoodPairs(int[] nums, int M, int D) {

        Map<String, Integer> map = new HashMap<>();

        long ans = 0;

        for (int num : nums) {

            int remM = num % M;
            int remD = num % D;

            int needM = (M - remM) % M;

            String needKey = needM + "#" + remD;

            ans += map.getOrDefault(needKey, 0);

            String currKey = remM + "#" + remD;

            map.put(currKey, map.getOrDefault(currKey, 0) + 1);
        }

        return ans;
    }

    public static void main(String[] args) {

        int[] nums = {2,4,6,8,10};

        Solution obj = new Solution();

        System.out.println(obj.countGoodPairs(nums,4,2));
    }
}
```

---

# Dry Run

### Input

```text
nums = [2,4,6,8,10]

M = 4

D = 2
```

| Number | remM | remD | Need (M,D) | Previous Count | Answer |
| -----: | ---: | ---: | ---------: | -------------: | -----: |
|      2 |    2 |    0 |      (2,0) |              0 |      0 |
|      4 |    0 |    0 |      (0,0) |              0 |      0 |
|      6 |    2 |    0 |      (2,0) |              1 |      1 |
|      8 |    0 |    0 |      (0,0) |              1 |      2 |
|     10 |    2 |    0 |      (2,0) |              2 |      4 |

Final Answer:

```text
4
```

---

# Why Does This Work?

A valid pair must satisfy two independent conditions:

* Their remainders modulo `M` must complement each other so that the sum is divisible by `M`.
* Their remainders modulo `D` must be identical so that the difference is divisible by `D`.

The HashMap stores the frequency of every `(remainder modulo M, remainder modulo D)` pair that has already appeared.

For each new element, we immediately know how many previous elements can form a valid pair with it.

Thus, every valid pair is counted exactly once.

---

# Complexity Analysis

### Time Complexity

Each element is processed once.

```text
O(N)
```

### Space Complexity

HashMap stores at most one entry for each distinct remainder pair.

```text
O(N)
```

---

# Similar Problems

* LeetCode 1 – Two Sum
* LeetCode 1497 – Check If Array Pairs Are Divisible by k
* LeetCode 1010 – Pairs of Songs With Total Durations Divisible by 60
* GeeksforGeeks – Count Pairs Divisible by K
* GeeksforGeeks – Pair Sum Problems Using HashMap

---

# Concepts Used

* HashMap
* Modular Arithmetic
* Frequency Counting
* Pair Counting
* Number Theory
* Time Optimization
