# Last Stone Weight II — Resonance Variant

## Problem Description

Given an array `stones` of `N` weights and an integer `R` called the resonance value, assign every stone to either Set A or Set B.

The weight of each set is:

```text
Set weight = sum of weights of its stones
             + R for every pair of originally adjacent stones
               that are placed in the same set
```

For every adjacent pair `(i, i+1)`:

- If both stones are in A, add `R` to A.
- If both stones are in B, add `R` to B.
- If they are in different sets, add nothing.

Find the minimum possible absolute difference between the weights of Set A and Set B.

## Example

```text
Input:
N = 3
R = 10
stones = [10,20,30]

Output:
10
```

One optimal assignment is:

```text
A = {10,20}
B = {30}
```

Set A:

```text
10 + 20 + 10 = 40
```

Set B:

```text
30
```

Difference:

```text
|40 - 30| = 10
```

Therefore, the answer is `10`.

## Test Case 1

```text
Input:
N = 3
R = 10
stones = [10,20,30]

Output:
10
```

## Test Case 2

```text
Input:
N = 4
R = 5
stones = [10,10,10,10]

Output:
0
```

One optimal assignment:

```text
A = {10,10}
B = {10,10}
```

Both sets have:

```text
10 + 10 + 5 = 25
```

Therefore:

```text
|25 - 25| = 0
```

# Approach — Memoization

Maintain:

```text
diff = weight(A) - weight(B)
```

The recursive state needs:

```text
i       = current stone index
diff    = current difference between A and B
prevSet = set containing the previous stone
```

So the memoization state is:

```text
(i, diff, prevSet)
```

Because `diff` can have many possible values, use a `HashMap`.

## State Meaning

```text
prevSet = 0 -> previous stone was in A
prevSet = 1 -> previous stone was in B
```

For every stone there are two choices.

### Put Current Stone in A

Normally:

```text
diff += stones[i]
```

If the previous stone was also in A, add the resonance value:

```text
diff += R
```

### Put Current Stone in B

Normally:

```text
diff -= stones[i]
```

If the previous stone was also in B, the resonance value is added to B, so:

```text
diff -= R
```

# Java Memoization Code

```java
import java.util.*;

class Solution {

    Map<String, Integer> dp;

    public int minDifference(int[] stones, int R) {

        dp = new HashMap<>();

        // First stone goes to A
        int a = fun(1, stones[0], 0, stones, R);

        // First stone goes to B
        int b = fun(1, -stones[0], 1, stones, R);

        return Math.min(a, b);
    }

    // diff = weight(A) - weight(B)
    // prev = 0 -> previous stone was in A
    // prev = 1 -> previous stone was in B
    public int fun(int i, int diff, int prev,
                   int[] stones, int R) {

        if (i == stones.length) {
            return Math.abs(diff);
        }

        String key = i + "," + diff + "," + prev;

        if (dp.containsKey(key)) {
            return dp.get(key);
        }

        // Put current stone in A
        int newDiffA = diff + stones[i];

        if (prev == 0) {
            newDiffA += R;
        }

        int ansA = fun(i + 1, newDiffA, 0, stones, R);

        // Put current stone in B
        int newDiffB = diff - stones[i];

        if (prev == 1) {
            newDiffB -= R;
        }

        int ansB = fun(i + 1, newDiffB, 1, stones, R);

        int ans = Math.min(ansA, ansB);

        dp.put(key, ans);

        return ans;
    }
}
```

# Dry Run

For:

```text
stones = [10,20,30]
R = 10
```

Choose:

```text
10 -> A
20 -> A
30 -> B
```

Initially:

```text
diff = 10
```

For `20`:

```text
20 -> A
```

Previous stone was also in A:

```text
diff = 10 + 20 + 10
     = 40
```

For `30`:

```text
30 -> B
```

Previous stone was in A, so no resonance bonus:

```text
diff = 40 - 30
     = 10
```

Final answer:

```text
|diff| = 10
```

# Why `prevSet` Is Needed

For every adjacent pair, the `R` bonus depends on whether both stones are in the same set.

For example:

```text
A -> A
```

adds `R` to A.

But:

```text
A -> B
```

adds no bonus.

Similarly:

```text
B -> B
```

adds `R` to B.

Therefore, the previous set must be part of the DP state.

# Difference Trick

Instead of maintaining two values:

```text
weightA
weightB
```

maintain only:

```text
diff = weightA - weightB
```

At the end:

```text
answer = abs(diff)
```

# Complexity

If `D` is the number of reachable `diff` values:

```text
Time:  O(N * D * 2)
Space: O(N * D * 2)
```

The exact number of reachable differences depends on the stone weights and `R`.

# Key Idea

The main trick is:

```text
diff = A - B
```

and the DP state:

```text
(i, diff, prevSet)
```

Transitions:

```text
Put in A:
diff + stones[i] + R  if prevSet == A

Put in B:
diff - stones[i] - R  if prevSet == B
```

At the end:

```text
answer = abs(diff)
```

This is a **memoization + partition DP + previous-state tracking** problem.
