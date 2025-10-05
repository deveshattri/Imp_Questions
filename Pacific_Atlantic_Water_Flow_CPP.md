# 🌊 Pacific Atlantic Water Flow

## 🧩 Problem Description

There is an `m x n` rectangular island that borders both the **Pacific Ocean** and **Atlantic Ocean**.  
- The **Pacific Ocean** touches the island's **left** and **top** edges.  
- The **Atlantic Ocean** touches the island's **right** and **bottom** edges.

The island is partitioned into a grid of square cells, and you are given an `m x n` integer matrix `heights` where `heights[r][c]` represents the **height above sea level** of the cell at coordinate `(r, c)`.

Rainwater can flow to neighboring cells **north, south, east, or west** if the neighboring cell’s height is **less than or equal to** the current cell’s height.

Return all grid coordinates `(r, c)` from which rainwater can flow to **both the Pacific and Atlantic oceans**.

---

## 💡 Example 1

**Input:**
```cpp
heights = [
  [1,2,2,3,5],
  [3,2,3,4,4],
  [2,4,5,3,1],
  [6,7,1,4,5],
  [5,1,1,2,4]
]
```

**Output:**
```cpp
[[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```

---

## 💡 Example 2

**Input:**
```cpp
heights = [[1]]
```

**Output:**
```cpp
[[0,0]]
```

---

## ⚙️ Constraints

```
m == heights.length
n == heights[r].length
1 <= m, n <= 200
0 <= heights[r][c] <= 10^5
```

---

## 🚀 Solution (DFS Approach)

### ✅ Intuition

Instead of simulating water flow **from each cell**,  
we can reverse the process — start from the oceans and **flow inward**.

- Start DFS from all **Pacific-border cells** (top and left).
- Start DFS from all **Atlantic-border cells** (bottom and right).
- Mark all cells that can reach each ocean.
- The answer is the intersection of both sets.

### 🧠 Algorithm

1. Create two boolean matrices `pacific` and `atlantic` of size `m x n` to mark reachable cells.
2. Perform DFS from:
   - Top and left edges → fill `pacific`.
   - Bottom and right edges → fill `atlantic`.
3. In DFS, move only to neighbors with **equal or higher** height (reverse direction of flow).
4. Collect cells that are reachable in both `pacific` and `atlantic`.

---

## 💻 Code Implementation (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int m, n;
    vector<vector<int>> directions = {{1,0}, {-1,0}, {0,1}, {0,-1}};

    void dfs(int r, int c, vector<vector<int>>& heights, vector<vector<int>>& ocean) {
        ocean[r][c] = 1;
        for (auto &dir : directions) {
            int nr = r + dir[0], nc = c + dir[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                !ocean[nr][nc] && heights[nr][nc] >= heights[r][c]) {
                dfs(nr, nc, heights, ocean);
            }
        }
    }

    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        m = heights.size();
        n = heights[0].size();
        vector<vector<int>> pacific(m, vector<int>(n, 0));
        vector<vector<int>> atlantic(m, vector<int>(n, 0));

        for (int i = 0; i < m; i++) {
            dfs(i, 0, heights, pacific);
            dfs(i, n - 1, heights, atlantic);
        }
        for (int j = 0; j < n; j++) {
            dfs(0, j, heights, pacific);
            dfs(m - 1, j, heights, atlantic);
        }

        vector<vector<int>> result;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (pacific[i][j] && atlantic[i][j]) {
                    result.push_back({i, j});
                }
            }
        }
        return result;
    }
};

int main() {
    Solution sol;
    vector<vector<int>> heights = {
        {1,2,2,3,5},
        {3,2,3,4,4},
        {2,4,5,3,1},
        {6,7,1,4,5},
        {5,1,1,2,4}
    };

    vector<vector<int>> result = sol.pacificAtlantic(heights);
    for (auto &cell : result) {
        cout << "[" << cell[0] << "," << cell[1] << "] ";
    }
    cout << endl;
    return 0;
}
```

---

## 🧾 Explanation of Code

- DFS runs from ocean borders inward.
- Moves only if the next cell height is **≥ current** (reverse of water flow).
- Cells reachable from both oceans are added to the result.

---

## ⏱️ Time and Space Complexity

| Type | Complexity | Explanation |
|------|-------------|-------------|
| **Time** | `O(m * n)` | Each cell visited at most twice (once per ocean). |
| **Space** | `O(m * n)` | For recursion stack and two visited matrices. |

---

## 🏁 Summary

✅ Reverse DFS from oceans instead of every cell.  
✅ Flow towards higher or equal heights.  
✅ Result = Intersection of Pacific and Atlantic reachable cells.
