# Greedy Algorithms

**Description:**
A Greedy algorithm builds up a solution piece by piece, always choosing the next piece that offers the most obvious and immediate benefit. It makes the choice that seems best at the current moment (locally optimal) with the hope that these choices will lead to a globally optimal solution for the problem. Unlike Dynamic Programming, it doesn't reconsider previous choices. Greedy algorithms are often intuitive and efficient but don't guarantee the optimal solution for all problems; their correctness often needs careful proof (e.g., using an exchange argument) or relies on the problem having a specific structure (like the greedy-choice property and optimal substructure).

**When to use the pattern:**
- Problems asking for a minimum or maximum result where you can make a sequence of choices.
- When a local optimal choice (e.g., picking the smallest/largest, earliest finish time, highest value-to-weight ratio) seems like it might lead to the global optimum.
- Activity Selection Problem: Select the maximum number of non-overlapping activities. (Greedy choice: pick the activity that finishes earliest).
- Coin Change Problem (Canonical Coin Systems): Give minimum number of coins for a certain amount. (Greedy choice: use as many of the largest denomination coins as possible). *Note: This only works for standard coin systems, not all arbitrary systems.*
- Fractional Knapsack Problem: Maximize value within a weight limit, items are divisible. (Greedy choice: pick items with the highest value-per-unit-weight first).
- Minimum Spanning Tree Algorithms: Prim's and Kruskal's algorithms have greedy components (Prim's: always add the cheapest edge connecting the tree to a non-tree vertex; Kruskal's: always add the cheapest edge that doesn't form a cycle).
- Huffman Coding: Building an optimal prefix code tree. (Greedy choice: merge the two nodes with the lowest frequencies).
- Task Scheduling: Minimizing maximum lateness or maximizing completed tasks by a deadline. (Often involves sorting by deadlines or durations).

**Template(s):**
Greedy algorithms don't have a single rigid code template like Sliding Window or Two Pointers, as the specific "greedy choice" varies greatly. However, a general structure often involves sorting and iterating:

- **Conceptual Template:**

  ```python
  def greedy_algorithm(items): # 'items' could be tasks, intervals, weights, etc.
      """
      Conceptual template for a greedy algorithm.
      The specific logic depends heavily on the problem.
      """
      # 1. Preprocessing / Sorting (Crucial Step!)
      # Sort items based on the chosen greedy criterion
      # (e.g., by end time, start time, value/weight ratio, weight)
      items.sort(key=lambda x: get_greedy_metric(x)) # Replace get_greedy_metric

      result = initialize_result() # e.g., 0, [], {}
      current_state = initialize_state() # e.g., current_time, current_weight

      # 2. Iteration and Greedy Choice
      for item in items:
          # Check if the current item can be chosen based on the current state
          # and the problem constraints
          if can_choose(item, current_state):
              # Make the greedy choice: select the item
              update_result(result, item)
              # Update the state based on the chosen item
              update_state(current_state, item)
              # Optional: sometimes you skip items if they conflict,
              # even if they *could* fit otherwise (like Activity Selection)

          # Else: cannot choose this item (e.g., conflicts, exceeds capacity)
          # Continue to the next item

      # 3. Return Result
      return result

  # Helper functions (need to be implemented based on the specific problem)
  def get_greedy_metric(item): raise NotImplementedError
  def initialize_result(): raise NotImplementedError
  def initialize_state(): raise NotImplementedError
  def can_choose(item, state): raise NotImplementedError
  def update_result(result, item): raise NotImplementedError
  def update_state(state, item): raise NotImplementedError

  # Example: Activity Selection (Maximize number of activities)
  # - get_greedy_metric: return activity's end time
  # - initialize_result: return 0 (count) or [] (list of activities)
  # - initialize_state: return negative infinity (last_finish_time)
  # - can_choose: return item.start_time >= last_finish_time
  # - update_result: increment count or append activity
  # - update_state: set last_finish_time = item.end_time
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Proof of Correctness:** The biggest pitfall! A greedy strategy *seems* right but might fail on certain inputs. If you're not sure the problem has a known greedy solution, be skeptical. Try to think of counterexamples. Formal proofs (like exchange arguments) are often needed but usually beyond typical interview scope unless it's a well-known greedy problem.
- **Choosing the Wrong Greedy Criterion:** Selecting an inappropriate metric for making the local choice is common. For example, in Activity Selection, sorting by start time or duration is incorrect; sorting by finish time works.
- **Incorrect Sorting:** Sorting by the wrong key or in the wrong order (ascending vs. descending) will break the algorithm.
- **Handling Edge Cases:** Test with empty input, single item input, cases where no solution is possible, and cases where all items can be chosen.
- **State Management:** Ensure the `current_state` (tracking constraints like time, weight, conflicts) is updated correctly after each greedy choice.
- **Confusing with Dynamic Programming:** Some optimization problems require DP because a locally optimal choice might prevent reaching the best overall solution later. If choices depend complexly on previous choices, or if you need to explore multiple options at each step, DP might be necessary.
- **Integer vs. Fractional Knapsack:** Standard 0/1 Knapsack (items are indivisible) is typically solved with DP, not greedy. Fractional Knapsack (items are divisible) *can* be solved greedily by value/weight ratio.

**Runtime:**
- **Time Complexity:** Highly dependent on the specific algorithm.
    - If sorting is the dominant step: **O(N log N)**, where N is the number of items/elements. The subsequent iteration is often O(N).
    - If no sorting is needed or sorting is cheaper than the selection process: Can be **O(N)** (e.g., simple coin change).
    - If a priority queue is used (e.g., Prim's, Dijkstra's): Can be **O(E log V)**, **O((V+E) log V)**, or **O(E + V log V)** depending on the graph representation and priority queue implementation, where V is vertices and E is edges.
- **Space Complexity:** Usually **O(1)** if the process is done in-place or only requires a few variables. Can be **O(N)** if a copy of the data is needed for sorting or to store the result. For graph algorithms, space depends on graph representation (adjacency list/matrix) and auxiliary structures (priority queue), often **O(V + E)** or **O(V)**.