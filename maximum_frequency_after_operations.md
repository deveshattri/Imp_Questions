
# Maximum Frequency After Operations

## Problem

You are given an integer array `nums` and two integers `k` and `numOperations`.

You must perform an operation `numOperations` times on `nums`, where in each operation you:

- Select an index `i` that was not selected in any previous operations.
- Add an integer in the range `[-k, k]` to `nums[i]`.

Return the **maximum possible frequency** of any element in `nums` after performing the operations.

---

## Example 1

**Input:**  
`nums = [1,4,5], k = 1, numOperations = 2`

**Output:**  
`2`

**Explanation:**  
We can achieve a maximum frequency of two by:
- Adding 0 to `nums[1]`, after which `nums = [1, 4, 5]`
- Adding -1 to `nums[2]`, after which `nums = [1, 4, 4]`

---

## Example 2

**Input:**  
`nums = [5,11,20,20], k = 5, numOperations = 1`

**Output:**  
`2`

**Explanation:**  
We can achieve a maximum frequency of two by adding 0 to `nums[1]`.

---

## Approach

1. Sort the array to make range calculations easier.
2. Use a **sliding window** approach to find the largest group of elements that can be made equal by adjusting up to `numOperations` elements within the allowed range `k`.
3. For each window `[l, r]`, check if the difference between `nums[r]` and `nums[l]` is within `2*k` (since each element can move by up to `k` both ways).
4. We can use at most `numOperations` elements to adjust within the range.
5. The answer is the maximum window size achievable under these conditions.

---

## C++ Solution

```cpp
class Solution {
public:
    int maxFrequency(vector<int>& nums, int k, int numOperations) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        int left = 0, ans = 1;
        
        for (int right = 0; right < n; right++) {
            while (nums[right] - nums[left] > 2 * k) left++;
            ans = max(ans, right - left + 1);
        }
        
        // We can adjust at most numOperations distinct elements, so add that limit
        return min(ans + numOperations, n);
    }
};
```

---

## Explanation of Code

- We first sort `nums`.
- The `while` loop ensures that all elements in the current window can be adjusted within the range `[-k, k]` to become equal.
- The window size `right - left + 1` represents the number of elements that can be made equal without exceeding the adjustment limit.
- Finally, since we can modify at most `numOperations` elements, the final frequency is capped by `ans + numOperations`, but not exceeding the array size.

---

## Time Complexity

- Sorting takes **O(n log n)**.
- The sliding window traversal takes **O(n)**.
- Hence, the total time complexity is **O(n log n)**.

---

## Space Complexity

- Only a few variables are used, so **O(1)** extra space.

