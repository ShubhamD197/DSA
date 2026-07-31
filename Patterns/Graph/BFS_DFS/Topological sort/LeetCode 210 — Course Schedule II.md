# LeetCode 210 — Course Schedule II

**Difficulty:** Medium  
**Topics:** Graph, Topological Sort, BFS, DFS

---

## Problem Statement

There are `numCourses` courses labeled:

```text
0 to numCourses - 1
```

You are given:

```text
prerequisites[i] = [a, b]
```

which means:

```text
To take course a,
you must first complete course b.
```

Return **any valid order** in which you can finish all courses.

If it is impossible (a cycle exists), return:

```text
[]
```

---

## Example 1

```text
Input:
numCourses = 2
prerequisites = [[1,0]]

Output:
[0,1]
```

Graph:

```text
0 → 1
```

Take:

```text
0
```

then:

```text
1
```

---

## Example 2

```text
Input:
numCourses = 4
prerequisites = [[1,0],[2,0],[3,1],[3,2]]

Output:
[0,1,2,3]
```

Another valid answer:

```text
[0,2,1,3]
```

Graph:

```text
      0
     / \
    1   2
     \ /
      3
```

---

## Example 3

```text
Input:
numCourses = 2
prerequisites = [[0,1],[1,0]]

Output:
[]
```

Graph:

```text
0 → 1
↑   |
|   ↓
└───┘
```

Cycle exists.

---

# Main Idea

Unlike **LeetCode 207**, we now need to return the **actual order** of courses.

This is exactly a **Topological Sorting** problem.

A topological order exists **only if the graph has no cycle**.

---

# Approach 1: BFS (Kahn's Algorithm) ✅ Recommended

### Steps

1. Build the graph.
2. Calculate indegree of every node.
3. Push all nodes with indegree `0` into a queue.
4. Remove one node at a time.
5. Add it to the answer.
6. Reduce indegree of its neighbors.
7. If neighbor becomes `0`, push it into the queue.

If all nodes are processed:

```text
Return the order
```

Otherwise:

```text
Return []
```

---

## Java Code

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> graph = new ArrayList<>();

        for (int i = 0; i < numCourses; i++) {
            graph.add(new ArrayList<>());
        }

        int[] indegree = new int[numCourses];

        for (int[] edge : prerequisites) {
            graph.get(edge[1]).add(edge[0]);
            indegree[edge[0]]++;
        }

        Queue<Integer> queue = new LinkedList<>();

        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }

        int[] order = new int[numCourses];
        int index = 0;

        while (!queue.isEmpty()) {
            int course = queue.poll();

            order[index++] = course;

            for (int next : graph.get(course)) {
                indegree[next]--;

                if (indegree[next] == 0) {
                    queue.offer(next);
                }
            }
        }

        if (index != numCourses) {
            return new int[]{};
        }

        return order;
    }
}
```

---

# Dry Run

Input:

```text
numCourses = 4

prerequisites =
[
 [1,0],
 [2,0],
 [3,1],
 [3,2]
]
```

Graph:

```text
      0
     / \
    1   2
     \ /
      3
```

---

### Step 1

Indegree:

```text
0 = 0
1 = 1
2 = 1
3 = 2
```

Queue:

```text
[0]
```

Answer:

```text
[]
```

---

### Step 2

Remove:

```text
0
```

Answer:

```text
[0]
```

Decrease indegree:

```text
1 → 0
2 → 0
```

Queue:

```text
[1,2]
```

---

### Step 3

Remove:

```text
1
```

Answer:

```text
[0,1]
```

Decrease:

```text
3 → 1
```

Queue:

```text
[2]
```

---

### Step 4

Remove:

```text
2
```

Answer:

```text
[0,1,2]
```

Decrease:

```text
3 → 0
```

Queue:

```text
[3]
```

---

### Step 5

Remove:

```text
3
```

Answer:

```text
[0,1,2,3]
```

Processed:

```text
4 courses
```

Answer:

```text
[0,1,2,3]
```

---

# Dry Run (Cycle)

Graph:

```text
0 → 1
↑   |
|   ↓
└───┘
```

Indegree:

```text
0 = 1
1 = 1
```

Queue:

```text
[]
```

Nothing can be processed.

Processed:

```text
0
```

Since:

```text
processed != numCourses
```

Return:

```text
[]
```

---

# Approach 2: DFS Topological Sort

Instead of BFS, we can use DFS.

Idea:

- Visit all neighbors first.
- Add the node after exploring all its neighbors.
- Reverse the result.

If a cycle is found, return:

```text
[]
```

---

## Java Code

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> graph = new ArrayList<>();

        for (int i = 0; i < numCourses; i++) {
            graph.add(new ArrayList<>());
        }

        for (int[] edge : prerequisites) {
            graph.get(edge[1]).add(edge[0]);
        }

        int[] state = new int[numCourses];
        List<Integer> order = new ArrayList<>();

        for (int i = 0; i < numCourses; i++) {
            if (dfs(i, graph, state, order)) {
                return new int[]{};
            }
        }

        Collections.reverse(order);

        int[] ans = new int[numCourses];

        for (int i = 0; i < numCourses; i++) {
            ans[i] = order.get(i);
        }

        return ans;
    }

    private boolean dfs(int node, List<List<Integer>> graph,
                        int[] state, List<Integer> order) {

        if (state[node] == 1) return true;
        if (state[node] == 2) return false;

        state[node] = 1;

        for (int next : graph.get(node)) {
            if (dfs(next, graph, state, order)) {
                return true;
            }
        }

        state[node] = 2;
        order.add(node);

        return false;
    }
}
```

---

# DFS Dry Run

Graph:

```text
0 → 1 → 3
 \
  → 2 → 3
```

DFS:

```text
0

↓

1

↓

3
```

Finish:

```text
3
```

Answer:

```text
[3]
```

Return:

```text
1
```

Answer:

```text
[3,1]
```

Visit:

```text
2
```

Finish:

```text
2
```

Answer:

```text
[3,1,2]
```

Finish:

```text
0
```

Answer:

```text
[3,1,2,0]
```

Reverse:

```text
[0,2,1,3]
```

Valid topological order.

---

# Comparison

| Approach | Time | Space | Recommendation |
|---|---:|---:|---|
| BFS (Kahn's Algorithm) | O(V + E) | O(V + E) | ⭐ Best |
| DFS Topological Sort | O(V + E) | O(V + E) | Also good |

---

# Why is BFS Recommended?

For Course Schedule II, BFS naturally builds the answer in the correct order.

The process is simple:

```text
Node with indegree 0
        ↓
Take course
        ↓
Reduce indegree of neighbors
        ↓
New course becomes available
```

No reversing of the answer is required.

---

# Final Recommended Solution

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> graph = new ArrayList<>();

        for (int i = 0; i < numCourses; i++) {
            graph.add(new ArrayList<>());
        }

        int[] indegree = new int[numCourses];

        for (int[] edge : prerequisites) {
            graph.get(edge[1]).add(edge[0]);
            indegree[edge[0]]++;
        }

        Queue<Integer> queue = new LinkedList<>();

        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }

        int[] order = new int[numCourses];
        int index = 0;

        while (!queue.isEmpty()) {
            int course = queue.poll();
            order[index++] = course;

            for (int next : graph.get(course)) {
                indegree[next]--;

                if (indegree[next] == 0) {
                    queue.offer(next);
                }
            }
        }

        return index == numCourses ? order : new int[]{};
    }
}
```

**Time Complexity:** `O(V + E)`  
**Space Complexity:** `O(V + E)`

---

# Core Pattern to Remember

This problem is:

```text
Course Dependencies
        ↓
Directed Graph
        ↓
Need a Valid Order
        ↓
Topological Sort
```

### BFS (Kahn's Algorithm)

```text
Build Graph
      ↓
Calculate Indegree
      ↓
Push indegree 0 nodes
      ↓
Process Queue
      ↓
Decrease neighbors' indegree
      ↓
Repeat
```

### DFS

```text
Visit node
      ↓
Visit all neighbors
      ↓
Add node to answer
      ↓
Reverse answer
```

### Difference Between 207 and 210

| Problem | Output |
|---------|--------|
| **207 - Course Schedule** | `true / false` (Can finish?) |
| **210 - Course Schedule II** | One valid course order (Topological Sort) |
