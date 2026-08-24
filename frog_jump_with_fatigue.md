# Frog Jump with Fatigue

## Problem

A frog starts on stone `0` and wants to reach the last stone.

The first jump must be exactly `1`.

If the previous jump was `k`, the next jump can be:

```text
k - 1
k
k + 1
```

The jump length must always be greater than `0`.

Whenever the next jump is greater than the previous jump, fatigue increases by `1`.

Find the minimum fatigue needed to reach the last stone.

Return `-1` if it is impossible.

---

## Example

```text
Stones = [0, 1, 3, 5, 6, 8, 12, 17]
```

A valid path is:

```text
0 -> 1 -> 3 -> 5 -> 8 -> 12 -> 17
```

Jump lengths:

```text
1 -> 2 -> 2 -> 3 -> 4 -> 5
```

Fatigue:

```text
1 -> 2 = +1
2 -> 2 = +0
2 -> 3 = +1
3 -> 4 = +1
4 -> 5 = +1
```

So under the stated fatigue rule, this path has fatigue `4`.

**Important:** If the original question says the answer is `2`, then the example is inconsistent with the stated fatigue rule. The path itself is valid, but its fatigue is `4` under the given definition.

---

## DP Approach

The next jump depends on the previous jump length, so the state is:

```text
DP(index, lastJump)
```

where:

- `index` = current stone index
- `lastJump` = jump used to reach the current stone
- return value = minimum additional fatigue needed to reach the last stone

From `(index, k)`, try:

```text
k - 1
k
k + 1
```

Ignore jumps `<= 0`.

For a valid next stone:

```text
nextPosition = stones[index] + jump
```

Additional fatigue:

```text
jump > k ? 1 : 0
```

Then:

```text
fun(nextIndex, jump) + fatigue
```

Take the minimum.

---

## Why HashMap?

Stones are not necessarily consecutive:

```text
[0, 1, 3, 5, 6, 8, 12, 17]
```

Use:

```java
Map<Integer, Integer>
```

to store:

```text
stone position -> array index
```

This lets us check whether the next stone exists in average `O(1)` time.

---

## Java Memoization Code

```java
import java.util.*;

class Solution {

    Map<Integer, Integer> map = new HashMap<>();
    Map<String, Integer> dp = new HashMap<>();

    public int minFatigue(int[] stones) {

        for (int i = 0; i < stones.length; i++) {
            map.put(stones[i], i);
        }

        // First jump must be exactly 1
        if (!map.containsKey(1)) {
            return -1;
        }

        int index = map.get(1);

        int ans = fun(index, 1, stones);

        return ans >= 1000000 ? -1 : ans;
    }

    public int fun(int i, int k, int[] stones) {

        // Reached last stone
        if (i == stones.length - 1) {
            return 0;
        }

        String key = i + "," + k;

        if (dp.containsKey(key)) {
            return dp.get(key);
        }

        int ans = 1000000;

        // Try k - 1, k, k + 1
        for (int jump = k - 1; jump <= k + 1; jump++) {

            if (jump <= 0) {
                continue;
            }

            int nextStone = stones[i] + jump;

            if (!map.containsKey(nextStone)) {
                continue;
            }

            int nextIndex = map.get(nextStone);

            int fatigue = 0;

            if (jump > k) {
                fatigue = 1;
            }

            int result = fun(nextIndex, jump, stones);

            if (result != 1000000) {
                ans = Math.min(ans, fatigue + result);
            }
        }

        dp.put(key, ans);

        return ans;
    }
}
```

---

## Test Case 1

```text
stones = [0, 1, 2, 3, 4, 5]
```

Path:

```text
0 -> 1 -> 2 -> 3 -> 4 -> 5
```

Jumps:

```text
1 -> 1 -> 1 -> 1 -> 1
```

No jump increases.

```text
Answer = 0
```

---

## Test Case 2

```text
stones = [0, 1, 4]
```

After:

```text
0 -> 1
```

the previous jump is `1`.

The next jump can only be:

```text
1 or 2
```

Possible positions:

```text
1 + 1 = 2
1 + 2 = 3
```

There is no stone at `2` or `3`.

```text
Answer = -1
```

---

## Test Case 3

```text
stones = [0, 1, 3, 4]
```

Path:

```text
0 -> 1 -> 3 -> 4
```

Jumps:

```text
1 -> 2 -> 1
```

Fatigue:

```text
1 -> 2 = +1
2 -> 1 = +0
```

```text
Answer = 1
```

---

## Complexity

There are approximately `N * N` possible `(index, lastJump)` states.

Each state tries only three jumps.

```text
Time Complexity  = O(N²)
Space Complexity = O(N²)
```

---

## Core Pattern

```text
State:
DP(index, lastJump)

Transitions:
lastJump - 1
lastJump
lastJump + 1

Cost:
nextJump > lastJump ? 1 : 0

Base:
last stone -> 0

Impossible:
INF
```

General structure:

```text
                 DP(i, k)
                /   |                k-1    k    k+1
              |     |      |
             DP    DP     DP
```

---

## Related Problems

### LeetCode 403 — Frog Jump
Closest standard problem. It uses the same `DP(stone, previousJump)` state.

https://leetcode.com/problems/frog-jump/

### LeetCode 1696 — Jump Game VI

https://leetcode.com/problems/jump-game-vi/

### LeetCode 45 — Jump Game II

https://leetcode.com/problems/jump-game-ii/

### LeetCode 746 — Min Cost Climbing Stairs

https://leetcode.com/problems/min-cost-climbing-stairs/

### LeetCode 983 — Minimum Cost For Tickets

https://leetcode.com/problems/minimum-cost-for-tickets/

## Recommended Order

```text
403 Frog Jump
    ↓
746 Min Cost Climbing Stairs
    ↓
45 Jump Game II
    ↓
1696 Jump Game VI
    ↓
Frog Jump with Fatigue
```

LeetCode 403 is the most important practice problem for this question because its state is almost exactly:

```text
DP(index, lastJump)
```

Your problem adds a minimum-fatigue cost to that state.
