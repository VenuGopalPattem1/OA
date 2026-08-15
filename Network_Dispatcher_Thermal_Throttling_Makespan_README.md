# Q6. Network Dispatcher — Thermal Throttling Makespan

## Problem Source

**Source:** `SP_DSE_SP-Off Campus-Test-01-Aug-26-10 AM`

> The exact title was not visible in the original screenshot. This README uses the descriptive title **Network Dispatcher — Thermal Throttling Makespan**.

---

## Problem Statement

You are simulating a network dispatcher that routes an array of `N` tasks to `M` identical servers.

Each task has a **base processing time**.

The dispatcher follows these strict rules:

### 1. Priority Queueing

The dispatcher always selects the **largest remaining unassigned task**.

If two or more tasks have the same base processing time, the task that appeared **earlier in the original input array** is selected first.

So tasks are ordered by:

1. Base processing time — **descending**
2. Original index — **ascending**

---

### 2. Load Balancing

After selecting a task, immediately assign it to the server having the **lowest current total load**.

If multiple servers have the same lowest load, select the server with the **smallest server ID**.

So servers are ordered by:

1. Current load — **ascending**
2. Server ID — **ascending**

---

### 3. Thermal Throttling / Fatigue

Servers become slower as more tasks are assigned to them.

Suppose:

- `p` = base processing time of the current task
- `k` = number of tasks already assigned to this server
- `w` = thermal penalty parameter

Then the actual processing time added to that server's load is:

```text
p + (k × w)
```

After assigning the task:

```text
serverLoad += p + (k × w)
k++
```

Important: `k` is the number of tasks assigned **before** the current task.

---

## Required Output

Return the **makespan**:

```text
maximum total load among all M servers
```

In other words, after every task has been dispatched, find the server with the largest final load.

---

# Example 1

### Input

```text
N = 5
M = 2
w = 2

tasks = [5, 2, 5, 1, 3]
```

### Step 1 — Sort Tasks

Original indices:

```text
index:  0  1  2  3  4
task:   5  2  5  1  3
```

Largest task first.

The two tasks with value `5` are tied, so index `0` comes before index `2`.

Dispatch order:

```text
(5, index 0)
(5, index 2)
(3, index 4)
(2, index 1)
(1, index 3)
```

### Step 2 — Dispatch

Initially:

```text
Server 1: load = 0, count = 0
Server 2: load = 0, count = 0
```

#### Task 5, index 0

Both servers have load `0`.

Tie → choose server `1`.

```text
added = 5 + (0 × 2) = 5

Server 1: load = 5, count = 1
Server 2: load = 0, count = 0
```

#### Task 5, index 2

Lowest load → server `2`.

```text
added = 5 + (0 × 2) = 5

Server 1: load = 5, count = 1
Server 2: load = 5, count = 1
```

#### Task 3, index 4

Both loads are `5`.

Tie → choose server `1`.

For server `1`, `k = 1`.

```text
added = 3 + (1 × 2) = 5

Server 1: load = 10, count = 2
Server 2: load = 5,  count = 1
```

#### Task 2, index 1

Lowest load → server `2`.

For server `2`, `k = 1`.

```text
added = 2 + (1 × 2) = 4

Server 1: load = 10, count = 2
Server 2: load = 9,  count = 2
```

#### Task 1, index 3

Lowest load → server `2`.

For server `2`, `k = 2`.

```text
added = 1 + (2 × 2) = 5

Server 1: load = 10, count = 2
Server 2: load = 14, count = 3
```

### Output

```text
14
```

The makespan is:

```text
max(10, 14) = 14
```

---

# Example 2

### Input

```text
N = 6
M = 3
w = 3

tasks = [8, 4, 8, 2, 6, 4]
```

### Sorted Dispatch Order

The largest task is selected first.

For equal values, smaller original index comes first.

```text
(8, index 0)
(8, index 2)
(6, index 4)
(4, index 1)
(4, index 5)
(2, index 3)
```

### Dispatch

Initial:

```text
Server 1: load = 0, count = 0
Server 2: load = 0, count = 0
Server 3: load = 0, count = 0
```

#### Task 8

Choose server 1.

```text
8 + (0 × 3) = 8

loads = [8, 0, 0]
counts = [1, 0, 0]
```

#### Task 8

Choose server 2.

```text
8 + (0 × 3) = 8

loads = [8, 8, 0]
counts = [1, 1, 0]
```

#### Task 6

Choose server 3.

```text
6 + (0 × 3) = 6

loads = [8, 8, 6]
counts = [1, 1, 1]
```

#### Task 4

Choose server 3 because it has the lowest load.

```text
4 + (1 × 3) = 7

loads = [8, 8, 13]
counts = [1, 1, 2]
```

#### Task 4

Lowest load is now tied between server 1 and server 2.

Tie → server 1.

```text
4 + (1 × 3) = 7

loads = [15, 8, 13]
counts = [2, 1, 2]
```

#### Task 2

Choose server 2.

```text
2 + (1 × 3) = 5

loads = [15, 13, 13]
counts = [2, 2, 2]
```

### Output

```text
15
```

Therefore:

```text
makespan = max(15, 13, 13) = 15
```

---

# Key Observation

This is a **priority queue / heap simulation problem**.

There are two independent priority rules:

### Task Priority Queue

We need the largest task first.

```text
(baseTime DESC, originalIndex ASC)
```

Therefore, use a **max-heap** for tasks.

---

### Server Priority Queue

We need the server with the smallest current load.

If loads are equal, use the smallest server ID.

```text
(load ASC, serverId ASC)
```

Therefore, use a **min-heap** for servers.

Each server should store:

```text
serverId
currentLoad
taskCount
```

---

# Algorithm

## Step 1 — Store Tasks With Their Original Indices

For every task:

```text
Task {
    processingTime,
    originalIndex
}
```

The original index is required because equal processing times must be dispatched according to their original order.

---

## Step 2 — Sort Tasks

Sort using:

```text
processingTime descending
originalIndex ascending
```

Example:

```text
tasks = [5, 2, 5, 3]
```

becomes:

```text
5(index 0)
5(index 2)
3(index 3)
2(index 1)
```

---

## Step 3 — Create Server Min-Heap

Initially every server has:

```text
load = 0
count = 0
```

The comparator is:

```text
smaller load first
if load is equal:
    smaller server ID first
```

---

## Step 4 — Process Tasks

For every task:

1. Remove the server with the smallest current load.
2. Let its current task count be `k`.
3. Calculate:

```text
actualTime = p + k × w
```

4. Update:

```text
load += actualTime
count++
```

5. Put the server back into the min-heap.

---

## Step 5 — Return Maximum Load

After all tasks are processed:

```text
answer = maximum server.load
```

---

# Java Solution

```java
import java.util.*;

class Solution {

    static class Task {
        long p;
        int index;

        Task(long p, int index) {
            this.p = p;
            this.index = index;
        }
    }

    static class Server {
        int id;
        long load;
        long count;

        Server(int id) {
            this.id = id;
            this.load = 0;
            this.count = 0;
        }
    }

    public long networkDispatcher(int[] tasks, int m, long w) {

        int n = tasks.length;

        // -----------------------------------------
        // 1. Sort tasks:
        //    larger processing time first
        //    smaller original index first on tie
        // -----------------------------------------
        Task[] arr = new Task[n];

        for (int i = 0; i < n; i++) {
            arr[i] = new Task(tasks[i], i);
        }

        Arrays.sort(arr, (a, b) -> {
            if (a.p != b.p) {
                return Long.compare(b.p, a.p);
            }

            return Integer.compare(a.index, b.index);
        });

        // -----------------------------------------
        // 2. Server min-heap:
        //    smaller load first
        //    smaller server ID on tie
        // -----------------------------------------
        PriorityQueue<Server> pq = new PriorityQueue<>(
            (a, b) -> {
                if (a.load != b.load) {
                    return Long.compare(a.load, b.load);
                }

                return Integer.compare(a.id, b.id);
            }
        );

        // Servers are numbered 1 to M
        for (int id = 1; id <= m; id++) {
            pq.offer(new Server(id));
        }

        long answer = 0;

        // -----------------------------------------
        // 3. Dispatch every task
        // -----------------------------------------
        for (Task task : arr) {

            Server server = pq.poll();

            long k = server.count;

            long actualTime = task.p + (k * w);

            server.load += actualTime;
            server.count++;

            answer = Math.max(answer, server.load);

            pq.offer(server);
        }

        return answer;
    }
}
```

---

# Why `long` Is Important

Do not use only `int` for the server load.

The thermal penalty contains:

```text
k × w
```

and the total load is the sum of many such values.

For example, even if every individual task fits inside an `int`, the accumulated server load can become much larger.

Therefore:

```java
long load;
long count;
long w;
long actualTime;
```

are safer.

---

# Complexity

Let:

- `N` = number of tasks
- `M` = number of servers

### Sorting Tasks

```text
O(N log N)
```

### Processing Tasks

Each task performs:

```text
poll()  -> O(log M)
offer() -> O(log M)
```

For `N` tasks:

```text
O(N log M)
```

### Total

```text
O(N log N + N log M)
```

### Space

Tasks:

```text
O(N)
```

Server heap:

```text
O(M)
```

Total:

```text
O(N + M)
```

---

# Common Mistakes

## Mistake 1 — Selecting the smallest task

The dispatcher selects the **largest** remaining task.

Wrong:

```text
smallest processing time first
```

Correct:

```text
largest processing time first
```

---

## Mistake 2 — Ignoring original index

For:

```text
tasks = [5, 2, 5]
```

the two `5`s are tied.

The first `5` must be selected before the second `5`.

Therefore:

```text
(5, index 0)
(5, index 2)
```

---

## Mistake 3 — Choosing the server with the smallest ID

The server priority is **not** simply the smallest ID.

First compare:

```text
current load
```

Only if loads are equal do we compare:

```text
server ID
```

---

## Mistake 4 — Using the wrong `k`

For a server with:

```text
count = 3
```

the next task uses:

```text
p + (3 × w)
```

not:

```text
p + (4 × w)
```

`k` represents the number of tasks assigned **before** the current task.

---

## Mistake 5 — Applying fatigue globally

The count is maintained **per server**.

For example:

```text
Server 1 count = 3
Server 2 count = 1
```

A task assigned to server 2 uses:

```text
k = 1
```

not `3`.

---

## Mistake 6 — Not putting the server back into the heap

After updating a server:

```java
server.load += actualTime;
server.count++;
```

it must be inserted back:

```java
pq.offer(server);
```

Otherwise that server disappears from consideration.

---

# Similar Problems

These are useful practice problems because they use the same core ideas: **priority queues, custom ordering, simulation, and task/server scheduling**.

### 1. LeetCode 1882 — Process Tasks Using Servers

Very closely related to this problem.

It uses priority queues to select the best server based on server priority and availability. citeturn0search0turn0search8

### 2. LeetCode 1834 — Single-Threaded CPU

Useful for practicing task ordering, tie-breaking, and priority queues. The CPU chooses tasks according to processing time and index. citeturn0search1turn0search11

### 3. LeetCode 2532 — Time to Cross a Bridge

A harder priority-queue simulation involving multiple worker states and strict priority rules. citeturn0search2turn0search3

---

# Recommended Practice Order

```text
1. LeetCode 1834
       ↓
2. LeetCode 1882
       ↓
3. Network Dispatcher — Thermal Throttling Makespan
       ↓
4. LeetCode 2532
```

The important concepts to master are:

```text
Custom Comparator
        ↓
Priority Queue / Heap
        ↓
Tie Breaking
        ↓
Simulation
        ↓
Multiple State Variables
```

---

# Final Pattern to Remember

For this problem, think:

```text
SORT TASKS
    ↓
Largest p first
    ↓
Original index for tie
    ↓
MIN-HEAP OF SERVERS
    ↓
Lowest load first
    ↓
Server ID for tie
    ↓
actual = p + count × w
    ↓
Update load and count
    ↓
Put server back
    ↓
Maximum final load = answer
```

## One-Line Idea

> **Sort tasks by descending processing time, then repeatedly assign each task to the minimum-load server using a min-heap, adding the server-specific thermal penalty based on how many tasks that server has already processed.**
