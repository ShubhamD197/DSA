
# GFG — Longest Common Substring

**Difficulty:** Medium  
**Topics:** Dynamic Programming, Strings

---

## Problem Statement

Given two strings `s1` and `s2`, find the length of the **Longest Common Substring**.

A substring is a sequence of characters that appears in both strings **continuously and in the same order**.

---

## Example 1

```text
Input:
s1 = "ABCDGH"
s2 = "ACDGHR"

Output:
4
````

Explanation:

The longest common substring is:

```text
"CDGH"
```

Length:

```text
4
```

---

## Example 2

```text
Input:
s1 = "ABC"
s2 = "ACB"
```

Common substrings include:

```text
"A"
"B"
"C"
```

There is no common substring of length `2`.

Output:

```text
1
```

---

## Example 3

```text
Input:
s1 = "ABCD"
s2 = "XYZ"
```

Output:

```text
0
```

There is no common substring.

---

# Important Difference: Subsequence vs Substring

This problem is very similar to **LeetCode 1143 — Longest Common Subsequence**, but there is one major difference.

### Longest Common Subsequence

Characters don't need to be continuous.

```text
s1 = "abcde"
s2 = "ace"

LCS = "ace"
```

Answer:

```text
3
```

---

### Longest Common Substring

Characters must be continuous.

```text
s1 = "abcde"
s2 = "ace"
```

Common substrings:

```text
"a"
"c"
"e"
```

Answer:

```text
1
```

So remember:

```text
Subsequence → Can skip characters

Substring → Cannot skip characters
```

---

# Approach 1: Recursion

For Longest Common Substring, recursion is not as straightforward as LCS because we need to track the **current continuous matching length**.

We use:

```text
i → current index in s1
j → current index in s2
count → current matching substring length
```

If:

```text
s1[i] == s2[j]
```

we continue the substring:

```text
count + 1
```

If they don't match, the current substring breaks, so:

```text
count = 0
```

But we still need to try starting from other positions.

---

## Java Code

```java
class Solution {
    public int longestCommonSubstr(String s1, String s2) {
        return solve(s1, s2, 0, 0, 0);
    }

    private int solve(String s1, String s2, int i, int j, int count) {
        if (i == s1.length() || j == s2.length()) {
            return count;
        }

        int currentCount = count;

        if (s1.charAt(i) == s2.charAt(j)) {
            currentCount = solve(s1, s2, i + 1, j + 1, count + 1);
        }

        int skipS1 = solve(s1, s2, i + 1, j, 0);
        int skipS2 = solve(s1, s2, i, j + 1, 0);

        return Math.max(currentCount, Math.max(skipS1, skipS2));
    }
}
```

---

## Dry Run

Consider:

```text
s1 = "ABCDGH"
s2 = "ACDGHR"
```

Start:

```text
i = 0 → A
j = 0 → A
```

Characters match:

```text
A == A
```

So:

```text
count = 1
```

Move both pointers:

```text
i = 1 → B
j = 1 → C
```

Now:

```text
B != C
```

The current substring breaks:

```text
count = 0
```

We try other positions.

Eventually we find:

```text
C == C
D == D
G == G
H == H
```

So:

```text
count = 4
```

The substring is:

```text
"CDGH"
```

Answer:

```text
4
```

---

## Complexity

```text
Time: O(2^(m+n))
Space: O(m+n)
```

This is very inefficient.

So we use Dynamic Programming.

---

# Approach 2: Tabulation

## Main Idea

Create:

```java
dp[i][j]
```

where:

```text
dp[i][j]
```

represents the length of the longest common substring **ending at**:

```text
s1[i - 1]
s2[j - 1]
```

This "ending at" part is very important.

---

## If Characters Match

If:

```text
s1[i - 1] == s2[j - 1]
```

then the substring continues from the diagonal:

```text
dp[i][j] = 1 + dp[i - 1][j - 1]
```

---

## If Characters Don't Match

The substring is broken.

So:

```text
dp[i][j] = 0
```

This is the **main difference from Longest Common Subsequence**.

For LCS:

```text
Mismatch:
dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

For Longest Common Substring:

```text
Mismatch:
dp[i][j] = 0
```

We maintain a separate variable `maxLength` to store the overall maximum.

---

## Java Code

```java
class Solution {
    public int longestCommonSubstr(String s1, String s2) {
        int m = s1.length();
        int n = s2.length();

        int[][] dp = new int[m + 1][n + 1];
        int maxLength = 0;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                    maxLength = Math.max(maxLength, dp[i][j]);
                } else {
                    dp[i][j] = 0;
                }
            }
        }

        return maxLength;
    }
}
```

---

# Dry Run

Let's take:

```text
s1 = "ABCDGH"
s2 = "ACDGHR"
```

The DP table is:

```text
       ""  A  C  D  G  H  R
    -------------------------
""  |  0  0  0  0  0  0  0
A   |  0  1  0  0  0  0  0
B   |  0  0  0  0  0  0  0
C   |  0  0  1  0  0  0  0
D   |  0  0  0  2  0  0  0
G   |  0  0  0  0  3  0  0
H   |  0  0  0  0  0  4  0
```

Let's understand the matching sequence.

### Match `A`

```text
s1 = A
s2 = A
```

Therefore:

```text
dp[1][1] = 1
```

---

### Match `C`

```text
s1 = C
s2 = C
```

The diagonal value is:

```text
dp[2][1] = 0
```

So:

```text
dp[3][2] = 1
```

---

### Match `D`

Now:

```text
D == D
```

The previous diagonal contains:

```text
dp[3][2] = 1
```

So:

```text
dp[4][3] = 1 + 1
         = 2
```

Substring:

```text
"CD"
```

---

### Match `G`

```text
dp[5][4] = 1 + dp[4][3]
         = 1 + 2
         = 3
```

Substring:

```text
"CDG"
```

---

### Match `H`

```text
dp[6][5] = 1 + dp[5][4]
         = 1 + 3
         = 4
```

Substring:

```text
"CDGH"
```

Maximum:

```text
4
```

Answer:

```text
4
```

---

# Approach 3: Space Optimized DP

We only need the previous row to calculate the current row.

So instead of:

```text
dp[m + 1][n + 1]
```

we can use:

```text
dp[n + 1]
```

For every match:

```text
dp[j] = 1 + previous diagonal
```

We need to preserve the old diagonal value before overwriting `dp[j]`.

---

## Java Code

```java
class Solution {
    public int longestCommonSubstr(String s1, String s2) {
        int m = s1.length();
        int n = s2.length();

        int[] dp = new int[n + 1];
        int maxLength = 0;

        for (int i = 1; i <= m; i++) {
            int diagonal = 0;

            for (int j = 1; j <= n; j++) {
                int previous = dp[j];

                if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                    dp[j] = 1 + diagonal;
                    maxLength = Math.max(maxLength, dp[j]);
                } else {
                    dp[j] = 0;
                }

                diagonal = previous;
            }
        }

        return maxLength;
    }
}
```

---

# Dry Run — Space Optimized

Input:

```text
s1 = "ABCDGH"
s2 = "ACDGHR"
```

Initially:

```text
dp = [0, 0, 0, 0, 0, 0, 0]
```

After processing characters, the matching sequence:

```text
C → D → G → H
```

updates the DP values:

```text
C → 1
D → 2
G → 3
H → 4
```

Final maximum:

```text
maxLength = 4
```

Answer:

```text
4
```

---

# Comparison with LCS

This is the most important thing to remember.

|                        | Longest Common Subsequence | Longest Common Substring |
| ---------------------- | -------------------------- | ------------------------ |
| Characters continuous? | No                         | Yes                      |
| Match                  | `1 + diagonal`             | `1 + diagonal`           |
| Mismatch               | `max(top, left)`           | `0`                      |
| Result                 | `dp[m][n]`                 | Maximum value in DP      |
| Example                | `"ace"`                    | `"CDGH"`                 |

---

# Example Difference

Consider:

```text
s1 = "abcde"
s2 = "ace"
```

### LCS

We can skip characters:

```text
a b c d e
↓   ↓   ↓
a   c   e
```

Answer:

```text
3
```

### Longest Common Substring

Characters must be continuous.

Common substrings:

```text
"a"
"c"
"e"
```

Answer:

```text
1
```

---

# Complexity Comparison

| Approach        |       Time |    Space |
| --------------- | ---------: | -------: |
| Recursion       | O(2^(m+n)) |   O(m+n) |
| Tabulation      |   O(m × n) | O(m × n) |
| Space Optimized |   O(m × n) |     O(n) |

---

# Which Approach Is Better?

### Recursion

Good for understanding the idea but not suitable for large inputs.

### Tabulation

Best for learning and explaining the DP logic.

The key transition is very easy:

```text
Match    → 1 + diagonal
Mismatch → 0
```

### Space Optimized

Best when you only need the **length** of the longest common substring.

```text
Time: O(m × n)
Space: O(n)
```

---

# Final Recommended Solution

For GFG, I recommend the **Space Optimized DP** solution:

```java
class Solution {
    public int longestCommonSubstr(String s1, String s2) {
        int m = s1.length();
        int n = s2.length();

        int[] dp = new int[n + 1];
        int maxLength = 0;

        for (int i = 1; i <= m; i++) {
            int diagonal = 0;

            for (int j = 1; j <= n; j++) {
                int previous = dp[j];

                if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                    dp[j] = 1 + diagonal;
                    maxLength = Math.max(maxLength, dp[j]);
                } else {
                    dp[j] = 0;
                }

                diagonal = previous;
            }
        }

        return maxLength;
    }
}
```

**Time Complexity:** `O(m × n)`
**Space Complexity:** `O(n)`

---

## 🔥 One Thing to Remember

```text
Longest Common Subsequence:

Mismatch → Take MAX(top, left)

Longest Common Substring:

Mismatch → RESET TO 0
```

That's the biggest difference between **LeetCode 1143 — LCS** and **GFG — Longest Common Substring**.

```
```
