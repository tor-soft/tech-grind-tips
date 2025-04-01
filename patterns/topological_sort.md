# Topological Sort

**Description:**
Topological Sort is an algorithm used to find a linear ordering of nodes in a Directed Acyclic Graph (DAG) such that for every directed edge from node `u` to node `v`, node `u` comes before node `v` in the ordering. In simpler terms, it's an ordering of nodes based on their dependencies – if task B depends on task A, then task A must appear before task B in the topological sort. A topological sort is only possible if and only if the graph is a DAG (i.e., it has no directed cycles). If the graph contains a cycle, a topological ordering cannot exist. Multiple valid topological sorts may exist for a given DAG.

There are two common algorithms to perform topological sort:
1.  **Kahn's Algorithm (BFS-based):** Uses indegrees (the count of incoming edges) of nodes and a queue.
2.  **DFS-based Algorithm:** Uses Depth First Search and tracks the order in which nodes finish their exploration (typically by adding nodes to the front of a list or the top of a stack upon finishing the DFS call for that node).

**When to use the pattern:**
- **Task Scheduling/Dependency Resolution:** Ordering tasks or jobs where some tasks must be completed before others can start (e.g., course prerequisites, build systems, compilation dependencies).
- **Detecting Cycles in Directed Graphs:** Both algorithms can be adapted to detect cycles. In Kahn's algorithm, if the final sorted list contains fewer nodes than the total number of nodes in the graph, a cycle exists. In the DFS approach, detecting a back edge (an edge to a node currently in the recursion stack) indicates a cycle.
- **Data Serialization/Processing Pipelines:** Ordering processing steps where the output of one step is the input to another.
- **Symbol Resolution in Compilers:** Determining the order to resolve symbols or dependencies.
- **Event Dependency:** Ordering events where some events trigger others.
- **Any problem involving ordering items based on directed constraints where cycles are not allowed.**

**Template(s):**
- **1. Kahn's Algorithm (BFS-based):**

  ```python
  from collections import deque, defaultdict

  def topological_sort_kahn(num_nodes: int, edges: list[list[int]]) -> list[int]:
      """
      Performs Topological Sort using Kahn's Algorithm (BFS).

      Args:
          num_nodes: The total number of nodes (often labeled 0 to num_nodes-1).
          edges: A list of directed edges, where each edge is [u, v] meaning u -> v.

      Returns:
          A list containing a valid topological sort order,
          or an empty list if a cycle is detected.
      """
      adj = defaultdict(list)
      in_degree = defaultdict(int)

      # Initialize adjacency list and in-degrees for all potential nodes
      for i in range(num_nodes):
          in_degree[i] = 0 # Ensure all nodes are in the map

      # Build adjacency list and calculate in-degrees
      for u, v in edges:
          adj[u].append(v)
          in_degree[v] += 1

      # Initialize queue with nodes having in-degree 0
      queue = deque([node for node in range(num_nodes) if in_degree[node] == 0])

      sorted_order = []

      while queue:
          node = queue.popleft()
          sorted_order.append(node)

          # Process neighbors
          if node in adj:
              for neighbor in adj[node]:
                  in_degree[neighbor] -= 1
                  # If neighbor's in-degree becomes 0, add it to the queue
                  if in_degree[neighbor] == 0:
                      queue.append(neighbor)

      # Check for cycles: If sorted_order length < num_nodes, a cycle exists
      if len(sorted_order) == num_nodes:
          return sorted_order
      else:
          return [] # Cycle detected

  # Example Usage:
  # num_nodes = 4
  # edges = [[1, 0], [2, 0], [3, 1], [3, 2]]
  # print(topological_sort_kahn(num_nodes, edges))
  # Output: Possible orders like [3, 1, 2, 0] or [3, 2, 1, 0]

  # Example with cycle:
  # num_nodes = 3
  # edges = [[0, 1], [1, 2], [2, 0]]
  # print(topological_sort_kahn(num_nodes, edges)) # Output: []
  ```

- **2. DFS-based Algorithm:**

  ```python
  from collections import defaultdict

  # Define states for visited tracking during DFS
  VISITING = 1
  VISITED = 2

  def topological_sort_dfs(num_nodes: int, edges: list[list[int]]) -> list[int]:
      """
      Performs Topological Sort using DFS.

      Args:
          num_nodes: The total number of nodes (often labeled 0 to num_nodes-1).
          edges: A list of directed edges, where each edge is [u, v] meaning u -> v.

      Returns:
          A list containing a valid topological sort order (in reverse finishing order),
          or an empty list if a cycle is detected.
      """
      adj = defaultdict(list)
      for u, v in edges:
          adj[u].append(v)

      visited_state = defaultdict(int) # 0: unvisited, 1: visiting, 2: visited
      sorted_order = []
      has_cycle = False

      def _dfs(node):
          nonlocal has_cycle
          if has_cycle: # Optimization: Stop early if cycle detected
              return

          visited_state[node] = VISITING # Mark as currently visiting

          if node in adj:
              for neighbor in adj[node]:
                  if visited_state[neighbor] == VISITING:
                      # Cycle detected (back edge to a node currently in recursion stack)
                      has_cycle = True
                      return
                  elif visited_state[neighbor] == 0: # Unvisited
                      _dfs(neighbor)
                      if has_cycle: # Propagate cycle detection upwards
                          return

          # Finished exploring all neighbors (or no neighbors)
          visited_state[node] = VISITED # Mark as fully visited
          # Add node to the front of the list (or top of stack)
          sorted_order.append(node)

      # Perform DFS starting from all unvisited nodes
      for node in range(num_nodes):
          if visited_state[node] == 0:
              _dfs(node)
              if has_cycle:
                  return [] # Cycle detected

      return sorted_order[::-1] # Reverse the list to get the correct topological order

  # Example Usage: (same as Kahn's)
  # num_nodes = 4
  # edges = [[1, 0], [2, 0], [3, 1], [3, 2]]
  # print(topological_sort_dfs(num_nodes, edges))
  # Output: Possible orders like [3, 1, 2, 0] or [3, 2, 1, 0]

  # Example with cycle:
  # num_nodes = 3
  # edges = [[0, 1], [1, 2], [2, 0]]
  # print(topological_sort_dfs(num_nodes, edges)) # Output: []
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Graph Must Be a DAG:** Topological sort only works on Directed Acyclic Graphs. Always include cycle detection in your implementation.
    - Kahn's: Check if `len(sorted_order) == num_nodes`.
    - DFS: Check for back edges using visited states (`VISITING`).
- **Handling Disconnected Components:** Both algorithms, as implemented in the templates, handle graphs with multiple disconnected components by iterating through all nodes (`for node in range(num_nodes): ...`).
- **Graph Representation:** Ensure you correctly build the adjacency list (`adj`). For Kahn's, you also need to correctly compute `in_degree` for all nodes.
- **Initialization (Kahn's):** Make sure the initial queue in Kahn's algorithm contains *all* nodes with an in-degree of 0.
- **Visited States (DFS):** Using three states (unvisited, visiting, visited) is crucial for reliable cycle detection in the DFS approach. Using only two states (visited/unvisited) can incorrectly identify cross-edges as back-edges in certain graph structures.
- **Result Order (DFS):** The DFS method naturally produces the reverse topological order as nodes are added when their DFS calls finish. Remember to reverse the result list at the end.
- **Node Labeling:** Be mindful if nodes are 0-indexed, 1-indexed, or have other labels. Adjust the range and data structure initialization accordingly. The templates assume 0-to-`num_nodes-1`.

**Runtime:**
- **Time Complexity:** **O(V + E)** for both Kahn's (BFS) and DFS-based algorithms, where V is the number of vertices (nodes) and E is the number of edges.
    - Kahn's: Each node is enqueued/dequeued once (O(V)). Each edge is processed once when reducing in-degrees (O(E)). Building the initial structures takes O(V+E).
    - DFS: Each node is visited once (O(V)). Each edge is explored once during DFS (O(E)). Building adjacency list takes O(E).
- **Space Complexity:** **O(V + E)**
    - Adjacency List: O(V + E) space.
    - Kahn's: O(V) for `in_degree` map and O(V) for the `queue` in the worst case.
    - DFS: O(V) for `visited_state` map and O(V) for the recursion call stack in the worst case (longest path).
    - Output list: O(V).
    - Overall dominates to O(V + E) mainly due to the adjacency list.