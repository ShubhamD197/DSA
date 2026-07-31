# LeetCode 1235 — Maximum Profit in Job Scheduling

**Difficulty:** Hard  
**Topics:** Dynamic Programming, Binary Search, Sorting

---

# Problem Statement

You are given three arrays:

```text
startTime[i]
endTime[i]
profit[i]
```

Each job:

```text
Starts at startTime[i]
Ends at endTime[i]
Earns profit[i]
```

You **cannot perform overlapping jobs**.

Return the **maximum profit** you can earn.

---

## Example 1

```text
Input:

startTime = [1,2,3,3]
endTime   = [3,4,5,6]
profit    = [50,10,40,70]

Output:

120
```

Jobs:

```text
(1,3) → 50
(2,4) → 10
(3,5) → 40
(3,6) → 70
```

Best Schedule:

```text
(1,3) → 50
(3,6) → 70

Profit = 120
```

---

## Example 2

```text
Input:

startTime = [1,2,3,4,6]
endTime   = [3,5,10,6,9]
profit    = [20,20,100,70,60]

Output:

150
```

Best Schedule:

```text
(1,3) → 20
(4,6) → 70
(6,9) → 60

Profit = 150
```

---

# Main Idea

At every job we have **two choices**:

```text
Take the job
```

or

```text
Skip the job
```

If we take it,

we must jump to the **next non-overlapping job**.

This makes it a classic:

```text
Pick / Not Pick DP
```

---

# Approach 1: DP + Binary Search ⭐ Recommended

### Steps

1. Store all jobs.
2. Sort jobs by start time.
3. For every job:
   - Skip it
   - Take it
4. Binary search the next valid job.

---

## Java Code (Memoization)

```java
class Solution {

    int[][] jobs;
    Integer[] dp;

    public int jobScheduling(int[] startTime, int[] endTime, int[] profit) {

        int n = startTime.length;

        jobs = new int[n][3];

        for (int i = 0; i < n; i++) {
            jobs[i] = new int[]{startTime[i], endTime[i], profit[i]};
        }

        Arrays.sort(jobs, (a, b) -> a[0] - b[0]);

        dp = new Integer[n];

        return solve(0);
    }

    private int solve(int i) {

        if (i == jobs.length)
            return 0;

        if (dp[i] != null)
            return dp[i];

        int skip = solve(i + 1);

        int next = binarySearch(jobs[i][1]);

        int take = jobs[i][2] + solve(next);

        return dp[i] = Math.max(skip, take);
    }

    private int binarySearch(int endTime) {

        int low = 0;
        int high = jobs.length - 1;

        while (low <= high) {

            int mid = (low + high) / 2;

            if (jobs[mid][0] < endTime)
                low = mid + 1;
            else
                high = mid - 1;
        }

        return low;
    }
}
```

---

# Dry Run

Input:

```text
Start : [1,2,3,3]
End   : [3,4,5,6]
Profit: [50,10,40,70]
```

Sorted Jobs:

```text
(1,3,50)

(2,4,10)

(3,5,40)

(3,6,70)
```

---

### Job 0

```text
(1,3,50)
```

Choices:

Skip

↓

```text
solve(1)
```

Take

↓

Binary Search:

```text
Next job starting ≥ 3

Index = 2
```

Profit:

```text
50 + solve(2)
```

---

### Job 2

```text
(3,5,40)
```

Next Job:

```text
None
```

Take:

```text
40
```

Skip:

```text
70
```

Best:

```text
70
```

Return:

```text
70
```

---

Job 0

Take:

```text
50 + 70 = 120
```

Skip:

```text
70
```

Answer:

```text
120
```

---

# Binary Search Visualization

Jobs:

```text
Index

0 → Start = 1

1 → Start = 2

2 → Start = 3

3 → Start = 3
```

Suppose current job ends at:

```text
3
```

Binary search finds:

```text
First job

Start >= 3
```

Result:

```text
Index = 2
```

---

# Approach 2: Bottom-Up DP

Instead of recursion,

fill DP from the end.

---

## Java Code

```java
class Solution {

    public int jobScheduling(int[] startTime, int[] endTime, int[] profit) {

        int n = startTime.length;

        int[][] jobs = new int[n][3];

        for (int i = 0; i < n; i++) {
            jobs[i] = new int[]{startTime[i], endTime[i], profit[i]};
        }

        Arrays.sort(jobs, (a, b) -> a[0] - b[0]);

        int[] dp = new int[n + 1];

        for (int i = n - 1; i >= 0; i--) {

            int next = binarySearch(jobs, jobs[i][1]);

            int take = jobs[i][2] + dp[next];

            int skip = dp[i + 1];

            dp[i] = Math.max(take, skip);
        }

        return dp[0];
    }

    private int binarySearch(int[][] jobs, int end) {

        int low = 0;
        int high = jobs.length - 1;

        while (low <= high) {

            int mid = (low + high) / 2;

            if (jobs[mid][0] < end)
                low = mid + 1;
            else
                high = mid - 1;
        }

        return low;
    }
}
```

---

# Comparison

| Approach | Time | Space | Recommendation |
|----------|------|-------|---------------|
| Memoization + Binary Search | O(n log n) | O(n) | ⭐ Best |
| Tabulation + Binary Search | O(n log n) | O(n) | Also Good |

---

# Why Binary Search?

Without Binary Search,

after choosing a job,

we'd have to scan linearly for the next valid job.

```text
O(n²)
```

Using Binary Search:

```text
Find next valid job

in O(log n)
```

Overall:

```text
Sorting

O(n log n)

+

DP

O(n)

+

Binary Search for each job

O(n log n)

=

O(n log n)
```

---

# Final Recommended Solution

```text
Sort Jobs
      ↓
For every job

Take
or
Skip

      ↓
Binary Search
for next valid job
      ↓
DP
```

---

# Complexity

**Time Complexity**

```text
O(n log n)
```

**Space Complexity**

```text
O(n)
```

---

# Core Pattern to Remember

```text
Weighted Interval Scheduling

        ↓

Sort Jobs

        ↓

Pick / Skip

        ↓

Binary Search

        ↓

DP
```

### Similar Problems

| Problem | Pattern |
|---------|---------|
| 1235. Maximum Profit in Job Scheduling | DP + Binary Search |
| 300. LIS | DP + Binary Search |
| 435. Non-overlapping Intervals | Greedy |
| 646. Maximum Length of Pair Chain | DP / Greedy |
| Weighted Interval Scheduling | DP + Binary Search |
