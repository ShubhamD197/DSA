
# Largest Rectangular Area in a Histogram — GFG

**Difficulty:** Medium  
**Topic:** Stack / Monotonic Stack

---

# Problem Statement

Given an array `arr[]` representing the heights of bars in a histogram, where each bar has a width of `1`, find the **largest rectangular area** that can be formed using one or more consecutive bars.

Return the maximum possible area.

---

# Example 1

Input:
arr = [2, 1, 5, 6, 2, 3]

Output:
10


The largest rectangle is formed using:

```text
5 and 6
```

The minimum height is `5` and width is `2`.

```text
Area = 5 × 2 = 10
```

Visualization:

```text
        █
    █   █
    █   █
█   █   █
█   █   █   █
----------------
2   1   5   6   2   3
        <--->
          2
```

---

# Example 2

```text
Input:
arr = [2, 4]

Output:
4
```

Possible rectangles:

```text
Bar 1:
2 × 1 = 2

Bar 2:
4 × 1 = 4

Both bars:
min(2,4) × 2
= 2 × 2
= 4
```

Answer:

```text
4
```

---

# Example 3

```text
Input:
arr = [1, 1, 1, 1]

Output:
4
```

All four bars can be used:

```text
Height = 1
Width = 4

Area = 1 × 4 = 4
```

---

# Approach 1: Brute Force

## Idea

For every bar, consider it as the minimum height of the rectangle.

Expand:

```text
Left ← current bar → Right
```

while the bars are at least as tall as the current bar.

For each bar:

```text
height = arr[i]
```

Find:

```text
left boundary
right boundary
```

Then:

```text
width = right - left + 1
```

And:

```text
area = height × width
```

---

## Java Code

```java
class Solution {

    public static int getMaxArea(int[] arr) {

        int n = arr.length;
        int maxArea = 0;

        for (int i = 0; i < n; i++) {

            int left = i;
            int right = i;

            // Expand left
            while (left >= 0 && arr[left] >= arr[i]) {
                left--;
            }

            // Expand right
            while (right < n && arr[right] >= arr[i]) {
                right++;
            }

            int width = right - left - 1;

            int area = arr[i] * width;

            maxArea = Math.max(maxArea, area);
        }

        return maxArea;
    }
}
```

---

# Dry Run

Input:

```text
arr = [2, 1, 5, 6, 2, 3]
```

Consider bar `5` at index `2`.

```text
[2, 1, 5, 6, 2, 3]
       ↑
```

Expand left:

```text
1 < 5
```

Stop.

Expand right:

```text
6 >= 5
```

Continue.

Next:

```text
2 < 5
```

Stop.

So:

```text
left = 1
right = 4
```

Width:

```text
right - left - 1
= 4 - 1 - 1
= 2
```

Area:

```text
5 × 2 = 10
```

Maximum:

```text
10
```

---

# Complexity

```text
Time: O(n²)
Space: O(1)
```

This is simple but inefficient for large arrays.

We can optimize it using a **Monotonic Stack**.

---

# Approach 2: Monotonic Stack

## Main Idea

For every bar, we want to know:

```text
Nearest Smaller Element on Left
```

and:

```text
Nearest Smaller Element on Right
```

Suppose:

```text
arr[i] = 5
```

and the nearest smaller bars are:

```text
Left  → index 1
Right → index 4
```

Then the rectangle can cover:

```text
index 2 and index 3
```

So:

```text
width = right - left - 1
```

and:

```text
area = arr[i] × width
```

genui{"shapes_measurement_learning_block":{"type_id":"RECTANGLE_AREA"}}

The stack helps us find these boundaries efficiently.

---

# Why Monotonic Stack?

We maintain a stack of indices where the heights are in **increasing order**.

For example:

```text
[2, 5, 6]
```

Stack contains:

```text
2 → 5 → 6
```

When we see:

```text
2
```

the height `2` is smaller than `6`.

Therefore, `6` cannot extend further to the right.

So we pop `6` and calculate its area.

Then:

```text
2 < 5
```

So we pop `5` and calculate its area.

This is where we find the maximum rectangle.

---

# Java Code — One Pass Stack

```java
import java.util.*;

class Solution {

    public static int getMaxArea(int[] arr) {

        int n = arr.length;

        Stack<Integer> stack = new Stack<>();

        int maxArea = 0;

        for (int i = 0; i <= n; i++) {

            // Use 0 as a sentinel after the last bar
            int currentHeight = (i == n) ? 0 : arr[i];

            while (!stack.isEmpty()
                    && arr[stack.peek()] >= currentHeight) {

                int height = arr[stack.pop()];

                int width;

                if (stack.isEmpty()) {
                    width = i;
                } else {
                    width = i - stack.peek() - 1;
                }

                int area = height * width;

                maxArea = Math.max(maxArea, area);
            }

            stack.push(i);
        }

        return maxArea;
    }
}
```

---

# Dry Run

Let's use:

```text
arr = [2, 1, 5, 6, 2, 3]
```

We process each index.

---

## Index 0

```text
height = 2
```

Stack:

```text
[0]
```

Heights:

```text
[2]
```

---

## Index 1

```text
height = 1
```

`1 < 2`, so pop index `0`.

```text
height = 2
```

Stack becomes empty.

Width:

```text
width = i
      = 1
```

Area:

```text
2 × 1 = 2
```

Maximum:

```text
2
```

Push index `1`.

Stack:

```text
[1]
```

---

## Index 2

```text
height = 5
```

`5 > 1`.

Push:

```text
[1, 2]
```

---

## Index 3

```text
height = 6
```

`6 > 5`.

Push:

```text
[1, 2, 3]
```

---

## Index 4

```text
height = 2
```

Now:

```text
2 < 6
```

Pop index `3`.

```text
height = 6
```

Stack:

```text
[1, 2]
```

Width:

```text
4 - 2 - 1
= 1
```

Area:

```text
6 × 1 = 6
```

Maximum:

```text
6
```

---

Now:

```text
2 < 5
```

Pop index `2`.

```text
height = 5
```

Stack:

```text
[1]
```

Width:

```text
4 - 1 - 1
= 2
```

Area:

```text
5 × 2 = 10
```

Maximum:

```text
10
```

Push index `4`.

Stack:

```text
[1,4]
```

---

## Index 5

```text
height = 3
```

`3 > 2`.

Push:

```text
[1,4,5]
```

---

# End of Array

We use:

```text
currentHeight = 0
```

This forces all remaining bars to be popped.

---

Pop index `5`:

```text
height = 3
width = 1
area = 3
```

Pop index `4`:

```text
height = 2
width = 4
area = 8
```

Maximum remains:

```text
10
```

Final answer:

```text
10
```

---

# Important Width Formula

When we pop a bar:

```java
int height = arr[stack.pop()];
```

Suppose:

```text
current index = i
```

After popping, the stack top gives the nearest smaller element on the left.

Therefore:

```text
Left Smaller = stack.peek()
Right Smaller = i
```

So:

```text
width = right - left - 1
```

Which becomes:

```java
width = i - stack.peek() - 1;
```

If the stack becomes empty:

```text
There is no smaller element on the left.
```

So:

```text
width = i;
```

---

# Approach 3: Previous Smaller + Next Smaller

Another way is to calculate two arrays.

```text
PSE = Previous Smaller Element
NSE = Next Smaller Element
```

For each bar:

```text
width = NSE[i] - PSE[i] - 1
```

Then:

```text
area = arr[i] × width
```

---

## Java Code

```java
import java.util.*;

class Solution {

    public static int getMaxArea(int[] arr) {

        int n = arr.length;

        int[] left = new int[n];
        int[] right = new int[n];

        Stack<Integer> stack = new Stack<>();

        // Previous Smaller Element
        for (int i = 0; i < n; i++) {

            while (!stack.isEmpty()
                    && arr[stack.peek()] >= arr[i]) {
                stack.pop();
            }

            if (stack.isEmpty()) {
                left[i] = -1;
            } else {
                left[i] = stack.peek();
            }

            stack.push(i);
        }

        stack.clear();

        // Next Smaller Element
        for (int i = n - 1; i >= 0; i--) {

            while (!stack.isEmpty()
                    && arr[stack.peek()] >= arr[i]) {
                stack.pop();
            }

            if (stack.isEmpty()) {
                right[i] = n;
            } else {
                right[i] = stack.peek();
            }

            stack.push(i);
        }

        int maxArea = 0;

        for (int i = 0; i < n; i++) {

            int width = right[i] - left[i] - 1;

            int area = arr[i] * width;

            maxArea = Math.max(maxArea, area);
        }

        return maxArea;
    }
}
```

---

# Dry Run of PSE + NSE

Input:

```text
arr = [2, 1, 5, 6, 2, 3]
```

For each element:

| Index | Height | PSE | NSE | Width | Area |
| ----: | -----: | --: | --: | ----: | ---: |
|     0 |      2 |  -1 |   1 |     1 |    2 |
|     1 |      1 |  -1 |   6 |     6 |    6 |
|     2 |      5 |   1 |   4 |     2 |   10 |
|     3 |      6 |   2 |   4 |     1 |    6 |
|     4 |      2 |   1 |   6 |     4 |    8 |
|     5 |      3 |   4 |   6 |     1 |    3 |

Maximum:

```text
10
```

---

# Comparison

| Approach       | Technique         | Time  | Space | Best For            |
| -------------- | ----------------- | ----- | ----- | ------------------- |
| Brute Force    | Expand Left/Right | O(n²) | O(1)  | Basic understanding |
| PSE + NSE      | Two Stack Passes  | O(n)  | O(n)  | Easy to understand  |
| One-Pass Stack | Monotonic Stack   | O(n)  | O(n)  | Best overall        |

---

# Which Approach Is Better?

## Best for Beginners

**PSE + NSE**

It breaks the problem into two simple questions:

```text
Where is the smaller bar on the left?
Where is the smaller bar on the right?
```

Then:

```text
width = right - left - 1
```

It is easier to visualize.

---

## Best Overall

**One-Pass Monotonic Stack**

It calculates the answer while processing the array.

The key idea is:

```text
Increasing heights → Push
Smaller height     → Pop and calculate
```

It has:

```text
Time: O(n)
Space: O(n)
```

Even though there is a `while` loop inside the `for` loop, the total complexity is still `O(n)`.

Why?

Every index is:

```text
Pushed once
Popped once
```

Therefore:

```text
O(n) + O(n)
= O(n)
```

---

# The Most Important Concept

When we encounter a smaller bar:

```text
currentHeight < stack.top()
```

we know that the taller bar on the stack **cannot continue beyond the current index**.

So we pop it.

Example:

```text
[2, 1, 5, 6, 2]
          ↑
```

When we reach `2`:

```text
6 > 2
```

So `6` stops here.

Then:

```text
5 > 2
```

So `5` also stops here.

This allows us to calculate:

```text
6 × 1 = 6
5 × 2 = 10
```

The maximum is:

```text
10
```

---

# Core Pattern to Remember

```text
for every bar:

    while current bar is smaller:

        pop taller bar

        calculate height

        calculate width

        update maximum

    push current index
```

The most important formula is:

```text
width = rightSmaller - leftSmaller - 1
```

And:

```text
area = height × width
```

---

# Final Recommended GFG Code

```java
import java.util.*;

class Solution {

    public static int getMaxArea(int[] arr) {

        int n = arr.length;

        Stack<Integer> stack = new Stack<>();

        int maxArea = 0;

        for (int i = 0; i <= n; i++) {

            int currentHeight =
                    (i == n) ? 0 : arr[i];

            while (!stack.isEmpty()
                    && arr[stack.peek()] >= currentHeight) {

                int height = arr[stack.pop()];

                int width;

                if (stack.isEmpty()) {
                    width = i;
                } else {
                    width = i - stack.peek() - 1;
                }

                int area = height * width;

                maxArea = Math.max(
                        maxArea,
                        area
                );
            }

            stack.push(i);
        }

        return maxArea;
    }
}
```

### Final Complexity

```text
Time: O(n)
Space: O(n)
```

### Remember This

```text
Monotonic Increasing Stack
        ↓
Find Smaller Element
        ↓
Pop Taller Bars
        ↓
Calculate Width
        ↓
Calculate Area
        ↓
Update Maximum
```

**One small implementation note:** the code above uses a sentinel iteration (`i == n`) and then pushes `n` onto the stack after processing. Since the loop ends immediately, that unused push is harmless, but a cleaner implementation can avoid pushing when `i == n`.

```
```
