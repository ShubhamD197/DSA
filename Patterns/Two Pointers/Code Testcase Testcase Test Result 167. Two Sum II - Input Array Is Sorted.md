For GitHub notes, a clean template like this works well:

---

# LeetCode 167 — Two Sum II (Input Array Is Sorted)

## Problem

Given a **1-indexed sorted array** `numbers[]` and an integer `target`.

Find two numbers such that:

```text
numbers[i] + numbers[j] = target
```

Return their indices as:

```text
[i + 1, j + 1]
```

### Constraints

* Exactly one valid answer exists.
* Cannot use the same element twice.
* Array is sorted in non-decreasing order.

---

## Examples

### Example 1

```text
Input:
numbers = [2,7,11,15]
target = 9

Output:
[1,2]
```

Explanation:

```text
numbers[0] + numbers[1]
= 2 + 7
= 9
```

---

### Example 2

```text
Input:
numbers = [2,3,4]
target = 6

Output:
[1,3]
```

Explanation:

```text
2 + 4 = 6
```

---

### Example 3

```text
Input:
numbers = [-1,0]
target = -1

Output:
[1,2]
```

Explanation:

```text
-1 + 0 = -1
```

---

# Approach 1: HashMap

## Idea

For every number:

1. Calculate complement.
2. Check if complement already exists.
3. If yes, return indices.
4. Otherwise store current number.

### Code

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {

        HashMap<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i < numbers.length; i++) {

            int complement = target - numbers[i];

            if(map.containsKey(complement)) {
                return new int[]{
                    map.get(complement),
                    i + 1
                };
            }

            map.put(numbers[i], i + 1);
        }

        return new int[]{};
    }
}
```

### Complexity

```text
Time  : O(N)
Space : O(N)
```

---

# Approach 2: Two Pointers (Optimal)

## Why?

Since the array is already sorted:

* Sum too small → move left pointer.
* Sum too large → move right pointer.

This helps eliminate unnecessary searches.

---

## Algorithm

```text
left = 0
right = n - 1

while(left < right)

    sum = numbers[left] + numbers[right]

    if(sum == target)
        return answer

    if(sum < target)
        left++

    else
        right--
```

---

## Dry Run

Input:

```text
numbers = [2,7,11,15]
target = 9
```

Initial:

```text
left = 0 (2)
right = 3 (15)
```

### Step 1

```text
2 + 15 = 17
```

Too large.

```text
right--
```

---

### Step 2

```text
2 + 11 = 13
```

Too large.

```text
right--
```

---

### Step 3

```text
2 + 7 = 9
```

Target found.

Return:

```text
[1,2]
```

---

## Optimal Code

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {

        int left = 0;
        int right = numbers.length - 1;

        while(left < right) {

            int sum = numbers[left] + numbers[right];

            if(sum == target) {
                return new int[]{
                    left + 1,
                    right + 1
                };
            }

            if(sum < target) {
                left++;
            } else {
                right--;
            }
        }

        return new int[]{};
    }
}
```

---

## Complexity

```text
Time  : O(N)
Space : O(1)
```

---

## Pattern

```text
Sorted Array + Pair Sum
          ↓
     Two Pointers
```

### Similar Problems

* Two Sum
* 3Sum
* Container With Most Water
* Valid Palindrome
* Remove Duplicates from Sorted Array

---

This format is ideal for GitHub because it follows:

**Problem → Examples → Idea → Dry Run → Code → Complexity → Pattern**

which makes revision before interviews much easier.
