**The 'Memorization' Strategy**

It's not about rote memorization like some chump reciting multiplication tables. It's about *internalizing the mechanics*.

1.  **Diagnose the Core Problem:** What are they *really* asking? Is it about finding a pair in sorted data? Optimizing subarray checks? Shortest path? Exploring possibilities? Finding the top K? Needing fast lookups?
2.  **Keyword Association (Initial Hint):** "Sorted array" -> Two Pointers. "Contiguous subarray" / "substring" -> Sliding Window. "Shortest path" / "fewest steps" / "level order" -> BFS. "Permutations" / "combinations" / "any path" / "connectivity" -> DFS/Backtracking. "Top K" / "median" / "scheduling" -> Heap. "Counts" / "duplicates" / "fast lookup needed" -> Hash Map.
3.  **Recall the Skeleton Key:** Visualize the core template structure (loops, data structures, main operations).
4.  **Adapt and Combine:** This is where you earn your paycheck. How does the *specific* problem change the template?
    *   What state needs tracking in the window/map/queue/recursion?
    *   What's the exact condition for shrinking the window or backtracking?
    *   How are neighbors defined (grid, graph, implicit state)?
    *   Do you need multiple patterns? (e.g., Sliding Window *with* a Hash Map, BFS *on* an implicit graph).
5.  **Code It Clean & Handle Edges:** Use good names. Think about empty input, `k=0`, target not found, single-element arrays. Don't look like a rookie.

---

Here's a more comprehensive list of keyword associations, expanding on the initial examples:

**Data Structure / Algorithm -> Keywords & Problem Characteristics**

**1. Hash Map / Hash Set (Dictionary / Set)**
    *   **Keywords:** "count", "frequency", "counts of", "how many", "duplicates", "contains", "find if exists", "unique", "group", "anagrams"
    *   **Characteristics:** Need very fast lookups (average O(1)), insertions, deletions. Checking for existence of items. Counting occurrences. Storing pairs (map) or just items (set). Removing duplicates. Grouping items by a property (e.g., anagrams group by sorted string or character count). Caching/Memoization in DP.

**2. Two Pointers**
    *   **Keywords:** "sorted array", "sorted list", "pair", "triplet", "sum", "target", "meet", "subsequence", "palindrome check", "in-place modification", "remove duplicates from sorted"
    *   **Characteristics:** Usually operates on sorted linear data (arrays, sometimes strings or linked lists). Pointers often start at opposite ends, both at the start, or one fast/one slow. Used for finding pairs/triplets with specific properties, comparing elements symmetrically, partitioning data in-place, or detecting cycles (fast/slow).

**3. Sliding Window**
    *   **Keywords:** "contiguous subarray", "substring", "fixed window size K", "minimum/maximum window", "longest/shortest substring/subarray with property", "contains all chars"
    *   **Characteristics:** Problems involving finding optimal (min/max/longest/shortest) *contiguous* segments within a linear structure (array, string). Often involves expanding a window and shrinking it based on certain conditions. Frequently used with a Hash Map to track contents within the window.

**4. Breadth-First Search (BFS)**
    *   **Keywords:** "shortest path" (unweighted graph), "fewest steps", "nearest", "level order traversal" (trees/graphs), "connectivity" (find *if* connected, short path), "matrix traversal" (shortest path)
    *   **Characteristics:** Exploring layer by layer. Finding the shortest path in terms of *number of edges/steps*. Used on explicit graphs, implicit graphs (e.g., state transitions like word ladder), trees, and grids. Requires a Queue.

**5. Depth-First Search (DFS) / Backtracking**
    *   **Keywords:** "any path", "find a path", "explore all possibilities", "permutations", "combinations", "subsets", "N-Queens", "Sudoku solver", "generate parentheses", "maze traversal" (find *any* path), "connectivity", "cycle detection", "tree traversals" (preorder, inorder, postorder)
    *   **Characteristics:** Exploring as deep as possible along one path before backtracking. Used for problems requiring exploration of a state space, finding *any* solution (not necessarily shortest), generating all possible configurations, graph/tree traversal. Typically implemented recursively or iteratively with a Stack. Backtracking specifically involves making a choice, exploring, and undoing the choice.

**6. Heap (Priority Queue)**
    *   **Keywords:** "top K", "smallest K", "largest K", "find median" (often 2 heaps), "frequent K", "scheduling", "meeting rooms", "merge K sorted lists", "minimum cost to connect" (related to Prim's MST)
    *   **Characteristics:** Need efficient retrieval of the min/max element (O(log N) insertion/deletion, O(1) peek). Used when you repeatedly need the smallest or largest item among a dynamically changing set. Useful for scheduling (heap of end times) or merging sorted sources.

**7. Binary Search**
    *   **Keywords:** "sorted array", "sorted space", "find element", "search", "minimum value such that [condition]", "maximum value such that [condition]", "find peak", "rotated sorted array"
    *   **Characteristics:** Requires a sorted search space (can be an explicit array, or an implicit range of possible answers). Efficiently narrows down the search space by half in each step (O(log N)). Key is defining the condition to go left or right.

**8. Dynamic Programming (DP)**
    *   **Keywords:** "minimum/maximum cost/value/profit", "number of ways", "count distinct ways", "longest/shortest sequence/subsequence (LCS, LIS)", "can it be done?", "optimal strategy", "overlapping subproblems", "optimal substructure"
    *   **Characteristics:** Problems where the optimal solution can be built from optimal solutions to smaller subproblems. Often involves filling a table (1D or 2D array) or using memoization (top-down DP). Look for recurrence relations. Keywords often imply optimization or counting possibilities constrained by previous choices.

**9. Trie (Prefix Tree)**
    *   **Keywords:** "prefix", "suffix" (if reversed), "dictionary search", "autocomplete", "word search", "starts with"
    *   **Characteristics:** Tree-like structure optimized for string prefix operations. Efficient for searching words based on prefixes, IP routing tables, etc. Each node represents a character.

**10. Union-Find (Disjoint Set Union - DSU)**
    *   **Keywords:** "connected components", "grouping", "disjoint sets", "connectivity" (dynamic), "cycle detection" (undirected graph), "minimum spanning tree" (Kruskal's)
    *   **Characteristics:** Efficiently manages partitions of a set into disjoint subsets. Supports fast union (merging sets) and find (determining which set an element belongs to, often checking connectivity).

**11. Graph Algorithms (Specific)**
    *   **Dijkstra:** "shortest path" (weighted graph, non-negative weights). Uses a priority queue.
    *   **Bellman-Ford:** "shortest path" (weighted graph, *can handle negative weights*), "detect negative cycles". Slower than Dijkstra.
    *   **Floyd-Warshall:** "all-pairs shortest path". Usually used when graph is small.
    *   **Topological Sort:** "order of tasks", "dependencies", "course schedule". Used on Directed Acyclic Graphs (DAGs). Can use BFS (Kahn's) or DFS.
    *   **Minimum Spanning Tree (MST) - Prim's / Kruskal's:** "minimum cost to connect all points", "network connection".

**12. Greedy Algorithms**
    *   **Keywords:** "maximum profit", "minimum cost", "optimal schedule", "select activities"
    *   **Characteristics:** Making the locally optimal choice at each step hoping to find the global optimum. Often involves sorting the input based on some criteria. Does *not* always work - need to justify/prove correctness. Often combined with heaps or simple iteration.

**13. Bit Manipulation**
    *   **Keywords:** "bitwise", "XOR", "powers of 2", "check if bit is set", "toggle bit", "efficient set representation" (for small N), "find unique number" (where others appear twice)
    *   **Characteristics:** Constraints might involve large numbers where standard arithmetic is too slow, or operations need to be highly optimized. Useful when dealing with binary representations, flags, or subsets where the domain size is small (e.g., <= 64).

**14. Prefix Sum / Fenwick Tree (BIT) / Segment Tree**
    *   **Keywords:** "range sum query", "range update", "subarray sum", "prefix calculations"
    *   **Characteristics:** Need to perform operations (sum, min, max, updates) on ranges of an array efficiently. Prefix Sum is simple for static arrays and sum queries (O(1) query, O(N) precomputation). BIT/Segment Tree handle updates more efficiently (O(log N) for both query and update).

**15. Monotonic Stack / Queue**
    *   **Keywords:** "next greater element", "previous smaller element", "largest rectangle in histogram", "sliding window maximum/minimum" (alternative to heap/multiset)
    *   **Characteristics:** Maintaining a stack or deque where elements are strictly increasing or decreasing. Useful for problems where you need to find the nearest element with a certain property relative to the current element.

**Important Caveats:**

*   **Not Mutually Exclusive:** Problems often require combining multiple patterns (e.g., Sliding Window + Hash Map, BFS on an implicit graph defined by states, Greedy + Heap).
*   **Keywords are Hints, Not Rules:** The same keyword might appear in different problem types. "Connectivity" could be BFS, DFS, or Union-Find depending on the exact question (shortest path? any path? dynamic grouping?).
*   **Problem Transformation:** Sometimes the core challenge is recognizing how to *model* the problem using one of these structures (e.g., treating states as nodes in an implicit graph for BFS/DFS).
*   **Focus on "Why":** Don't just map keywords; understand *why* a particular structure/algorithm is suitable for the characteristics implied by those keywords (e.g., why BFS for shortest path? Because it explores layer by layer).
