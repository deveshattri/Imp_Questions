# Minimum Groups for Company Party

## Problem Statement
A company has n employees with a hierarchical manager structure (no cycles). We need to divide all employees into groups such that:
- Each employee is in exactly one group
- No group contains both an employee and their superior (direct or indirect manager)
- The number of groups is minimized

## Approach
### Key Insight
The problem reduces to finding the *longest path in the managerial hierarchy tree*, as employees along the longest chain must be placed in separate groups.

### Solution Steps
1. *Build the hierarchy tree* from the given manager relationships
2. *Perform BFS* starting from all root employees (those with no manager)
3. *Track depth* during BFS traversal
4. The *maximum depth* found equals the minimum groups needed

## Complexity Analysis
| Complexity | Description |
|------------|-------------|
| *Time*   | O(n)        | 
| *Space*  | O(n)        |

## Solution Code
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    
    vector<int> v(n + 1);
    for (int i = 1; i <= n; i++) cin >> v[i];
    
    // Build adjacency list (manager -> subordinates)
    vector<vector<int>> adj(n + 1);
    for (int i = 1; i <= n; i++) {
        if (v[i] != -1) adj[v[i]].push_back(i);
    }
    
    // BFS queue with (node, current_depth)
    queue<pair<int, int>> q;
    // Start with all root nodes (no manager)
    for (int i = 1; i <= n; i++) {
        if (v[i] == -1) q.push({i, 1});
    }
    
    int ans = 0;
    while(!q.empty()) {
        int node = q.front().first;
        int depth = q.front().second;
        q.pop();
        ans = max(ans, depth);
        
        // Add all subordinates with increased depth
        for (auto it : adj[node]) {
            q.push({it, depth + 1});
        }
    }
    
    cout << ans << endl;
    return 0;
}
