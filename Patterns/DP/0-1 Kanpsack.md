# 0/1 Knapsack

**Difficulty:** Medium  
**Topic:** Dynamic Programming

---

# Problem Statement

You are given:

- `n` items
- `weight[i]` → weight of the `i-th` item
- `value[i]` → value of the `i-th` item
- `W` → maximum capacity of the knapsack

You need to select items such that:

1. The total weight does not exceed `W`.
2. The total value is maximum.

The important condition is:

> Each item can either be **taken once** or **not taken at all**.

You cannot take the same item multiple times.

That's why it is called **0/1 Knapsack**.


# Example 1

Input:

weight = [1, 2, 3]
value  = [6, 10, 12]
W = 5


We can choose:

```text
Item 1 → weight = 1, value = 6
Item 2 → weight = 2, value = 10
Item 3 → weight = 3, value = 12
```

Possible choices:

```text
Item 1 + Item 2
Weight = 1 + 2 = 3
Value  = 6 + 10 = 16

Item 1 + Item 3
Weight = 1 + 3 = 4
Value  = 6 + 12 = 18

Item 2 + Item 3
Weight = 2 + 3 = 5
Value  = 10 + 12 = 22
```

Maximum value:

```text
22
```

Output:

```text
22
```

---

# Example 2

```text
Input:

weight = [3, 4, 5]
value  = [30, 50, 60]
W = 8
```

Best choice:

```text
Item 2 + Item 3

Weight = 4 + 5 = 9
```

This exceeds capacity, so it is not allowed.

Try:

```text
Item 1 + Item 2

Weight = 3 + 4 = 7
Value  = 30 + 50 = 80
```

Try:

```text
Item 1 + Item 3

Weight = 3 + 5 = 8
Value  = 30 + 60 = 90
```

Maximum:

```text
90
```

Output:

```text
90
```

---

# Core Idea

For every item, we have exactly **two choices**:

```text
             Item
            /    \
       Take it   Don't take it
```

If we take the item:

```text
value[i] + answer of remaining capacity
```

If we don't take it:

```text
answer without current item
```

Therefore:

```text
answer = max(
    take,
    notTake
)
```

This is the main idea behind all 0/1 Knapsack solutions.

---

# Approach 1: Recursion

## Idea

At every item, we make two choices:

```text
Take
```

or:

```text
Not Take
```

We start from the last item.

---

## Java Code

```java
class Solution {

    static int knapsack(
            int W,
            int[] val,
            int[] wt) {

        int n = wt.length;

        return solve(
                n - 1,
                W,
                val,
                wt
        );
    }

    private static int solve(
            int index,
            int capacity,
            int[] val,
            int[] wt) {

        // Base case
        if (index == 0) {

            if (wt[0] <= capacity) {
                return val[0];
            }

            return 0;
        }

        // Don't take current item
        int notTake = solve(
                index - 1,
                capacity,
                val,
                wt
        );

        int take = 0;

        // Take current item if possible
        if (wt[index] <= capacity) {

            take = val[index] +
                    solve(
                            index - 1,
                            capacity - wt[index],
                            val,
                            wt
                    );
        }

        return Math.max(
                take,
                notTake
        );
    }
}
```

---

# Dry Run

Consider:

```text
weight = [1, 2, 3]
value  = [6, 10, 12]
W = 5
```

Start:

```text
index = 2
capacity = 5
```

Item 2:

```text
weight = 3
value = 12
```

We have two choices.

### Choice 1: Take Item 2

```text
Value = 12
Remaining capacity = 5 - 3 = 2
```

Now solve:

```text
index = 1
capacity = 2
```

We can take Item 1:

```text
weight = 2
value = 10
```

Total:

```text
12 + 10 = 22
```

### Choice 2: Don't Take Item 2

Solve:

```text
index = 1
capacity = 5
```

Best value:

```text
16
```

Therefore:

```text
max(22, 16)
= 22
```

Answer:

```text
22
```

---

# Recursion Tree

For the same example:

```text
                         (Item 2, Capacity 5)
                           /              \
                       Take              Not Take
                         /                  \
              (Item 1, Capacity 2)    (Item 1, Capacity 5)
                   /       \              /       \
                Take     Not Take      Take     Not Take
                  |          |           |          |
                 22         12          18          6
```

The answer is:

```text
22
```

---

# Complexity

Each item creates two choices.

```text
Time: O(2^n)
Space: O(n)
```

The space is due to the recursion stack.

This solution is inefficient because the same states are calculated repeatedly.

For example:

```text
solve(index = 1, capacity = 2)
```

may be calculated multiple times.

We can solve this using **Memoization**.

---

# Approach 2: Memoization

## Idea

The state of the problem is determined by:

```text
index
capacity
```

So we create:

```text
dp[index][capacity]
```

where:

```text
dp[index][capacity]
```

stores the maximum value we can obtain using items from `0` to `index` with the given capacity.

If we have already calculated a state, return it directly.

---

## Java Code

```java
import java.util.*;

class Solution {

    static int knapsack(
            int W,
            int[] val,
            int[] wt) {

        int n = wt.length;

        int[][] dp = new int[n][W + 1];

        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(
                n - 1,
                W,
                val,
                wt,
                dp
        );
    }

    private static int solve(
            int index,
            int capacity,
            int[] val,
            int[] wt,
            int[][] dp) {

        // Base case
        if (index == 0) {

            if (wt[0] <= capacity) {
                return val[0];
            }

            return 0;
        }

        // Already calculated
        if (dp[index][capacity] != -1) {
            return dp[index][capacity];
        }

        // Don't take
        int notTake = solve(
                index - 1,
                capacity,
                val,
                wt,
                dp
        );

        int take = 0;

        // Take
        if (wt[index] <= capacity) {

            take = val[index] +
                    solve(
                            index - 1,
                            capacity - wt[index],
                            val,
                            wt,
                            dp
                    );
        }

        return dp[index][capacity] =
                Math.max(
                        take,
                        notTake
                );
    }
}
```

---

# Dry Run

Input:

```text
weight = [1, 2, 3]
value  = [6, 10, 12]
W = 5
```

Initially:

```text
dp = -1
```

We call:

```text
solve(2, 5)
```

Take Item 2:

```text
12 + solve(1, 2)
```

`solve(1,2)`:

```text
Take Item 1:
10 + solve(0,0)
```

Result:

```text
10
```

So:

```text
Take = 12 + 10
     = 22
```

Don't take Item 2:

```text
solve(1,5)
```

Result:

```text
16
```

Therefore:

```text
dp[2][5] = max(22, 16)
         = 22
```

If we encounter:

```text
solve(1,2)
```

again, we simply return:

```text
dp[1][2]
```

instead of calculating it again.

---

# Complexity

There are:

```text
n × W
```

possible states.

Each state is calculated once.

```text
Time: O(n × W)
Space: O(n × W) + O(n)
```

The `O(n)` is recursion stack space.

This is much better than:

```text
O(2^n)
```

---

# Approach 3: Tabulation

## Idea

Convert the recursive solution into bottom-up DP.

We create:

```text
dp[n][W + 1]
```

Meaning:

```text
dp[i][w]
```

= maximum value using items from:

```text
0 to i
```

with capacity:

```text
w
```

---

## Base Case

For the first item:

```text
if wt[0] <= capacity
```

we can take it.

So:

```text
dp[0][capacity] = val[0]
```

Otherwise:

```text
dp[0][capacity] = 0
```

---

## Transition

For every item:

```text
i
```

and capacity:

```text
w
```

Don't take:

```text
notTake = dp[i - 1][w]
```

Take:

```text
take = val[i] + dp[i - 1][w - wt[i]]
```

if:

```text
wt[i] <= w
```

Then:

```text
dp[i][w] = max(
    take,
    notTake
)
```

---

## Java Code

```java
class Solution {

    static int knapsack(
            int W,
            int[] val,
            int[] wt) {

        int n = wt.length;

        int[][] dp = new int[n][W + 1];

        // Base case
        for (int capacity = wt[0];
             capacity <= W;
             capacity++) {

            dp[0][capacity] = val[0];
        }

        // Process remaining items
        for (int i = 1; i < n; i++) {

            for (int capacity = 0;
                 capacity <= W;
                 capacity++) {

                // Don't take current item
                int notTake = dp[i - 1][capacity];

                int take = 0;

                // Take current item
                if (wt[i] <= capacity) {

                    take = val[i] +
                            dp[
                                i - 1
                            ][
                                capacity - wt[i]
                            ];
                }

                dp[i][capacity] =
                        Math.max(
                                take,
                                notTake
                        );
            }
        }

        return dp[n - 1][W];
    }
}
```

---

# Dry Run — Tabulation

Input:

```text
weight = [1, 2, 3]
value  = [6, 10, 12]
W = 5
```

DP table:

```text
       Capacity
       0   1   2   3   4   5
       -----------------------
Item 0 0   6   6   6   6   6
Item 1 0   6  10  16  16  16
Item 2 0   6  10  16  18  22
```

Let's understand the final cell:

```text
dp[2][5]
```

We consider Item 2:

```text
weight = 3
value = 12
```

### Don't Take

```text
dp[1][5]
= 16
```

### Take

Remaining capacity:

```text
5 - 3 = 2
```

So:

```text
12 + dp[1][2]
```

```text
12 + 10
= 22
```

Therefore:

```text
dp[2][5] = max(16, 22)
         = 22
```

---

# Approach 4: Space Optimized DP

## Observation

In the 2D DP solution:

```text
dp[i][capacity]
```

only depends on:

```text
dp[i - 1]
```

So we don't need the entire 2D table.

We can use a single array:

```text
dp[capacity]
```

---

## Important Point

When using a **1D DP array**, we must iterate capacity from:

```text
W → 0
```

Why?

Because each item can be used only **once**.

If we iterate from:

```text
0 → W
```

we may accidentally use the same item multiple times.

For example:

```text
dp[capacity - wt[i]]
```

might already have been updated using the current item.

Going backward prevents this.

---

## Java Code

```java
class Solution {

    static int knapsack(
            int W,
            int[] val,
            int[] wt) {

        int n = wt.length;

        int[] dp = new int[W + 1];

        for (int i = 0; i < n; i++) {

            // Go backwards
            for (int capacity = W;
                 capacity >= wt[i];
                 capacity--) {

                dp[capacity] = Math.max(
                        dp[capacity],
                        val[i] +
                        dp[
                            capacity - wt[i]
                        ]
                );
            }
        }

        return dp[W];
    }
}
```

---

# Dry Run — 1D DP

Input:

```text
weight = [1, 2, 3]
value  = [6, 10, 12]
W = 5
```

Initially:

```text
dp = [0, 0, 0, 0, 0, 0]
```

---

## Process Item 0

```text
weight = 1
value = 6
```

Process capacity backward.

After processing:

```text
dp = [0, 6, 6, 6, 6, 6]
```

---

## Process Item 1

```text
weight = 2
value = 10
```

For capacity `5`:

```text
take = 10 + dp[3]
     = 10 + 6
     = 16
```

So:

```text
dp[5] = 16
```

After processing:

```text
dp = [0, 6, 10, 16, 16, 16]
```

---

## Process Item 2

```text
weight = 3
value = 12
```

For capacity `5`:

```text
take = 12 + dp[2]
     = 12 + 10
     = 22
```

Therefore:

```text
dp[5] = max(16, 22)
      = 22
```

Final:

```text
dp = [0, 6, 10, 16, 18, 22]
```

Answer:

```text
22
```

---

# Why Do We Iterate Backward?

This is one of the most important concepts in **0/1 Knapsack**.

Consider:

```text
weight = [2]
value = [10]
W = 4
```

There is only one item.

We should be able to take it only once.

Correct answer:

```text
10
```

If we iterate forward:

```text
capacity = 2
```

We get:

```text
dp[2] = 10
```

Then:

```text
capacity = 4
```

We might use:

```text
dp[4 - 2]
= dp[2]
```

which was just updated using the same item.

So we get:

```text
10 + 10 = 20
```

This means we used the same item twice.

That's wrong.

Therefore, for 0/1 Knapsack:

```text
capacity → W to 0
```

For **Unbounded Knapsack**, where items can be reused, we generally iterate:

```text
capacity → 0 to W
```

---

# Comparison

| Approach    | Time     | Space    | Best For       |
| ----------- | -------- | -------- | -------------- |
| Recursion   | O(2ⁿ)    | O(n)     | Understanding  |
| Memoization | O(n × W) | O(n × W) | Top-down DP    |
| Tabulation  | O(n × W) | O(n × W) | Bottom-up DP   |
| 1D DP       | O(n × W) | O(W)     | Best optimized |

---

# Which Approach Is Better?

## Best for Understanding

**Recursion**

It clearly shows the two choices:

```text
Take
Not Take
```

But it is too slow for large `n`.

---

## Best for Learning DP

**Memoization**

Start with recursion and then add:

```text
dp[index][capacity]
```

This teaches how repeated states are optimized.

---

## Best for Interview Explanation

**Tabulation**

It avoids recursion and clearly shows the DP table.

You can explain:

```text
dp[i][w]
```

as:

> Maximum value we can obtain using the first `i` items with capacity `w`.

---

## Best for Space Optimization

**1D DP**

The final solution uses:

```text
Time: O(n × W)
Space: O(W)
```

This is usually the most efficient implementation.

---

# 2D vs 1D DP

### 2D DP

```text
dp[i][w]
```

Stores the result for every item and every capacity.

Space:

```text
O(n × W)
```

### 1D DP

```text
dp[w]
```

Only stores the previous required results.

Space:

```text
O(W)
```

But remember:

```text
0/1 Knapsack
→ Iterate capacity backward
```

---

# Core Pattern to Remember

For every item:

```text
             Item
            /    \
         Take    Not Take
           |        |
     value +        |
   remaining        |
      DP            |
           \        /
             MAX
```

The recurrence is:

```text
dp[i][w] =
    max(
        dp[i - 1][w],
        value[i] + dp[i - 1][w - weight[i]]
    )
```

when:

```text
weight[i] <= w
```

Otherwise:

```text
dp[i][w] = dp[i - 1][w]
```

---

# Final Recommended Code

```java
class Solution {

    static int knapsack(
            int W,
            int[] val,
            int[] wt) {

        int n = wt.length;

        int[] dp = new int[W + 1];

        for (int i = 0; i < n; i++) {

            // Reverse direction ensures
            // each item is used at most once
            for (int capacity = W;
                 capacity >= wt[i];
                 capacity--) {

                int take =
                        val[i] +
                        dp[capacity - wt[i]];

                int notTake =
                        dp[capacity];

                dp[capacity] =
                        Math.max(
                                take,
                                notTake
                        );
            }
        }

        return dp[W];
    }
}
```

### Final Complexity

```text
Time: O(n × W)
Space: O(W)
```

### Most Important Things to Remember

```text
0/1 Knapsack
     ↓
Every item: Take / Not Take
     ↓
DP State: Item + Capacity
     ↓
1D Optimization
     ↓
Iterate Capacity Backward
```

**The key difference from Unbounded Knapsack:**

```text
0/1 Knapsack
→ Each item once
→ Iterate capacity backward

Unbounded Knapsack
→ Item can be reused
→ Iterate capacity forward
```

```
```
