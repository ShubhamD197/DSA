# LeetCode 200 — Number of Islands

**Difficulty:** Medium  
**Topics:** Graph, DFS, BFS, Matrix

---

# Problem Statement

You are given an `m × n` grid where:

```text
'1' → Land
'0' → Water
```

An island is formed by **adjacent land cells** connected horizontally or vertically.

Return the **number of islands** in the grid.

---

## Example 1

```text
Input:

grid =
[
 ["1","1","1","1","0"],
 ["1","1","0","1","0"],
 ["1","1","0","0","0"],
 ["0","0","0","0","0"]
]

Output:
1
```

Graphically:

```text
1 1 1 1 0
1 1 0 1 0
1 1 0 0 0
0 0 0 0 0
```

There is only **one connected component** of land.

---

## Example 2

```text
Input:

grid =
[
 ["1","1","0","0","0"],
 ["1","1","0","0","0"],
 ["0","0","1","0","0"],
 ["0","0","0","1","1"]
]

Output:
3
```

Graphically:

```text
1 1 0 0 0
1 1 0 0 0

0 0 1 0 0

0 0 0 1 1
```

There are:

```text
Island 1
Island 2
Island 3
```

---

# Main Idea

Every island is simply a **connected component** of land.

Whenever we find an unvisited land cell:

```text
Start DFS/BFS
```

Visit every connected land cell.

After finishing,

we have discovered exactly **one island**.

Increase the answer by:

```text
1
```

---

# Approach 1: DFS ⭐ Recommended

### Steps

1. Traverse every cell.
2. If the cell is:
   - Land (`1`)
   - Not visited
3. Start DFS.
4. Mark the whole island as visited.
5. Increase island count.

---

## Java Code

```java
class Solution {

    int[] dr = {-1, 1, 0, 0};
    int[] dc = {0, 0, -1, 1};

    public int numIslands(char[][] grid) {

        int m = grid.length;
        int n = grid[0].length;

        boolean[][] vis = new boolean[m][n];

        int islands = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {

                if (grid[i][j] == '1' && !vis[i][j]) {

                    dfs(i, j, grid, vis);

                    islands++;
                }
            }
        }

        return islands;
    }

    private void dfs(int r, int c, char[][] grid, boolean[][] vis) {

        vis[r][c] = true;

        for (int k = 0; k < 4; k++) {

            int nr = r + dr[k];
            int nc = c + dc[k];

            if (nr >= 0 && nr < grid.length &&
                nc >= 0 && nc < grid[0].length &&
                grid[nr][nc] == '1' &&
                !vis[nr][nc]) {

                dfs(nr, nc, grid, vis);
            }
        }
    }
}
```

---

# Dry Run

Input:

```text
1 1 0
1 0 0
0 1 1
```

Initially:

```text
Visited = Empty
Count = 0
```

---

### Start at (0,0)

```text
1 1 0
1 0 0
0 1 1
```

DFS visits:

```text
✓ ✓ 0
✓ 0 0
0 1 1
```

Island completed.

```text
Count = 1
```

---

Continue scanning.

Next unvisited land:

```text
(2,1)
```

DFS visits:

```text
✓ ✓ 0
✓ 0 0
0 ✓ ✓
```

Island completed.

```text
Count = 2
```

No more land.

Answer:

```text
2
```

---

# Approach 2: BFS

Instead of recursion,

use a queue.

Whenever we find a new land cell:

```text
Push into queue
```

Visit all connected land.

That completes one island.

---

## Java Code

```java
class Solution {

    int[] dr = {-1, 1, 0, 0};
    int[] dc = {0, 0, -1, 1};

    public int numIslands(char[][] grid) {

        int m = grid.length;
        int n = grid[0].length;

        boolean[][] vis = new boolean[m][n];

        Queue<int[]> queue = new LinkedList<>();

        int islands = 0;

        for (int i = 0; i < m; i++) {

            for (int j = 0; j < n; j++) {

                if (grid[i][j] == '1' && !vis[i][j]) {

                    queue.offer(new int[]{i, j});
                    vis[i][j] = true;

                    while (!queue.isEmpty()) {

                        int[] cell = queue.poll();

                        int r = cell[0];
                        int c = cell[1];

                        for (int k = 0; k < 4; k++) {

                            int nr = r + dr[k];
                            int nc = c + dc[k];

                            if (nr >= 0 && nr < m &&
                                nc >= 0 && nc < n &&
                                grid[nr][nc] == '1' &&
                                !vis[nr][nc]) {

                                vis[nr][nc] = true;
                                queue.offer(new int[]{nr, nc});
                            }
                        }
                    }

                    islands++;
                }
            }
        }

        return islands;
    }
}
```

---

# BFS Dry Run

Input:

```text
1 1 0
1 0 0
0 1 1
```

Queue:

```text
[(0,0)]
```

Visit:

```text
(0,0)
```

Queue:

```text
[(0,1),(1,0)]
```

Visit:

```text
(0,1)
```

Nothing new.

Visit:

```text
(1,0)
```

Island completed.

```text
Count = 1
```

Continue scanning.

Queue:

```text
[(2,1)]
```

Visit:

```text
(2,1)
```

↓

```text
(2,2)
```

Island completed.

```text
Count = 2
```

Answer:

```text
2
```

---

# Approach 3: DFS Without Visited Array (In-place)

Instead of using a visited array,

change visited land:

```text
'1' → '0'
```

This saves extra space.

---

## Java Code

```java
class Solution {

    int[] dr = {-1,1,0,0};
    int[] dc = {0,0,-1,1};

    public int numIslands(char[][] grid) {

        int m = grid.length;
        int n = grid[0].length;

        int islands = 0;

        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){

                if(grid[i][j]=='1'){

                    dfs(i,j,grid);

                    islands++;
                }
            }
        }

        return islands;
    }

    private void dfs(int r,int c,char[][] grid){

        if(r<0 || c<0 ||
           r>=grid.length ||
           c>=grid[0].length ||
           grid[r][c]=='0'){

            return;
        }

        grid[r][c]='0';

        for(int k=0;k<4;k++){
            dfs(r+dr[k],c+dc[k],grid);
        }
    }
}
```

---

# Comparison

| Approach | Time | Space | Recommendation |
|----------|------|-------|---------------|
| DFS + Visited | O(m × n) | O(m × n) | Good |
| BFS | O(m × n) | O(m × n) | Good |
| DFS In-place | O(m × n) | O(1)\* | ⭐ Best |

> \*Ignoring the recursion stack (`O(m × n)` in the worst case).

---

# Why is In-place DFS Better?

Every land cell is visited exactly once.

Instead of maintaining:

```text
visited[][]
```

we simply convert:

```text
1 → 0
```

This reduces extra memory usage.

---

# Final Recommended Solution

Use **DFS with in-place marking**.

```text
Find Land
     ↓
Start DFS
     ↓
Convert every connected
land into water
     ↓
Island Finished
     ↓
Count++
```

---

# Complexity

**Time Complexity**

```text
O(m × n)
```

Every cell is visited once.

**Space Complexity**

```text
O(1)
```

Extra space (excluding recursion stack).

Worst-case recursion stack:

```text
O(m × n)
```

---

# Core Pattern to Remember

```text
Need Connected Components?
        ↓
Traverse Grid
        ↓
Found Unvisited Land
        ↓
DFS/BFS
        ↓
Mark Entire Component
        ↓
Count++
```

### Similar Problems

| Problem | Pattern |
|---------|---------|
| 200. Number of Islands | DFS/BFS Connected Components |
| 695. Max Area of Island | DFS/BFS + Area Count |
| 1020. Number of Enclaves | Boundary DFS/BFS |
| 130. Surrounded Regions | Boundary DFS/BFS |
| 994. Rotting Oranges | Multi-source BFS |
| 733. Flood Fill | DFS/BFS |
