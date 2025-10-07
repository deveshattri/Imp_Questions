# Minimum Edges to Collect All Coins in a Tree

## Problem
You are given an undirected and unrooted tree with `n` nodes indexed from `0` to `n - 1`. Each node may or may not have a coin. Starting from any vertex, you can:

1. Collect all coins within distance ≤ 2.
2. Move to any adjacent vertex.

Your task is to find the **minimum number of edges** that must be traversed to collect all coins and return to the starting point.

---

### Example 1
**Input:**
```cpp
coins = [1,0,0,0,0,1]
edges = [[0,1],[1,2],[2,3],[3,4],[4,5]]
```
**Output:**
```
2
```
**Explanation:**
Start at vertex 2, collect the coin at vertex 0, move to vertex 3, collect the coin at vertex 5, then move back to vertex 2.

---

### Example 2
**Input:**
```cpp
coins = [0,0,0,1,1,0,0,1]
edges = [[0,1],[0,2],[1,3],[1,4],[2,5],[5,6],[5,7]]
```
**Output:**
```
2
```
**Explanation:**
Start at vertex 0, collect the coins at vertices 4 and 3, move to vertex 2, collect the coin at vertex 7, then move back to vertex 0.

---

## Approach
The problem can be reduced to pruning the tree to remove unnecessary edges and nodes that don’t contribute to collecting any coins.

### Steps
1. **Build adjacency list** from `edges`.
2. **Prune leaves without coins:** Remove all leaf nodes that do not contain coins. Repeat until no such leaves exist.
3. **Prune leaves twice more:** Since we can collect coins within distance 2, we remove two more layers of leaves from the remaining tree.
4. The remaining tree represents the area that actually needs traversal.
5. The minimum number of edges to traverse and return = `2 * (remaining_edges_count)`.

---

## Code (C++)
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int collectTheCoins(vector<int>& coins, vector<vector<int>>& edges) {
        int n = coins.size();
        vector<vector<int>> adj(n);
        vector<int> degree(n, 0);

        for (auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
            degree[e[0]]++;
            degree[e[1]]++;
        }

        queue<int> q;
        for (int i = 0; i < n; ++i)
            if (degree[i] == 1 && coins[i] == 0)
                q.push(i);

        // Step 1: prune leaves with no coins
        while (!q.empty()) {
            int node = q.front(); q.pop();
            degree[node]--;
            for (int nei : adj[node]) {
                if (--degree[nei] == 1 && coins[nei] == 0)
                    q.push(nei);
            }
        }

        // Step 2: prune 2 more layers
        for (int k = 0; k < 2; ++k) {
            queue<int> temp;
            for (int i = 0; i < n; ++i)
                if (degree[i] == 1)
                    temp.push(i);

            while (!temp.empty()) {
                int node = temp.front(); temp.pop();
                degree[node]--;
                for (int nei : adj[node]) {
                    if (--degree[nei] == 1)
                        temp.push(nei);
                }
            }
        }

        // Step 3: count remaining edges
        int remainingEdges = 0;
        for (auto& e : edges)
            if (degree[e[0]] > 0 && degree[e[1]] > 0)
                remainingEdges++;

        return remainingEdges * 2;
    }
};
```

---

## Explanation of Logic
- **Why prune non-coin leaves?** They add no value; you don’t need to travel to them.
- **Why prune two more layers?** Coins within distance ≤ 2 can be collected without visiting nodes beyond that range.
- **Why multiply by 2?** You must travel each necessary edge twice — once to go and once to return.

---

## Complexity
- **Time:** O(n)
- **Space:** O(n)

Efficient for up to `3 × 10⁴` nodes.
