# Minimum Makespan on M Machines (LPT with Thermal Penalty)

## Problem Statement

You are given:

- `N` tasks
- `M` identical machines (servers)
- A thermal penalty `W`

Each task has a base processing time.

The dispatcher assigns tasks according to the following rules.

---

## Rule 1: Task Selection

Always choose the **largest remaining task**.

If multiple tasks have the same processing time, choose the one that appeared **earlier in the original input**.

This is simply a **stable descending sort**.

---

## Rule 2: Machine Selection

Assign the selected task to the machine having the **minimum current load**.

If multiple machines have the same load, choose the machine with the **smallest machine ID**.

---

## Rule 3: Thermal Penalty

Suppose a machine has already processed **k** tasks.

If a task has processing time **D**, then the actual load added is

```
D + (k × W)
```

where

- `D` = task processing time
- `k` = number of tasks already assigned to that machine
- `W` = thermal penalty

After assignment

- Machine load increases.
- Number of assigned tasks increases by one.

---

## Goal

Return the **makespan**, i.e.,

```
Maximum load among all machines.
```

---

# Input Format

```
N M W
```

Second line

```
N integers
```

representing task processing times.

---

# Constraints

```
1 ≤ N ≤ 2 × 10^5
1 ≤ M ≤ 10^5
0 ≤ W ≤ 10^4
1 ≤ task[i] ≤ 10^6
```

---

# Example

## Input

```text
6 3 2
3 9 5 2 8 6
```

---

## Step 1

Sort tasks in descending order.

```
9 8 6 5 3 2
```

---

## Initial State

```
S1 = Load 0, Tasks 0
S2 = Load 0, Tasks 0
S3 = Load 0, Tasks 0
```

---

## Dry Run

|Step|Task|Chosen Machine|Current Load|Tasks Assigned (k)|Added Load|New Load|Machine Loads|
|---:|---:|:------------:|-----------:|-----------------:|---------:|-------:|-------------|
|1|9|S1|0|0|9 + (0×2)=9|9|S1=9 S2=0 S3=0|
|2|8|S2|0|0|8 + (0×2)=8|8|S1=9 S2=8 S3=0|
|3|6|S3|0|0|6 + (0×2)=6|6|S1=9 S2=8 S3=6|
|4|5|S3|6|1|5 + (1×2)=7|13|S1=9 S2=8 S3=13|
|5|3|S2|8|1|3 + (1×2)=5|13|S1=9 S2=13 S3=13|
|6|2|S1|9|1|2 + (1×2)=4|13|S1=13 S2=13 S3=13|

---

## Final Loads

```
S1 = 13
S2 = 13
S3 = 13
```

### Makespan

```
13
```

---

# Algorithm

### Step 1

Store each task along with its original index.

```
(time, index)
```

---

### Step 2

Perform a **stable descending sort**.

Sort by

- Larger processing time first.
- If equal, smaller original index first.

---

### Step 3

Maintain a **Priority Queue (Min Heap)** of machines.

Each machine stores

- Current load
- Machine ID
- Number of assigned tasks

Priority

1. Smaller load
2. Smaller machine ID

---

### Step 4

For every task

Take the least loaded machine.

```
Machine machine = pq.poll();
```

Actual processing time

```
task + machine.tasksAssigned × W
```

Update

```
machine.load += actualTime
machine.tasksAssigned++
```

Push the machine back into the heap.

Track the maximum load.

---

# Java Code

```java
import java.util.*;

class Solution {

    static class Task {
        int time;
        int index;

        Task(int time, int index) {
            this.time = time;
            this.index = index;
        }
    }

    static class Machine {
        long load;
        int id;
        int tasksAssigned;

        Machine(long load, int id, int tasksAssigned) {
            this.load = load;
            this.id = id;
            this.tasksAssigned = tasksAssigned;
        }
    }

    static long minimumMakespan(int[] tasks, int m, int w) {

        int n = tasks.length;

        Task[] arr = new Task[n];

        for (int i = 0; i < n; i++) {
            arr[i] = new Task(tasks[i], i);
        }

        Arrays.sort(arr, (a, b) -> {
            if (a.time != b.time)
                return b.time - a.time;
            return a.index - b.index;
        });

        PriorityQueue<Machine> pq = new PriorityQueue<>((a, b) -> {
            if (a.load != b.load)
                return Long.compare(a.load, b.load);
            return a.id - b.id;
        });

        for (int i = 1; i <= m; i++) {
            pq.offer(new Machine(0, i, 0));
        }

        long answer = 0;

        for (Task task : arr) {

            Machine cur = pq.poll();

            long actualTime =
                    task.time + (long) cur.tasksAssigned * w;

            cur.load += actualTime;
            cur.tasksAssigned++;

            answer = Math.max(answer, cur.load);

            pq.offer(cur);
        }

        return answer;
    }

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        int m = sc.nextInt();
        int w = sc.nextInt();

        int[] tasks = new int[n];

        for (int i = 0; i < n; i++)
            tasks[i] = sc.nextInt();

        System.out.println(minimumMakespan(tasks, m, w));
    }
}
```

---

# Complexity

Sorting

```
O(N log N)
```

Priority Queue Operations

```
O(N log M)
```

Overall

```
O(N log N + N log M)
```

Space

```
O(N + M)
```

---

# Important Interview Points

✅ This is a greedy scheduling problem.

✅ The task order never changes after sorting.

✅ Use a **stable descending sort**.

✅ Use a **Min Heap** for selecting the least loaded machine.

Heap ordering:

1. Current load
2. Machine ID

Actual processing time:

```
task + (assignedTasks × W)
```

The thermal penalty depends on **how many tasks were already assigned**, **not** on the current load.

---

# Similar Problems

- LeetCode 1882 — Process Tasks Using Servers
- LeetCode 1834 — Single-Threaded CPU
- Parallel Machine Scheduling (LPT Algorithm)
- Greedy Scheduling using Priority Queue

---

# Revision Notes (30 Seconds)

1. Stable sort tasks in descending order.
2. Maintain a min-heap of machines `(load, machineId)`.
3. Pick the least-loaded machine.
4. Add:
   ```
   task + assignedTasks × W
   ```
5. Increment assigned task count.
6. Push the machine back.
7. Track the maximum machine load.

**Time:** `O(N log N + N log M)`  
**Space:** `O(N + M)`
