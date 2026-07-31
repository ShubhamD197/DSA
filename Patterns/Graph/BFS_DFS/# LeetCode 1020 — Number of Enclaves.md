# LeetCode 1020 — Number of Enclaves

**Difficulty:** Medium  
**Topics:** Graph, DFS, BFS, Matrix

---

# Problem Statement

You are given an `m x n` binary matrix `grid`.

```text
0 → Sea
1 → Land
```

A move can be made in four directions:

```text
Up
Down
Left
Right
```

A land cell is called an **enclave** if it **cannot reach the boundary** by moving through adjacent land cells.

Return the **number of land cells** that are enclaves.

---

## Example 1

```text
Input:

grid =
[
 [0,0,0,0],
 [1,0,1,0],
 [0,1,1,0],
 [0,0,0,0]
]

Output:
3
```

Grid:

```text
0 0 0 0
1 0 1 0
0 1 1 0
0 0 0 0
```

Boundary land:

```text
(1,0)
```

can escape.

Remaining land:

```text
(1,2)
(2,1)
(2,2)
```

cannot reach boundary.

Answer:

```text
3
```

---

## Example 2

```text
Input:

grid =
[
 [0,1,1,0],
 [0,0,1,0],
 [0,0,1,0],
 [0,0,0,0]
]

Output:
0
```

All land is connected to boundary.

---

# Main Idea

Instead of finding enclosed land,

remove every land cell that **can reach the boundary**.

Whatever land remains is an enclave.

---

# Approach 1: DFS from Boundary

### Steps

1. Traverse all boundary cells.
2. Whenever boundary cell is land (`1`), perform DFS.
3. Mark every reachable land as visited.
4. Count remaining unvisited land cells.

---

## Java Code

```java
class Solution {

    int[] dr = {-1,1,0,0};
    int[] dc = {0,0,-1,1};

    public int numEnclaves(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;

        boolean[][] vis = new boolean[m][n];

        for (int i = 0; i < m; i++) {

            if (grid[i][0] == 1 && !vis[i][0])
                dfs(i,0,grid,vis);

            if (grid[i][n-1] == 1 && !vis[i][n-1])
                dfs(i,n-1,grid,vis);
        }

        for (int j = 0; j < n; j++) {

            if (grid[0][j] == 1 && !vis[0][j])
                dfs(0,j,grid,vis);

            if (grid[m-1][j] == 1 && !vis[m-1][j])
                dfs(m-1,j,grid,vis);
        }

        int count = 0;

        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(grid[i][j]==1 && !vis[i][j]){
                    count++;
                }
            }
        }

        return count;
    }

    private void dfs(int r,int c,int[][] grid,boolean[][] vis){

        vis[r][c]=true;

        for(int k=0;k<4;k++){

            int nr=r+dr[k];
            int nc=c+dc[k];

            if(nr>=0 && nr<grid.length &&
               nc>=0 && nc<grid[0].length &&
               grid[nr][nc]==1 &&
               !vis[nr][nc]){

                dfs(nr,nc,grid,vis);
            }
        }
    }
}
```

---

# Dry Run

Input:

```text
0 0 0 0
1 0 1 0
0 1 1 0
0 0 0 0
```

Boundary land:

```text
(1,0)
```

DFS marks:

```text
✓ 0 0 0
✓ 0 1 0
0 1 1 0
0 0 0 0
```

Visited:

```text
(1,0)
```

Remaining land:

```text
(1,2)
(2,1)
(2,2)
```

Count:

```text
3
```

Answer:

```text
3
```

---

# Approach 2: BFS from Boundary

Instead of DFS,

use a queue.

---

## Java Code

```java
class Solution {

    int[] dr={-1,1,0,0};
    int[] dc={0,0,-1,1};

    public int numEnclaves(int[][] grid) {

        int m=grid.length;
        int n=grid[0].length;

        boolean[][] vis=new boolean[m][n];

        Queue<int[]> q=new LinkedList<>();

        for(int i=0;i<m;i++){

            if(grid[i][0]==1){
                q.offer(new int[]{i,0});
                vis[i][0]=true;
            }

            if(grid[i][n-1]==1){
                q.offer(new int[]{i,n-1});
                vis[i][n-1]=true;
            }
        }

        for(int j=0;j<n;j++){

            if(grid[0][j]==1 && !vis[0][j]){
                q.offer(new int[]{0,j});
                vis[0][j]=true;
            }

            if(grid[m-1][j]==1 && !vis[m-1][j]){
                q.offer(new int[]{m-1,j});
                vis[m-1][j]=true;
            }
        }

        while(!q.isEmpty()){

            int[] cell=q.poll();

            int r=cell[0];
            int c=cell[1];

            for(int k=0;k<4;k++){

                int nr=r+dr[k];
                int nc=c+dc[k];

                if(nr>=0 && nr<m &&
                   nc>=0 && nc<n &&
                   grid[nr][nc]==1 &&
                   !vis[nr][nc]){

                    vis[nr][nc]=true;
                    q.offer(new int[]{nr,nc});
                }
            }
        }

        int count=0;

        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(grid[i][j]==1 && !vis[i][j]){
                    count++;
                }
            }
        }

        return count;
    }
}
```

---

# BFS Dry Run

Queue:

```text
[(1,0)]
```

Visit:

```text
(1,0)
```

No connected land.

Queue becomes:

```text
[]
```

Now count all unvisited land:

```text
(1,2)
(2,1)
(2,2)
```

Answer:

```text
3
```

---

# Comparison

| Approach | Time | Space | Recommendation |
|----------|------|-------|---------------|
| DFS | O(m × n) | O(m × n) | ⭐ Best |
| BFS | O(m × n) | O(m × n) | Also Good |

---

# Why Start from Boundary?

Instead of asking:

```text
Which land is enclosed?
```

Ask:

```text
Which land can escape?
```

All boundary land can escape.

Any land connected to boundary land can also escape.

Only the remaining land is enclosed.

---

# Final Recommended Solution

Use **DFS from all boundary land cells**.

```text
Boundary Land
      ↓
Mark all reachable land
      ↓
Ignore visited cells
      ↓
Count remaining land
```

---

# Complexity

**Time Complexity**

```text
O(m × n)
```

Each cell is visited at most once.

**Space Complexity**

```text
O(m × n)
```

Visited array + recursion stack.

---

# Core Pattern to Remember

```text
Need enclosed land?
        ↓
Start from boundary
        ↓
Mark everything that can escape
        ↓
Remaining land = Enclaves
```

### Similar Problems

| Problem | Pattern |
|---------|---------|
| 1020. Number of Enclaves | Boundary DFS/BFS |
| 130. Surrounded Regions | Boundary DFS/BFS |
| 200. Number of Islands | DFS/BFS Components |
| 994. Rotting Oranges | Multi-source BFS |
| 695. Max Area of Island | DFS/BFS |
