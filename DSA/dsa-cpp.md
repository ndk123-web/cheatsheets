# DSA in C++ Cheatsheet

Comprehensive, interview-focused Data Structures and Algorithms reference in Modern C++ covering essential algorithmic patterns, complexity bounds, STL templates, graph algorithms, dynamic programming, and high-frequency problem structures.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 Complexity Analysis and Problem Constraints](#1-complexity-analysis-and-problem-constraints)
- [2 C++ STL Essentials for DSA](#2-c-stl-essentials-for-dsa)
- [3 Prefix Sums and Difference Arrays](#3-prefix-sums-and-difference-arrays)
- [4 Two Pointers Techniques](#4-two-pointers-techniques)
- [5 Sliding Window (Fixed and Dynamic)](#5-sliding-window-fixed-and-dynamic)
- [6 Binary Search and Binary Search on Answer](#6-binary-search-and-binary-search-on-answer)
- [7 Monotonic Stack and Monotonic Queue](#7-monotonic-stack-and-monotonic-queue)
- [8 Linked Lists and LRU Cache](#8-linked-lists-and-lru-cache)
- [9 Binary Trees, BST, and Traversals](#9-binary-trees-bst-and-traversals)
- [10 Heaps, Priority Queues, and Top-K Patterns](#10-heaps-priority-queues-and-top-k-patterns)
- [11 Recursion and Backtracking](#11-recursion-and-backtracking)
- [12 Graph Fundamentals (BFS, DFS, Topological Sort)](#12-graph-fundamentals-bfs-dfs-topological-sort)
- [13 Shortest Path Algorithms (Dijkstra, Bellman-Ford, Floyd-Warshall, 0-1 BFS)](#13-shortest-path-algorithms-dijkstra-bellman-ford-floyd-warshall-0-1-bfs)
- [14 Disjoint Set Union (DSU / Union-Find) and Kruskal's MST](#14-disjoint-set-union-dsu--union-find-and-kruskals-mst)
- [15 Dynamic Programming (1D, 2D, Knapsack, LIS, LCS)](#15-dynamic-programming-1d-2d-knapsack-lis-lcs)
- [16 Greedy Algorithms and Interval Scheduling](#16-greedy-algorithms-and-interval-scheduling)
- [17 Trie (Prefix Tree)](#17-trie-prefix-tree)
- [18 Bit Manipulation and Bitmasking](#18-bit-manipulation-and-bitmasking)
- [19 High-Yield Interview Formulas and Reality Check](#19-high-yield-interview-formulas-and-reality-check)

---

## High Priority Topics

Most frequently tested patterns in technical interviews:
1. **Sliding Window (Variable Size & Minimum Window)**
2. **Binary Search on Answer Space (Monotonic Predicates)**
3. **Monotonic Stack (Next Greater Element & Histogram Area)**
4. **Fast & Slow Pointers (Floyd's Cycle Detection & Linked List Middle)**
5. **Lowest Common Ancestor (LCA) & Tree Path Traversals**
6. **Top K Elements using Heaps / Two Heaps for Running Median**
7. **Graph Topological Sort (Kahn's Algorithm & DFS Cycle Check)**
8. **Dijkstra's Shortest Path with `std::priority_queue`**
9. **Union-Find (DSU) with Path Compression & Rank**
10. **Dynamic Programming (0/1 Knapsack, LIS $O(N \log N)$, Edit Distance)**

---

## 1 Complexity Analysis and Problem Constraints

### Input Size vs Expected Time Complexity Guide
When solving interview problems, the constraints in the problem description tell you what algorithm is expected:

| Input Size ($N$) | Expected Time Complexity | Typical Algorithms / Approaches |
| :--- | :--- | :--- |
| $N \le 10$ | $O(N!)$ or $O(N^2 \cdot 2^N)$ | Permutations, Travelling Salesperson (TSP) |
| $N \le 20$ | $O(2^N)$ | Backtracking, Subsets, Bitmask DP |
| $N \le 100$ | $O(N^4)$ or $O(N^3)$ | Floyd-Warshall, Matrix Multiplications, 3D DP |
| $N \le 1,000$ | $O(N^2)$ | 2D DP, Nested Loops, All pairs comparison |
| $N \le 10^5$ | $O(N \log N)$ or $O(N)$ | Sorting, Binary Search, Heaps, Trees, Divide & Conquer |
| $N \le 10^6$ | $O(N)$ | Two Pointers, Sliding Window, Prefix Sums, Monotonic Stack |
| $N \ge 10^9$ | $O(\log N)$ or $O(1)$ | Binary Search, Math formulas, Fast Exponentiation |

> **Rule of Thumb**: A modern CPU executes $\approx 10^8$ operations per second. If $N = 10^5$, an $O(N^2) = 10^{10}$ solution will get a **Time Limit Exceeded (TLE)** error.

---

## 2 C++ STL Essentials for DSA

### Fast I/O Template
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    // Unties cin from cout and disables sync with C stdio for fast I/O
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(NULL);
    return 0;
}
```

### Essential STL Containers for DSA
```cpp
#include <vector>
#include <deque>
#include <unordered_map>
#include <map>
#include <unordered_set>
#include <set>
#include <queue>
#include <stack>

// Dynamic Array
std::vector<int> v = {1, 2, 3};

// Double-ended Queue (O(1) push/pop both ends)
std::deque<int> dq;

// Hash Map (O(1) average lookup) vs Ordered Map (Red-Black Tree, O(log N) lookup)
std::unordered_map<int, int> freq;
std::map<int, int> orderedMap; // Keys always sorted

// Max-Heap (Default) vs Min-Heap
std::priority_queue<int> maxHeap;
std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;

// Pair and Custom Comparator for Priority Queue
using pii = std::pair<int, int>; // {distance, node}
std::priority_queue<pii, std::vector<pii>, std::greater<pii>> pq;
```

---

## 3 Prefix Sums and Difference Arrays

### 1D Prefix Sum ($O(1)$ Range Sum Queries)
```cpp
#include <vector>

class PrefixSum {
    std::vector<long long> prefix;
public:
    PrefixSum(const std::vector<int>& nums) {
        int n = nums.size();
        prefix.assign(n + 1, 0);
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }

    // Query sum in range [L, R] (0-indexed inclusive) in O(1)
    long long query(int L, int R) {
        return prefix[R + 1] - prefix[L];
    }
};
```

### Difference Array ($O(1)$ Range Updates)
Apply $+val$ to range $[L, R]$ in $O(1)$ time, then reconstruct in $O(N)$.

```cpp
#include <vector>

class DifferenceArray {
    std::vector<int> diff;
    int n;
public:
    DifferenceArray(int size) : n(size), diff(size + 1, 0) {}

    // Add 'val' to all elements in range [L, R] (inclusive)
    void update(int L, int R, int val) {
        diff[L] += val;
        diff[R + 1] -= val;
    }

    // Reconstruct final array in O(N)
    std::vector<int> build() {
        std::vector<int> res(n);
        int current = 0;
        for (int i = 0; i < n; i++) {
            current += diff[i];
            res[i] = current;
        }
        return res;
    }
};
```

---

## 4 Two Pointers Techniques

### 1. Opposite Direction Pointers (Sorted Two Sum / Palindrome)
```cpp
#include <vector>

bool twoSumSorted(const std::vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) return true;
        if (sum < target) left++;
        else right--;
    }
    return false;
}
```

### 2. Fast & Slow Pointers (Floyd's Cycle Detection)
```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

bool hasCycle(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true; // Cycle detected
    }
    return false;
}
```

---

## 5 Sliding Window (Fixed and Dynamic)

### 1. Fixed Window Size $K$ (Maximum Subarray Sum of Length $K$)
```cpp
#include <vector>
#include <algorithm>

int maxSumSubarray(const std::vector<int>& nums, int k) {
    int n = nums.size();
    if (n < k) return 0;

    int windowSum = 0;
    for (int i = 0; i < k; i++) windowSum += nums[i];

    int maxSum = windowSum;
    for (int i = k; i < n; i++) {
        windowSum += nums[i] - nums[i - k]; // Slide window
        maxSum = std::max(maxSum, windowSum);
    }
    return maxSum;
}
```

### 2. Dynamic Window (Longest Substring Without Repeating Characters)
```cpp
#include <string>
#include <vector>
#include <algorithm>

int lengthOfLongestSubstring(const std::string& s) {
    std::vector<int> lastIndex(128, -1);
    int maxLength = 0;
    int left = 0;

    for (int right = 0; right < (int)s.size(); right++) {
        char c = s[right];
        if (lastIndex[c] >= left) {
            left = lastIndex[c] + 1; // Shrink window past duplicate
        }
        lastIndex[c] = right;
        maxLength = std::max(maxLength, right - left + 1);
    }
    return maxLength;
}
```

---

## 6 Binary Search and Binary Search on Answer

### Standard Binary Search Template (Lower & Upper Bound)
```cpp
#include <vector>

int binarySearch(const std::vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2; // Prevents integer overflow
        if (nums[mid] == target) return mid;
        if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1; // Not found
}
```

### Binary Search on Answer Space (Capacity / Predicate)
```cpp
#include <vector>
#include <numeric>
#include <algorithm>

// Example: Minimum Capacity to Ship Packages Within D Days
bool canShip(const std::vector<int>& weights, int days, int capacity) {
    int requiredDays = 1, currentLoad = 0;
    for (int w : weights) {
        if (currentLoad + w > capacity) {
            requiredDays++;
            currentLoad = 0;
        }
        currentLoad += w;
    }
    return requiredDays <= days;
}

int shipWithinDays(std::vector<int>& weights, int days) {
    int low = *std::max_element(weights.begin(), weights.end());
    int high = std::accumulate(weights.begin(), weights.end(), 0);
    int ans = high;

    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (canShip(weights, days, mid)) {
            ans = mid;
            high = mid - 1; // Try smaller capacity
        } else {
            low = mid + 1;  // Must increase capacity
        }
    }
    return ans;
}
```

---

## 7 Monotonic Stack and Monotonic Queue

### Next Greater Element ($O(N)$ with Monotonic Stack)
```cpp
#include <vector>
#include <stack>

std::vector<int> nextGreaterElements(const std::vector<int>& nums) {
    int n = nums.size();
    std::vector<int> result(n, -1);
    std::stack<int> st; // Stores indices

    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] < nums[i]) {
            result[st.top()] = nums[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```

### Sliding Window Maximum ($O(N)$ with Monotonic Deque)
```cpp
#include <vector>
#include <deque>

std::vector<int> maxSlidingWindow(const std::vector<int>& nums, int k) {
    std::deque<int> dq; // Decreasing deque of indices
    std::vector<int> result;

    for (int i = 0; i < (int)nums.size(); i++) {
        // Remove elements out of current window
        if (!dq.empty() && dq.front() <= i - k) dq.pop_front();

        // Maintain decreasing order in deque
        while (!dq.empty() && nums[dq.back()] <= nums[i]) dq.pop_back();

        dq.push_back(i);

        // Add max to result once window reaches size k
        if (i >= k - 1) result.push_back(nums[dq.front()]);
    }
    return result;
}
```

---

## 8 Linked Lists and LRU Cache

### Reverse Linked List (Iterative & In-Place)
```cpp
ListNode* reverseList(ListNode* head) {
    ListNode *prev = nullptr, *curr = head;
    while (curr) {
        ListNode* nextTemp = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextTemp;
    }
    return prev;
}
```

### LRU Cache Implementation ($O(1)$ Get and Put)
```cpp
#include <unordered_map>
#include <list>

class LRUCache {
    int capacity;
    std::list<std::pair<int, int>> dll; // {key, value} (Most recent at front)
    std::unordered_map<int, std::list<std::pair<int, int>>::iterator> cacheMap;

public:
    LRUCache(int cap) : capacity(cap) {}

    int get(int key) {
        auto it = cacheMap.find(key);
        if (it == cacheMap.end()) return -1;
        dll.splice(dll.begin(), dll, it->second); // Move accessed node to front in O(1)
        return it->second->second;
    }

    void put(int key, int value) {
        auto it = cacheMap.find(key);
        if (it != cacheMap.end()) {
            it->second->second = value;
            dll.splice(dll.begin(), dll, it->second);
            return;
        }
        if ((int)dll.size() == capacity) {
            int evictKey = dll.back().first;
            dll.pop_back();
            cacheMap.erase(evictKey);
        }
        dll.emplace_front(key, value);
        cacheMap[key] = dll.begin();
    }
};
```

---

## 9 Binary Trees, BST, and Traversals

### Core Binary Tree Definitions & Traversals
```cpp
#include <vector>
#include <queue>
#include <algorithm>

struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// 1. Level Order Traversal (BFS)
std::vector<std::vector<int>> levelOrder(TreeNode* root) {
    std::vector<std::vector<int>> result;
    if (!root) return result;

    std::queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int levelSize = q.size();
        std::vector<int> currentLevel;
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front(); q.pop();
            currentLevel.push_back(node->val);
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(currentLevel);
    }
    return result;
}

// 2. Lowest Common Ancestor (LCA) in Binary Tree
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root || root == p || root == q) return root;
    TreeNode* left = lowestCommonAncestor(root->left, p, q);
    TreeNode* right = lowestCommonAncestor(root->right, p, q);
    if (left && right) return root; // p and q are on separate subtrees
    return left ? left : right;
}
```

---

## 10 Heaps, Priority Queues, and Top-K Patterns

### Top $K$ Frequent Elements ($O(N \log K)$)
```cpp
#include <vector>
#include <unordered_map>
#include <queue>

std::vector<int> topKFrequent(const std::vector<int>& nums, int k) {
    std::unordered_map<int, int> counts;
    for (int n : nums) counts[n]++;

    // Min-heap storing pair: {frequency, num}
    using pii = std::pair<int, int>;
    std::priority_queue<pii, std::vector<pii>, std::greater<pii>> minHeap;

    for (const auto& [num, freq] : counts) {
        minHeap.emplace(freq, num);
        if ((int)minHeap.size() > k) minHeap.pop(); // Evict smallest frequency
    }

    std::vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().second);
        minHeap.pop();
    }
    return result;
}
```

---

## 11 Recursion and Backtracking

### Subsets Generator Template ($O(2^N)$)
```cpp
#include <vector>

void backtrackSubsets(int start, const std::vector<int>& nums, std::vector<int>& current, std::vector<std::vector<int>>& result) {
    result.push_back(current);

    for (int i = start; i < (int)nums.size(); i++) {
        current.push_back(nums[i]); // 1. Choose
        backtrackSubsets(i + 1, nums, current, result); // 2. Explore
        current.pop_back(); // 3. Un-choose (Backtrack)
    }
}

std::vector<std::vector<int>> subsets(const std::vector<int>& nums) {
    std::vector<std::vector<int>> result;
    std::vector<int> current;
    backtrackSubsets(0, nums, current, result);
    return result;
}
```

---

## 12 Graph Fundamentals (BFS, DFS, Topological Sort)

### Topological Sort using Kahn's Algorithm (BFS In-Degree)
```cpp
#include <vector>
#include <queue>

std::vector<int> topologicalSort(int numCourses, const std::vector<std::pair<int, int>>& edges) {
    std::vector<std::vector<int>> adj(numCourses);
    std::vector<int> inDegree(numCourses, 0);

    for (const auto& [u, v] : edges) {
        adj[u].push_back(v);
        inDegree[v]++;
    }

    std::queue<int> q;
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) q.push(i);
    }

    std::vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);

        for (int v : adj[u]) {
            if (--inDegree[v] == 0) q.push(v);
        }
    }

    if ((int)order.size() == numCourses) return order;
    return {}; // Cycle detected!
}
```

---

## 13 Shortest Path Algorithms

### Dijkstra's Single Source Shortest Path ($O((V + E) \log V)$)
```cpp
#include <vector>
#include <queue>

const int INF = 1e9;

std::vector<int> dijkstra(int n, int src, const std::vector<std::vector<std::pair<int, int>>>& adj) {
    std::vector<int> dist(n, INF);
    dist[src] = 0;

    // Min-heap storing {distance, node}
    using pii = std::pair<int, int>;
    std::priority_queue<pii, std::vector<pii>, std::greater<pii>> pq;
    pq.emplace(0, src);

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue; // Stale distance entry

        for (const auto& [v, weight] : adj[u]) {
            if (dist[u] + weight < dist[v]) {
                dist[v] = dist[u] + weight;
                pq.emplace(dist[v], v);
            }
        }
    }
    return dist;
}
```

---

## 14 Disjoint Set Union (DSU / Union-Find) and Kruskal's MST

```cpp
#include <vector>
#include <numeric>
#include <algorithm>

class DSU {
    std::vector<int> parent, rank;
public:
    DSU(int n) : parent(n), rank(n, 0) {
        std::iota(parent.begin(), parent.end(), 0);
    }

    // Path Compression: O(alpha(N))
    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    // Union by Rank
    bool unite(int i, int j) {
        int rootI = find(i), rootJ = find(j);
        if (rootI == rootJ) return false; // Already connected

        if (rank[rootI] < rank[rootJ]) parent[rootI] = rootJ;
        else if (rank[rootI] > rank[rootJ]) parent[rootJ] = rootI;
        else {
            parent[rootJ] = rootI;
            rank[rootI]++;
        }
        return true;
    }
};
```

---

## 15 Dynamic Programming (1D, 2D, Knapsack, LIS, LCS)

### 1. 0/1 Knapsack Problem ($O(N \cdot W)$ Space-Optimized)
```cpp
#include <vector>
#include <algorithm>

int knapsack01(int W, const std::vector<int>& weights, const std::vector<int>& values) {
    int n = weights.size();
    std::vector<int> dp(W + 1, 0);

    for (int i = 0; i < n; i++) {
        for (int w = W; w >= weights[i]; w--) { // Reverse loop for 0/1 Knapsack
            dp[w] = std::max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    return dp[W];
}
```

### 2. Longest Increasing Subsequence ($O(N \log N)$ with Binary Search)
```cpp
#include <vector>
#include <algorithm>

int lengthOfLIS(const std::vector<int>& nums) {
    std::vector<int> tails;
    for (int x : nums) {
        auto it = std::lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end()) {
            tails.push_back(x);
        } else {
            *it = x; // Replace element to maintain smallest possible tail
        }
    }
    return tails.size();
}
```

### 3. Longest Common Subsequence (LCS)
```cpp
#include <string>
#include <vector>
#include <algorithm>

int longestCommonSubsequence(const std::string& text1, const std::string& text2) {
    int m = text1.size(), n = text2.size();
    std::vector<std::vector<int>> dp(m + 1, std::vector<int>(n + 1, 0));

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            } else {
                dp[i][j] = std::max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp[m][n];
}
```

---

## 16 Greedy Algorithms and Interval Scheduling

### Merge Intervals ($O(N \log N)$)
```cpp
#include <vector>
#include <algorithm>

std::vector<std::vector<int>> mergeIntervals(std::vector<std::vector<int>>& intervals) {
    if (intervals.empty()) return {};

    // Sort intervals by start time
    std::sort(intervals.begin(), intervals.end());

    std::vector<std::vector<int>> merged;
    merged.push_back(intervals[0]);

    for (size_t i = 1; i < intervals.size(); i++) {
        if (intervals[i][0] <= merged.back()[1]) {
            // Overlapping intervals: expand current interval end
            merged.back()[1] = std::max(merged.back()[1], intervals[i][1]);
        } else {
            merged.push_back(intervals[i]);
        }
    }
    return merged;
}
```

---

## 17 Trie (Prefix Tree)

```cpp
#include <string>
#include <vector>

class Trie {
    struct TrieNode {
        TrieNode* children[26] = {nullptr};
        bool isEndOfWord = false;
    };
    TrieNode* root;

public:
    Trie() : root(new TrieNode()) {}

    void insert(const std::string& word) {
        TrieNode* curr = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!curr->children[idx]) curr->children[idx] = new TrieNode();
            curr = curr->children[idx];
        }
        curr->isEndOfWord = true;
    }

    bool search(const std::string& word) {
        TrieNode* curr = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!curr->children[idx]) return false;
            curr = curr->children[idx];
        }
        return curr->isEndOfWord;
    }

    bool startsWith(const std::string& prefix) {
        TrieNode* curr = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!curr->children[idx]) return false;
            curr = curr->children[idx];
        }
        return true;
    }
};
```

---

## 18 Bit Manipulation and Bitmasking

### Essential Bitwise Hacks for DSA
```cpp
// 1. Check if integer is a power of 2
bool isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}

// 2. Count number of set bits (1s)
int countSetBits(unsigned int n) {
    return __builtin_popcount(n); // Built-in compiler intrinsic in GCC/Clang
}

// 3. Isolate the lowest set bit
int lowestSetBit(int n) {
    return n & (-n);
}

// 4. Iterate over all subsets of length N (Bitmask Enumeration)
void generateSubsetsBitmask(int n) {
    for (int mask = 0; mask < (1 << n); mask++) {
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) {
                // Element i is included in this subset
            }
        }
    }
}
```

---

## 19 High-Yield Interview Formulas and Reality Check

### Critical Formulas & Constants
- **Middle of range without overflow**: `mid = low + (high - low) / 2;`
- **Sum of first $N$ natural numbers**: $\frac{N(N + 1)}{2}$
- **Number of Subarrays in array of size $N$**: $\frac{N(N + 1)}{2}$
- **Number of Subsequences in array of size $N$**: $2^N$
- **Number of Permutations of array of size $N$**: $N!$

---

### Reality Check & Best Practices
- Always check bounds when calculating `mid` or range sums to avoid 32-bit signed integer overflow; cast to `long long`.
- Prefer `std::vector` over raw dynamic arrays (`new[]`) to avoid memory leaks.
- In sliding window problems with frequency maps, avoid scanning the whole map every iteration; maintain a `matchCount` variable.
- In tree recursion, verify base cases `if (!root) return ...` before accessing `root->left` or `root->right`.
- Always pass large containers by `const reference` (`const std::vector<int>& v`) to avoid unintended $O(N)$ copies.
