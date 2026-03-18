# DSA with C++: Complete Learning Cheatsheet

This guide is written for learning Data Structures and Algorithms using C++.
Every major topic follows a practical format:
- What
- Why
- How
- Logic/Concept
- Example
- Behavior of Example

---

## Table of Contents
- [1. Pick a Language](#1-pick-a-language)
- [2. Programming Fundamentals](#2-programming-fundamentals)
- [3. Core Understanding](#3-core-understanding)
- [4. Basic Data Structures](#4-basic-data-structures)
- [5. Algorithmic Complexity](#5-algorithmic-complexity)
- [6. Sorting Algorithms](#6-sorting-algorithms)
- [7. Search Algorithms](#7-search-algorithms)
- [8. Tree Data Structures](#8-tree-data-structures)
- [9. Graph Data Structures](#9-graph-data-structures)
- [10. Advanced Data Structures](#10-advanced-data-structures)
- [11. Complex Data Structures](#11-complex-data-structures)
- [12. Indexing](#12-indexing)
- [13. Problem Solving Techniques](#13-problem-solving-techniques)
- [14. Patterns and Special Techniques](#14-patterns-and-special-techniques)

---

## 1. Pick a Language

### C++ as DSA Language
**What**
- C++ is your selected language for DSA.

**Why**
- Fast execution.
- Rich STL (`vector`, `queue`, `stack`, `priority_queue`, `unordered_map`, `set`, algorithms).
- Close to memory model and pointers, useful for interview + systems thinking.

**How**
- Use C++17 or C++20.
- Practice with `<vector>`, `<algorithm>`, `<queue>`, `<stack>`, `<unordered_map>`.

**Logic/Concept**
- For DSA, language should help you express data structures cleanly and run efficiently.

**Example**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {5, 1, 4};
    sort(a.begin(), a.end());
    for (int x : a) cout << x << ' ';
}
```

**Behavior of Example**
- Sorts the array in ascending order and prints `1 4 5`.

### Quick Language Context (Other Options)
- JavaScript: great for web, not ideal for low-level memory concepts.
- Java: strong OOP and standard library, good for interviews.
- Go: clean syntax, excellent concurrency.
- C#: enterprise and game dev ecosystem.
- Python: fastest for problem-solving speed, slower runtime.
- Rust: memory safety and performance, steeper learning curve.
- Ruby: expressive, less common in DSA interviews.

---

## 2. Programming Fundamentals

### Language Syntax
**What**
- Rules to write valid C++ statements.

**Why**
- Without syntax fluency, DSA logic gets blocked.

**How**
- Learn declarations, loops, conditionals, functions, classes.

**Logic/Concept**
- Syntax is not DSA, but syntax enables DSA implementation.

**Example**
```cpp
int n = 10;
if (n > 0) cout << "positive\n";
```

**Behavior of Example**
- Prints `positive` because condition is true.

### Control Structures
**What**
- `if`, `else`, `switch`, loops.

**Why**
- Every algorithm is built from decisions + repetition.

**How**
- Use `for` for fixed iterations, `while` for condition-driven loops.

**Logic/Concept**
- Control flow determines algorithm path and complexity.

**Example**
```cpp
for (int i = 0; i < 3; i++) cout << i << ' ';
```

**Behavior of Example**
- Prints `0 1 2`.

### Functions
**What**
- Reusable code blocks.

**Why**
- Break problems into smaller testable units.

**How**
- Use input parameters + return values.

**Logic/Concept**
- A clean function usually solves one subproblem.

**Example**
```cpp
int square(int x) { return x * x; }
```

**Behavior of Example**
- `square(5)` returns `25`.

### OOP Basics
**What**
- Classes, objects, encapsulation, inheritance.

**Why**
- Useful in design-heavy interview rounds and system code.

**How**
- Wrap data + behavior together.

**Logic/Concept**
- OOP is less used in pure algorithm rounds, but important in larger problem modeling.

**Example**
```cpp
class Counter {
    int c = 0;
public:
    void inc() { c++; }
    int get() const { return c; }
};
```

**Behavior of Example**
- State is private and controlled via methods.

### Pseudo Code
**What**
- Language-independent algorithm description.

**Why**
- Helps think clearly before coding.

**How**
- Write steps in plain structured style.

**Logic/Concept**
- Good pseudo code reduces coding errors.

**Example**
```txt
find max:
1) max = first element
2) iterate remaining elements
3) if current > max, update max
4) return max
```

**Behavior of Example**
- Gives clear implementation plan.

---

## 3. Core Understanding

### What are Data Structures?
**What**
- Ways to organize and store data for efficient access and updates.

**Why**
- Same problem can be slow or fast depending on chosen structure.

**How**
- Choose based on operations: insert, delete, search, iterate.

**Logic/Concept**
- Data structure choice controls algorithm complexity.

**Example**
```txt
Need fast key lookup? choose hash table.
Need sorted order? choose balanced tree.
Need FIFO processing? choose queue.
```

**Behavior of Example**
- Correct structure makes operations naturally efficient.

### Why are Data Structures Important?
**What**
- They are the foundation of scalable problem solving.

**Why**
- Performance and memory efficiency are interview and production essentials.

**How**
- Map each problem to operation profile and constraints.

**Logic/Concept**
- Problem -> operations -> structure -> algorithm.

**Example**
```txt
Autocompletion uses Trie instead of array scan.
```

**Behavior of Example**
- Prefix search becomes much faster than linear checking.

---

## 4. Basic Data Structures

### Array
**What**
- Contiguous memory collection of same type.

**Why**
- O(1) index access.

**How**
- Use `vector<int>` in C++ for dynamic array behavior.

**Logic/Concept**
- Random access fast, insertion/deletion in middle costly.

**Example**
```cpp
vector<int> a = {10, 20, 30};
cout << a[1] << '\n';
```

**Behavior of Example**
- Prints `20` in constant time.

### Linked Lists
**What**
- Nodes connected via pointers.

**Why**
- Dynamic size and efficient insertion/deletion when node location known.

**How**
- Each node stores value + next pointer.

**Logic/Concept**
- No direct random access, traversal required.

**Example**
```cpp
struct Node {
    int val;
    Node* next;
    Node(int v) : val(v), next(nullptr) {}
};
```

**Behavior of Example**
- Defines list node structure for chain-based storage.

### Queues
**What**
- FIFO (first in first out) structure.

**Why**
- Scheduling, BFS, buffering.

**How**
- Use `queue<int>`.

**Logic/Concept**
- Push at back, pop from front.

**Example**
```cpp
queue<int> q;
q.push(1); q.push(2);
cout << q.front() << '\n';
q.pop();
```

**Behavior of Example**
- First element (`1`) leaves first.

### Stacks
**What**
- LIFO (last in first out) structure.

**Why**
- Expression parsing, recursion simulation, monotonic stack patterns.

**How**
- Use `stack<int>`.

**Logic/Concept**
- Push/pop from same end (top).

**Example**
```cpp
stack<int> st;
st.push(10); st.push(20);
cout << st.top() << '\n';
st.pop();
```

**Behavior of Example**
- `20` is removed first.

### Hash Tables
**What**
- Key-value storage using hash function.

**Why**
- Average O(1) lookup/insert/delete.

**How**
- Use `unordered_map<Key, Value>`.

**Logic/Concept**
- Hash collisions handled internally (buckets/chains/open addressing).

**Example**
```cpp
unordered_map<string, int> freq;
freq["cpp"]++;
cout << freq["cpp"] << '\n';
```

**Behavior of Example**
- Prints `1` after one insertion.

---

## 5. Algorithmic Complexity

### Time vs Space Complexity
**What**
- Time: operation growth with input size `n`.
- Space: extra memory growth with `n`.

**Why**
- Helps choose scalable solutions.

**How**
- Count dominant operations and auxiliary memory.

**Logic/Concept**
- Trade-offs are common: faster time may use more memory.

**Example**
```txt
Using hash map may reduce time from O(n^2) to O(n), but uses O(n) extra space.
```

**Behavior of Example**
- Faster runtime with additional memory usage.

### How to Calculate Complexity?
**What**
- Method to estimate algorithm growth.

**Why**
- Interviewers expect reasoning, not only code.

**How**
- Identify loops, nested loops, recursive branches, dominant terms.

**Logic/Concept**
- Ignore constants and lower-order terms for asymptotic analysis.

**Example**
```txt
for i in [0..n): O(n)
for i in [0..n): for j in [0..n): O(n^2)
```

**Behavior of Example**
- Complexity increases rapidly with nesting depth.

### Complexity Types

#### Constant O(1)
- Access array by index.

#### Logarithmic O(log n)
- Binary search halves search space.

#### Linear O(n)
- Single pass over array.

#### Polynomial O(n^2), O(n^3)
- Nested loops over same data.

#### Exponential O(2^n)
- Include/exclude recursion.

#### Factorial O(n!)
- Permutations generation.

### Asymptotic Notation

#### Big-O
- Upper bound (worst-case growth class).

#### Big-theta
- Tight bound (exact growth class).

#### Big-omega
- Lower bound (best-case or minimum growth class).

**Example**
```txt
T(n) = 3n^2 + 2n + 5 -> O(n^2), Theta(n^2), Omega(n^2)
```

**Behavior of Example**
- Dominant term `n^2` governs scaling.

---

## 6. Sorting Algorithms

### Bubble Sort
**What**
- Repeatedly swaps adjacent out-of-order elements.

**Why**
- Educational baseline.

**How**
- Multiple passes; largest element bubbles to end.

**Logic/Concept**
- Stable, in-place, usually O(n^2).

**Example**
```cpp
void bubbleSort(vector<int>& a) {
    int n = (int)a.size();
    for (int i = 0; i < n - 1; i++) {
        bool swapped = false;
        for (int j = 0; j < n - i - 1; j++) {
            if (a[j] > a[j + 1]) {
                swap(a[j], a[j + 1]);
                swapped = true;
            }
        }
        if (!swapped) break;
    }
}
```

**Behavior of Example**
- Sorts array ascending; stops early if already sorted.

### Merge Sort
**What**
- Divide-and-conquer stable sort.

**Why**
- Guaranteed O(n log n) time.

**How**
- Recursively split and merge sorted halves.

**Logic/Concept**
- Extra O(n) memory for merging.

**Example**
```cpp
void mergeSort(vector<int>& a, int l, int r) {
    if (l >= r) return;
    int m = l + (r - l) / 2;
    mergeSort(a, l, m);
    mergeSort(a, m + 1, r);
    vector<int> tmp;
    int i = l, j = m + 1;
    while (i <= m && j <= r) {
        if (a[i] <= a[j]) tmp.push_back(a[i++]);
        else tmp.push_back(a[j++]);
    }
    while (i <= m) tmp.push_back(a[i++]);
    while (j <= r) tmp.push_back(a[j++]);
    for (int k = l; k <= r; k++) a[k] = tmp[k - l];
}
```

**Behavior of Example**
- Produces sorted output with predictable performance.

### Insertion Sort
**What**
- Builds sorted prefix one item at a time.

**Why**
- Simple and efficient for small/nearly sorted arrays.

**How**
- Insert current element into proper position in left side.

**Logic/Concept**
- Best-case O(n), worst-case O(n^2).

**Example**
```cpp
void insertionSort(vector<int>& a) {
    for (int i = 1; i < (int)a.size(); i++) {
        int key = a[i], j = i - 1;
        while (j >= 0 && a[j] > key) {
            a[j + 1] = a[j];
            j--;
        }
        a[j + 1] = key;
    }
}
```

**Behavior of Example**
- Elements shift right until `key` fits sorted order.

### Quick Sort
**What**
- Partition-based divide-and-conquer sort.

**Why**
- Very fast average performance in practice.

**How**
- Pick pivot, partition smaller/greater, recurse.

**Logic/Concept**
- Average O(n log n), worst O(n^2) (bad pivots).

**Example**
```cpp
int partition(vector<int>& a, int l, int r) {
    int pivot = a[r], i = l - 1;
    for (int j = l; j < r; j++) {
        if (a[j] <= pivot) swap(a[++i], a[j]);
    }
    swap(a[i + 1], a[r]);
    return i + 1;
}
void quickSort(vector<int>& a, int l, int r) {
    if (l >= r) return;
    int p = partition(a, l, r);
    quickSort(a, l, p - 1);
    quickSort(a, p + 1, r);
}
```

**Behavior of Example**
- Pivot lands in final position each recursion.

### Selection Sort
**What**
- Repeatedly selects minimum and places at current index.

**Why**
- Simple and memory-efficient.

**How**
- For each position, scan remaining array for min.

**Logic/Concept**
- O(n^2), in-place, usually not stable.

**Example**
```cpp
void selectionSort(vector<int>& a) {
    int n = (int)a.size();
    for (int i = 0; i < n; i++) {
        int mn = i;
        for (int j = i + 1; j < n; j++)
            if (a[j] < a[mn]) mn = j;
        swap(a[i], a[mn]);
    }
}
```

**Behavior of Example**
- Array becomes sorted after repeated min placement.

### Heap Sort
**What**
- Sorts using heap data structure.

**Why**
- O(n log n) worst-case with O(1) extra space.

**How**
- Build max-heap, repeatedly extract max.

**Logic/Concept**
- Not stable, strong guaranteed bound.

**Example**
```cpp
void heapSort(vector<int>& a) {
    make_heap(a.begin(), a.end());
    sort_heap(a.begin(), a.end());
}
```

**Behavior of Example**
- STL heap utilities return sorted ascending sequence.

---

## 7. Search Algorithms

### Linear Search
**What**
- Check each element sequentially.

**Why**
- Works on unsorted data.

**How**
- Iterate and compare target.

**Logic/Concept**
- O(n) time, O(1) space.

**Example**
```cpp
int linearSearch(const vector<int>& a, int target) {
    for (int i = 0; i < (int)a.size(); i++)
        if (a[i] == target) return i;
    return -1;
}
```

**Behavior of Example**
- Returns index if found, else `-1`.

### Binary Search
**What**
- Search in sorted array by halving range.

**Why**
- Faster than linear search on sorted data.

**How**
- Compare with middle, eliminate half.

**Logic/Concept**
- O(log n) time, needs sorted input.

**Example**
```cpp
int binarySearch(const vector<int>& a, int target) {
    int l = 0, r = (int)a.size() - 1;
    while (l <= r) {
        int m = l + (r - l) / 2;
        if (a[m] == target) return m;
        if (a[m] < target) l = m + 1;
        else r = m - 1;
    }
    return -1;
}
```

**Behavior of Example**
- Finds target quickly by discarding half each step.

---

## 8. Tree Data Structures

### Binary Trees
**What**
- Tree where each node has up to two children.

**Why**
- Hierarchical representation and recursive algorithms.

**How**
- Node has value + left/right pointers.

**Logic/Concept**
- Many operations naturally recursive.

**Example**
```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int v) : val(v), left(nullptr), right(nullptr) {}
};
```

**Behavior of Example**
- Defines base tree node model.

### Binary Search Trees (BST)
**What**
- Ordered binary tree: left < root < right.

**Why**
- Efficient ordered search/insert/delete (average case).

**How**
- Traverse left or right based on comparison.

**Logic/Concept**
- Average O(log n), worst O(n) if skewed.

**Example**
```cpp
TreeNode* insertBST(TreeNode* root, int x) {
    if (!root) return new TreeNode(x);
    if (x < root->val) root->left = insertBST(root->left, x);
    else root->right = insertBST(root->right, x);
    return root;
}
```

**Behavior of Example**
- Inserts preserving BST ordering.

### AVL Trees
**What**
- Self-balancing BST with height difference constraint.

**Why**
- Keeps operations O(log n).

**How**
- Perform rotations on imbalance.

**Logic/Concept**
- Balance factor in {-1,0,1} after every update.

**Example**
```txt
AVL insertion may trigger LL/LR/RL/RR rotations.
```

**Behavior of Example**
- Prevents degenerate skewed tree growth.

### B-Trees
**What**
- Multi-way balanced search tree optimized for disk.

**Why**
- Fewer disk reads in databases/filesystems.

**How**
- Nodes hold multiple keys and children.

**Logic/Concept**
- High branching factor reduces tree height.

**Example**
```txt
Databases use B-Tree indexes for range queries.
```

**Behavior of Example**
- Efficient IO-aware key lookup.

### Tree Traversals

#### DFS (In-Order, Pre-Order, Post-Order)
**What**
- Depth-wise traversal patterns.

**Why**
- Different orders for different tasks.

**How**
- Recursive or stack-based traversal.

**Logic/Concept**
- Inorder on BST returns sorted sequence.

**Example**
```cpp
void inorder(TreeNode* r) {
    if (!r) return;
    inorder(r->left);
    cout << r->val << ' ';
    inorder(r->right);
}
void preorder(TreeNode* r) {
    if (!r) return;
    cout << r->val << ' ';
    preorder(r->left);
    preorder(r->right);
}
void postorder(TreeNode* r) {
    if (!r) return;
    postorder(r->left);
    postorder(r->right);
    cout << r->val << ' ';
}
```

**Behavior of Example**
- Prints nodes in chosen DFS order.

#### BFS (Level Order)
**What**
- Traverses level by level.

**Why**
- Shortest path in unweighted trees, level grouping.

**How**
- Queue-based traversal.

**Logic/Concept**
- Push children while popping current nodes.

**Example**
```cpp
void levelOrder(TreeNode* root) {
    if (!root) return;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        auto node = q.front(); q.pop();
        cout << node->val << ' ';
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

**Behavior of Example**
- Prints nodes top-to-bottom, left-to-right.

---

## 9. Graph Data Structures

### Directed Graph
**What**
- Edges have direction (`u -> v`).

**Why**
- Dependency graphs, workflows, routes with one-way constraints.

**How**
- Adjacency list: `vector<vector<int>>`.

**Logic/Concept**
- Reachability depends on edge direction.

**Example**
```cpp
int n = 4;
vector<vector<int>> g(n);
g[0].push_back(1); // 0 -> 1
```

**Behavior of Example**
- Edge usable only from 0 to 1.

### Undirected Graph
**What**
- Edges are bidirectional.

**Why**
- Social networks, roads (two-way), connectivity.

**How**
- Add both `u->v` and `v->u`.

**Logic/Concept**
- Connectivity is symmetric.

**Example**
```cpp
g[1].push_back(2);
g[2].push_back(1);
```

**Behavior of Example**
- Node 1 and 2 mutually reachable (if path exists).

### Graph BFS
**What**
- Level-wise traversal from source.

**Why**
- Shortest path in unweighted graph.

**How**
- Queue + visited array.

**Logic/Concept**
- First time reaching node gives shortest edge distance.

**Example**
```cpp
vector<int> bfs(int src, vector<vector<int>>& g) {
    vector<int> dist(g.size(), -1);
    queue<int> q;
    dist[src] = 0; q.push(src);
    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : g[u]) if (dist[v] == -1) {
            dist[v] = dist[u] + 1;
            q.push(v);
        }
    }
    return dist;
}
```

**Behavior of Example**
- Returns shortest unweighted distances from source.

### Graph DFS
**What**
- Depth-first traversal.

**Why**
- Connectivity, cycle detection, topological prep.

**How**
- Recursion or explicit stack.

**Logic/Concept**
- Goes deep before backtracking.

**Example**
```cpp
void dfs(int u, vector<vector<int>>& g, vector<int>& vis) {
    vis[u] = 1;
    for (int v : g[u]) if (!vis[v]) dfs(v, g, vis);
}
```

**Behavior of Example**
- Marks all nodes reachable from `u`.

### Shortest Path
**What**
- Minimum path cost between vertices.

**Why**
- Routing, map navigation, network optimization.

**How**
- Unweighted: BFS.
- Weighted non-negative: Dijkstra.
- Weighted with negatives: Bellman-Ford.

**Logic/Concept**
- Edge properties determine algorithm choice.

### Dijkstra's Algorithm
**What**
- Single-source shortest path for non-negative weights.

**Why**
- Efficient for large sparse graphs.

**How**
- Min-heap priority queue.

**Logic/Concept**
- Greedy choice: finalize smallest tentative distance first.

**Example**
```cpp
vector<long long> dijkstra(int n, vector<vector<pair<int,int>>>& g, int src) {
    const long long INF = 1e18;
    vector<long long> dist(n, INF);
    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> pq;
    dist[src] = 0; pq.push({0, src});
    while (!pq.empty()) {
        auto [du, u] = pq.top(); pq.pop();
        if (du != dist[u]) continue;
        for (auto [v, w] : g[u]) {
            if (dist[v] > du + w) {
                dist[v] = du + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

**Behavior of Example**
- Computes shortest distances when all weights are non-negative.

### Bellman-Ford Algorithm
**What**
- Shortest path with support for negative edges.

**Why**
- Detects negative cycles reachable from source.

**How**
- Relax all edges `n-1` times.

**Logic/Concept**
- One extra pass detects further relaxation => negative cycle.

**Example**
```cpp
struct Edge { int u, v, w; };

bool bellmanFord(int n, int src, const vector<Edge>& edges, vector<long long>& dist) {
    const long long INF = 1e18;
    dist.assign(n, INF);
    dist[src] = 0;
    for (int i = 1; i <= n - 1; i++) {
        bool changed = false;
        for (auto &e : edges) {
            if (dist[e.u] != INF && dist[e.v] > dist[e.u] + e.w) {
                dist[e.v] = dist[e.u] + e.w;
                changed = true;
            }
        }
        if (!changed) break;
    }
    for (auto &e : edges)
        if (dist[e.u] != INF && dist[e.v] > dist[e.u] + e.w) return false;
    return true;
}
```

**Behavior of Example**
- Returns `false` if negative cycle exists, else valid distances.

### Minimum Spanning Tree (MST)
**What**
- Minimum total edge weight connecting all vertices (undirected weighted graph).

**Why**
- Network design with minimal cost.

**How**
- Prim or Kruskal algorithms.

**Logic/Concept**
- Tree with `n-1` edges and no cycles.

### Prim's Algorithm
**What**
- Greedy MST growth from a starting node.

**Why**
- Good with adjacency list + priority queue.

**How**
- Expand cheapest edge to unvisited node.

**Logic/Concept**
- Similar to Dijkstra style heap processing.

**Example**
```txt
Use min-heap of (weight, node), add cheapest valid edge repeatedly.
```

**Behavior of Example**
- Builds MST incrementally.

### Kruskal's Algorithm
**What**
- Sort edges by weight and add if no cycle.

**Why**
- Simple and effective with Disjoint Set Union (DSU).

**How**
- Process edges smallest to largest.

**Logic/Concept**
- DSU prevents cycle formation.

**Example**
```txt
Sort edges, union endpoints if in different sets, add weight.
```

**Behavior of Example**
- Produces minimum spanning tree if graph is connected.

---

## 10. Advanced Data Structures

### Trie
**What**
- Prefix tree for strings.

**Why**
- Fast prefix operations (search/autocomplete).

**How**
- Node has child links per character.

**Logic/Concept**
- Query complexity proportional to word length, not number of words.

**Example**
```cpp
struct TrieNode {
    TrieNode* ch[26]{};
    bool end = false;
};
```

**Behavior of Example**
- Base node model for dictionary prefix operations.

### Segment Trees
**What**
- Tree for range queries + updates.

**Why**
- Efficient on mutable arrays.

**How**
- Build tree of segments, query/update in O(log n).

**Logic/Concept**
- Divide interval recursively into halves.

**Example**
```txt
Supports operations like range sum/min/max efficiently.
```

**Behavior of Example**
- Huge speedup over O(n) range recomputation.

### Fenwick Trees (BIT)
**What**
- Binary Indexed Tree for prefix sums.

**Why**
- Simpler than segment tree for sum-like operations.

**How**
- Update and prefix query in O(log n).

**Logic/Concept**
- Uses binary lowbit jumps (`i += i & -i`).

**Example**
```cpp
struct BIT {
    int n; vector<long long> bit;
    BIT(int n): n(n), bit(n+1,0) {}
    void add(int i, long long v){ for(; i<=n; i+=i&-i) bit[i]+=v; }
    long long sum(int i){ long long s=0; for(; i>0; i-=i&-i) s+=bit[i]; return s; }
};
```

**Behavior of Example**
- Supports point update and prefix query quickly.

### Disjoint Set (Union-Find)
**What**
- Maintains connected components.

**Why**
- Fast union/find in graph problems.

**How**
- Parent array + path compression + union by rank/size.

**Logic/Concept**
- Nearly constant amortized time.

**Example**
```cpp
struct DSU {
    vector<int> p, sz;
    DSU(int n): p(n), sz(n,1) { iota(p.begin(), p.end(), 0); }
    int find(int x){ return p[x]==x?x:p[x]=find(p[x]); }
    bool unite(int a,int b){
        a=find(a); b=find(b);
        if(a==b) return false;
        if(sz[a]<sz[b]) swap(a,b);
        p[b]=a; sz[a]+=sz[b];
        return true;
    }
};
```

**Behavior of Example**
- Quickly checks/merges components and avoids cycles in Kruskal.

### Suffix Trees and Arrays
**What**
- Structures for fast substring queries.

**Why**
- String-heavy problems (pattern matching, repeated substrings).

**How**
- Suffix Array stores sorted suffix indices.

**Logic/Concept**
- Preprocessing enables fast search.

**Example**
```txt
Suffix Array of "banana" enables binary search on suffixes for pattern lookup.
```

**Behavior of Example**
- Pattern search improves beyond naive O(n*m).

---

## 11. Complex Data Structures

### B / B+ Trees
**What**
- Balanced multi-way search trees for storage systems.

**Why**
- Efficient disk/block access.

**How**
- Internal nodes store separators; B+ keeps full records in leaves.

**Logic/Concept**
- High branching factor gives small height.

**Example**
```txt
Databases commonly use B+ tree indexes for range scans.
```

**Behavior of Example**
- Fast ordered retrieval over large disk-backed datasets.

### Skip List
**What**
- Layered linked list with probabilistic balancing.

**Why**
- Alternative to balanced trees with simpler implementation.

**How**
- Randomly promote nodes to upper levels.

**Logic/Concept**
- Expected O(log n) search/insert/delete.

**Example**
```txt
Search starts from top layer and drops down while moving right.
```

**Behavior of Example**
- Efficient average lookup with randomized structure.

### ISAM
**What**
- Indexed Sequential Access Method.

**Why**
- Combines sequential file organization with indexed lookup.

**How**
- Static index blocks point to data blocks.

**Logic/Concept**
- Overflow chains can appear after many inserts.

**Example**
```txt
Legacy database/file systems use ISAM for indexed record access.
```

**Behavior of Example**
- Fast reads, but updates can degrade due to overflow areas.

### 2-3 Trees
**What**
- Balanced search tree with 2-node or 3-node variants.

**Why**
- Guarantees logarithmic operations.

**How**
- Split and promote on insert overflow.

**Logic/Concept**
- All leaves at same depth.

**Example**
```txt
Insertion may split a 3-node and push median key upward.
```

**Behavior of Example**
- Tree remains balanced after operations.

---

## 12. Indexing

### Linear Indexing
**What**
- Simple sequential indexing/access patterns.

**Why**
- Easy to implement for small/simple datasets.

**How**
- Maintain sorted list or direct array offsets.

**Logic/Concept**
- Good for small data, poor scaling for large frequent inserts.

**Example**
```txt
Store records in vector and scan linearly for key.
```

**Behavior of Example**
- O(n) search cost.

### Tree-Based Indexing
**What**
- Hierarchical index (BST, AVL, B-Tree, B+ Tree).

**Why**
- Faster lookup and range queries.

**How**
- Organize keys in balanced tree structure.

**Logic/Concept**
- O(log n) search in balanced cases.

**Example**
```txt
B+ tree index on database column accelerates WHERE and range queries.
```

**Behavior of Example**
- Query latency drops significantly compared to full scans.

---

## 13. Problem Solving Techniques

### Brute Force
**What**
- Try all possibilities.

**Why**
- Baseline approach, simple to validate correctness.

**How**
- Enumerate all candidates.

**Logic/Concept**
- Often too slow, but useful starting point.

**Example**
```txt
Check every subarray to find max sum.
```

**Behavior of Example**
- Correct but typically O(n^2) or worse.

### Backtracking
**What**
- DFS + undo choices.

**Why**
- Solves combinatorial constraint problems.

**How**
- Choose, recurse, unchoose.

**Logic/Concept**
- Pruning cuts impossible branches.

**Example**
```cpp
void gen(int n, string& cur) {
    if ((int)cur.size() == n) { cout << cur << '\n'; return; }
    cur.push_back('0'); gen(n, cur); cur.pop_back();
    cur.push_back('1'); gen(n, cur); cur.pop_back();
}
```

**Behavior of Example**
- Generates all binary strings of length `n`.

### Greedy Algorithms
**What**
- Make locally optimal choice each step.

**Why**
- Often simpler and faster when greedy-choice property holds.

**How**
- Prove local choices lead to global optimum.

**Logic/Concept**
- Works only for specific problem classes.

**Example**
```txt
Activity selection by earliest finish time.
```

**Behavior of Example**
- Maximizes number of non-overlapping activities.

### Randomised Algorithms
**What**
- Use random choices during execution.

**Why**
- Helps avoid worst-case input patterns.

**How**
- Random pivot in quicksort, randomized hashing.

**Logic/Concept**
- Analyze expected complexity, not only worst case.

**Example**
```txt
Random pivot quicksort gives expected O(n log n).
```

**Behavior of Example**
- More stable performance distribution.

### Divide and Conquer
**What**
- Split problem into smaller subproblems, solve, combine.

**Why**
- Enables powerful algorithms like merge sort.

**How**
- Recurrence relation modeling.

**Logic/Concept**
- Balanced splitting often yields log-depth recursion.

**Example**
```txt
Merge sort: divide array in half, sort halves, merge.
```

**Behavior of Example**
- O(n log n) stable sorting.

### Recursion
**What**
- Function calls itself on smaller input.

**Why**
- Natural for tree/divide-and-conquer problems.

**How**
- Define base case + recursive transition.

**Logic/Concept**
- Missing base case leads to stack overflow.

**Example**
```cpp
int fact(int n) { return (n <= 1) ? 1 : n * fact(n - 1); }
```

**Behavior of Example**
- Computes factorial by recursive multiplication.

### Dynamic Programming
**What**
- Store overlapping subproblem answers.

**Why**
- Converts exponential recursion to polynomial time.

**How**
- Top-down memoization or bottom-up tabulation.

**Logic/Concept**
- Requires optimal substructure + overlapping subproblems.

**Example**
```cpp
int fib(int n) {
    vector<int> dp(max(2, n + 1));
    dp[0] = 0; dp[1] = 1;
    for (int i = 2; i <= n; i++) dp[i] = dp[i - 1] + dp[i - 2];
    return dp[n];
}
```

**Behavior of Example**
- Computes `fib(n)` in O(n) time instead of exponential recursion.

### Two Pointer Technique
**What**
- Use two indices moving under rules.

**Why**
- Efficient linear-time solutions on sorted arrays/strings.

**How**
- Start at both ends or moving window boundaries.

**Logic/Concept**
- Pointers encode constraints and reduce nested loops.

**Example**
```cpp
bool hasPairSum(vector<int>& a, int target) {
    sort(a.begin(), a.end());
    int l = 0, r = (int)a.size() - 1;
    while (l < r) {
        int s = a[l] + a[r];
        if (s == target) return true;
        if (s < target) l++;
        else r--;
    }
    return false;
}
```

**Behavior of Example**
- Finds pair sum in O(n log n) including sort, O(n) scan phase.

### Sliding Window Technique
**What**
- Maintain moving contiguous range over array/string.

**Why**
- Efficient subarray/substring problems.

**How**
- Expand right, shrink left while maintaining condition.

**Logic/Concept**
- Reuses previous computation instead of restarting each range.

**Example**
```cpp
int maxSumK(const vector<int>& a, int k) {
    int n = (int)a.size();
    if (k > n) return 0;
    int cur = 0;
    for (int i = 0; i < k; i++) cur += a[i];
    int best = cur;
    for (int i = k; i < n; i++) {
        cur += a[i] - a[i - k];
        best = max(best, cur);
    }
    return best;
}
```

**Behavior of Example**
- Computes max sum subarray of fixed length `k` in O(n).

---

## 14. Patterns and Special Techniques

### Fast and Slow Pointers
**What**
- Two pointers moving at different speeds.

**Why**
- Detect cycles, find middle node.

**How**
- Slow moves 1 step, fast moves 2 steps.

**Logic/Concept**
- If cycle exists, fast eventually meets slow.

**Example**
```cpp
bool hasCycle(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }
    return false;
}
```

**Behavior of Example**
- Returns true for cyclic linked list.

### Cyclic Sort
**What**
- Place numbers at correct indices for range-based arrays.

**Why**
- Finds missing/duplicate efficiently.

**How**
- Swap current element to its target index repeatedly.

**Logic/Concept**
- Works when values are in known range like `1..n` or `0..n`.

**Example**
```cpp
void cyclicSort(vector<int>& a) {
    int i = 0;
    while (i < (int)a.size()) {
        int correct = a[i] - 1;
        if (a[i] >= 1 && a[i] <= (int)a.size() && a[i] != a[correct])
            swap(a[i], a[correct]);
        else
            i++;
    }
}
```

**Behavior of Example**
- Rearranges many range-constrained arrays close to index-correct form.

### Merge Intervals
**What**
- Merge overlapping ranges.

**Why**
- Scheduling/calendar/range normalization tasks.

**How**
- Sort by start and merge greedily.

**Logic/Concept**
- Overlap exists when next.start <= current.end.

**Example**
```cpp
vector<pair<int,int>> mergeIntervals(vector<pair<int,int>> v) {
    if (v.empty()) return {};
    sort(v.begin(), v.end());
    vector<pair<int,int>> ans;
    ans.push_back(v[0]);
    for (int i = 1; i < (int)v.size(); i++) {
        auto &last = ans.back();
        if (v[i].first <= last.second) last.second = max(last.second, v[i].second);
        else ans.push_back(v[i]);
    }
    return ans;
}
```

**Behavior of Example**
- Outputs non-overlapping merged intervals.

### Two Heaps
**What**
- Use max-heap + min-heap together.

**Why**
- Median in stream and balanced partition problems.

**How**
- Keep sizes balanced and ordering invariant.

**Logic/Concept**
- Left heap holds smaller half, right heap larger half.

**Example**
```cpp
priority_queue<int> leftMax;
priority_queue<int, vector<int>, greater<int>> rightMin;
```

**Behavior of Example**
- Enables O(log n) insert and O(1) median query (with extra logic).

### Kth Element (Heap pattern)
**What**
- Find kth smallest/largest efficiently.

**Why**
- Avoid full sorting when only one rank needed.

**How**
- Use heap of size `k`.

**Logic/Concept**
- Maintain only most relevant elements.

**Example**
```cpp
int kthLargest(vector<int>& a, int k) {
    priority_queue<int, vector<int>, greater<int>> pq;
    for (int x : a) {
        pq.push(x);
        if ((int)pq.size() > k) pq.pop();
    }
    return pq.top();
}
```

**Behavior of Example**
- Returns kth largest in O(n log k).

### Multi-threaded Problems
**What**
- Problems involving parallel execution and synchronization.

**Why**
- Real systems need throughput + responsiveness.

**How**
- Use mutex, condition_variable, atomic, thread-safe queues.

**Logic/Concept**
- Correctness first: avoid race conditions and deadlocks.

**Example**
```cpp
#include <thread>
#include <mutex>

int counter = 0;
mutex m;

void work() {
    for (int i = 0; i < 1000; i++) {
        lock_guard<mutex> lock(m);
        counter++;
    }
}
```

**Behavior of Example**
- Correct synchronized increment from multiple threads.

### Island Traversal
**What**
- Grid DFS/BFS to count connected components.

**Why**
- Common matrix graph interview pattern.

**How**
- Visit land cell, flood-fill neighbors.

**Logic/Concept**
- Mark visited to avoid reprocessing.

**Example**
```cpp
int dr[4] = {-1, 1, 0, 0};
int dc[4] = {0, 0, -1, 1};

void dfsGrid(int r, int c, vector<string>& g) {
    int n = g.size(), m = g[0].size();
    g[r][c] = '0';
    for (int k = 0; k < 4; k++) {
        int nr = r + dr[k], nc = c + dc[k];
        if (nr >= 0 && nr < n && nc >= 0 && nc < m && g[nr][nc] == '1')
            dfsGrid(nr, nc, g);
    }
}
```

**Behavior of Example**
- Converts one connected island to water during traversal.

### Heap (Pattern)
**What**
- Priority-based retrieval structure.

**Why**
- Efficient min/max extraction and scheduling.

**How**
- Use `priority_queue`.

**Logic/Concept**
- Top element always highest/lowest priority.

**Example**
```cpp
priority_queue<int> maxh;
maxh.push(3); maxh.push(10); maxh.push(1);
cout << maxh.top() << '\n';
```

**Behavior of Example**
- Prints `10` (maximum at top).

### A* Algorithm
**What**
- Heuristic shortest path search.

**Why**
- Faster pathfinding than Dijkstra in many map/grid contexts.

**How**
- Priority by `f(n)=g(n)+h(n)`.

**Logic/Concept**
- `h(n)` is heuristic estimate to goal; admissible heuristic preserves optimality.

**Example**
```txt
On grid pathfinding, use Manhattan distance as heuristic.
```

**Behavior of Example**
- Explores promising nodes first, often reducing search space.

---

## How to Use This File Effectively
1. Read one section and immediately code 3 problems on that topic.
2. For each solution, write time and space complexity.
3. Re-solve same problem after 3 days without seeing code.
4. Build your own pattern sheet from mistakes.

---

## Suggested C++ DSA Practice Workflow
1. Fundamentals + arrays/strings
2. Hashing + two pointers + sliding window
3. Stack/queue + linked list
4. Trees + BST + recursion
5. Graph BFS/DFS + shortest paths + DSU
6. Heaps + greedy
7. DP and advanced structures