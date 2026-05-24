
# Matrix Chain Multiplication (MCM)

## Problem Statement

Given an array `arr[]` where the `i-th` matrix has dimensions:

[
Matrix_i = arr[i-1] \times arr[i]
]

Find the **minimum number of multiplications** needed to multiply all matrices.

---

# Example

Input:

```text
arr = [2, 1, 3, 4]
```

Matrices:

```text
M1 = 2×1
M2 = 1×3
M3 = 3×4
```

Two possible ways:

```text
((M1 × M2) × M3) = 30 operations

(M1 × (M2 × M3)) = 20 operations
```

✅ Minimum = `20`

---

# Intuition

We try every possible partition:

```text
(A B C D)

Can split as:

(A)(BCD)
(AB)(CD)
(ABC)(D)
```

For every partition `k`:

```text
cost =
cost of left part
+ cost of right part
+ cost of multiplying both results
```

---

# Recursive Formula

If matrices are from `i → j`

dp[i][j]=\min_{k=i}^{j-1}\left(dp[i][k]+dp[k+1][j]+arr[i-1]\times arr[k]\times arr[j]\right)

---

# Memoization (Top Down DP)

## Time Complexity

```text
O(N³)
```

## Space Complexity

```text
O(N²) + O(N) recursion stack
```

```java
import java.util.*;

class Solution {

    // Function to find minimum multiplication cost
    private static int solve(int i, int j, int[] arr, int[][] dp) {

        // Base Case:
        // Single matrix → no multiplication needed
        if (i == j) {
            return 0;
        }

        // Already computed
        if (dp[i][j] != -1) {
            return dp[i][j];
        }

        int minCost = Integer.MAX_VALUE;

        // Try every possible partition
        for (int k = i; k < j; k++) {

            // Cost of splitting at k
            int cost =
                    solve(i, k, arr, dp) +          // Left part
                    solve(k + 1, j, arr, dp) +      // Right part
                    arr[i - 1] * arr[k] * arr[j];   // Multiplication cost

            minCost = Math.min(minCost, cost);
        }

        return dp[i][j] = minCost;
    }

    static int matrixMultiplication(int[] arr) {

        int n = arr.length;

        int[][] dp = new int[n][n];

        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(1, n - 1, arr, dp);
    }
}
```

---

# Tabulation (Bottom Up DP)

## Time Complexity

```text
O(N³)
```

## Space Complexity

```text
O(N²)
```

```java
import java.util.*;

class Solution {

    static int matrixMultiplication(int[] arr) {

        int n = arr.length;

        // dp[i][j] = minimum cost to multiply matrices i → j
        int[][] dp = new int[n][n];

        // Build from smaller partitions to larger
        for (int i = n - 1; i >= 1; i--) {

            for (int j = i + 1; j < n; j++) {

                int minCost = Integer.MAX_VALUE;

                // Partition point
                for (int k = i; k < j; k++) {

                    int cost =
                            dp[i][k] +
                            dp[k + 1][j] +
                            arr[i - 1] * arr[k] * arr[j];

                    minCost = Math.min(minCost, cost);
                }

                dp[i][j] = minCost;
            }
        }

        return dp[1][n - 1];
    }
}
```

---



