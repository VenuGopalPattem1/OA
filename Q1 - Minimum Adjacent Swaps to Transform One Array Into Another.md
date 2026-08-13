# Infosys Q1 - Minimum Adjacent Swaps to Transform One Array Into Another

## Problem Description

You are given two arrays A and B of the same length. Both arrays contain the same elements, but possibly in different orders.

You need to transform array A into array B using the minimum number of swaps.

The only allowed operation is swapping two adjacent elements.

Return the minimum number of adjacent swaps required.

Example:

A = [1, 2, 3, 4]
B = [2, 1, 4, 3]

We can transform A into B:

[1, 2, 3, 4]
[2, 1, 3, 4]    -> swap 1 and 2
[2, 1, 4, 3]    -> swap 3 and 4

Answer = 2


## Test Case 1

Input:

A = [1, 2, 3, 4]
B = [2, 1, 4, 3]

Output:

2

Transformation:

[1, 2, 3, 4]
[2, 1, 3, 4]
[2, 1, 4, 3]


## Test Case 2

Input:

A = [1, 2, 3]
B = [3, 1, 2]

Output:

2

Transformation:

[1, 2, 3]
[1, 3, 2]
[3, 1, 2]


## Approach

Process array B from left to right.

For every index i:

1. We need B[i] at position i.
2. Search for B[i] in array A starting from index i.
3. Suppose it is found at index j.
4. Move A[j] to index i using adjacent swaps.
5. Count every adjacent swap.

If the required element is currently at index j, moving it to index i requires exactly:

j - i

adjacent swaps.

Why?

Because the element has to cross every element between positions i and j.

For example:

A = [1, 2, 3, 4]
Need = 4

4 is at index 3 and we need it at index 2.

One adjacent swap is required:

[1, 2, 3, 4]
       ↓
[1, 2, 4, 3]

Therefore, the cost is:

3 - 2 = 1


## Java Solution

class Solution {
    public int minSwaps(int[] a, int[] b) {
        int n = a.length;
        int ans = 0;

        for (int i = 0; i < n; i++) {

            // Find B[i] in A
            int j = i;

            while (j < n && a[j] != b[i]) {
                j++;
            }

            // Move A[j] to position i
            while (j > i) {

                int temp = a[j];
                a[j] = a[j - 1];
                a[j - 1] = temp;

                ans++;
                j--;
            }
        }

        return ans;
    }
}


## Dry Run

Input:

A = [1, 2, 3, 4]
B = [2, 1, 4, 3]

Initially:

A = [1, 2, 3, 4]
ans = 0


### i = 0

We need:

B[0] = 2

Search for 2 in A.

2 is at index 1.

Current:

[1, 2, 3, 4]

Move 2 from index 1 to index 0:

[2, 1, 3, 4]

One swap is required.

ans = 1


### i = 1

We need:

B[1] = 1

Current:

[2, 1, 3, 4]

1 is already at index 1.

No swap required.

ans = 1


### i = 2

We need:

B[2] = 4

Current:

[2, 1, 3, 4]

4 is at index 3.

Move 4 from index 3 to index 2:

[2, 1, 4, 3]

One swap is required.

ans = 2


### i = 3

We need:

B[3] = 3

Current:

[2, 1, 4, 3]

3 is already at index 3.

No swap required.

ans = 2


Final Answer:

2


## Why Is This Minimum?

Suppose the required element B[i] is currently at position j.

To move it to position i, it must cross all elements between i and j.

There are:

j - i

elements to cross.

Since only adjacent swaps are allowed, each crossing requires exactly one swap.

Therefore:

cost = j - i

The greedy algorithm performs exactly these necessary swaps and fixes one position at a time.


## Important Observation

We should process B from left to right.

Once position i is correct, we never need to change it again.

For every position:

Find the required element
        ↓
Move it to the current position
        ↓
Count the swaps
        ↓
Move to the next position


## Complexity

For every position, we may scan the remaining array to find the required element.

The shifting operation can also take O(N).

Therefore:

Time Complexity = O(N²)

Space Complexity = O(1)


## Difficulty

Easy to Medium

Approximate competitive programming level:

1000 - 1200


## Similar Practice Problems

1. GeeksforGeeks - Convert One Array to Another Using Adjacent Swaps

https://www.geeksforgeeks.org/dsa/convert-one-array-to-another-using-adjacent-swaps-of-elements/

2. GeeksforGeeks - Minimum Number of Swaps Needed

https://www.geeksforgeeks.org/problems/minimum-number-of-swaps-needed2136/0

3. GeeksforGeeks - Number of Swaps to Sort When Only Adjacent Swapping Is Allowed

https://www.geeksforgeeks.org/dsa/number-swaps-sort-adjacent-swapping-allowed/

4. GeeksforGeeks - Minimum Adjacent Swaps to Sort Binary Array

https://www.geeksforgeeks.org/dsa/minimum-swaps-required-sort-binary-array/


## Key Pattern

Process B from left to right.

For every B[i]:

1. Find B[i] in A.
2. Let its current position be j.
3. Move it left until it reaches position i.
4. Add j - i to the answer.
5. Continue.

The core idea is:

Minimum adjacent swaps = number of positions the required element has to move.
