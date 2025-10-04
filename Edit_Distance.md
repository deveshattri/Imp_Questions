# Edit Distance

**LeetCode Problem 72 – Medium**

---

## Problem Statement

Given two strings `word1` and `word2`, return the minimum number of operations required to convert `word1` to `word2`.

You have the following three operations permitted on a word:
1. Insert a character  
2. Delete a character  
3. Replace a character  

---

### Example 1:
**Input:**  
```
word1 = "horse", word2 = "ros"
```

**Output:**  
```
3
```

**Explanation:**  
- horse -> rorse (replace 'h' with 'r')  
- rorse -> rose (remove 'r')  
- rose -> ros (remove 'e')  

---

### Example 2:
**Input:**  
```
word1 = "intention", word2 = "execution"
```

**Output:**  
```
5
```

**Explanation:**  
- intention -> inention (remove 't')  
- inention -> enention (replace 'i' with 'e')  
- enention -> exention (replace 'n' with 'x')  
- exention -> exection (replace 'n' with 'c')  
- exection -> execution (insert 'u')  

---

## Constraints:
- `0 <= word1.length, word2.length <= 500`  
- `word1` and `word2` consist of lowercase English letters.  

---

## Solution

We use **Dynamic Programming (DP)**.  
Define `dp[i][j]` as the minimum number of operations to convert the first `i` characters of `word1` into the first `j` characters of `word2`.

### Recurrence:
- If the current characters are equal:  
  ```
  dp[i][j] = dp[i-1][j-1]
  ```
- Else, we take the minimum of three operations:
  - Insert (`dp[i][j-1] + 1`)
  - Delete (`dp[i-1][j] + 1`)
  - Replace (`dp[i-1][j-1] + 1`)

### Base cases:
- `dp[0][j] = j` (need `j` insertions to convert empty string to word2)  
- `dp[i][0] = i` (need `i` deletions to convert word1 to empty string)  

---

### C++ Implementation

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));

        for (int i = 0; i <= n; i++) dp[i][0] = i;
        for (int j = 0; j <= m; j++) dp[0][j] = j;

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    int insertOp = dp[i][j - 1] + 1;
                    int deleteOp = dp[i - 1][j] + 1;
                    int replaceOp = dp[i - 1][j - 1] + 1;
                    dp[i][j] = min({insertOp, deleteOp, replaceOp});
                }
            }
        }

        return dp[n][m];
    }
};
```

---

## Explanation

- We build a DP table of size `(n+1) x (m+1)` where `n` is the length of `word1` and `m` is the length of `word2`.  
- Each cell `dp[i][j]` represents the minimum edit distance between the first `i` characters of `word1` and first `j` characters of `word2`.  
- If characters match, no operation is needed. Otherwise, we choose the best among insert, delete, or replace.  
- The final answer is `dp[n][m]`.  

**Time Complexity:** `O(n * m)`  
**Space Complexity:** `O(n * m)`  
