Minimum Adjacent Swaps to Group Identical Characters
Problem Description

You are given a binary string S containing only '0' and '1'.

In one operation, you can swap two adjacent characters.

Your task is to find the minimum number of adjacent swaps required to make all 0s or all 1s appear in one contiguous block.

Return the minimum swaps among these two choices.

Input

A binary string S.

Output

Print the minimum number of adjacent swaps.

Test Case 1
Input
1001
Output
0
Explanation
Group 1s → 2 swaps
Group 0s → Already contiguous

Answer = 0

Test Case 2
Input
10101
Output
1
Explanation
10101
  ↑ ↑
Swap adjacent characters at indices 2 and 3

↓

10011

Now all 0s are together.

Brute Force
Idea
Store the positions of all 0s (or 1s).
Suppose there are k occurrences.
Try every possible block of length k.
Calculate the total moves required.
Take the minimum.
Repeat for both 0 and 1.
Return the smaller answer.
Time Complexity
O(n²)
Optimal Approach
Intuition

Instead of checking every possible block, store the positions of the required character.

Example

S = 10101

Positions of 0 = [1,3]

If they become consecutive,

Current : [1,3]

Target  : [1,2]

Only 1 swap is required.

For every position,

adjusted[i] = position[i] - i

Now find the median of the adjusted array.

The median gives the minimum total adjacent swaps.

Repeat for both 0 and 1.

Return the smaller answer.

Algorithm
For each character (0 and 1)

1. Store all positions.
2. If occurrences <= 1, return 0.
3. Compute adjusted[i] = position[i] - i.
4. Find the median.
5. Sum |adjusted[i] - median|.
6. Return the minimum of both answers.
Time Complexity
O(n)
Space Complexity
O(n)
Pattern
Adjacent Swaps
        ↓
Store Positions
        ↓
Subtract Index (position - i)
        ↓
Median
        ↓
Minimum Adjacent Swaps

Revision Tip:
If a problem says "adjacent swaps + make identical elements consecutive", immediately think:

Positions Array → Adjusted Positions (position - index) → Median.



Optimal Code (Java)
import java.util.*;

class Solution {

    static long solve(String s, char ch) {

        List<Integer> pos = new ArrayList<>();

        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == ch)
                pos.add(i);
        }

        int m = pos.size();

        if (m <= 1)
            return 0;

        long[] adjusted = new long[m];

        for (int i = 0; i < m; i++) {
            adjusted[i] = pos.get(i) - i;
        }

        long median = adjusted[m / 2];

        long swaps = 0;

        for (long x : adjusted)
            swaps += Math.abs(x - median);

        return swaps;
    }

    static long minAdjacentSwaps(String s) {

        return Math.min(solve(s, '0'), solve(s, '1'));
    }
}








Brute Force Algorithm
For each character (0 and 1)

    Store its positions.

    Let k be its frequency.

    Try every possible starting position.

    Compute total adjacent swaps.

Return minimum.
Brute Force Code (Java)
import java.util.*;

class Solution {

    static long solve(String s, char ch) {

        List<Integer> pos = new ArrayList<>();

        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == ch)
                pos.add(i);
        }

        int k = pos.size();

        if (k <= 1)
            return 0;

        long ans = Long.MAX_VALUE;

        // Try every possible consecutive block
        for (int start = 0; start <= s.length() - k; start++) {

            long cost = 0;

            for (int i = 0; i < k; i++) {
                cost += Math.abs(pos.get(i) - (start + i));
            }

            ans = Math.min(ans, cost);
        }

        return ans;
    }

    static long minAdjacentSwaps(String s) {

        return Math.min(solve(s, '0'), solve(s, '1'));
    }
}
Complexity
Time : O(n²)

Space : O(n)
