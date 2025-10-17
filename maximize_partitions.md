# 3003. Maximize the Number of Partitions After Operations

**Hard**

---

## 🧩 Problem Description

You are given a string `s` and an integer `k`.

First, you are allowed to **change at most one index** in `s` to another lowercase English letter.

After that, perform the following partitioning operation **until `s` becomes empty**:

- Choose the **longest prefix** of `s` that contains **at most `k` distinct characters**.
- Delete this prefix from `s` and increase the number of partitions by one.

Return the **maximum number of partitions** after optimally choosing at most one index to change.

---

## 🧠 Example 1

**Input:**  
`s = "accca"`, `k = 2`

**Output:**  
`3`

**Explanation:**  
Optimal way is to change `s[2]` (the middle `c`) to another letter, say `b`. Then `s = "acbca"`.

Now perform the operation:
1. Longest prefix with at most 2 distinct characters = `"ac"` → remove it → `s = "bca"`
2. Longest prefix = `"bc"` → remove it → `s = "a"`
3. Remove `"a"` → `s` becomes empty.

Total partitions = **3**.

---

## 🧠 Example 2

**Input:**  
`s = "aabaab"`, `k = 3`

**Output:**  
`1`

**Explanation:**  
The string initially has only 2 distinct characters. Even after one change, it still contains ≤ 3 distinct characters, so the whole string will always be taken as one partition.

---

## 🧠 Example 3

**Input:**  
`s = "xxyz"`, `k = 1`

**Output:**  
`4`

**Explanation:**  
Change `s[0]` to another character (say `w`), so `s = "wxyz"`.

Now each character forms its own partition because `k = 1` allows only one distinct character per prefix.

Hence, total partitions = **4**.

---

## 🔍 Constraints

- `1 <= s.length <= 10^4`
- `s` consists only of lowercase English letters.
- `1 <= k <= 26`

---

## 💡 Intuition

The brute-force approach of trying every character change is too slow (`O(26 * n^2)`). To optimize, we need a way to understand how partitions form from both left and right sides efficiently.

We can precompute the partitions, masks (bit representations of distinct letters), and counts of distinct characters from both directions.

---

## 🧮 Approach

1. Traverse from **left to right**, maintaining:
   - `num`: number of partitions formed so far.
   - `mask`: bitmask of current distinct characters.
   - `count`: number of distinct characters in the current segment.

   When `count > k`, start a new partition.

   Store results in `left[i] = {num, mask, count}`.

2. Similarly, traverse from **right to left**, storing `right[i]` values.

3. For each possible change position `i`, combine `left[i]` and `right[i]` to compute possible partition counts after an optimal change.

4. Track the **maximum partition count** over all indices.

This method works in **O(n * 26)** at most and avoids recomputation from scratch.

---

## 🚀 Optimized C++ Solution

```cpp
class Solution {
public:
    int maxPartitionsAfterOperations(string s, int k) {
        int n = s.length();
        vector<vector<int>> left(n, vector<int>(3)), right(n, vector<int>(3));
        int num = 0, mask = 0, count = 0;
        for (int i = 0; i < n - 1; i++) {
            int binary = 1 << (s[i] - 'a');
            if (!(mask & binary)) {
                count++;
                if (count <= k) {
                    mask |= binary;
                } else {
                    num++;
                    mask = binary;
                    count = 1;
                }
            }
            left[i + 1][0] = num;
            left[i + 1][1] = mask;
            left[i + 1][2] = count;
        }

        num = 0, mask = 0, count = 0;
        for (int i = n - 1; i > 0; i--) {
            int binary = 1 << (s[i] - 'a');
            if (!(mask & binary)) {
                count++;
                if (count <= k) {
                    mask |= binary;
                } else {
                    num++;
                    mask = binary;
                    count = 1;
                }
            }
            right[i - 1][0] = num;
            right[i - 1][1] = mask;
            right[i - 1][2] = count;
        }

        int Max = 0;
        for (int i = 0; i < n; i++) {
            int seg = left[i][0] + right[i][0] + 2;
            int totMask = left[i][1] | right[i][1];
            int totCount = 0;
            while (totMask) {
                totMask = totMask & (totMask - 1);
                totCount++;
            }
            if (left[i][2] == k && right[i][2] == k && totCount < 26) {
                seg++;
            } else if (min(totCount + 1, 26) <= k) {
                seg--;
            }
            Max = max(Max, seg);
        }
        return Max;
    }
};
```

---

## 🧾 Explanation of Code

- The `left` and `right` arrays precompute how many partitions and distinct characters exist up to (or from) each point.
- Each bitmask (`mask`) represents distinct characters seen so far.
- Combining masks from both sides allows determining the number of distinct characters when merging partitions.
- Finally, we evaluate all possible single character changes and calculate the resulting partition count efficiently.

---

## ⏱️ Complexity Analysis

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(n)`

---

## ✅ Final Thoughts

This optimized approach leverages **bitmasking** and **prefix/suffix computation** to achieve near-linear performance, easily handling strings of length up to `10^4` without TLE.

It’s a clean and efficient solution accepted on LeetCode for **3003. Maximize the Number of Partitions After Operations**.

---

