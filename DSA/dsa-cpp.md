# DSA-CPPP Interview Cheatsheet (Brief)

Short, interview-focused DSA with C++ reference.
Format per topic: What, Example, Logic, Input, Output.

## Quick Links
- [High Priority Topics](#high-priority-topics)
- [1 Complexity](#1-complexity)
- [2 Arrays Strings](#2-arrays-strings)
- [3 Hashing](#3-hashing)
- [4 Two Pointers](#4-two-pointers)
- [5 Sliding Window](#5-sliding-window)
- [6 Binary Search](#6-binary-search)
- [7 Sorting](#7-sorting)
- [8 Stack](#8-stack)
- [9 Queue Deque](#9-queue-deque)
- [10 Linked List](#10-linked-list)
- [11 Recursion Backtracking](#11-recursion-backtracking)
- [12 Trees](#12-trees)
- [13 BST](#13-bst)
- [14 Heap Priority Queue](#14-heap-priority-queue)
- [15 Graph BFS DFS](#15-graph-bfs-dfs)
- [16 Shortest Path](#16-shortest-path)
- [17 DSU Union Find](#17-dsu-union-find)
- [18 Greedy](#18-greedy)
- [19 Dynamic Programming](#19-dynamic-programming)
- [20 Prefix Sum Difference Array](#20-prefix-sum-difference-array)
- [21 STL Must-Know](#21-stl-must-know)
- [22 STL Basic Code Examples](#22-stl-basic-code-examples)
- [Ultra-Short Priority](#ultra-short-priority)

---

## High Priority Topics

Most asked in coding interviews:
- Time/Space Complexity
- Hashing
- Two Pointers and Sliding Window
- Binary Search
- Trees + BST
- Graph BFS/DFS + shortest path basics
- Dynamic Programming
- STL speed usage

---

## 1 Complexity

### What
Measures algorithm growth as input size n increases.

### Example
```cpp
for (int i = 0; i < n; i++) {
    cout << i;
}
```

### Logic
Single loop over n elements gives linear growth.

### Input
n = 5

### Output
Time O(n), Space O(1)

## 2 Arrays Strings

### What
Contiguous data structures used for indexing and iteration.

### Example
```cpp
vector<int> a = {10, 20, 30};
string s = "abc";
cout << a[1] << " " << s[2];
```

### Logic
Indexing is constant time for vectors/strings.

### Input
a = {10,20,30}, s = abc

### Output
20 c

## 3 Hashing

### What
Store key-value pairs for fast average lookups.

### Example
```cpp
unordered_map<int, int> freq;
freq[5]++;
freq[5]++;
cout << freq[5];
```

### Logic
Hash table maps key to bucket, expected O(1) operations.

### Input
Keys inserted: 5, 5

### Output
2

## 4 Two Pointers

### What
Two indices move with rules to reduce brute force.

### Example
```cpp
vector<int> a = {1, 2, 4, 7, 11};
int l = 0, r = (int)a.size() - 1, target = 9;
while (l < r) {
    int sum = a[l] + a[r];
    if (sum == target) break;
    if (sum < target) l++;
    else r--;
}
cout << a[l] << " " << a[r];
```

### Logic
Sorted array allows moving left/right pointer by sum comparison.

### Input
[1,2,4,7,11], target = 9

### Output
2 7

## 5 Sliding Window

### What
Maintain moving subarray/substring range.

### Example
```cpp
vector<int> a = {1, 2, 3, 4};
int k = 2, cur = a[0] + a[1], best = cur;
for (int i = k; i < (int)a.size(); i++) {
    cur += a[i] - a[i - k];
    best = max(best, cur);
}
cout << best;
```

### Logic
Reuse previous window sum in O(1) per move.

### Input
[1,2,3,4], k = 2

### Output
7

## 6 Binary Search

### What
Search in sorted space by halving range.

### Example
```cpp
vector<int> a = {1, 3, 5, 7, 9};
int l = 0, r = 4, x = 7, ans = -1;
while (l <= r) {
    int m = l + (r - l) / 2;
    if (a[m] == x) { ans = m; break; }
    if (a[m] < x) l = m + 1;
    else r = m - 1;
}
cout << ans;
```

### Logic
Each step removes half search space.

### Input
Sorted array, x = 7

### Output
3

## 7 Sorting

### What
Arrange elements in order.

### Example
```cpp
vector<int> a = {4, 1, 3, 2};
sort(a.begin(), a.end());
for (int x : a) cout << x << " ";
```

### Logic
C++ sort uses introsort, typically O(n log n).

### Input
[4,1,3,2]

### Output
1 2 3 4

## 8 Stack

### What
LIFO structure.

### Example
```cpp
stack<int> st;
st.push(10);
st.push(20);
cout << st.top() << " ";
st.pop();
cout << st.top();
```

### Logic
Last inserted is first removed.

### Input
Push 10, 20

### Output
20 10

## 9 Queue Deque

### What
Queue is FIFO; deque supports push/pop from both ends.

### Example
```cpp
queue<int> q;
q.push(1); q.push(2);
cout << q.front() << " ";

deque<int> dq;
dq.push_front(5);
dq.push_back(8);
cout << dq.front() << " " << dq.back();
```

### Logic
Queue processes arrival order; deque is bi-directional.

### Input
Queue: 1,2 and Deque: front 5, back 8

### Output
1 5 8

## 10 Linked List

### What
Node-based linear structure with pointers.

### Example
```cpp
struct Node {
    int val;
    Node* next;
    Node(int v) : val(v), next(nullptr) {}
};

Node* a = new Node(1);
Node* b = new Node(2);
a->next = b;
cout << a->next->val;
```

### Logic
Each node stores data and link to next node.

### Input
Nodes 1 -> 2

### Output
2

## 11 Recursion Backtracking

### What
Recursion solves smaller subproblem; backtracking explores + undoes choices.

### Example
```cpp
void gen(int n, string& cur) {
    if ((int)cur.size() == n) {
        cout << cur << " ";
        return;
    }
    cur.push_back('0'); gen(n, cur); cur.pop_back();
    cur.push_back('1'); gen(n, cur); cur.pop_back();
}
```

### Logic
Tree of choices, backtrack restores state after each branch.

### Input
n = 2

### Output
00 01 10 11

## 12 Trees

### What
Hierarchical structure with root and children.

### Example
```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int v) : val(v), left(nullptr), right(nullptr) {}
};
```

### Logic
Recursive traversals naturally fit tree structure.

### Input
Create tree nodes

### Output
Tree model ready for DFS/BFS

## 13 BST

### What
Binary search tree keeps left < root < right.

### Example
```cpp
TreeNode* insertBST(TreeNode* root, int x) {
    if (!root) return new TreeNode(x);
    if (x < root->val) root->left = insertBST(root->left, x);
    else root->right = insertBST(root->right, x);
    return root;
}
```

### Logic
Comparison decides insertion path.

### Input
Insert 5, 2, 8

### Output
Valid BST

## 14 Heap Priority Queue

### What
Heap gives fast access to min/max element.

### Example
```cpp
priority_queue<int> maxh;
maxh.push(3); maxh.push(10); maxh.push(1);
cout << maxh.top();
```

### Logic
Default priority_queue in C++ is max-heap.

### Input
3, 10, 1

### Output
10

## 15 Graph BFS DFS

### What
Graph traversal techniques.

### Example
```cpp
vector<vector<int>> g = {{1,2},{0,3},{0,3},{1,2}};
vector<int> vis(4, 0);
queue<int> q;
q.push(0); vis[0] = 1;
while (!q.empty()) {
    int u = q.front(); q.pop();
    for (int v : g[u]) if (!vis[v]) {
        vis[v] = 1;
        q.push(v);
    }
}
cout << vis[3];
```

### Logic
BFS explores layer by layer; DFS explores depth first.

### Input
Start BFS from node 0

### Output
1

## 16 Shortest Path

### What
Find minimum distance from source to nodes.

### Example
```cpp
vector<vector<pair<int,int>>> g(3);
g[0].push_back({1, 4});
g[0].push_back({2, 1});
g[2].push_back({1, 2});

vector<long long> dist(3, (long long)1e18);
priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> pq;
dist[0] = 0; pq.push({0, 0});
while (!pq.empty()) {
    auto [d, u] = pq.top(); pq.pop();
    if (d != dist[u]) continue;
    for (auto [v, w] : g[u]) {
        if (dist[v] > d + w) {
            dist[v] = d + w;
            pq.push({dist[v], v});
        }
    }
}
cout << dist[1];
```

### Logic
Dijkstra works for non-negative edge weights.

### Input
Edges: 0->1(4), 0->2(1), 2->1(2)

### Output
3

## 17 DSU Union Find

### What
Track connected components with union and find.

### Example
```cpp
struct DSU {
    vector<int> p, sz;
    DSU(int n) : p(n), sz(n, 1) { iota(p.begin(), p.end(), 0); }
    int find(int x) { return p[x] == x ? x : p[x] = find(p[x]); }
    bool unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return false;
        if (sz[a] < sz[b]) swap(a, b);
        p[b] = a; sz[a] += sz[b];
        return true;
    }
};
```

### Logic
Path compression + union by size gives near O(1) amortized.

### Input
Union(1,2), Union(2,3)

### Output
1 and 3 become connected

## 18 Greedy

### What
Take locally best decision each step.

### Example
```cpp
vector<pair<int,int>> a = {{1,3},{2,4},{3,5}};
sort(a.begin(), a.end(), [](auto& x, auto& y) {
    return x.second < y.second;
});
int cnt = 0, lastEnd = -1;
for (auto &it : a) {
    if (it.first >= lastEnd) {
        cnt++;
        lastEnd = it.second;
    }
}
cout << cnt;
```

### Logic
For interval scheduling, earliest finish first is optimal.

### Input
Intervals (1,3), (2,4), (3,5)

### Output
2

## 19 Dynamic Programming

### What
Store subproblem answers to avoid recomputation.

### Example
```cpp
int n = 6;
vector<int> dp(n + 1, 0);
dp[1] = 1;
for (int i = 2; i <= n; i++) dp[i] = dp[i - 1] + dp[i - 2];
cout << dp[n];
```

### Logic
Bottom-up tabulation builds answer from small states.

### Input
n = 6

### Output
8

## 20 Prefix Sum Difference Array

### What
Prefix sum accelerates range sum; difference array accelerates range updates.

### Example
```cpp
vector<int> a = {2, 4, 6, 8};
vector<int> pref(a.size() + 1, 0);
for (int i = 0; i < (int)a.size(); i++) pref[i + 1] = pref[i] + a[i];
int l = 1, r = 3;
cout << pref[r + 1] - pref[l];
```

### Logic
Range sum [l..r] computed in O(1) after O(n) preprocess.

### Input
[2,4,6,8], l=1, r=3

### Output
18

## 21 STL Must-Know

### What
Core STL pieces that directly improve coding speed.

### Example
```cpp
vector<int> v = {5, 1, 4, 4};
sort(v.begin(), v.end());
cout << binary_search(v.begin(), v.end(), 4) << " ";
cout << (lower_bound(v.begin(), v.end(), 4) - v.begin()) << " ";
cout << __gcd(18, 24);
```

### Logic
Fast ready-made utilities reduce bugs and implementation time.

### Input
Vector [5,1,4,4], gcd(18,24)

### Output
1 1 6

## 22 STL Basic Code Examples

### vector
```cpp
vector<int> v = {1, 2};
v.push_back(3);
cout << v[0] << " " << v.size();
```
Output: `1 3`

### map
```cpp
map<string, int> mp;
mp["a"] = 10;
mp["b"] = 20;
cout << mp["a"];
```
Output: `10`

### unordered_map
```cpp
unordered_map<string, int> ump;
ump["x"]++;
ump["x"]++;
cout << ump["x"];
```
Output: `2`

### set
```cpp
set<int> s;
s.insert(5);
s.insert(5);
s.insert(2);
cout << s.size();
```
Output: `2`

### unordered_set
```cpp
unordered_set<int> us;
us.insert(7);
us.insert(7);
us.insert(1);
cout << us.count(7);
```
Output: `1`

### multiset
```cpp
multiset<int> ms;
ms.insert(4);
ms.insert(4);
cout << ms.count(4);
```
Output: `2`

### pair
```cpp
pair<string, int> p = {"age", 21};
cout << p.first << " " << p.second;
```
Output: `age 21`

### list
```cpp
list<int> li;
li.push_back(10);
li.push_front(5);
cout << li.front() << " " << li.back();
```
Output: `5 10`

### deque
```cpp
deque<int> dq;
dq.push_front(1);
dq.push_back(3);
cout << dq.front() << " " << dq.back();
```
Output: `1 3`

### queue
```cpp
queue<int> q;
q.push(11);
q.push(22);
cout << q.front() << " " << q.back();
```
Output: `11 22`

### stack
```cpp
stack<int> st;
st.push(100);
st.push(200);
cout << st.top();
```
Output: `200`

### priority_queue (max heap)
```cpp
priority_queue<int> mx;
mx.push(3);
mx.push(10);
mx.push(6);
cout << mx.top();
```
Output: `10`

### priority_queue (min heap)
```cpp
priority_queue<int, vector<int>, greater<int>> mn;
mn.push(3);
mn.push(10);
mn.push(6);
cout << mn.top();
```
Output: `3`

### dynamic allocation
```cpp
int n = 3;
int* arr = new int[n]{10, 20, 30};
cout << arr[1];
delete[] arr;
```
Output: `20`

---

## Ultra-Short Priority

Complexity
Hashing
Two Pointers / Sliding Window
Binary Search
Trees + Graphs
DP
STL speed

---

## Reality

DSA interviews usually decide on:
- Pattern recognition speed
- Correct complexity choice
- Clean STL usage under time pressure

If these are strong, selection chance increases sharply.
