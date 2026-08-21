# Q2. Weighted Balanced Substrings

## Problem

You are given a string `S` of length `N` containing only two characters `L` and `R`.

You are also given an integer array `weight` of length `N`, where `weight[i]` is the weight associated with `S[i]`.

You need to split the string into the maximum number of contiguous substrings such that, for every substring:

```text
Sum of weights of L = Sum of weights of R
```

Return the maximum number of such balanced substrings.

## Example 1

```text
S = "LRLR"
weight = [2, 2, 3, 3]

Output = 2
```

We can split the string as:

```text
"LR" | "LR"
```

First substring:

```text
L sum = 2
R sum = 2
```

Second substring:

```text
L sum = 3
R sum = 3
```

Both substrings are balanced.

Therefore:

```text
Answer = 2
```

## Example 2

```text
S = "LLRR"
weight = [2, 3, 1, 4]

Output = 1
```

The complete string is:

```text
"LLRR"
```

Weight of `L`:

```text
2 + 3 = 5
```

Weight of `R`:

```text
1 + 4 = 5
```

Therefore the complete string is balanced.

```text
Answer = 1
```

## Approach

Maintain a variable called `balance`.

```text
balance = sum of L weights - sum of R weights
```

For every character:

```text
If S[i] == 'L':
    balance += weight[i]

If S[i] == 'R':
    balance -= weight[i]
```

Whenever:

```text
balance == 0
```

the current substring has equal total weights of `L` and `R`.

Therefore, increment the answer.

## Dry Run

For:

```text
S = "LRLR"
weight = [2, 2, 3, 3]
```

| i | S[i] | weight[i] | balance | count |
|---|------|-----------|---------|-------|
| 0 | L | 2 | 2 | 0 |
| 1 | R | 2 | 0 | 1 |
| 2 | L | 3 | 3 | 1 |
| 3 | R | 3 | 0 | 2 |

Final answer:

```text
2
```

## Java Solution

```java
class Main {

    public static void main(String[] args) {

        String s = "LRLR";
        int[] weight = {2, 2, 3, 3};

        System.out.println(fun(s, weight));
    }

    public static int fun(String s, int[] weight) {

        int balance = 0;
        int count = 0;

        for(int i = 0; i < s.length(); i++) {

            if(s.charAt(i) == 'L') {
                balance += weight[i];
            } else {
                balance -= weight[i];
            }

            if(balance == 0) {
                count++;
            }
        }

        return count;
    }
}
```

## Complexity

```text
Time  : O(N)
Space : O(1)
```

## Key Pattern

This is a simple **Greedy / Prefix Balance** problem.

```text
L -> +weight
R -> -weight
```

Whenever:

```text
balance == 0
```

we have found one valid balanced substring.

We do not need to explicitly create the substrings. We only maintain the running balance.

## Related Problem

**LeetCode 1221 - Split a String in Balanced Strings**

https://leetcode.com/problems/split-a-string-in-balanced-strings/

In LeetCode 1221, balance is based on the count of `L` and `R`.

Here, balance is based on the **sum of their weights**.
