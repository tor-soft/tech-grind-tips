# Union-Find (Disjoint Set Union - DSU)

**Description:**
The Union-Find data structure, also known as Disjoint Set Union (DSU), is designed to efficiently manage a collection of disjoint (non-overlapping) sets. It provides two primary operations:
1.  **`find(i)`**: Determine which set an element `i` belongs to. It returns a representative (or root) element of that set. An important optimization is **path compression**, which flattens the structure during finds, making subsequent finds faster.
2.  **`union(i, j)`**: Merge the sets containing elements `i` and `j`. If they are already in the same set, do nothing. Optimizations like **union by rank** or **union by size** are used to keep the trees representing the sets shallow, improving performance.

Internally, it's often implemented using an array (let's call it `parent` or `root`) where `parent[i]` stores the parent of element `i`. The root of a set is an element `i` such that `parent[i] == i`.

**When to use the pattern:**
- Problems involving grouping elements into disjoint sets.
- Determining connectivity between elements (e.g., checking if two nodes are in the same connected component in a graph dynamically).
- Detecting cycles in an undirected graph (if adding an edge `(u, v)` results in `find(u) == find(v)`, then adding the edge would create a cycle).
- Kruskal's algorithm for finding the Minimum Spanning Tree (MST) uses DSU to check if adding an edge connects two previously disconnected components.
- Problems involving equivalence relations (e.g., finding groups of equivalent equations or strings).
- Counting the number of connected components in a graph or grid.
- Network connectivity problems (e.g., finding the earliest time all nodes become connected).

**Template(s):**
- **Basic DSU Implementation (with Path Compression and Union by Size/Rank):**

  ```python
  class UnionFind:
      def __init__(self, n: int):
          """
          Initializes the DSU structure for 'n' elements (0 to n-1).
          Each element starts in its own set.
          """
          # parent[i] stores the parent of element i
          self.parent = list(range(n))
          # size[i] stores the size of the set rooted at i (used for Union by Size)
          # Alternatively, use 'rank' for Union by Rank (tree height approximation)
          self.size = [1] * n
          # Optional: Keep track of the number of distinct sets/components
          self.num_sets = n

      def find(self, i: int) -> int:
          """
          Finds the representative (root) of the set containing element i.
          Implements path compression.
          """
          # If i is the root, return i
          if self.parent[i] == i:
              return i
          # Path Compression: Recursively find the root and update parent[i] directly
          self.parent[i] = self.find(self.parent[i])
          return self.parent[i]

      def union(self, i: int, j: int) -> bool:
          """
          Merges the sets containing elements i and j.
          Implements Union by Size (attaching smaller tree to larger tree).
          Returns True if a merge happened, False if i and j were already in the same set.
          """
          # Find the roots of the sets containing i and j
          root_i = self.find(i)
          root_j = self.find(j)

          # If i and j are already in the same set, do nothing
          if root_i == root_j:
              return False

          # Union by Size: Attach the smaller tree to the root of the larger tree
          if self.size[root_i] < self.size[root_j]:
              # Make root_j the parent of root_i
              self.parent[root_i] = root_j
              # Update the size of the merged set (rooted at root_j)
              self.size[root_j] += self.size[root_i]
          else:
              # Make root_i the parent of root_j
              self.parent[root_j] = root_i
              # Update the size of the merged set (rooted at root_i)
              self.size[root_i] += self.size[root_j]

          # Decrease the number of distinct sets
          self.num_sets -= 1
          return True

      def is_connected(self, i: int, j: int) -> bool:
          """Checks if elements i and j belong to the same set."""
          return self.find(i) == self.find(j)

      def get_num_sets(self) -> int:
          """Returns the current number of disjoint sets."""
          return self.num_sets

  # Example Usage:
  # uf = UnionFind(10) # Initialize for elements 0 through 9
  # uf.union(0, 1)
  # uf.union(1, 2)
  # uf.union(5, 6)
  # print(uf.is_connected(0, 2)) # Output: True
  # print(uf.is_connected(0, 5)) # Output: False
  # print(uf.get_num_sets())     # Output: 7 (sets are {0,1,2}, {3}, {4}, {5,6}, {7}, {8}, {9})
  # print(uf.find(1))            # Output: might be 0 (depends on union implementation details)
  # print(uf.size[uf.find(1)])   # Output: 3
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Initialization:** Ensure the `parent` array is correctly initialized (`parent[i] = i`) and the `size`/`rank` array is initialized (usually to 1 or 0 respectively). The number of elements `n` must be correctly determined.
- **Forgetting Optimizations:** Not implementing Path Compression and Union by Size/Rank significantly degrades performance, potentially leading to O(N) for `find` and `union` in the worst case (skewed tree). With optimizations, it's nearly constant time.
- **Path Compression Implementation:** Ensure the `find` operation correctly updates the parent pointers along the path to point directly to the root. Recursive implementation is concise; iterative is also possible.
- **Union by Size/Rank Logic:** Make sure the comparison logic (`<` or `<=`) and the updates to `size`/`rank` and `parent` are correct when merging. Attach the *root* of the smaller tree to the *root* of the larger tree.
- **0-based vs 1-based Indexing:** Be consistent with whether your elements are indexed from 0 or 1. Adjust initialization and array sizes accordingly. The template uses 0-based indexing.
- **Handling Non-Integer Elements:** If dealing with strings or other objects, map them to integers (0 to n-1) first using a dictionary/hash map before using the DSU structure.
- **Idempotency:** Calling `union(i, j)` multiple times when `i` and `j` are already connected should not change the structure or the count of sets. The template handles this correctly.
- **Off-by-One Errors:** Be careful with array bounds, especially during initialization and access.

**Runtime:**
- **Time Complexity:** With both **Path Compression** and **Union by Rank/Size**, the amortized time complexity for both `find` and `union` operations is nearly constant, technically O(α(N)), where α(N) is the inverse Ackermann function. This function grows extremely slowly, so for all practical purposes, α(N) is less than 5. Therefore, the operations are considered **effectively O(1) on average (amortized)**.
    - A sequence of `M` operations on `N` elements takes O(M * α(N)) time.
    - Without optimizations, the worst-case time complexity can be O(N) per operation.
- **Space Complexity:** **O(N)** to store the `parent` and `size`/`rank` arrays.