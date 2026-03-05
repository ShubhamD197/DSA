
# ✅ 1️⃣ Fixed Size Sliding Window Template

(When window size = k)

👉 Used in problems like:

* Maximum sum subarray of size k
* First negative number in every window
* Count distinct elements in window of size k

### 🔹 Template (Java)

```java
public void fixedWindow(int[] arr, int k) {
    int n = arr.length;

    int left = 0;
    int sum = 0;

    for (int right = 0; right < n; right++) {
        // 1. Add current element
        sum += arr[right];

        // 2. If window size < k → just expand
        if (right - left + 1 < k) {
            continue;
        }

        // 3. If window size == k → process answer
        if (right - left + 1 == k) {
            System.out.println(sum); // example operation

            // 4. Remove left element before sliding
            sum -= arr[left];
            left++;
        }
    }
}
```

---

# ✅ 2️⃣ Variable Size Sliding Window Template

(When window size depends on condition)

👉 Used in problems like:

* Longest substring without repeating characters
* Subarray sum equals k
* Minimum size subarray sum
* Longest repeating character replacement

---

### 🔹 Template (Java)

```java
public void variableWindow(int[] arr, int k) {
    int n = arr.length;

    int left = 0;
    int sum = 0;

    for (int right = 0; right < n; right++) {

        // 1. Expand window
        sum += arr[right];

        // 2. Shrink window if condition breaks
        while (sum > k) {
            sum -= arr[left];
            left++;
        }

        // 3. Update answer
        // example: maximum length
        int length = right - left + 1;
        System.out.println(length);
    }
}
```

---

# ✅ 3️⃣ Sliding Window with HashMap (Very Common in Interviews)

👉 For substring problems

```java
public int slidingWindow(String s) {
    Map<Character, Integer> map = new HashMap<>();

    int left = 0;
    int maxLen = 0;

    for (int right = 0; right < s.length(); right++) {

        char ch = s.charAt(right);
        map.put(ch, map.getOrDefault(ch, 0) + 1);

        while (map.get(ch) > 1) {
            char leftChar = s.charAt(left);
            map.put(leftChar, map.get(leftChar) - 1);
            left++;
        }

        maxLen = Math.max(maxLen, right - left + 1);
    }

    return maxLen;
}
```

---

# 🎯 How To Identify Sliding Window in Interview


* Is it about **subarray / substring**?
* Is it **contiguous**?
* Are they asking for **longest / smallest / count**?
* Can I maintain answer while moving left & right pointers?


