# Kadane’s Algorithm Patterns

Kadane’s Algorithm is used to solve **maximum/minimum subarray problems** in **O(n)** time.

Core idea:

```
At each index:
extend previous subarray
OR
start a new subarray
```

Most Kadane problems follow this formula:

```
curr = max(num , curr + num)
ans = max(ans , curr)
```

---

# 1. Basic Kadane (Maximum Subarray)

Problem reference: LeetCode 53 – Maximum Subarray

### Use When

* Find **maximum sum of contiguous subarray**

### Template

```java
public int kadane(int[] nums){

    int curr = 0;
    int max = Integer.MIN_VALUE;

    for(int n : nums){

        curr += n;

        max = Math.max(max, curr);

        if(curr < 0)
            curr = 0;
    }

    return max;
}
```

### Pattern

```
curr += num
update answer
reset if negative
```

---

# 2. DP Style Kadane

Another common form used in interviews.

### Template

```java
public int kadaneDP(int[] nums){

    int curr = nums[0];
    int max = nums[0];

    for(int i = 1; i < nums.length; i++){

        curr = Math.max(nums[i], curr + nums[i]);

        max = Math.max(max, curr);
    }

    return max;
}
```

### Pattern

```
curr = max(start new, extend previous)
```

---

# 3. Minimum Subarray (Inverse Kadane)

Used in **circular array problems**.

### Template

```java
public int minKadane(int[] nums){

    int curr = 0;
    int min = Integer.MAX_VALUE;

    for(int n : nums){

        curr += n;

        min = Math.min(min, curr);

        if(curr > 0)
            curr = 0;
    }

    return min;
}
```

---

# 4. Maximum Circular Subarray

Problem reference: LeetCode 918

Idea:

```
maxCircular = max(normalKadane , totalSum - minSubarray)
```

### Template

```java
public int maxCircularSubarray(int[] nums){

    int total = 0;

    int currMax = 0, max = Integer.MIN_VALUE;
    int currMin = 0, min = Integer.MAX_VALUE;

    for(int n : nums){

        total += n;

        currMax = Math.max(n, currMax + n);
        max = Math.max(max, currMax);

        currMin = Math.min(n, currMin + n);
        min = Math.min(min, currMin);
    }

    if(max < 0)
        return max;

    return Math.max(max, total - min);
}
```

---

# 5. Maximum Product Subarray

Problem reference: LeetCode 152

Key idea:

```
negative × negative = positive
```

So track **max product and min product**.

### Template

```java
public int maxProduct(int[] nums){

    int max = nums[0];
    int min = nums[0];
    int ans = nums[0];

    for(int i = 1; i < nums.length; i++){

        if(nums[i] < 0){
            int temp = max;
            max = min;
            min = temp;
        }

        max = Math.max(nums[i], max * nums[i]);
        min = Math.min(nums[i], min * nums[i]);

        ans = Math.max(ans, max);
    }

    return ans;
}
```

---

# 6. Maximum Absolute Subarray Sum

Problem reference: LeetCode 1749

Idea:

```
max absolute = max(maxSubarray , |minSubarray|)
```

### Template

```java
public int maxAbsoluteSum(int[] nums){

    int currMax = 0, max = 0;
    int currMin = 0, min = 0;

    for(int n : nums){

        currMax = Math.max(n, currMax + n);
        max = Math.max(max, currMax);

        currMin = Math.min(n, currMin + n);
        min = Math.min(min, currMin);
    }

    return Math.max(max, Math.abs(min));
}
```

---

# 7. Maximum Subarray With One Deletion

Problem reference: LeetCode 1186

Idea:

```
Forward Kadane
Backward Kadane
Combine both
```

### Template

```java
public int maxSubarraySumWithOneDeletion(int[] arr){

    int n = arr.length;

    int[] left = new int[n];
    int[] right = new int[n];

    left[0] = arr[0];
    int curr = arr[0];

    for(int i = 1; i < n; i++){
        curr = Math.max(arr[i], curr + arr[i]);
        left[i] = curr;
    }

    right[n-1] = arr[n-1];
    curr = arr[n-1];

    for(int i = n-2; i >= 0; i--){
        curr = Math.max(arr[i], curr + arr[i]);
        right[i] = curr;
    }

    int ans = Integer.MIN_VALUE;

    for(int i = 1; i < n-1; i++){
        ans = Math.max(ans, left[i-1] + right[i+1]);
    }

    return ans;
}
```

---

# Kadane Pattern Recognition

You should think of Kadane when the problem says:

```
maximum sum subarray
maximum product subarray
maximum score contiguous
maximum profit continuous segment
```

Key signals:

```
contiguous
maximum / minimum
subarray
```

---

# Complexity

```
Time Complexity: O(n)
Space Complexity: O(1)
```

Except variants that use prefix arrays.

---

# Interview Tip

Most interview questions reduce to **3 Kadane ideas**:

```
1. Maximum Sum
2. Minimum Sum
3. Maximum Product
```

Other problems are **variations of these three**.

---

# Related Problems to Practice

* LeetCode 53 – Maximum Subarray
* LeetCode 918 – Maximum Sum Circular Subarray
* LeetCode 152 – Maximum Product Subarray
* LeetCode 1749 – Maximum Absolute Sum
* LeetCode 1186 – Maximum Subarray With One Deletion

