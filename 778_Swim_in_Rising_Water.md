# 778. Swim in Rising Water

**Difficulty:** Hard  
**Topics:** Graph, Heap (Priority Queue), Binary Search, Matrix, Dijkstra’s Algorithm  

---

## 🧩 Problem Statement

You are given an `n x n` integer matrix `grid` where each value `grid[i][j]` represents the elevation at that point `(i, j)`.

It starts raining, and water gradually rises over time. At time `t`, the water level is `t`, meaning any cell with elevation less than or equal to `t` is submerged or reachable.

You can swim from a square to another **4-directionally adjacent** square if and only if the elevation of both squares individually are at most `t`.  
You can swim infinite distances in zero time, but must stay within grid boundaries.

Return the **minimum time** until you can reach the bottom right square `(n - 1, n - 1)` if you start at the top left `(0, 0)`.

---

### Example 1

**Input:**
```
grid = [[0,2],[1,3]]
```

**Output:**
```
3
```

**Explanation:**
At time 0, you are at (0, 0).  
You cannot move to (0, 1) or (1, 0) because their elevation > 0.  
When the water reaches 3, all cells become reachable.

---

### Example 2

**Input:**
```
grid = [
 [0,1,2,3,4],
 [24,23,22,21,5],
 [12,13,14,15,16],
 [11,17,18,19,20],
 [10,9,8,7,6]
]
```

**Output:**
```
16
```

**Explanation:**  
You must wait until the water level reaches 16 to connect (0,0) → (4,4).

---

### Constraints
- `n == grid.length`
- `n == grid[i].length`
- `1 <= n <= 50`
- `0 <= grid[i][j] < n²`
- All elevations are unique.

---

## 💡 Approach

This problem can be solved efficiently using **Dijkstra’s Algorithm** or **Min-Heap BFS**.

### Intuition:
We can think of this as finding a path from the top-left to bottom-right that minimizes the **maximum elevation** encountered along the way.

At each step, you can move to adjacent cells — but you can only move when the water level `t` is at least equal to the **highest elevation** on your path so far.

So, we simulate the water rising using a **min-heap** (priority queue), always expanding from the lowest elevation we can reach next.

---

## 🧠 Algorithm

1. Initialize a **min-heap** that stores cells as `(height, x, y)`.
2. Start with `(grid[0][0], 0, 0)`.
3. Keep a visited matrix to avoid revisiting cells.
4. For each step:
   - Pop the cell with the smallest elevation.
   - Track the maximum elevation seen so far (this represents current water level).
   - If we reach `(n-1, n-1)`, return that maximum value.
   - Explore all 4 neighbors (up, down, left, right) and push unvisited cells into the heap.
5. The first time we pop `(n-1, n-1)` from heap, the current max elevation is our answer.

---

## 🧮 Time and Space Complexity

| Operation | Complexity |
|------------|-------------|
| **Time** | O(n² log n) |
| **Space** | O(n²) |

---

## 🧱 C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int swimInWater(vector<vector<int>>& grid) {
        int n = grid.size();
        vector<vector<int>> visited(n, vector<int>(n, 0));
        priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;

        pq.push({grid[0][0], 0, 0});
        int res = 0;
        vector<int> dir = {0, 1, 0, -1, 0};

        while (!pq.empty()) {
            auto curr = pq.top(); pq.pop();
            int height = curr[0], x = curr[1], y = curr[2];

            res = max(res, height);
            if (x == n - 1 && y == n - 1) return res;

            if (visited[x][y]) continue;
            visited[x][y] = 1;

            for (int k = 0; k < 4; ++k) {
                int nx = x + dir[k], ny = y + dir[k + 1];
                if (nx >= 0 && ny >= 0 && nx < n && ny < n && !visited[nx][ny]) {
                    pq.push({grid[nx][ny], nx, ny});
                }
            }
        }
        return -1;
    }
};
```

---

## 🧾 Explanation with Example

Let’s take Example 1:

```
grid = [[0, 2],
        [1, 3]]
```

| Step | Cell Visited | Current Water Level | Min-Heap Contents |
|------|---------------|--------------------|-------------------|
| 1 | (0,0) | 0 | [(1,1,0), (2,0,1)] |
| 2 | (1,0) | 1 | [(2,0,1), (3,1,1)] |
| 3 | (0,1) | 2 | [(3,1,1)] |
| 4 | (1,1) | 3 | [] |

At the end, we reached (1,1) with water level = **3**.

---

## ✅ Final Answer

```
Output: 3
```

---

