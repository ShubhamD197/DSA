# Frog Jump I — Minimum Energy

**Difficulty:** Easy  
**Topics:** Dynamic Programming, Recursion, Memoization, Tabulation

---

## Problem Statement

There are `n` stairs, and the frog starts at the first stair.

The frog wants to reach the last stair.

From the current stair, the frog can jump:

```text
1 step
or
2 steps
```

The energy required for a jump is the absolute difference between the heights of the two stairs.

Return the **minimum energy** required to reach the last stair.

---

## Example 1

```text
Input:
height = [10, 20, 30, 10]

Output:
20
```

Explanation:

The frog can take:

```text
10 → 20 → 30 → 10
```

Cost:

```text
|20 - 10| + |30 - 20| + |10 - 30|
= 10 + 10 + 20
= 40
```

But a better path is:

```text
10 → 30 → 10
```

Cost:

```text
|30 - 10| + |10 - 30|
= 20 + 20
= 40
```

Actually, the minimum for this input is **40**, not 20.

So let's use the standard example:

```text
Input:
height = [10, 20, 30, 10]

Output:
20
```

The intended optimal path is:

```text
10 → 20 → 10
```

But this skips from index `1` to index `3`, which is a 2-step jump:

```text
|20 - 10| + |10 - 20|
= 10 + 10
= 20
```

Therefore:

```text
Answer = 20
```

---

## Example 2

```text
Input:
height = [10, 50, 10]

Output:
0
```

The frog can jump directly:

```text
10 → 10
```

Cost:

```text
|10 - 10| = 0
```

---

# Main Idea

At every stair, the frog has two choices:

```text
Jump 1 step
Jump 2 steps
```

For example:

```text
        10
       /  \
     20    30
    /  \   /
   30   10
```

We calculate the minimum cost to reach every stair.

This is a classic **Dynamic Programming** problem.

---

# Approach 1: Recursion

We define:

```text
f(i)
```

as:

> Minimum energy required to reach stair `i`.

To reach stair `i`, the frog can come from:

```text
i - 1
```

or:

```text
i - 2
```

Therefore:

```text
f(i) = min(
    f(i - 1) + |height[i] - height[i - 1]|,
    f(i - 2) + |height[i] - height[i - 2]|
)
```

Base cases:

```text
f(0) = 0
```

If there is only one stair, the frog is already there.

For `i = 1`:

```text
f(1) = |height[1] - height[0]|
```

---

# Approach 2: Recursion + Memoization

The recursive solution calculates the same states repeatedly.

For example:

```text
f(4)
├── f(3)
│   ├── f(2)
│   └── f(1)
└── f(2)
```

`f(2)` is calculated multiple times.

Memoization stores the answer for every index.

---

## Java Code

```java
class Solution {
    public int frogJump(int[] height) {
        int n = height.length;

        int[] dp = new int[n];
        Arrays.fill(dp, -1);

        return helper(n - 1, height, dp);
    }

    private int helper(int index, int[] height, int[] dp) {
        if (index == 0) {
            return 0;
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int oneStep = helper(index - 1, height, dp)
                + Math.abs(height[index] - height[index - 1]);

        int twoStep = Integer.MAX_VALUE;

        if (index > 1) {
            twoStep = helper(index - 2, height, dp)
                    + Math.abs(height[index] - height[index - 2]);
        }

        return dp[index] = Math.min(oneStep, twoStep);
    }
}
```

---

# Dry Run

Consider:

```text
height = [10, 20, 30, 10]
```

We need:

```text
f(3)
```

---

## Calculate `f(3)`

From stair `3`, we can come from:

```text
stair 2
```

or:

```text
stair 1
```

### One-step jump

```text
f(2) + |10 - 30|
```

### Two-step jump

```text
f(1) + |10 - 20|
```

So:

```text
f(3) = min(f(2) + 20, f(1) + 10)
```

---

## Calculate `f(2)`

```text
f(2) = min(
    f(1) + |30 - 20|,
    f(0) + |30 - 10|
)
```

```text
f(2) = min(
    f(1) + 10,
    0 + 20
)
```

Since:

```text
f(1) = 10
```

We get:

```text
f(2) = min(20, 20)
     = 20
```

---

## Calculate `f(3)`

```text
f(3) = min(
    20 + 20,
    10 + 10
)
```

```text
f(3) = min(40, 20)
     = 20
```

Final answer:

```text
20
```

Optimal path:

```text
10 → 20 → 10
```

---

# Approach 3: Tabulation

Instead of recursion, we can calculate the DP array from left to right.

```text
dp[i] = minimum energy required to reach stair i
```

For every stair:

```text
oneStep = dp[i - 1] + |height[i] - height[i - 1]|

twoStep = dp[i - 2] + |height[i] - height[i - 2]|
```

Then:

```text
dp[i] = min(oneStep, twoStep)
```

---

## Java Code

```java
class Solution {
    public int frogJump(int[] height) {
        int n = height.length;

        int[] dp = new int[n];

        dp[0] = 0;

        for (int i = 1; i < n; i++) {
            int oneStep = dp[i - 1]
                    + Math.abs(height[i] - height[i - 1]);

            int twoStep = Integer.MAX_VALUE;

            if (i > 1) {
                twoStep = dp[i - 2]
                        + Math.abs(height[i] - height[i - 2]);
            }

            dp[i] = Math.min(oneStep, twoStep);
        }

        return dp[n - 1];
    }
}
```

---

# Dry Run — Tabulation

Input:

```text
height = [10, 20, 30, 10]
```

Initialize:

```text
dp = [0, ?, ?, ?]
```

### `i = 1`

```text
oneStep = dp[0] + |20 - 10|
        = 0 + 10
        = 10
```

No two-step jump.

```text
dp[1] = 10
```

Now:

```text
dp = [0, 10, ?, ?]
```

---

### `i = 2`

One step:

```text
dp[1] + |30 - 20|
= 10 + 10
= 20
```

Two steps:

```text
dp[0] + |30 - 10|
= 0 + 20
= 20
```

Therefore:

```text
dp[2] = 20
```

Now:

```text
dp = [0, 10, 20, ?]
```

---

### `i = 3`

One step:

```text
dp[2] + |10 - 30|
= 20 + 20
= 40
```

Two steps:

```text
dp[1] + |10 - 20|
= 10 + 10
= 20
```

Therefore:

```text
dp[3] = 20
```

Final DP:

```text
dp = [0, 10, 20, 20]
```

Answer:

```text
20
```

---

# Approach 4: Space Optimized DP

To calculate:

```text
dp[i]
```

we only need:

```text
dp[i - 1]
dp[i - 2]
```

We don't need the complete DP array.

So we can use two variables:

```text
prev1 = dp[i - 1]
prev2 = dp[i - 2]
```

---

## Java Code

```java
class Solution {
    public int frogJump(int[] height) {
        int n = height.length;

        int prev2 = 0;
        int prev1 = 0;

        for (int i = 1; i < n; i++) {
            int oneStep = prev1
                    + Math.abs(height[i] - height[i - 1]);

            int twoStep = Integer.MAX_VALUE;

            if (i > 1) {
                twoStep = prev2
                        + Math.abs(height[i] - height[i - 2]);
            }

            int current = Math.min(oneStep, twoStep);

            prev2 = prev1;
            prev1 = current;
        }

        return prev1;
    }
}
```

---

# Dry Run — Space Optimized

Input:

```text
height = [10, 20, 30, 10]
```

Initial:

```text
prev2 = 0
prev1 = 0
```

---

### `i = 1`

```text
oneStep = 0 + |20 - 10|
        = 10
```

```text
current = 10
```

Update:

```text
prev2 = 0
prev1 = 10
```

---

### `i = 2`

```text
oneStep = 10 + |30 - 20|
        = 20
```

```text
twoStep = 0 + |30 - 10|
        = 20
```

```text
current = 20
```

Update:

```text
prev2 = 10
prev1 = 20
```

---

### `i = 3`

```text
oneStep = 20 + |10 - 30|
        = 40
```

```text
twoStep = 10 + |10 - 20|
        = 20
```

```text
current = 20
```

Final:

```text
prev1 = 20
```

Answer:

```text
20
```

---

# Comparison

| Approach | Time | Space | Recommendation |
|---|---:|---:|---|
| Recursion | O(2^n) | O(n) | Not recommended |
| Memoization | O(n) | O(n) + recursion | Good |
| Tabulation | O(n) | O(n) | Better |
| Space Optimized | O(n) | O(1) | Best |

---

# Which Approach Is Better?

The **Space Optimized DP** is the best when you only need the minimum energy.

Why?

The recurrence only depends on:

```text
dp[i - 1]
dp[i - 2]
```

So storing the entire `dp` array is unnecessary.

We only maintain:

```text
prev2 → dp[i - 2]
prev1 → dp[i - 1]
```

Then calculate:

```text
current → dp[i]
```

The pattern is:

```text
prev2     prev1     current
  ↓         ↓          ↓
dp[i-2]   dp[i-1]    dp[i]
```

---

# Core Pattern to Remember

This is a **1D DP** problem.

At every index:

```text
Reach i from i-1
        OR
Reach i from i-2
```

So:

```text
dp[i] = min(
    dp[i-1] + abs(height[i] - height[i-1]),
    dp[i-2] + abs(height[i] - height[i-2])
)
```

The general pattern is:

```text
Current state
     ↓
Try all possible previous states
     ↓
Calculate transition cost
     ↓
Take minimum
```

**Time Complexity:** `O(n)`  
**Space Complexity:** `O(1)` with space optimization.
