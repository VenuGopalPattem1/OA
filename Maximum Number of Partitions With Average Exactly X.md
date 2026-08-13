# Maximum Number of Partitions With Average Exactly X

## Problem Statement

Given an integer array `A` of size `N` and an integer `X`, partition the array into the maximum possible number of contiguous, non-empty subarrays such that the average of every partition is exactly `X`.

For every partition:

average = X

We need to return the maximum number of valid partitions.

Every element must belong to exactly one partition, and the partitions must contain contiguous elements.

## Example 1

Input:

A = [1, 5, 3, 2, 4, 3]
X = 3

Output:

4

Partition:

[1, 5] | [3] | [2, 4] | [3]

Check:

[1, 5] -> (1 + 5) / 2 = 3
[3]    -> 3 / 1 = 3
[2, 4] -> (2 + 4) / 2 = 3
[3]    -> 3 / 1 = 3

Therefore the answer is 4.

## Example 2

Input:

A = [4, 4, 4, 4]
X = 4

Output:

4

Partition:

[4] | [4] | [4] | [4]

Every partition has average 4.

Therefore the answer is 4.

## Example 3

Input:

A = [2, 4, 6, 3, 3]
X = 4

Output:

1

The first three elements form a valid partition:

[2, 4, 6]

Average:

(2 + 4 + 6) / 3 = 12 / 3 = 4

But:

[3, 3]

has average:

(3 + 3) / 2 = 3

So we cannot make another valid partition.

Therefore the answer is 1.

# Key Observation

For every valid partition:

average = X

We know:

average = sum / length

Therefore:

sum / length = X

Multiply both sides by length:

sum = X * length

Move everything to one side:

sum - X * length = 0

Suppose a partition contains:

a1, a2, a3, ..., ak

Then:

(a1 + a2 + a3 + ... + ak) - X * k = 0

This can be written as:

(a1 - X) + (a2 - X) + ... + (ak - X) = 0

This is the main trick.

# Transformation

For every element, calculate:

B[i] = A[i] - X

Now the original problem becomes:

Find the maximum number of contiguous partitions whose sum is exactly 0.

For example:

A = [1, 5, 3, 2, 4, 3]
X = 3

Transform every element:

1 - 3 = -2
5 - 3 = 2
3 - 3 = 0
2 - 3 = -1
4 - 3 = 1
3 - 3 = 0

Therefore:

B = [-2, 2, 0, -1, 1, 0]

Now we need to split this transformed array into the maximum number of zero-sum partitions.

# Greedy Approach

Maintain a running sum of the transformed elements.

For every element:

sum += A[i] - X

Whenever:

sum == 0

we can finish the current partition and increase the answer.

Why does this work?

Suppose the current partition contains elements from index `l` to `i`.

The running sum is:

(A[l] - X) + (A[l + 1] - X) + ... + (A[i] - X)

If this is zero:

sum = 0

Then:

A[l] + A[l + 1] + ... + A[i] - X * length = 0

Therefore:

partitionSum = X * length

So:

partitionSum / length = X

Therefore the current partition has average exactly `X`.

# Why Greedy Works

We want the maximum number of partitions.

Whenever the current running sum becomes zero, the elements from the previous partition boundary up to the current index already form a valid partition.

There is no benefit in extending this partition further because we want to maximize the number of partitions.

Therefore, we immediately cut the partition whenever:

sum == 0

Then we start calculating the next partition.

This gives the maximum possible number of valid partitions.

# Java Solution

```java
class Solution {
    public int maxPartitions(int[] a, int X) {
        int sum = 0;
        int count = 0;

        for (int num : a) {
            sum += num - X;

            if (sum == 0) {
                count++;
            }
        }

        return count;
    }
}
