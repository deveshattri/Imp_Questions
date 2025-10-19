# Lexicographically Smallest String After Applying Operations

## Problem

You are given a string `s` of even length consisting of digits from 0 to
9, and two integers `a` and `b`.

You can apply either of the following two operations any number of times
and in any order on `s`:

1.  **Add Operation:** Add `a` to all odd indices of `s` (0-indexed).
    Digits post 9 are cycled back to 0.\
    For example, if `s = "3456"` and `a = 5`, `s` becomes `"3951"`.

2.  **Rotate Operation:** Rotate `s` to the right by `b` positions.\
    For example, if `s = "3456"` and `b = 1`, `s` becomes `"6345"`.

Return the **lexicographically smallest string** you can obtain by
applying the above operations any number of times on `s`.

------------------------------------------------------------------------

### Example 1

**Input:**\
`s = "5525", a = 9, b = 2`

**Output:**\
`"2050"`

**Explanation:**\
We can apply the following operations:

    Start:  "5525"
    Rotate: "2555"
    Add:    "2454"
    Add:    "2353"
    Rotate: "5323"
    Add:    "5222"
    Add:    "5121"
    Rotate: "2151"
    Add:    "2050"

There is no way to obtain a string that is lexicographically smaller
than `"2050"`.

------------------------------------------------------------------------

### Example 2

**Input:**\
`s = "74", a = 5, b = 1`

**Output:**\
`"24"`

**Explanation:**

    Start:  "74"
    Rotate: "47"
    Add:    "42"
    Rotate: "24"

------------------------------------------------------------------------

### Example 3

**Input:**\
`s = "0011", a = 4, b = 2`

**Output:**\
`"0011"`

**Explanation:**\
No sequence of operations can produce a lexicographically smaller
string.

------------------------------------------------------------------------

## Constraints

-   2 \<= s.length \<= 100\
-   s.length is even\
-   s consists of digits from 0 to 9 only\
-   1 \<= a \<= 9\
-   1 \<= b \<= s.length - 1

------------------------------------------------------------------------

## Solution

### Approach

We can simulate all possible transformations using **Breadth-First
Search (BFS)** to ensure we explore every reachable string
configuration.\
Since there are only 10 possible values for each digit and a finite
number of rotations, the total state space is limited.

At each step: - Apply the "Add" operation to odd indices. - Apply the
"Rotate" operation by `b` positions. - Track all visited strings to
avoid cycles.

Finally, return the lexicographically smallest string encountered.

------------------------------------------------------------------------

### Code Implementation

``` cpp
class Solution {
public:
    string findLexSmallestString(string s, int a, int b) {
        queue<string> q;
        unordered_set<string> visited;
        string smallest = s;

        q.push(s);
        visited.insert(s);

        while (!q.empty()) {
            string cur = q.front();
            q.pop();
            if (cur < smallest) smallest = cur;

            // Operation 1: Add 'a' to odd indices
            string t = cur;
            for (int i = 1; i < t.size(); i += 2) {
                t[i] = ((t[i] - '0' + a) % 10) + '0';
            }
            if (!visited.count(t)) {
                visited.insert(t);
                q.push(t);
            }

            // Operation 2: Rotate right by b positions
            string r = cur.substr(cur.size() - b) + cur.substr(0, cur.size() - b);
            if (!visited.count(r)) {
                visited.insert(r);
                q.push(r);
            }
        }
        return smallest;
    }
};
```

------------------------------------------------------------------------

### Time Complexity

-   Each state has at most `10 * n` possibilities.\
-   Therefore, **O(10 \* n²)** in the worst case.

### Space Complexity

-   O(10 \* n) for visited states.

------------------------------------------------------------------------

### Key Insights

-   BFS ensures we find the lexicographically smallest reachable
    configuration.
-   Modulo arithmetic handles digit wrapping cleanly.
-   Storing visited states prevents infinite loops.
