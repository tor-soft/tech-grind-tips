# Breadth First Search (BFS)

**Description:**
Breadth First Search (BFS) is a graph and tree traversal algorithm that explores level by level. It starts at a selected node (root for trees, arbitrary node for graphs) and explores all of its immediate neighbors first. Then, for each of those neighbors, it explores their unvisited neighbors, and so on. In essence, BFS explores all nodes at the present depth prior to moving on to nodes at the next depth level. BFS is typically implemented iteratively using a queue data structure. Nodes are enqueued as they are discovered and dequeued for processing and exploring their neighbors.

**When to use the pattern:**
- **Shortest Path in Unweighted Graphs:** BFS is guaranteed to find the shortest path (in terms of the number of edges) in an unweighted graph. This is a primary use case.
- **Level Order Traversal (Trees):** BFS naturally performs a level-order traversal of a tree, visiting nodes level by level.
- **Finding Nodes at a Certain Level/Distance:** Because BFS explores level by level, it's easy to track the distance (number of edges) from the starting node.
- **Connectivity Check:** Determining if a path exists between two nodes or finding connected components in a graph (similar to DFS but BFS can also be used).
- **Flood Fill / Shortest Path Maze Problems:**  In grid-based problems like mazes or flood fill, BFS can find the shortest path or explore reachable areas.
- **Finding the Closest Node with a Property:** If you need to find the node closest to a starting node that satisfies a certain property, BFS is often a good approach.
- **Keywords:** "shortest path", "level order", "nearest", "closest", "connected components (sometimes better than DFS for certain types)", "unweighted graph".

**Template(s):**
- **Iterative BFS (Graph with Adjacency List & Visited Set):**

  ```python
  from collections import deque

  def bfs_graph(graph: dict[int, list[int]], start_node: int):
      """
      Performs iterative BFS on a graph represented by an adjacency list.

      Args:
          graph: Adjacency list (e.g., {0: [1, 2], 1: [2], 2: [0, 3], 3: [3]})
          start_node: The node to start the BFS from.
      """
      if start_node not in graph: # Handle case where start node isn't in graph keys
          print(f"Node {start_node} not in graph.")
          return

      visited = set()
      queue = deque([start_node]) # Initialize queue with the start node

      visited.add(start_node) # Mark the start node as visited immediately

      while queue:
          node = queue.popleft() # Dequeue the first node

          # --- Process Node ---
          print(f"Visiting node: {node}") # Example action
          # --- ---

          # Explore neighbors
          if node in graph:
              for neighbor in graph[node]:
                  if neighbor not in visited:
                      visited.add(neighbor) # Mark neighbor as visited when enqueued
                      queue.append(neighbor)  # Enqueue unvisited neighbors

      # Note: This only visits nodes reachable from start_node.
      # To visit all nodes in potentially disconnected graph, loop through all nodes:
      # visited = set()
      # for node in graph:
      #     if node not in visited and node in graph: #check node in graph keys
      #         bfs_graph(graph, node) # Or use a single queue for all components
  ```

- **Iterative BFS (Binary Tree - Level Order Traversal):**

  ```python
  from collections import deque

  class TreeNode:
      def __init__(self, val=0, left=None, right=None):
          self.val = val
          self.left = left
          self.right = right

  def level_order_traversal_bfs(root: TreeNode) -> list[list[int]]:
      """Iterative Level Order BFS for a binary tree."""
      if not root:
          return []

      levels = [] # To store nodes at each level
      queue = deque([root]) # Initialize queue with the root

      while queue:
          level_size = len(queue) # Number of nodes at the current level
          current_level_nodes = []

          for _ in range(level_size): # Process all nodes at the current level
              node = queue.popleft()
              current_level_nodes.append(node.val)

              # Enqueue children (next level)
              if node.left:
                  queue.append(node.left)
              if node.right:
                  queue.append(node.right)

          levels.append(current_level_nodes) # Add current level's nodes to result

      return levels # Returns a list of lists, each inner list representing a level.
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Queue Usage:** BFS *must* use a queue (FIFO - First-In, First-Out) to ensure level-by-level exploration. Using a stack will turn it into DFS.
- **Visited Tracking:** Like DFS, using a `visited` set (or array) is crucial for graphs to prevent infinite loops in cyclic or undirected graphs and to avoid redundant processing. For trees, `visited` is generally not needed in basic traversals as there are no cycles, but might be necessary for more complex tree problems.
- **Enqueuing vs. Visiting:** It's generally better to mark a node as `visited` **when it is enqueued**, not when it's dequeued and processed. This prevents adding the same node to the queue multiple times if it has multiple paths leading to it, which is important for shortest path correctness and efficiency. (See example templates).
- **Level Tracking (for Level Order):** If you need level order traversal (especially for trees), you need to track the nodes at the current level and then process the next level.  The template for tree BFS demonstrates how to use `level_size` to iterate through all nodes at the current level before moving to the next.
- **Shortest Path Guarantee (Unweighted):** Remember BFS finds the shortest path in *unweighted* graphs. For weighted graphs, Dijkstra's algorithm is needed.
- **Graph Representation:** Be comfortable with adjacency lists and adjacency matrices for graphs. BFS works well with both. Adjacency lists are usually more space-efficient for sparse graphs.
- **Starting Node:**  Ensure you start BFS from the correct starting node or set of nodes, as per the problem requirements. For disconnected graphs, you may need to iterate through all nodes to initiate BFS from unvisited components.
- **Edge Cases:** Consider empty graphs, graphs with only one node, or when the start node is not in the graph keys.

**Runtime:**
- **Time Complexity:** **O(V + E)** for graphs, where V is the number of vertices and E is the number of edges. In BFS, each vertex and each edge is visited and processed at most once. For trees (where E = V-1), this simplifies to **O(V)** or O(N) where N is the number of nodes.
- **Space Complexity:** **O(V)** in the worst case. This is because in the worst-case scenario (e.g., a complete graph or a breadth-wise bushy tree), the queue could potentially hold all vertices at some level. Additionally, the `visited` set also takes O(V) space.
- **In Level Order Tree BFS (returning levels):** The space complexity might be considered O(W) where W is the maximum width of the tree (number of nodes at the widest level) for the queue itself *during the execution*. However, if you consider storing the `levels` list in the output, the space complexity to store the output itself can also be significant, potentially up to O(N) in total for a balanced tree. But the auxiliary space used by the BFS algorithm itself is dominated by the queue and visited set, which in the worst case is O(V).