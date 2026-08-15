# Maximum Points Covered by a Length-L Window

## Problem Statement

You are given an array `pts` of `N` integers representing point positions on a number line.

You are also given:

- `L` - maximum allowed distance between the minimum and maximum selected points.
- `K` - modulo factor.

You must select a subset of points satisfying both conditions:

1. All selected points must have the same remainder when divided by `K`.
2. The difference between the maximum and minimum selected point must be less than or equal to `L`.

Return the maximum number of points that can be included in a valid subset.

---

## Example

Input:

    N = 8
    L = 6
    K = 3
    pts = [1, 4, 7, 10, 2, 5, 8, 20]

Group the points based on `point % K`:

    Remainder 1:
    [1, 4, 7, 10]

    Remainder 2:
    [2, 5, 8, 20]

For remainder `1`:

    [1, 4, 7]

    maximum - minimum
    = 7 - 1
    = 6

Since `6 <= L`, we can select 3 points.

For remainder `2`:

    [2, 5, 8]

    maximum - minimum
    = 8 - 2
    = 6

Again, we can select 3 points.

Therefore:

    Answer = 3

---

## Key Observation

The first condition says that every selected point must have the same remainder when divided by `K`.

So:

    point1 % K == point2 % K

for every pair of selected points.

Therefore, points with different remainders cannot be selected together.

So we first group the points by:

    point % K

For example, if:

    K = 3

then:

    1 % 3 = 1
    4 % 3 = 1
    7 % 3 = 1
    10 % 3 = 1

and:

    2 % 3 = 2
    5 % 3 = 2
    8 % 3 = 2

So the groups are:

    Remainder 1 -> [1, 4, 7, 10]
    Remainder 2 -> [2, 5, 8]

---

## Second Observation

For every remainder group, we need to find the maximum number of points such that:

    maximum point - minimum point <= L

If the group is sorted:

    [1, 4, 7, 10]

we can use two pointers.

Maintain:

    left
    right

For every `right`, check:

    group[right] - group[left] <= L

If the difference is greater than `L`, move `left` forward.

    while group[right] - group[left] > L:
        left++

The current valid window contains:

    right - left + 1

points.

---

## Algorithm

1. Create a map where the key is `point % K`.
2. Put every point into its corresponding remainder group.
3. Sort every group.
4. For every group:
   - Set `left = 0`.
   - Move `right` from left to right.
   - While `group[right] - group[left] > L`, increment `left`.
   - Calculate `right - left + 1`.
   - Update the maximum answer.
5. Return the maximum answer.

---

## Java Solution

    import java.util.*;

    class Solution {

        public int maxPoints(int[] pts, int L, int K) {

            // Group points according to their remainder modulo K
            Map<Integer, List<Integer>> groups = new HashMap<>();

            for (int point : pts) {

                int remainder = point % K;

                groups
                    .computeIfAbsent(remainder, x -> new ArrayList<>())
                    .add(point);
            }

            int answer = 0;

            // Process every remainder group
            for (List<Integer> group : groups.values()) {

                // Sort the group
                Collections.sort(group);

                int left = 0;

                // Sliding window
                for (int right = 0; right < group.size(); right++) {

                    // Shrink the window if distance is greater than L
                    while (group.get(right) - group.get(left) > L) {
                        left++;
                    }

                    // Number of points in current valid window
                    int count = right - left + 1;

                    answer = Math.max(answer, count);
                }
            }

            return answer;
        }
    }

---

## Dry Run

Consider:

    pts = [1, 4, 7, 10, 2, 5, 8, 20]
    L = 6
    K = 3

### Step 1: Group by Remainder

    1 % 3 = 1
    4 % 3 = 1
    7 % 3 = 1
    10 % 3 = 1

    2 % 3 = 2
    5 % 3 = 2
    8 % 3 = 2
    20 % 3 = 2

Therefore:

    Group 1 = [1, 4, 7, 10]
    Group 2 = [2, 5, 8, 20]

### Step 2: Process Group 1

Sorted group:

    [1, 4, 7, 10]

Start with:

    left = 0
    right = 0

Window:

    [1]

Count:

    1

Move `right`:

    [1, 4]

    4 - 1 = 3 <= 6

Count:

    2

Move `right`:

    [1, 4, 7]

    7 - 1 = 6 <= 6

Count:

    3

Move `right`:

    [1, 4, 7, 10]

    10 - 1 = 9 > 6

The window is invalid.

Move `left`:

    left = 1

Now:

    [4, 7, 10]

    10 - 4 = 6 <= 6

Count:

    3

Maximum for this group:

    3

### Step 3: Process Group 2

Sorted group:

    [2, 5, 8, 20]

Window:

    [2]

Count = 1

Next:

    [2, 5]

    5 - 2 = 3 <= 6

Count = 2

Next:

    [2, 5, 8]

    8 - 2 = 6 <= 6

Count = 3

Next:

    [2, 5, 8, 20]

    20 - 2 = 18 > 6

Move `left`.

    [5, 8, 20]

    20 - 5 = 15 > 6

Move `left` again.

    [8, 20]

    20 - 8 = 12 > 6

Move `left` again.

    [20]

Count = 1

Maximum for this group:

    3

Therefore:

    Answer = 3

---

## Complete Program

    import java.util.*;

    public class Main {

        public static int maxPoints(int[] pts, int L, int K) {

            Map<Integer, List<Integer>> groups = new HashMap<>();

            // Group points by remainder
            for (int point : pts) {

                int remainder = point % K;

                groups
                    .computeIfAbsent(remainder, x -> new ArrayList<>())
                    .add(point);
            }

            int answer = 0;

            // Process every group
            for (List<Integer> group : groups.values()) {

                Collections.sort(group);

                int left = 0;

                for (int right = 0; right < group.size(); right++) {

                    while (group.get(right) - group.get(left) > L) {
                        left++;
                    }

                    int count = right - left + 1;

                    answer = Math.max(answer, count);
                }
            }

            return answer;
        }

        public static void main(String[] args) {

            int[] pts = {1, 4, 7, 10, 2, 5, 8, 20};

            int L = 6;
            int K = 3;

            System.out.println(maxPoints(pts, L, K));
        }
    }

Output:

    3

---

## Test Case 1

Input:

    N = 8
    L = 6
    K = 3

    pts = [1, 4, 7, 10, 2, 5, 8, 20]

Groups:

    Remainder 1 -> [1, 4, 7, 10]
    Remainder 2 -> [2, 5, 8, 20]

Valid maximum subsets include:

    [1, 4, 7]

    [4, 7, 10]

    [2, 5, 8]

For each:

    maximum - minimum = 6

Therefore:

    Output = 3

---

## Test Case 2

Input:

    N = 10
    L = 5
    K = 4

    pts = [1, 5, 9, 13, 2, 6, 10, 14, 18, 30]

Groups:

    Remainder 1 -> [1, 5, 9, 13]

    Remainder 2 -> [2, 6, 10, 14, 18, 30]

For remainder `1`:

    [1, 5]

    5 - 1 = 4 <= 5

But:

    9 - 1 = 8 > 5

So maximum = 2.

For remainder `2`:

    [2, 6]

    6 - 2 = 4 <= 5

But:

    10 - 2 = 8 > 5

So maximum = 2.

Therefore:

    Output = 2

---

## Complexity Analysis

Let `N` be the number of points.

### Grouping

Every point is processed once:

    O(N)

### Sorting

All groups together require at most:

    O(N log N)

### Sliding Window

For every group, `left` and `right` only move forward.

Therefore:

    O(N)

### Overall

    Time Complexity:  O(N log N)
    Space Complexity: O(N)

---

## Why Sliding Window Works

After sorting a group:

    [1, 4, 7, 10]

Suppose:

    left = 0
    right = 3

Then:

    group[right] - group[left]
    = 10 - 1
    = 9

If:

    L = 6

then:

    9 > 6

so the current window is invalid.

We move `left`:

    left++

Now:

    [4, 7, 10]

and:

    10 - 4 = 6

which is valid.

Because the array is sorted, moving `left` is always the correct way to reduce the difference.

---

## Important Pattern

Remember this problem as:

    GROUP
       |
       v
    point % K
       |
       v
    SORT
       |
       v
    SLIDING WINDOW
       |
       v
    maximum - minimum <= L
       |
       v
    MAXIMUM COUNT

The most important code is:

    while (group.get(right) - group.get(left) > L) {
        left++;
    }

    answer = Math.max(answer, right - left + 1);

---

## Interview Explanation

A short explanation:

> First, I group all points according to `point % K`, because all selected points must have the same remainder. Then I sort each group. For every sorted group, I use two pointers to maintain the largest window where the difference between the maximum and minimum point is at most `L`. If the difference becomes greater than `L`, I move the left pointer forward. The size of the current window is `right - left + 1`, and I maintain the maximum across all groups. The overall time complexity is `O(N log N)` because of sorting.

---

## Edge Cases

### Single Point

If:

    pts = [10]

then:

    maximum - minimum = 0

So:

    Answer = 1

### L = 0

Only points having the same position can be selected.

Example:

    pts = [2, 2, 2, 5]
    L = 0
    K = 3

The three `2`s can be selected:

    2 - 2 = 0

Therefore:

    Answer = 3

### Different Remainders

If every point has a different remainder modulo `K`, no two points can be selected together.

Therefore:

    Answer = 1

assuming `N > 0`.

### Large L

If `L` is large enough to cover the complete range of a remainder group, the entire group can be selected.
