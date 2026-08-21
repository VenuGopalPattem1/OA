# Q3. Longest Increasing Subsequence with Excluded Sum Limit

## Problem

You are given:

- An integer `N`
- An integer `K`
- An integer array `A` of size `N`

Find the length of the **longest strictly increasing subsequence** such that the sum of the elements excluded from the subsequence is at most `K`.

A subsequence must maintain the original order of the array.

The selected elements must satisfy:

```text
A[i1] < A[i2] < A[i3] < ...
```

The condition is:

```text
sum of excluded elements <= K
```

## Example 1

```text
N = 6
K = 7
A = [3, 1, 2, 5, 4, 6]

Output = 4
```

One valid subsequence is:

```text
[1, 2, 5, 6]
```

Selected sum:

```text
1 + 2 + 5 + 6 = 14
```

Total sum:

```text
3 + 1 + 2 + 5 + 4 + 6 = 21
```

Excluded sum:

```text
21 - 14 = 7
```

Since `7 <= K`, the subsequence is valid.

Therefore:

```text
Answer = 4
```

## Example 2

```text
N = 5
K = 5
A = [1, 2, 3, 4, 5]

Output = 4
```

Choose:

```text
[1, 2, 3, 4]
```

Excluded element:

```text
5
```

Excluded sum:

```text
5 <= K
```

Therefore:

```text
Answer = 4
```

## Example 3

```text
N = 5
K = 6
A = [5, 1, 2, 3, 4]

Output = 4
```

Choose:

```text
[1, 2, 3, 4]
```

Excluded element:

```text
5
```

Excluded sum:

```text
5 <= 6
```

The subsequence is strictly increasing.

Therefore:

```text
Answer = 4
```

## Important Transformation

Let:

```text
totalSum = sum of all elements
selectedSum = sum of selected elements
excludedSum = sum of excluded elements
```

Since every element is either selected or excluded:

```text
excludedSum = totalSum - selectedSum
```

The condition is:

```text
excludedSum <= K
```

Therefore:

```text
totalSum - selectedSum <= K
```

Rearranging:

```text
selectedSum >= totalSum - K
```

So we define:

```text
target = totalSum - K
```

Now the problem becomes:

> Find the longest strictly increasing subsequence whose selected sum is at least `target`.

## Effect of Increasing K

If `K` increases, the target decreases.

For example:

```text
totalSum = 20

K = 5
target = 15

K = 8
target = 12
```

Therefore:

```text
Larger K
    ↓
Smaller target
    ↓
More elements can potentially be excluded
    ↓
More subsequences can become valid
    ↓
Maximum length can stay same or increase
```

We are **not minimizing K**.

`K` is the given limit. We are maximizing the subsequence length while keeping:

```text
excludedSum <= K
```

## Memoization Idea

Define:

```text
fun(i, prev, sum)
```

where:

- `i` = current index
- `prev` = index of the previously selected element
- `sum` = sum of selected elements

Initially, no element has been selected.

Instead of using `-1` for `prev`, use:

```text
prev = N
```

So:

```text
fun(0, N, 0)
```

Here `N` is a dummy previous index.

## Choices

At every index we have two choices.

### Do Not Take

```text
fun(i + 1, prev, sum)
```

### Take

We can take `A[i]` when:

```text
prev == N
```

or:

```text
A[i] > A[prev]
```

Then:

```text
1 + fun(i + 1, i, sum + A[i])
```

Therefore:

```text
fun(i, prev, sum) = max(take, notTake)
```

## Base Case

When:

```text
i == N
```

we have processed the entire array.

If:

```text
sum >= target
```

the selected subsequence satisfies the excluded-sum condition.

Return:

```text
0
```

Otherwise the path is invalid:

```text
-1000000
```

## Java Memoization Solution

```java
class Main {

    static Integer[][][] dp;

    public static void main(String[] args) {

        int[] a = {3, 1, 2, 5, 4, 6};
        int k = 7;

        int n = a.length;

        int totalSum = 0;

        for(int x : a) {
            totalSum += x;
        }

        int target = totalSum - k;

        dp = new Integer[n + 1][n + 1][totalSum + 1];

        System.out.println(fun(0, n, 0, a, target));
    }

    public static int fun(int i, int prev, int sum,
                          int[] a, int target) {

        if(i == a.length) {

            if(sum >= target) {
                return 0;
            }

            return -1000000;
        }

        if(dp[i][prev][sum] != null) {
            return dp[i][prev][sum];
        }

        // Do not take
        int notTake = fun(i + 1, prev, sum, a, target);

        // Take
        int take = -1000000;

        if(prev == a.length || a[i] > a[prev]) {

            take = 1 + fun(
                i + 1,
                i,
                sum + a[i],
                a,
                target
            );
        }

        return dp[i][prev][sum] = Math.max(take, notTake);
    }
}
```

## Dry Run

For:

```text
A = [3, 1, 2, 5, 4, 6]
K = 7
```

```text
totalSum = 21
target = 21 - 7 = 14
```

Choose:

```text
[1, 2, 5, 6]
```

Selected sum:

```text
1 + 2 + 5 + 6 = 14
```

Excluded elements:

```text
3, 4
```

Excluded sum:

```text
3 + 4 = 7
```

Since:

```text
7 <= K
```

the subsequence is valid.

Length:

```text
4
```

Therefore:

```text
Answer = 4
```

## Complexity

```text
Time  : O(N² × totalSum)
Space : O(N² × totalSum)
```

## Key Pattern

This is a combination of:

```text
Longest Increasing Subsequence
+
Subsequence Sum Constraint
+
Memoization
```

The important transformation is:

```text
excludedSum <= K
```

becomes:

```text
totalSum - selectedSum <= K
```

which becomes:

```text
selectedSum >= totalSum - K
```

Therefore:

```text
target = totalSum - K
```

The memoization state is:

```text
fun(index, previousIndex, selectedSum)
```

with:

```text
previousIndex = N
```

used as the initial dummy previous index.

## Related Problems

- LeetCode 300 - Longest Increasing Subsequence
- LeetCode 2915 - Length of the Longest Subsequence That Sums to Target

https://leetcode.com/problems/longest-increasing-subsequence/

https://leetcode.com/problems/length-of-the-longest-subsequence-that-sums-to-target/
