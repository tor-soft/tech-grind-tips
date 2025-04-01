# Backtracking

**Description:**
Backtracking is a general algorithmic technique for solving problems recursively by trying to build a solution incrementally, one piece at a time, removing those solutions ("backtracking") that fail to satisfy the constraints of the problem at any point in time. It systematically explores the space of all possible solutions by incrementally building candidates. If a partial candidate can't possibly be completed to a valid solution, the algorithm abandons it and tries a different path.

Think of it as exploring a decision tree. At each node, you make a choice and move to the next level. If you reach a leaf node that represents a valid solution, you record it. If you reach a node (leaf or internal) from which no valid solution can be formed, you "backtrack" to the previous node and try a different choice. It's essentially a form of Depth-First Search (DFS) applied to the implicit state-space tree of potential solutions.

**When to use the pattern:**
Backtracking is suitable when you need to explore multiple possibilities to find *some* or *all* solutions that satisfy certain constraints, especially when the solution space can be represented as a sequence of choices.

-   **Generating all possible combinations/permutations/subsets:**
    -   Problems like "Subsets", "Permutations", "Combinations", "Combination Sum".
-   **Constraint Satisfaction Problems (CSPs):**
    -   Problems where you need to find a configuration satisfying given rules, like "N-Queens", "Sudoku Solver".
-   **Pathfinding in a grid or graph where you need to explore possibilities:**
    -   Problems like "Word Search" on a board (finding if a word exists by traversing adjacent cells).
-   **Problems involving decomposition or partitioning:**
    -   Problems like "Palindrome Partitioning" (finding all ways to split a string into palindromic substrings).

**Template(s):**

-   **General Recursive Template (for finding all solutions):**
    This is the most common structure. You maintain the current state (e.g., the path built so far) and explore possibilities from there.

    ```python
    def backtrack(params...):
        # Base Case 1: Check if the current state represents a valid solution
        if is_solution(current_state):
            add_solution(result, current_state) # Often requires making a copy
            # Optionally return if only one solution is needed, or continue searching
            # return True # If only one needed
            return # If searching for all

        # Base Case 2: Check if the current state is invalid or cannot lead to a solution (pruning)
        if not is_valid(current_state):
            return # Prune this path

        # Recursive Step: Iterate through possible choices/moves
        for choice in get_possible_choices(current_state):
            # 1. Make the choice: Update the current state
            make_choice(current_state, choice)

            # 2. Recurse: Explore further down this path
            backtrack(params...) # Pass updated state implicitly or explicitly

            # 3. Undo the choice (Backtrack): Restore the state for the next iteration
            undo_choice(current_state, choice)

    # --- Example Setup ---
    result = [] # To store all found solutions
    initial_state = ... # Define the starting state (e.g., empty path)

    # --- Initial Call ---
    backtrack(initial_state, ...)
    # return result
    ```

-   **Example: Generating Subsets (Illustrative)**

    ```python
    def find_subsets(nums):
        results = []
        path = []

        def backtrack(start_index):
            # 1. Add the current subset path as a potential solution
            # Make a copy because 'path' will be modified later
            results.append(list(path))

            # Base case for pruning is implicitly handled by the loop range
            # If start_index >= len(nums), the loop won't execute

            # 2. Iterate through choices (elements to include starting from start_index)
            for i in range(start_index, len(nums)):
                # 3. Make choice: Add the element to the current path
                path.append(nums[i])

                # 4. Recurse: Explore subsets starting with the current path
                # Move to the next index to avoid duplicate elements in the subset path
                backtrack(i + 1)

                # 5. Undo choice: Remove the element to backtrack
                path.pop()

        backtrack(0) # Start the process from the first element
        return results

    # Example usage:
    # nums = [1, 2, 3]
    # print(find_subsets(nums))
    # Output: [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]
    ```

**Key Considerations & Screw-ups to Avoid:**

-   **Forgetting the Backtrack Step:** The most critical error is failing to undo the choice (`undo_choice` / `path.pop()`). This corrupts the state for subsequent iterations at the same level of recursion.
-   **Incorrect Base Cases:** Ensure your base cases correctly identify a complete solution and correctly prune invalid paths. Missing or incorrect base cases can lead to infinite recursion (stack overflow) or incorrect/incomplete results.
-   **Modifying Shared State:** When adding a found solution (like a list `path`) to your results, make sure you add a *copy* (e.g., `results.append(list(path))` or `results.append(path[:])`). Otherwise, subsequent backtracking steps will modify the list already stored in `results`.
-   **Inefficient Pruning:** Sometimes, you can check constraints *before* making the recursive call to prune invalid branches earlier, improving performance.
-   **Deep Recursion:** Problems with very deep recursion levels might lead to stack overflow errors. While less common in typical interviews, iterative approaches using an explicit stack exist but are generally more complex to implement than recursion for backtracking.
-   **Duplicate Solutions:** In problems like combinations or subsets with duplicate input numbers, ensure your logic (e.g., skipping duplicates in the choice loop, sorting input) prevents generating the same solution multiple times if only unique solutions are required.

**Runtime:**
The runtime complexity of backtracking is generally related to the number of nodes in the state-space tree explored.

-   It's often exponential: **O(b^d)**, where `b` is the branching factor (maximum number of choices at any step) and `d` is the maximum depth of the recursion (length of the longest path).
-   Sometimes, the work done at each step (like copying a solution) adds a factor. For example:
    -   Generating permutations of N elements: O(N * N!) - roughly N! solutions, takes O(N) to copy each.
    -   Generating subsets of N elements: O(N * 2^N) - roughly 2^N subsets, takes O(N) to copy each.
-   The actual performance heavily depends on how effectively constraints are used to **prune** the search space. Good pruning can make a theoretically exponential algorithm feasible for practical input sizes.