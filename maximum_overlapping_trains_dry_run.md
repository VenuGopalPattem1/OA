# Dry Run - Maximum Overlapping Trains

## Input

```text
Arrival:
[900, 940, 950, 1100, 1500, 1800]

Departure:
[910, 1200, 1120, 1130, 1900, 2000]
```

## Step 1: Sort Both Arrays

Arrival is already sorted:

```text
[900, 940, 950, 1100, 1500, 1800]
```

Sort departure:

```text
[910, 1120, 1130, 1200, 1900, 2000]
```

We use two pointers:

```text
i       -> arrival pointer
j       -> departure pointer
current -> number of trains currently at station
max     -> maximum number of trains at station
```

Initially:

```text
i = 0
j = 0
current = 0
max = 0
```

## Dry Run

### Step 1

```text
arrival[i] = 900
departure[j] = 910

900 <= 910
```

A train arrives.

```text
current = 1
max = 1
i = 1
```

### Step 2

```text
arrival[i] = 940
departure[j] = 910

940 > 910
```

A train leaves.

```text
current = 0
j = 1
```

### Step 3

```text
arrival[i] = 940
departure[j] = 1120

940 <= 1120
```

A train arrives.

```text
current = 1
max = 1
i = 2
```

### Step 4

```text
arrival[i] = 950
departure[j] = 1120

950 <= 1120
```

A train arrives.

```text
current = 2
max = 2
i = 3
```

### Step 5

```text
arrival[i] = 1100
departure[j] = 1120

1100 <= 1120
```

A train arrives.

```text
current = 3
max = 3
i = 4
```

Now we have the maximum overlap:

```text
current = 3
max = 3
```

### Step 6

```text
arrival[i] = 1500
departure[j] = 1120

1500 > 1120
```

A train leaves.

```text
current = 2
j = 2
```

### Step 7

```text
arrival[i] = 1500
departure[j] = 1130

1500 > 1130
```

A train leaves.

```text
current = 1
j = 3
```

### Step 8

```text
arrival[i] = 1500
departure[j] = 1200

1500 > 1200
```

A train leaves.

```text
current = 0
j = 4
```

### Step 9

```text
arrival[i] = 1500
departure[j] = 1900

1500 <= 1900
```

A train arrives.

```text
current = 1
max = 3
i = 5
```

### Step 10

```text
arrival[i] = 1800
departure[j] = 1900

1800 <= 1900
```

A train arrives.

```text
current = 2
max = 3
i = 6
```

All arrivals are processed.

Therefore:

```text
Answer = 3
```

## Why Is the Maximum 3?

Around time `1100`, these three trains are present:

```text
Train 2: 940  -------- 1120
Train 3: 950  -------- 1130
Train 4: 1100 -------- 1200
```

So at that time:

```text
3 trains are present
```

Therefore:

```text
Maximum overlap = 3
```

## Code

```java
class Solution {
    public int maxOverlap(int[] arrival, int[] departure) {
        Arrays.sort(arrival);
        Arrays.sort(departure);

        int i = 0;
        int j = 0;

        int current = 0;
        int max = 0;

        while (i < arrival.length && j < departure.length) {

            if (arrival[i] <= departure[j]) {
                current++;
                max = Math.max(max, current);
                i++;
            } else {
                current--;
                j++;
            }
        }

        return max;
    }
}
```

## Important Condition

We use:

```java
arrival[i] <= departure[j]
```

because if arrival and departure happen at the same time, the train arriving is also counted at that time.

The overall idea is:

```text
If arrival <= departure:
    train arrives
    current++

Otherwise:
    train departs
    current--
```

Finally, `max` gives the maximum number of overlapping trains.

## Complexity

Sorting both arrays:

```text
O(N log N)
```

Two-pointer traversal:

```text
O(N)
```

Total:

```text
Time Complexity = O(N log N)
Space Complexity = O(1)
```
