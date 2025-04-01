# Tree Traversal: Preorder, Inorder, Postorder, Level Order

**Description:**
Tree traversal refers to the process of visiting (e.g., checking, updating, printing) each node in a tree data structure exactly once. The order in which the nodes are visited defines the type of traversal. The four most common traversal methods for binary trees are:

1.  **Preorder (Root-Left-Right):** Visit the current node first, then recursively traverse the left subtree, and finally, recursively traverse the right subtree. Often associated with Depth First Search (DFS).
2.  **Inorder (Left-Root-Right):** Recursively traverse the left subtree first, then visit the current node, and finally, recursively traverse the right subtree. Often associated with Depth First Search (DFS). Primarily used with Binary Search Trees (BSTs).
3.  **Postorder (Left-Right-Root):** Recursively traverse the left subtree first, then recursively traverse the right subtree, and finally, visit the current node. Often associated with Depth First Search (DFS).
4.  **Level Order (Level by Level):** Visit nodes level by level, from top to bottom, and within each level, typically from left to right. Implemented using Breadth First Search (BFS) with a queue.

**When to use the pattern:**
- **Preorder:**
    - Creating a copy of a tree.
    - Generating prefix expressions (e.g., `+ A B` from an expression tree).
    - Hierarchical representations (e.g., file system directory listing where parent directory is listed before contents).
- **Inorder:**
    - Retrieving data in sorted order from a Binary Search Tree (BST).
    - Generating infix expressions from an expression tree.
- **Postorder:**
    - Deleting or freeing nodes in a tree (process children before deleting the parent).
    - Generating postfix expressions (Reverse Polish Notation).
    - Calculating properties that depend on children's values (e.g., size of subtrees).
- **Level Order:**
    - Finding the minimum depth or shortest path from the root to a node in an unweighted tree.
    - Problems requiring visiting nodes level by level (e.g., find the maximum value at each level, connect nodes at the same level).
    - Visualizing the tree structure breadth-wise.

**Template(s):**

*Common `TreeNode` definition:*
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

- **1. Preorder Traversal (Root-Left-Right):**

  *Recursive:*
  ```python
  def preorder_recursive(root: TreeNode) -> list[int]:
      result = []
      def _traverse(node):
          if not node:
              return
          result.append(node.val) # Visit Root
          _traverse(node.left)   # Traverse Left
          _traverse(node.right)  # Traverse Right
      _traverse(root)
      return result
  ```
  *Iterative (using Stack):*
  ```python
  def preorder_iterative(root: TreeNode) -> list[int]:
      if not root:
          return []
      result = []
      stack = [root]
      while stack:
          node = stack.pop()
          result.append(node.val) # Visit Root
          # Push right child first, then left (so left is processed first - LIFO)
          if node.right:
              stack.append(node.right)
          if node.left:
              stack.append(node.left)
      return result
  ```

- **2. Inorder Traversal (Left-Root-Right):**

  *Recursive:*
  ```python
  def inorder_recursive(root: TreeNode) -> list[int]:
      result = []
      def _traverse(node):
          if not node:
              return
          _traverse(node.left)   # Traverse Left
          result.append(node.val) # Visit Root
          _traverse(node.right)  # Traverse Right
      _traverse(root)
      return result
  ```
  *Iterative (using Stack):*
  ```python
  def inorder_iterative(root: TreeNode) -> list[int]:
      result = []
      stack = []
      current = root
      while current or stack:
          # Go left as far as possible
          while current:
              stack.append(current)
              current = current.left
          # Current is now None, pop from stack (visit node)
          current = stack.pop()
          result.append(current.val) # Visit Root
          # Move to the right subtree
          current = current.right
      return result
  ```

- **3. Postorder Traversal (Left-Right-Root):**

  *Recursive:*
  ```python
  def postorder_recursive(root: TreeNode) -> list[int]:
      result = []
      def _traverse(node):
          if not node:
              return
          _traverse(node.left)   # Traverse Left
          _traverse(node.right)  # Traverse Right
          result.append(node.val) # Visit Root
      _traverse(root)
      return result
  ```
  *Iterative (using Two Stacks or Modified Preorder):*
  ```python
  # Method 1: Using two stacks
  def postorder_iterative_two_stacks(root: TreeNode) -> list[int]:
      if not root: return []
      s1, s2 = [root], []
      while s1:
          node = s1.pop()
          s2.append(node.val) # Store in reverse order
          if node.left: s1.append(node.left) # Push left then right
          if node.right: s1.append(node.right)
      return s2[::-1] # Reverse the second stack content

  # Method 2: Modified Preorder (Visit Root, push Right, push Left -> results in Root-Right-Left, then reverse)
  def postorder_iterative_modified_preorder(root: TreeNode) -> list[int]:
      if not root: return []
      result = []
      stack = [root]
      while stack:
          node = stack.pop()
          result.append(node.val) # Add root to front (conceptually)
          if node.left: stack.append(node.left) # Push left first
          if node.right: stack.append(node.right) # Push right second
      return result[::-1] # Reverse to get Left-Right-Root
  ```

- **4. Level Order Traversal (BFS):**

  *Iterative (using Queue):*
  ```python
  from collections import deque

  def level_order_bfs(root: TreeNode) -> list[list[int]]: # Returns list of levels
      if not root:
          return []

      levels = []
      queue = deque([root])

      while queue:
          level_size = len(queue)
          current_level_nodes = []
          for _ in range(level_size): # Process all nodes at the current level
              node = queue.popleft()
              current_level_nodes.append(node.val)
              if node.left:
                  queue.append(node.left)
              if node.right:
                  queue.append(node.right)
          levels.append(current_level_nodes)

      return levels
  # If just need a flat list:
  # def level_order_flat(root: TreeNode) -> list[int]:
  #     if not root: return []
  #     result = []
  #     queue = deque([root])
  #     while queue:
  #         node = queue.popleft()
  #         result.append(node.val)
  #         if node.left: queue.append(node.left)
  #         if node.right: queue.append(node.right)
  #     return result
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Null Nodes:** Always handle the base case where a node is `None` (or `null`) in both recursive and iterative approaches to prevent errors.
- **Choosing the Right Traversal:** Understand the properties of each traversal and choose the one that fits the problem requirements (e.g., sorted order -> Inorder for BST, level-based processing -> Level Order).
- **Recursive Stack Depth:** Deeply unbalanced trees can cause stack overflow errors with recursive traversals. Iterative approaches avoid this system limit but manage their own stack/queue.
- **Iterative Implementation Details:**
    - *Preorder:* Straightforward stack usage.
    - *Inorder:* Requires careful handling of going left, processing the node after popping, and then going right.
    - *Postorder:* Trickier iteratively. The two-stack or modified-preorder-then-reverse methods are common. A single-stack version exists but is more complex, often tracking visited status.
    - *Level Order:* Requires a queue (FIFO) and often tracking the size of the current level if per-level results are needed.
- **Order Matters:** Ensure the node visit (`result.append(node.val)`) happens at the correct point relative to recursive calls or stack/queue operations for the desired traversal type.
- **Modifying the Tree:** Be cautious if the traversal logic modifies the tree structure (e.g., deleting nodes), as it can interfere with the traversal process itself if not handled carefully.

**Runtime:**
- **Time Complexity:** **O(N)** for all four standard traversals, where N is the number of nodes in the tree. Each node is visited exactly once.
- **Space Complexity:**
    - **Recursive (Pre/In/Post):** O(H), where H is the height of the tree. This space is used by the call stack. In the best case (perfectly balanced tree), H = O(log N). In the worst case (skewed tree), H = O(N).
    - **Iterative (Pre/In/Post):** O(H), where H is the height of the tree. This space is used by the explicit stack. Worst case O(N).
    - **Iterative (Level Order):** O(W), where W is the maximum width (maximum number of nodes at any single level) of the tree. This space is used by the queue. In the worst case (a complete binary tree), the last level can contain roughly N/2 nodes, so W can be O(N).