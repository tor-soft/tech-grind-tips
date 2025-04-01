# Depth First Search (DFS)

**Description:**
Depth First Search (DFS) is a graph and tree traversal algorithm that explores as far as possible along each branch before backtracking. It starts at a selected node (root for trees, arbitrary node for graphs) and explores one of its adjacent nodes. It then explores that node's adjacent nodes, going deeper and deeper until it reaches a node with no unvisited adjacent nodes or hits a dead end (like a leaf node in a tree or already visited node in a graph). At this point, it backtracks to the previous node and explores the next available unvisited adjacent node. This process continues until all reachable nodes have been visited. DFS typically uses recursion (leveraging the call stack) or an explicit stack for an iterative implementation.

**When to use the pattern:**
- **Tree Traversals:** Implementing Preorder, Inorder, and Postorder traversals of a tree.
- **Graph Traversal:** Visiting all nodes reachable from a starting node in a graph.
- **Path Finding:** Determining if a path exists between two nodes in a graph (though BFS is often better for *shortest* paths in unweighted graphs).
- **Cycle Detection:** Detecting cycles in both undirected and directed graphs (often using visited states like visiting/visited).
- **Connectivity:** Finding connected components in an undirected graph.
- **Topological Sort:** Ordering nodes in a Directed Acyclic Graph (DAG) based on dependencies (often combined with tracking finishing times or using a stack).
- **Backtracking Problems:** Many backtracking algorithms (like finding permutations, combinations, subsets, solving mazes, N-Queens, Sudoku) use a DFS-like recursive structure to explore the solution space.
- **Flood Fill / Island Problems:** Exploring connected regions in a grid (treating grid cells as nodes and adjacent cells as edges).

**Template(s):**
- **Recursive DFS (Graph with Adjacency List & Visited Set):**

  ```python
  def dfs_recursive_graph(graph: dict[int, list[int]], start_node: int):
      """
      Performs recursive DFS on a graph represented by an adjacency list.

      Args:
          graph: Adjacency list (e.g., {0: [1, 2], 1: [2], 2: [0, 3], 3: [3]})
          start_node: The node to start the DFS from.
      """
      visited = set() # Keep track of visited nodes to avoid cycles/redundancy

      def _dfs_helper(node: int):
          # --- Process Node (Pre-order position) ---
          print(f"Visiting node: {node}") # Example action
          visited.add(node)
          # --- ---

          # Explore neighbors
          if node in graph: # Check if node has neighbors
              for neighbor in graph[node]:
                  if neighbor not in visited:
                      _dfs_helper(neighbor) # Recursive call

          # --- Process Node (Post-order position) ---
          # print(f"Finished with node: {node}") # Example action
          # --- ---

      _dfs_helper(start_node)

      # Note: This only visits nodes reachable from start_node.
      # To visit all nodes in potentially disconnected graph, loop through all nodes:
      # visited = set()
      # for node in graph:
      #     if node not in visited:
      #         _dfs_helper(node)
  ```

- **Iterative DFS (Graph with Adjacency List & Visited Set):**

  ```python
  def dfs_iterative_graph(graph: dict[int, list[int]], start_node: int):
      """
      Performs iterative DFS using a stack on a graph represented by an adjacency list.

      Args:
          graph: Adjacency list (e.g., {0: [1, 2], 1: [2], 2: [0, 3], 3: [3]})
          start_node: The node to start the DFS from.
      """
      if start_node not in graph: # Handle case where start node isn't in graph keys
          print(f"Node {start_node} not in graph.")
          return

      visited = set()
      stack = [start_node] # Initialize stack with the start node

      while stack:
          node = stack.pop() # Get the next node to visit

          # Check if already visited (important for iterative approach with cycles)
          if node in visited:
              continue

          # --- Process Node ---
          print(f"Visiting node: {node}") # Example action
          visited.add(node)
          # --- ---

          # Add neighbors to the stack (in reverse order for same traversal as recursion)
          if node in graph:
              # Add neighbors that haven't been visited yet OR let the check above handle it
              # Option 1: Check before adding (can be slightly faster sometimes)
              # for neighbor in reversed(graph[node]): # Reverse to mimic recursion order
              #     if neighbor not in visited:
              #          stack.append(neighbor)
              # Option 2: Add all neighbors, let visited check handle redundancy (simpler code)
              for neighbor in reversed(graph[node]):
                  stack.append(neighbor)

      # Note: Like recursive, may need outer loop for disconnected graphs.
  ```

- **Recursive DFS (Binary Tree - Preorder Example):**

  ```python
  class TreeNode:
      def __init__(self, val=0, left=None, right=None):
          self.val = val
          self.left = left
          self.right = right

  def preorder_traversal_recursive(root: TreeNode) -> list[int]:
      """Recursive Preorder DFS for a binary tree."""
      result = []
      def _dfs(node):
          if not node:
              return

          # Process node (Preorder: Root -> Left -> Right)
          result.append(node.val)
          # Explore left subtree
          _dfs(node.left)
          # Explore right subtree
          _dfs(node.right)

      _dfs(root)
      return result
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Visited Tracking:** For graphs (especially cyclic or undirected), **absolutely crucial** to keep track of visited nodes (`visited` set or array) to prevent infinite loops. For trees, it's generally not needed as there are no cycles.
- **Stack Overflow (Recursive):** Deeply nested recursive calls (e.g., a skewed tree or a long path in a graph) can lead to a stack overflow error. The iterative approach avoids this.
- **Iterative vs. Recursive Order:** The order in which neighbors are added to the stack in the iterative version determines the traversal order. Adding neighbors `n1, n2, n3` results in processing `n3` first (LIFO). To mimic the typical recursive order (exploring `n1`'s branch fully before `n2`), add neighbors in reverse order (`stack.extend(reversed(neighbors))`).
- **Graph Representation:** Be comfortable with different graph representations (adjacency list, adjacency matrix, edge list) and how DFS interacts with them. Adjacency lists are generally preferred for sparse graphs.
- **Base Cases:** Ensure correct base cases in recursive implementations (e.g., `if not node: return`).
- **Connected Components:** A single DFS run from one node only visits nodes in its connected component. To cover a potentially disconnected graph, iterate through all nodes and start a DFS if the node hasn't been visited yet.
- **Backtracking State:** When using DFS for backtracking, remember to "undo" changes to the state as you backtrack up the recursion tree (e.g., remove an element added to a temporary list, unmark a cell in a grid).
- **Implicit Graphs:** Problems involving grids (mazes, flood fill) or state spaces (puzzle solving) can often be modeled as implicit graphs where nodes are states/cells and edges are valid transitions/adjacencies. Apply DFS accordingly.

**Runtime:**
- **Time Complexity:** **O(V + E)** for graphs, where V is the number of vertices (nodes) and E is the number of edges. Each vertex and each edge is visited/processed at most once (thanks to the `visited` set). For trees (where E = V-1), this simplifies to **O(V)** or O(N) where N is the number of nodes.
- **Space Complexity:**
    - **Recursive:** O(H) where H is the maximum depth of the recursion stack. In the worst case (a skewed tree or a long path graph), H can be O(V), leading to O(V) space. In a balanced tree, it's O(log V). This space is *in addition* to the space needed for the `visited` set.
    - **Iterative:** O(V) in the worst case for the explicit `stack` (e.g., a star graph where the center node pushes all other V-1 nodes onto the stack).
    - **Visited Set:** O(V) space is required to store the visited nodes.
    - **Overall Space:** Often dominated by the recursion stack/explicit stack and the visited set, resulting in **O(V)** space complexity in most graph scenarios. For trees, recursive can be O(H) and iterative O(W) where W is the maximum width of the tree (for the stack).