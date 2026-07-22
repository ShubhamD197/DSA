# 51. N-Queens

**Difficulty:** Hard

**Topic:** Backtracking

---

# Problem Statement

The **N-Queens** problem asks us to place `n` queens on an `n × n` chessboard such that no two queens attack each other.

A queen can attack another queen if they are in the same:

- Row
- Column
- Diagonal

Given an integer `n`, return all distinct solutions.

Each solution is represented as a board where:

```text
Q = Queen
. = Empty cell
```

---

# Example 1

```text
Input:
n = 4

Output:
[
    [".Q..",
     "...Q",
     "Q...",
     "..Q."],

    ["..Q.",
     "Q...",
     "...Q",
     ".Q.."]
]
```

There are exactly `2` possible solutions for `n = 4`.

---

# Example 2

```text
Input:
n = 1

Output:
[
    ["Q"]
]
```

There is only one cell, so we can place the queen there.

---

# Example 3

```text
Input:
n = 2

Output:
[]
```

It is impossible to place `2` queens on a `2 × 2` board without them attacking each other.

---

# Understanding the Problem

For:

```text
n = 4
```

we have a:

```text
4 × 4
```

board.

We need to place exactly:

```text
4 queens
```

The important observation is:

> We can place exactly one queen in every row.

Once we place a queen in a row, we move to the next row.

For example:

```text
. Q . .
. . . Q
Q . . .
. . Q .
```

This is a valid solution.

The queens are:

```text
Row 0 → Column 1
Row 1 → Column 3
Row 2 → Column 0
Row 3 → Column 2
```

---

# Approach 1: Basic Backtracking

## Idea

We process the board row by row.

For every row:

1. Try every column.
2. Check whether placing a queen is safe.
3. Place the queen.
4. Recursively move to the next row.
5. Remove the queen while backtracking.

The recursive structure is:

```text
Place Queen
     ↓
Move to Next Row
     ↓
Place Next Queen
     ↓
...
     ↓
Undo Choice
```

---

## Java Code

```java
import java.util.*;

class Solution {

    public List<List<String>> solveNQueens(int n) {

        List<List<String>> answer = new ArrayList<>();

        char[][] board = new char[n][n];

        // Fill board with empty cells
        for (int i = 0; i < n; i++) {
            Arrays.fill(board[i], '.');
        }

        backtrack(0, board, answer);

        return answer;
    }

    private void backtrack(
            int row,
            char[][] board,
            List<List<String>> answer) {

        // All rows processed
        if (row == board.length) {
            answer.add(createBoard(board));
            return;
        }

        // Try every column in current row
        for (int col = 0; col < board.length; col++) {

            // Check whether queen can be placed
            if (isSafe(row, col, board)) {

                // Choose
                board[row][col] = 'Q';

                // Explore next row
                backtrack(row + 1, board, answer);

                // Undo
                board[row][col] = '.';
            }
        }
    }

    private boolean isSafe(
            int row,
            int col,
            char[][] board) {

        int n = board.length;

        // Check same column
        for (int i = 0; i < row; i++) {
            if (board[i][col] == 'Q') {
                return false;
            }
        }

        // Check upper-left diagonal
        for (
            int i = row - 1, j = col - 1;
            i >= 0 && j >= 0;
            i--, j--
        ) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }

        // Check upper-right diagonal
        for (
            int i = row - 1, j = col + 1;
            i >= 0 && j < n;
            i--, j++
        ) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }

        return true;
    }

    private List<String> createBoard(char[][] board) {

        List<String> result = new ArrayList<>();

        for (char[] row : board) {
            result.add(new String(row));
        }

        return result;
    }
}
```

---

# Dry Run

Let's use:

```text
n = 4
```

Initially:

```text
....
....
....
....
```

We start from:

```text
row = 0
```

---

## Step 1: Place Queen at `(0,0)`

```text
Q...
....
....
....
```

Move to row `1`.

Try `(1,0)`:

```text
Q...
Q...
....
....
```

Invalid because same column.

Try `(1,1)`:

```text
Q...
.Q..
....
....
```

Invalid because diagonal.

Try `(1,2)`:

```text
Q...
..Q.
....
....
```

Invalid because diagonal.

Try `(1,3)`:

```text
Q...
...Q
....
....
```

Valid.

---

## Step 2: Continue

Now:

```text
Q...
...Q
....
....
```

Try row `2`.

Possible positions are checked.

No valid placement allows us to complete all remaining rows.

So we backtrack.

Remove queen from row `1`:

```text
Q...
....
....
....
```

Then try other columns.

Eventually, no solution is found starting with:

```text
Q...
```

So we backtrack from row `0`.

---

## Step 3: Place Queen at `(0,1)`

```text
.Q..
....
....
....
```

Try row `1`.

Place queen at `(1,3)`:

```text
.Q..
...Q
....
....
```

Now row `2`.

Place queen at `(2,0)`:

```text
.Q..
...Q
Q...
....
```

Now row `3`.

Place queen at `(3,2)`:

```text
.Q..
...Q
Q...
..Q.
```

All `4` rows contain a queen.

Valid solution:

```text
[
    ".Q..",
    "...Q",
    "Q...",
    "..Q."
]
```

Add it to answer.

---

# Recursion Tree

A simplified recursion tree looks like:

```text
                         Row 0
                 /    /    |    \
                C0   C1    C2    C3
                |    |     |     |
              Fail   |    Fail    |
                     |            |
                   Row 1        Row 1
                     |            |
                   ...          ...
                     |
                   Row 2
                     |
                   Row 3
                     |
                  Solution
```

For `n = 4`, the final solutions are:

```text
.Q..      ..Q.
...Q      Q...
Q...      ...Q
..Q.      .Q..
```

---

# Approach 2: Backtracking + HashSets

## Idea

In Approach 1, every time we want to place a queen, we scan:

- The column
- Upper-left diagonal
- Upper-right diagonal

This takes `O(n)` time for every safety check.

We can optimize this using three sets.

We maintain:

```text
columns
```

Stores occupied columns.

```text
positiveDiagonals
```

Stores:

```text
row - col
```

```text
negativeDiagonals
```

Stores:

```text
row + col
```

---

# Why Do Diagonals Use `row - col` and `row + col`?

Consider:

```text
\ diagonal
```

For cells on the same `\` diagonal:

```text
row - col
```

is constant.

Example:

```text
(0,0) → 0 - 0 = 0
(1,1) → 1 - 1 = 0
(2,2) → 2 - 2 = 0
```

So they belong to the same diagonal.

For:

```text
/ diagonal
```

the value:

```text
row + col
```

is constant.

Example:

```text
(0,2) → 0 + 2 = 2
(1,1) → 1 + 1 = 2
(2,0) → 2 + 0 = 2
```

So they belong to the same diagonal.

Therefore:

```text
Column        → col
\ Diagonal    → row - col
/ Diagonal    → row + col
```

---

## Java Code

```java
import java.util.*;

class Solution {

    public List<List<String>> solveNQueens(int n) {

        List<List<String>> answer = new ArrayList<>();

        char[][] board = new char[n][n];

        for (int i = 0; i < n; i++) {
            Arrays.fill(board[i], '.');
        }

        Set<Integer> columns = new HashSet<>();
        Set<Integer> positiveDiagonals = new HashSet<>();
        Set<Integer> negativeDiagonals = new HashSet<>();

        backtrack(
                0,
                board,
                columns,
                positiveDiagonals,
                negativeDiagonals,
                answer
        );

        return answer;
    }

    private void backtrack(
            int row,
            char[][] board,
            Set<Integer> columns,
            Set<Integer> positiveDiagonals,
            Set<Integer> negativeDiagonals,
            List<List<String>> answer) {

        if (row == board.length) {
            answer.add(createBoard(board));
            return;
        }

        for (int col = 0; col < board.length; col++) {

            int diagonal1 = row - col;
            int diagonal2 = row + col;

            // Check whether position is safe
            if (
                columns.contains(col) ||
                positiveDiagonals.contains(diagonal1) ||
                negativeDiagonals.contains(diagonal2)
            ) {
                continue;
            }

            // Choose
            board[row][col] = 'Q';

            columns.add(col);
            positiveDiagonals.add(diagonal1);
            negativeDiagonals.add(diagonal2);

            // Explore
            backtrack(
                    row + 1,
                    board,
                    columns,
                    positiveDiagonals,
                    negativeDiagonals,
                    answer
            );

            // Undo
            board[row][col] = '.';

            columns.remove(col);
            positiveDiagonals.remove(diagonal1);
            negativeDiagonals.remove(diagonal2);
        }
    }

    private List<String> createBoard(char[][] board) {

        List<String> result = new ArrayList<>();

        for (char[] row : board) {
            result.add(new String(row));
        }

        return result;
    }
}
```

---

# Dry Run

Input:

```text
n = 4
```

Start:

```text
row = 0
```

Try:

```text
(0,0)
```

Add:

```text
columns = {0}

positiveDiagonals = {0 - 0}
                   = {0}

negativeDiagonals = {0 + 0}
                   = {0}
```

Board:

```text
Q...
....
....
....
```

---

Try `(1,0)`:

```text
column = 0
```

Already present:

```text
columns.contains(0)
```

So:

```text
Invalid
```

---

Try `(1,1)`:

```text
row - col = 1 - 1 = 0
```

`0` already exists in:

```text
positiveDiagonals
```

So:

```text
Invalid
```

---

Try `(1,2)`:

```text
row - col = -1
row + col = 3
```

But:

```text
row - col = -1
```

does not exist.

And:

```text
row + col = 3
```

does not exist.

So it is safe.

Place:

```text
Q...
..Q.
....
....
```

Continue recursively.

If a branch cannot place all queens, we remove the queen and try another column.

This is the **backtracking** process.

---

# Approach 3: Optimized Boolean Arrays

## Idea

Instead of using `HashSet`, we know the possible ranges.

Columns:

```text
0 to n - 1
```

For:

```text
row + col
```

range is:

```text
0 to 2n - 2
```

For:

```text
row - col
```

range is:

```text
-(n-1) to n-1
```

To avoid negative indexes, we can shift:

```text
row - col + n - 1
```

We can therefore use boolean arrays.

This gives `O(1)` checking without HashSet overhead.

---

## Java Code

```java
import java.util.*;

class Solution {

    public List<List<String>> solveNQueens(int n) {

        List<List<String>> answer = new ArrayList<>();

        char[][] board = new char[n][n];

        for (int i = 0; i < n; i++) {
            Arrays.fill(board[i], '.');
        }

        boolean[] columns = new boolean[n];

        boolean[] positiveDiagonals =
                new boolean[2 * n - 1];

        boolean[] negativeDiagonals =
                new boolean[2 * n - 1];

        backtrack(
                0,
                board,
                columns,
                positiveDiagonals,
                negativeDiagonals,
                answer
        );

        return answer;
    }

    private void backtrack(
            int row,
            char[][] board,
            boolean[] columns,
            boolean[] positiveDiagonals,
            boolean[] negativeDiagonals,
            List<List<String>> answer) {

        if (row == board.length) {
            answer.add(createBoard(board));
            return;
        }

        int n = board.length;

        for (int col = 0; col < n; col++) {

            int diagonal1 = row - col + n - 1;
            int diagonal2 = row + col;

            if (
                columns[col] ||
                positiveDiagonals[diagonal1] ||
                negativeDiagonals[diagonal2]
            ) {
                continue;
            }

            // Choose
            board[row][col] = 'Q';

            columns[col] = true;
            positiveDiagonals[diagonal1] = true;
            negativeDiagonals[diagonal2] = true;

            // Explore
            backtrack(
                    row + 1,
                    board,
                    columns,
                    positiveDiagonals,
                    negativeDiagonals,
                    answer
            );

            // Undo
            board[row][col] = '.';

            columns[col] = false;
            positiveDiagonals[diagonal1] = false;
            negativeDiagonals[diagonal2] = false;
        }
    }

    private List<String> createBoard(char[][] board) {

        List<String> result = new ArrayList<>();

        for (char[] row : board) {
            result.add(new String(row));
        }

        return result;
    }
}
```

---

# Dry Run

For:

```text
n = 4
```

Suppose we try:

```text
row = 1
col = 3
```

Then:

```text
diagonal1 = row - col + n - 1
          = 1 - 3 + 3
          = 1
```

and:

```text
diagonal2 = row + col
          = 1 + 3
          = 4
```

We check:

```java
columns[3]
positiveDiagonals[1]
negativeDiagonals[4]
```

If all are `false`, the position is safe.

We mark them:

```java
columns[3] = true;
positiveDiagonals[1] = true;
negativeDiagonals[4] = true;
```

After exploring:

```java
columns[3] = false;
positiveDiagonals[1] = false;
negativeDiagonals[4] = false;
```

This is the **undo step** of backtracking.

---

# Comparison

| Approach | Safety Check | Extra Space | Recommendation |
|----------|--------------|-------------|----------------|
| Basic Backtracking | Scan board | O(n²) | Best for learning |
| HashSet | O(1) average | O(n) | Easy optimized solution |
| Boolean Arrays | O(1) | O(n) | Best implementation |

---

# Which Approach Is Better?

## Best for Understanding: Basic Backtracking

The first approach directly represents the chessboard.

For every queen:

```text
Check Column
Check Left Diagonal
Check Right Diagonal
```

It is the easiest version to understand.

However, every safety check scans the board.

---

## Best Overall: Boolean Arrays

The third approach is generally the best implementation.

Instead of scanning the board, we directly check:

```java
columns[col]
```

```java
positiveDiagonals[row - col + n - 1]
```

```java
negativeDiagonals[row + col]
```

Each check takes:

```text
O(1)
```

So the solution is faster and avoids `HashSet` overhead.

---

# Why Do We Process One Row at a Time?

We know that a valid solution must have exactly one queen in every row.

Therefore, we don't need to check:

```text
Same row
```

We simply place one queen per row.

Our recursion becomes:

```text
row 0
  ↓
row 1
  ↓
row 2
  ↓
...
row n-1
```

This significantly simplifies the problem.

---

# Why Do We Use Backtracking?

Suppose we place:

```text
Q...
...Q
....
....
```

Now suppose no valid position exists for row `2`.

We need to undo the previous choice:

```text
...Q
```

and try another position.

This is exactly what backtracking does:

```text
Choose
   ↓
Explore
   ↓
Failure?
   ↓
Undo
   ↓
Try Another Choice
```

The core code is:

```java
board[row][col] = 'Q';

backtrack(row + 1, ...);

board[row][col] = '.';
```

The second line is the **backtracking step**.

---

# Complexity

Let:

```text
N = board size
```

The worst-case number of arrangements explored is approximately:

```text
O(N!)
```

because we place one queen per row and cannot reuse a column.

With the optimized boolean-array approach, checking a position takes:

```text
O(1)
```

Creating a completed board takes:

```text
O(N²)
```

Therefore, a commonly stated complexity is:

```text
Time: O(N! × N)
```

or, when accounting for copying each solution:

```text
O(N! + S × N²)
```

where `S` is the number of valid solutions.

Auxiliary space:

```text
O(N²)
```

for the board, plus:

```text
O(N)
```

for column and diagonal tracking.

---

# Important Interview Takeaways

- N-Queens is a classic **Backtracking** problem.
- Place exactly one queen in each row.
- Try every column in the current row.
- A position is invalid if another queen exists in:
  - Same column
  - Same `\` diagonal
  - Same `/` diagonal
- Diagonal identifiers:

```text
\ diagonal → row - col
/ diagonal → row + col
```

- Use:

```java
row - col + n - 1
```

to convert negative diagonal indexes into valid array indexes.
- Use `i + 1` conceptually when moving to the next row because each row is processed once.
- Always undo the placement after recursion.

The core backtracking pattern is:

```text
for every column
       ↓
   Is Safe?
       ↓
    Choose
       ↓
 Explore Next Row
       ↓
     Undo
```

The most important lines are:

```java
board[row][col] = 'Q';

backtrack(row + 1, ...);

board[row][col] = '.';
```

This represents:

```text
Choose → Explore → Undo
```

---

# Final Recommended Code

```java
import java.util.*;

class Solution {

    public List<List<String>> solveNQueens(int n) {

        List<List<String>> answer = new ArrayList<>();

        char[][] board = new char[n][n];

        for (int i = 0; i < n; i++) {
            Arrays.fill(board[i], '.');
        }

        boolean[] columns = new boolean[n];
        boolean[] positiveDiagonals = new boolean[2 * n - 1];
        boolean[] negativeDiagonals = new boolean[2 * n - 1];

        backtrack(
                0,
                board,
                columns,
                positiveDiagonals,
                negativeDiagonals,
                answer
        );

        return answer;
    }

    private void backtrack(
            int row,
            char[][] board,
            boolean[] columns,
            boolean[] positiveDiagonals,
            boolean[] negativeDiagonals,
            List<List<String>> answer) {

        if (row == board.length) {
            answer.add(createBoard(board));
            return;
        }

        int n = board.length;

        for (int col = 0; col < n; col++) {

            int diagonal1 = row - col + n - 1;
            int diagonal2 = row + col;

            if (
                columns[col] ||
                positiveDiagonals[diagonal1] ||
                negativeDiagonals[diagonal2]
            ) {
                continue;
            }

            // Choose
            board[row][col] = 'Q';

            columns[col] = true;
            positiveDiagonals[diagonal1] = true;
            negativeDiagonals[diagonal2] = true;

            // Explore
            backtrack(
                    row + 1,
                    board,
                    columns,
                    positiveDiagonals,
                    negativeDiagonals,
                    answer
            );

            // Undo
            board[row][col] = '.';

            columns[col] = false;
            positiveDiagonals[diagonal1] = false;
            negativeDiagonals[diagonal2] = false;
        }
    }

    private List<String> createBoard(char[][] board) {

        List<String> result = new ArrayList<>();

        for (char[] row : board) {
            result.add(new String(row));
        }

        return result;
    }
}
```
```
