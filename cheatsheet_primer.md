Of course! That's a great starting point. We can flesh this out into a much more comprehensive and actionable guide.

The key to LeetCode is **pattern recognition**. Your friend's list is the beginning of that. This expanded guide will organize these patterns, explain the *why* behind them, provide common keywords and problem types, and offer more advanced techniques.

---

### The Super Comprehensive LeetCode Pattern Cheatsheet

#### Guiding Principles
1.  **Read and Understand the Constraints:** `n <= 1000`? An O(n²) solution might pass. `n <= 10^5`? You need O(n log n) or O(n). `n <= 10^18`? You need an O(log n) or O(1) math-based solution.
2.  **It's Not Magic:** This list is a guide to help you recognize patterns. True mastery comes from understanding the data structures and algorithms (DSA) themselves, not just memorizing pairings.
3.  **Always Think About Trade-offs:** Every choice has a time and space complexity. Be ready to discuss them.

---

### 1. Array & List Patterns

#### Clue: The input array is **SORTED**.
This is a massive hint. The sorted property lets you make assumptions that enable faster algorithms.

*   **Pattern: Binary Search**
    *   **Why:** You can eliminate half of the remaining search space in each step.
    *   **Keywords:** `sorted array`, `find element`, `find position/index`, `find the first/last occurrence`, `find minimum value that satisfies a condition`.
    *   **Use Cases:**
        *   Find a specific target value.
        *   Find the insertion position for a new value.
        *   Find the first or last element that meets a condition (e.g., first `true` in a `[false, false, true, true]` array).
        *   **Advanced:** Binary Search on the *Answer Space*. If a problem asks for the "minimum time" or "maximum capacity" that satisfies a condition, you can often binary search for that value.
    *   **Complexity:** O(log n) time, O(1) space.

*   **Pattern: Two Pointers (Converging Pointers)**
    *   **Why:** By starting at opposite ends and moving inwards, you can explore all pairs or valid subarrays in linear time.
    *   **Keywords:** `pair of values`, `sum`, `target`, `find two/three elements`.
    *   **Use Cases:**
        *   Find a pair that sums to a target (Two Sum II).
        *   Find a triplet that sums to a target (3Sum).
        *   Find a container with the most water.
    *   **Complexity:** O(n) time (after initial O(n log n) sort, if needed), O(1) space.

#### Clue: You are dealing with a **contiguous subarray/substring**.
*   **Pattern: Sliding Window**
    *   **Why:** To avoid re-computing results for overlapping subarrays. A window (defined by a `start` and `end` pointer) slides over the data, expanding and shrinking.
    *   **Keywords:** `longest/shortest subarray/substring`, `maximum/minimum subarray sum`, `contains...`, `within a given size`.
    *   **Use Cases:**
        *   Find the longest substring with no repeating characters.
        *   Find the minimum size subarray whose sum is >= a target.
        *   Find all anagrams of a string within another.
    *   **Complexity:** O(n) time, O(k) space (where k is the size of the window or number of distinct characters).

#### Clue: You need to perform repeated calculations on **ranges/subarrays**.
*   **Pattern: Prefix Sum / Prefix Product**
    *   **Why:** Pre-calculating running sums/products allows you to find the sum/product of any range `[i, j]` in O(1) time.
    *   **Keywords:** `range sum/query`, `subarray sum`.
    *   **Use Cases:**
        *   Range Sum Query - Immutable.
        *   Find a subarray that sums to a target `k`. (Use a hash map to store `prefix_sum` and look for `prefix_sum - k`).
    *   **Complexity:** O(n) to build, then O(1) per query. O(n) space.

---

### 2. Recursion & Graph-like Traversal Patterns

#### Clue: You need to find **all possible** outcomes.
*   **Pattern: Backtracking**
    *   **Why:** It systematically explores all potential solutions by building a candidate solution step-by-step ("choose"), and abandoning it ("un-choose" or backtrack) as soon as it determines the candidate cannot be completed.
    *   **Keywords:** `all permutations`, `all combinations`, `all subsets`, `all paths`, `N-Queens`, `Sudoku`.
    *   **Structure:** A recursive function that takes the current state.
        1.  Base Case: Found a valid solution? Add it to the results.
        2.  Loop through choices.
        3.  **Choose:** Add a choice to the current state.
        4.  **Explore:** Recurse with the new state.
        5.  **Un-choose:** Backtrack by removing the choice.
    *   **Complexity:** Can be exponential, e.g., O(n! * n) for permutations.

#### Clue: The problem involves a **Tree** or **Graph**.

*   **Pattern: Depth-First Search (DFS)**
    *   **When to Use:**
        *   **Path Finding:** Does a path exist between two nodes?
        *   **Topological Sort:** Ordering tasks with dependencies (e.g., Course Schedule).
        *   **Cycle Detection:** Does this graph have a cycle?
        *   **Tree Traversals:** In-order, Pre-order, Post-order.
        *   Exploring an entire tree/graph structure down to its leaves.
    *   **Implementation:** Recursion (elegant, but risks stack overflow) or an explicit **Stack** (iterative).
    *   **Complexity:** O(V + E) where V is vertices, E is edges.

*   **Pattern: Breadth-First Search (BFS)**
    *   **When to Use:**
        *   **Shortest Path in an Unweighted Graph:** Finding the minimum number of steps/edges from a start to an end node.
        *   **Level-Order Traversal:** Visiting a tree/graph level by level.
        *   Web crawler, finding nearby locations.
    *   **Implementation:** **Queue**.
    *   **Complexity:** O(V + E).

#### Clue: The problem involves **groups**, **connected components**, or dynamic connectivity.
*   **Pattern: Union-Find (Disjoint Set Union - DSU)**
    *   **Why:** Incredibly efficient for determining if two elements are in the same set and for merging sets.
    *   **Keywords:** `connected components`, `number of islands` (can also be solved with DFS/BFS), `redundant connection`, `accounts merge`.
    *   **Core Operations:** `find` (with path compression) and `union` (by rank/size).
    *   **Complexity:** Nearly constant time on average, O(α(n)), where α is the inverse Ackermann function.

---

### 3. Optimization & "Max/Min" Patterns

#### Clue: You need to find an **optimal solution** (max/min value, total ways).
This is the hardest category to distinguish.

*   **Pattern: Dynamic Programming (DP)**
    *   **Why:** Solves complex problems by breaking them down into simpler, overlapping subproblems. Solves each subproblem once and stores the result.
    *   **Keywords:** `max/min value`, `number of ways to...`, `longest/shortest...` (but not of a *contiguous* subarray), `is it possible to reach...`.
    *   **Litmus Test:** Can you define a function `dp(i)` or `dp(i, j)` that represents the optimal solution for the problem up to state `i` (or `(i, j)`)?
    *   **Common Patterns:**
        *   **1D DP:** Climbing Stairs, House Robber, Coin Change. `dp[i]` depends on previous values like `dp[i-1]`.
        *   **2D DP:** Longest Common Subsequence, Knapsack, Edit Distance. `dp[i][j]` depends on `dp[i-1][j]`, `dp[i][j-1]`, etc.
    *   **Implementations:**
        1.  **Top-Down (Memoization):** A natural recursive solution with a cache (map or array).
        2.  **Bottom-Up (Tabulation):** An iterative solution building up from base cases.

*   **Pattern: Greedy Algorithm**
    *   **Why:** Makes the locally optimal choice at each step with the hope of finding a global optimum. Simpler and faster than DP.
    *   **Keywords:** `max/min`, `optimal`, often involves sorting first.
    *   **Litmus Test:** Can you prove that making the "best" immediate choice will always lead to a globally optimal solution?
    *   **Use Cases:** Interval Scheduling (e.g., Meeting Rooms II), Fractional Knapsack, assigning cookies.

*   **Pattern: Sliding Window** (as mentioned before)
    *   Use this for `max/min` of a **contiguous** subarray/substring.

---

### 4. Specialized Data Structure Patterns

#### Clue: You need the **Top / Least / K-th** items.
*   **Pattern: Heap (Priority Queue)**
    *   **Why:** A heap maintains the min (or max) element at the root, accessible in O(1) time. Insertions and deletions are O(log n).
    *   **Keywords:** `top K`, `least K`, `Kth largest/smallest`, `median`, `most frequent`.
    *   **Use Cases:**
        *   **Top K Largest:** Maintain a min-heap of size `K`.
        *   **Median of a Data Stream:** Use a max-heap (for the smaller half) and a min-heap (for the larger half).
        *   **Kth Largest Element:** A max-heap can solve this, but QuickSelect is better.
*   **Pattern: QuickSelect**
    *   **Why:** A modification of the QuickSort algorithm. Instead of recurring on both sides of the pivot, it only recurs on the side that contains the Kth element.
    *   **Use Cases:** Kth Largest/Smallest element.
    *   **Complexity:** O(n) on average, O(n²) in the worst case.

#### Clue: The problem involves **string prefixes** or dictionary lookups.
*   **Pattern: Trie (Prefix Tree)**
    *   **Why:** An n-ary tree structure where each node represents a character. It's extremely efficient for storing and searching for strings based on their prefixes.
    *   **Keywords:** `prefix`, `autocomplete`, `dictionary`, `word search`.
    *   **Use Cases:** Implement an autocomplete system, find all words matching a pattern.

#### Clue: You need to work with individual bits or find a single unique element.
*   **Pattern: Bit Manipulation**
    *   **Why:** Provides highly efficient, low-level solutions for specific problems.
    *   **Keywords:** `power of two`, `exactly one/two unique elements`, `all subsets`.
    *   **Key Tricks:**
        *   `a ^ a = 0` (XORing with self is zero)
        *   `a ^ 0 = a` (XORing with zero is self)
        *   `x & (x - 1)` clears the least significant bit.
        *   `x & -x` isolates the least significant bit.
    *   **Use Cases:** Finding the single number that appears once, finding two numbers that appear once, counting set bits.

---

### 5. General Strategies & Constraints

#### Clue: **Recursion is banned** or you have **deep recursion**.
*   **Pattern: Use an explicit Stack**
    *   **Why:** Any recursive algorithm can be converted into an iterative one using a stack to manually manage the "call stack".
    *   **Use Cases:** In-order traversal of a BST without recursion, any deep DFS.

#### Clue: You must solve it **in-place** (O(1) space).
*   **Patterns:**
    *   **Swap corresponding values:** Reverse a list, rotate an array.
    *   **Store multiple values in one location:** Use math (`a = a + n*b`) or bit manipulation to store two values in one memory slot.
    *   **Use the input array itself as memory:** Mark visited cells by changing their sign (e.g., `nums[i] = -nums[i]`) if the values are positive. This is common in "Find the Duplicate/Missing Number" problems.

#### Clue: No obvious algorithm fits. (The "Else" clause)
*   **Pattern: Use a HashMap/HashSet**
    *   **Why:** Provides O(1) average time for insertions, deletions, and lookups. It's the ultimate tool for trading space for time.
    *   **When to Use:**
        *   **Counting:** Tally frequencies of elements (e.g., Top K Frequent Elements).
        *   **Two Sum:** Store `target - nums[i]` and look for it.
        *   **Finding Duplicates:** Add elements to a set and check if an element is already there.
        *   **Caching/Memoization:** The backbone of top-down DP.
    *   **Complexity:** O(n) time, O(n) space.

*   **Pattern: Sort the input first**
    *   **Why:** Sometimes, simply sorting the input array makes the problem trivial or enables one of the other patterns (like Two Pointers or easily finding duplicates).
    *   **When to Use:** When the order doesn't matter and the O(n log n) cost is acceptable.
    *   **Examples:** Check if an array contains duplicates, find all anagrams.
    *   **Complexity:** O(n log n) time, O(1) or O(n) space depending on the sort implementation.
