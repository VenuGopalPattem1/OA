# Infosys OA — Array Problems

---

# Problem 1 — Maximum Count Using Alternating Weights

## Problem Description

You are given an integer array `A` of size `N` and two integer weights `X` and `Y`.

You have to check every element of the array.

For the first pass:

- If the index `i` is odd, its corresponding weight is `X`.
- If the index `i` is even, its corresponding weight is `Y`.

For every index, check:

```text
A[i] <= corresponding weight
```

If the condition is true, increase the count by `1`.

Otherwise, skip the element.

After completing the first pass, repeat the entire process by **swapping the weights**:

- If the index `i` is odd, its corresponding weight is `Y`.
- If the index `i` is even, its corresponding weight is `X`.

Again, count the number of elements satisfying:

```text
A[i] <= corresponding weight
```

Return the maximum count obtained from the two passes.

> Indexing starts from `0`.

---

## Example

```text
A = [2, 5, 3, 4]
X = 3
Y = 5
```

### First Pass

Odd index → `X = 3`  
Even index → `Y = 5`

```text
Index:   0   1   2   3
A:       2   5   3   4
Weight:  5   3   5   3

         ✓   ✗   ✓   ✗
```

Count:

```text
2
```

### Second Pass

Swap the weights.

Odd index → `Y = 5`  
Even index → `X = 3`

```text
Index:   0   1   2   3
A:       2   5   3   4
Weight:  3   5   3   5

         ✓   ✓   ✓   ✓
```

Count:

```text
4
```

Therefore:

```text
Answer = 4
```

---

## Test Case 1

### Input

```text
N = 5
A = [2, 7, 4, 3, 5]
X = 5
Y = 3
```

### Output

```text
4
```

---

## Test Case 2

### Input

```text
N = 6
A = [1, 2, 6, 4, 3, 7]
X = 4
Y = 5
```

### Output

```text
4
```

---

## Test Case 3

### Input

```text
N = 7
A = [8, 2, 5, 6, 1, 4, 7]
X = 4
Y = 6
```

### Output

```text
4
```

---

## Java Code

```java
import java.util.*;

class Main {

    public static int maxCount(int[] A, int X, int Y) {

        int count1 = 0;
        int count2 = 0;

        for (int i = 0; i < A.length; i++) {

            // First pass
            // Even index -> Y
            // Odd index  -> X
            if (i % 2 == 0) {
                if (A[i] <= Y) {
                    count1++;
                }
            } else {
                if (A[i] <= X) {
                    count1++;
                }
            }

            // Second pass - swap weights
            // Even index -> X
            // Odd index  -> Y
            if (i % 2 == 0) {
                if (A[i] <= X) {
                    count2++;
                }
            } else {
                if (A[i] <= Y) {
                    count2++;
                }
            }
        }

        return Math.max(count1, count2);
    }

    public static void main(String[] args) {

        int[] A = {2, 7, 4, 3, 5};
        int X = 5;
        int Y = 3;

        System.out.println(maxCount(A, X, Y));
    }
}
```

### Complexity

```text
Time  : O(N)
Space : O(1)
```

---

# Problem 2 — Maximum Locks with One Momentum Reset

## Problem Description

You are given an integer array `A` of size `N`.

You have to traverse the array from **left to right**.

Initially:

```text
momentum = 0
```

For every index `i`:

- If `A[i] >= momentum`, you can **lock** the element.
- After locking the element, update:

```text
momentum = A[i]
```

- If `A[i] < momentum`, you must **discard** the element.

You are allowed to reset:

```text
momentum = 0
```

**exactly once** at any point during the traversal.

Your goal is to maximize the total number of elements that can be locked.

Return the maximum number of locks possible.

---

## Example

```text
A = [2, 5, 3, 4, 1, 2]
```

Initially:

```text
momentum = 0
```

Process the array:

```text
2 → lock   momentum = 2
5 → lock   momentum = 5
```

Now use the reset:

```text
momentum = 0
```

Continue:

```text
3 → lock   momentum = 3
4 → lock   momentum = 4
1 → discard
2 → discard
```

Total locks:

```text
4
```

Therefore:

```text
Answer = 4
```

---

## Test Case 1

### Input

```text
N = 6
A = [2, 5, 3, 4, 1, 2]
```

### Output

```text
4
```

### One Possible Traversal

```text
2 → lock
5 → lock

RESET

3 → lock
4 → lock
1 → discard
2 → discard
```

Total:

```text
4
```

---

## Test Case 2

### Input

```text
N = 7
A = [3, 6, 2, 4, 5, 1, 7]
```

### Output According to the Given Rules

```text
6
```

### One Possible Traversal

```text
3 → lock
6 → lock

RESET

2 → lock
4 → lock
5 → lock
1 → discard
7 → lock
```

Locked elements:

```text
3, 6, 2, 4, 5, 7
```

Total:

```text
6
```

> If the original OA has an expected output of `5` for this input, then an additional rule is missing from the reconstructed problem statement. Under the rules written here, the maximum is `6`.

---

## Test Case 3

### Input

```text
N = 8
A = [1, 2, 3, 1, 5, 2, 4, 6]
```

### Output

```text
7
```

---

## Java Code — Without DP

We can try every possible position for the single reset.

For each possible reset position:

1. Traverse the elements before the reset.
2. Reset `momentum` to `0`.
3. Traverse the remaining elements.
4. Count the number of locks.
5. Keep the maximum count.

```java
import java.util.*;

class Main {

    public static int maxLocks(int[] A) {

        int n = A.length;
        int ans = 0;

        // Try every possible reset position
        for (int reset = 0; reset <= n; reset++) {

            int momentum = 0;
            int count = 0;

            // Process elements before reset
            for (int i = 0; i < reset; i++) {

                if (A[i] >= momentum) {
                    count++;
                    momentum = A[i];
                }
            }

            // Use the one reset
            momentum = 0;

            // Process elements after reset
            for (int i = reset; i < n; i++) {

                if (A[i] >= momentum) {
                    count++;
                    momentum = A[i];
                }
            }

            ans = Math.max(ans, count);
        }

        return ans;
    }

    public static void main(String[] args) {

        int[] A = {3, 6, 2, 4, 5, 1, 7};

        System.out.println(maxLocks(A));
    }
}
```

### Output

```text
6
```

---

## Complexity

There are `N + 1` possible positions at which the reset can be performed.

For every reset position, the array can be traversed.

Therefore:

```text
Time  : O(N^2)
Space : O(1)
```

---

# Quick Revision

## Problem 1

Two possible weight arrangements:

```text
Case 1:
Even → Y
Odd  → X

Case 2:
Even → X
Odd  → Y
```

For each element:

```text
if (A[i] <= weight)
    count++;
```

Answer:

```text
max(count1, count2)
```

Complexity:

```text
O(N) time
O(1) space
```

---

## Problem 2

Initially:

```text
momentum = 0
```

For each element:

```text
if (A[i] >= momentum) {
    count++;
    momentum = A[i];
}
```

Reset momentum exactly once:

```text
momentum = 0;
```

Without DP, try every possible reset position.

Complexity:

```text
O(N^2) time
O(1) space
```
