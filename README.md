**1. Two Pointers - The Cheap Workhorse**

*   **What:** Simple index wrangling (usually two) in arrays/strings/lists. Exploits some kind of ordering or condition. Dead simple, surprisingly effective.
*   **When to Reach for It (Don't Overthink):**
    *   **Sorted Arrays:** Target sums (pairs, triplets), finding ranges, anything where sorted order lets you discard chunks. Mandatory first thought if `sorted` is mentioned.
    *   **In-Place Array Modification:** *Crucial*. Removing duplicates, moving zeroes, sorting colors (Dutch National Flag). One pointer (`write_idx` or `slow`) marks the end of the "good" section, the other (`read_idx` or `fast`) scans. `arr[write_idx] = arr[read_idx]` based on some condition. Burn this into your skull.
    *   **Palindromes:** Obvious check from opposite ends.
    *   **Linked Lists:** Cycle detection (Floyd's Tortoise & Hare - slow/fast), finding the middle node (slow/fast), finding intersections. Non-negotiable for list problems.
    *   **Comparing from Opposite Ends/Different Speeds:** Finding pairs, container with most water, trapping rain water (sometimes).
    *   **Merging Sorted Data (In-Place):** Sometimes you merge B into A (if A has space) by working *backwards* from the end of both arrays/lists.
*   **Template (Opposite Ends - Target Sum):**
    ```python
    def two_pointers_opposite(arr, target): # Assumes arr is SORTED
        left = 0
        right = len(arr) - 1
        while left < right: # Or left <= right depending on if element can pair with itself
            current_sum = arr[left] + arr[right]
            if current_sum == target:
                # Found it. Return, store, increment/decrement BOTH, depends on problem
                return [left, right] # Example
                # left += 1 # Often needed if duplicates allowed / multiple pairs needed
                # right -= 1
            elif current_sum < target:
                left += 1 # Need bigger sum
            else: # current_sum > target
                right -= 1 # Need smaller sum
        return [] # Or appropriate "not found" value
    ```
*   **Key Considerations & Screw-ups to Avoid:**
    *   **SORTING:** Is the array sorted? If not, can you sort it? (Cost: O(N log N)). This is often the *first* step.
    *   **Boundaries:** `left < right` vs `left <= right`? Think: can an element be paired with itself? Does the window need to have size >= 1 or >= 2? Get this wrong, you fail edge cases.
    *   **In-Place Movement:** Understand the `slow` (write/anchor) vs `fast` (read/scanner) logic cold. Practice "Move Zeroes."
    *   **Linked Lists:** Null checks. Everywhere.

**2. Sliding Window - The Subarray Slayer**

*   **What:** A dynamic view (window) over a contiguous part of an array/string. Expand right, conditionally shrink left. Optimizes brute-force subarray/substring checks from O(N^2)/O(N^3) down to O(N).
*   **When to Unleash It:**
    *   **Min/Max/Avg/Count of a Contiguous Subarray/Substring:** The absolute classic use case (max sum, min length, average).
    *   **Constraints on Subarrays:** "Longest substring with at most K distinct characters," "Smallest subarray with sum >= X," "Subarray containing specific elements/counts."
    *   **Frequency Matching:** Finding substrings that are permutations/anagrams of a pattern string. Usually needs a hash map *inside* the window logic. (Window + Map = Common Combo)
    *   **Fixed vs. Variable Size:** Know which one you need. Fixed is simpler (just maintain window size `k`). Variable requires a `while` loop for shrinking based on a condition.
    *   **Problem Reframing:** Sometimes the *complement* is easier. "Max points from ends" = Total sum - "Min sum subarray of size n-k".
*   **Template (Variable Size - Min Length Subarray Sum >= Target):**
    ```python
    import math

    def sliding_window_variable(arr, target):
        left = 0
        current_sum = 0 # Or other state: map for counts, etc.
        min_length = math.inf # Or other result accumulator

        for right in range(len(arr)):
            # EXPAND WINDOW: Add the right element to the current state
            current_sum += arr[right]

            # SHRINK WINDOW: Check if condition allows/requires shrinking
            while current_sum >= target: # Or other condition (e.g., distinct_chars > k)
                # PROCESS/UPDATE RESULT: Do this *before* shrinking alters the valid state
                min_length = min(min_length, right - left + 1)

                # SHRINK: Remove the left element from the current state
                current_sum -= arr[left]
                left += 1

        return min_length if min_length != math.inf else 0 # Handle "not found"
    ```
*   **Key Considerations & Screw-ups to Avoid:**
    *   **State Management:** What exactly defines the window's state (sum, char counts map, distinct elements set)? Get this wrong, the logic collapses.
    *   **Shrink Condition:** The `while` loop condition for shrinking is critical. Is it `>` target, `>=` target, `==` target? Does it depend on map size?
    *   **Update Timing:** Update your result (min_length, max_sum, count) at the right spot – usually *inside* the `while` loop just *before* shrinking, or sometimes after the `for` loop for fixed windows.
    *   **Edge Cases:** Empty input, `k=0`, target unachievable.

**3. Breadth-First Search (BFS) - The Shortest Path Seeker (Unweighted)**

*   **What:** Level-by-level exploration. Uses a `deque` (Queue) to keep track of the frontier. Finds the shortest path in terms of *number of steps/edges*.
*   **When to Deploy It:**
    *   **Shortest Path in UNWEIGHTED Graphs/Grids:** This is the #1 trigger. If weights are involved, you need Dijkstra (often with a Heap), not BFS.
    *   **Level Order Traversal (Trees):** Standard tree traversal. Variations: zigzag, level averages, right-side view.
    *   **Connectivity:** Can you reach B from A? Is the graph connected?
    *   **Minimum Steps/Transformations:** "Word Ladder," "Minimum Knight Moves," reaching a target state with fewest operations. These are often implicit graphs where nodes are states.
    *   **Multi-Source BFS:** Start exploration from *multiple* points simultaneously (e.g., "Rotting Oranges," distance to nearest 'X'). Initialize queue with all sources.
*   **Template (Grid Shortest Path):**
    ```python
    from collections import deque

    def bfs_shortest_path(grid, start_row, start_col): # Example: find path to target 'G'
        rows, cols = len(grid), len(grid[0])
        queue = deque([(start_row, start_col, 0)]) # (row, col, distance)
        visited = set([(start_row, start_col)]) # CRITICAL: Avoid cycles/redundancy
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)] # Adapt for diagonals etc.

        while queue:
            r, c, distance = queue.popleft() # FIFO - Explores level by level

            if grid[r][c] == 'G': # Target condition
                 return distance

            # Explore neighbors
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                # Check bounds, obstacles ('#'), and if already visited
                if 0 <= nr < rows and 0 <= nc < cols and \
                   grid[nr][nc] != '#' and (nr, nc) not in visited:
                    visited.add((nr, nc))
                    queue.append((nr, nc, distance + 1)) # Add to *end* of queue

        return -1 # Target not reachable
    ```
*   **Key Considerations & Screw-ups to Avoid:**
    *   **`deque`, not `list`:** `list.pop(0)` is O(N). `deque.popleft()` is O(1). Using a list kills performance. Non-negotiable.
    *   **`visited` Set:** Absolutely essential for graphs/grids to prevent infinite loops and re-visiting. Forget this, your code hangs or TLEs. Add coordinates/nodes to `visited` *when you add them to the queue*, not when you pop them.
    *   **State in Queue:** What needs to be stored? Coordinates? Distance? Path? Current value?
    *   **Unweighted vs Weighted:** If edge weights matter, BFS gives the wrong answer. Think Dijkstra.

**4. Depth-First Search (DFS) - The Explorer / Backtracker**

*   **What:** Go deep first, backtrack when you hit a dead end or finish exploring a branch. Uses Recursion (implicitly uses call stack) or an explicit Stack.
*   **When to Dig Deep:**
    *   **Finding *Any* Path / Checking Connectivity:** Does a path exist? (BFS also works, DFS might be simpler code recursively).
    *   **Exploring *All* Possibilities (Backtracking):** Generating permutations, combinations, subsets, solving Sudoku, N-Queens. DFS is the engine for backtracking. Mark visited/used, explore, unmark.
    *   **Cycle Detection:** Keep track of nodes currently in the recursion stack (visiting state) vs fully visited.
    *   **Topological Sort:** For Directed Acyclic Graphs (DAGs) - ordering tasks with dependencies (Course Schedule). Use visited + visiting states.
    *   **Graph Traversal / Structure Analysis:** Finding connected components ("Number of Islands" - often DFS/BFS), Flood Fill.
    *   **Tree Traversals:** In-order, Pre-order, Post-order are all DFS variations defined by *when* you process the node relative to recursive calls.
*   **Template (Recursive Grid Exploration / Flood Fill):**
    ```python
    def dfs_recursive(grid, r, c, visited, target_color, new_color): # Example flood fill
        rows, cols = len(grid), len(grid[0])
        # BASE CASES: Out of bounds, wrong color, already visited
        if not (0 <= r < rows and 0 <= c < cols and \
                (r, c) not in visited and grid[r][c] == target_color):
            return

        visited.add((r, c)) # Mark as visited
        grid[r][c] = new_color # Process node (change color)

        # Explore neighbors recursively
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
        for dr, dc in directions:
            dfs_recursive(grid, r + dr, c + dc, visited, target_color, new_color)

    # Initial call requires setup:
    # visited = set()
    # start_r, start_c = ...
    # initial_color = grid[start_r][start_c]
    # dfs_recursive(grid, start_r, start_c, visited, initial_color, desired_color)
    ```
*   **Key Considerations & Screw-ups to Avoid:**
    *   **`visited` Set:** Still crucial for graphs to avoid infinite loops unless it's a tree.
    *   **Recursion Depth:** Recursive DFS can hit Python's (or other languages') recursion depth limit on large/deep inputs. Iterative DFS with an explicit stack avoids this but can be slightly more complex to code.
    *   **Backtracking State:** For backtracking problems, correctly managing the "state" (current permutation, path, board state) and *undoing* changes upon returning (un-marking visited, removing element from path) is critical. Get the backtrack step wrong, and you explore invalid states.
    *   **Implicit vs Explicit Stack:** Understand the trade-offs. Recursion is often cleaner but has limits.

**5. Heap (Priority Queue) - The Top K / Min/Max Finder**

*   **What:** Binary tree structure (usually implemented with an array) that keeps the min (or max) element easily accessible (O(1) lookup, O(log N) insertion/deletion).
*   **When to Prioritize It:**
    *   **"Top K" Problems:** Largest, smallest, most frequent elements. Use a min-heap of size K for "Top K largest," max-heap of size K for "Top K smallest."
    *   **Kth Largest/Smallest Element:** Find the Kth order statistic efficiently (O(N log K)), better than sorting (O(N log N)) if K << N.
    *   **Merging K Sorted Lists:** Use a min-heap to keep track of the smallest current element from each list.
    *   **Efficient Scheduling / Intervals:** "Meeting Rooms II" (min-heap of end times), task scheduling based on priority/expiry.
    *   **Median of a Data Stream:** Classic two-heap problem (max-heap for lower half, min-heap for upper half).
    *   **Dijkstra's Algorithm (Weighted Shortest Path):** Uses a min-heap to efficiently select the next node to visit based on the *minimum known distance*. BFS won't work here.
*   **Template (Top K Largest Elements):**
    ```python
    import heapq

    def top_k_largest(nums, k):
        # Python's heapq is a MIN-heap. We keep the K largest elements.
        # The smallest element in the heap is the Kth largest overall so far.
        min_heap = []
        for num in nums:
            heapq.heappush(min_heap, num)
            # If heap size exceeds K, remove the smallest element (root of min-heap)
            if len(min_heap) > k:
                heapq.heappop(min_heap)
        # The heap now contains the K largest elements.
        # Return them, or min_heap[0] if you need the Kth largest specifically.
        return min_heap
    ```
*   **Key Considerations & Screw-ups to Avoid:**
    *   **Min-Heap vs Max-Heap:** Python's `heapq` is a min-heap. To simulate a max-heap, push `-num` and negate when popping/reading. Don't mess this up.
    *   **Complexity:** O(N log K) for Top K, O(N log N) if K approaches N. Know the trade-off vs sorting.
    *   **Heapify:** `heapq.heapify(list)` builds a heap in O(N) time, which can be faster than N pushes if you have all elements upfront.
    *   **Custom Objects:** If you heap custom objects, you need to define `__lt__` (less than) or use tuples `(priority, item)`.

**6. Hash Map / Dictionary - The God Mode Lookup**

*   **What:** Key-value storage with O(1) *average* time complexity for insertion, deletion, and lookup. The duct tape and WD-40 of algorithms.
*   **When to Use It (Answer: Almost Fucking Always):**
    *   **Frequency Counting:** Characters, numbers, objects. `Counter(items)` or manual `map[item] = map.get(item, 0) + 1`.
    *   **Checking Existence / Duplicates:** "Two Sum" pattern (store `num -> index`, check for `target - num`). Find duplicates, check constraints quickly. Use `set` if you only need existence, not values/counts.
    *   **Grouping / Categorization:** "Group Anagrams" (key: sorted string, value: list of anagrams). Group items by any hashable property.
    *   **Caching / Memoization (Dynamic Programming):** Store results of expensive subproblems (`memo[state] = result`) to avoid re-computation. Key = subproblem state, Value = result.
    *   **Representing Sparse Data / Graphs:** Adjacency lists for graphs (`graph[node] = [neighbor1, neighbor2]`).
    *   **Constraint Checking:** "Valid Sudoku" - use sets/maps to track seen numbers in rows, columns, boxes.
*   **Template (Two Sum):**
    ```python
    def two_sum(nums, target):
        seen_map = {} # Stores {number: index}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in seen_map:
                # Found the pair
                return [seen_map[complement], i]
            # Only add to map AFTER checking - avoids using the same element twice
            seen_map[num] = i
        return [] # No solution found
    ```
*   **Key Considerations & Screw-ups to Avoid:**
    *   **Space Complexity:** The cost of O(1) time is O(N) space (usually). Be aware of the trade-off.
    *   **Hashable Keys:** Keys *must* be immutable (numbers, strings, tuples). You can't use lists or dictionaries as keys directly.
    *   **Collisions (Rare Interview Issue):** Worst-case lookup can degrade to O(N) with pathologically bad hash collisions, but assume O(1) average unless the interviewer specifically probes this.
    *   **Order:** Standard Python dicts (3.7+) maintain insertion order. If you need sorted order, use `collections.OrderedDict` (less common now) or sort keys/items separately.

**7. Merge Intervals**

*   **What:** Combining overlapping intervals into larger, merged intervals.
*   **When:**
    *   Scheduling problems (meeting rooms, bookings).
    *   Finding overlapping ranges.
    *   Geometric problems involving segments on a line.
*   **Template:**
    ```python
    def merge_intervals(intervals):
        if not intervals:
            return []

        # CRITICAL: Sort intervals by start time
        intervals.sort(key=lambda x: x[0])

        merged = [intervals[0]]

        for i in range(1, len(intervals)):
            current_start, current_end = intervals[i]
            last_merged_start, last_merged_end = merged[-1]

            # Check for overlap
            if current_start <= last_merged_end:
                # Merge: update the end time of the last merged interval
                merged[-1] = [last_merged_start, max(last_merged_end, current_end)]
            else:
                # No overlap, add the current interval as a new merged interval
                merged.append([current_start, current_end])

        return merged
    ```
*   **Key Considerations:** Sorting by start time is **mandatory**. The core logic is comparing the current interval's start with the *previous merged interval's end*. Handle empty input edge case.

**8. Backtracking (DFS on State Space)**

*   **What:** Exploring all possible solutions by trying choices and undoing them (backtracking) if they don't lead to a solution. Essentially, a structured DFS for building solutions incrementally.
*   **When:**
    *   Generating permutations, combinations, subsets.
    *   Solving puzzles like Sudoku, N-Queens.
    *   Finding paths in a maze or grid where you need to explore options.
*   **Template (e.g., Generating Subsets):**
    ```python
    def backtrack_subsets(nums):
        result = []
        subset = []

        def find_subsets(start_index):
            # 1. Add the current state (subset) to results
            result.append(list(subset)) # Important: use list() for a copy

            # 2. Iterate through choices
            for i in range(start_index, len(nums)):
                # 3. Make a choice
                subset.append(nums[i])

                # 4. Recurse (explore further)
                find_subsets(i + 1) # Move start index to avoid duplicates/reusing elements

                # 5. Backtrack (undo the choice)
                subset.pop()

        find_subsets(0)
        return result
    ```
*   **Key Considerations:** Identify the "state" being built (e.g., `subset`, `current_permutation`, `board_configuration`). Define the "choices" at each step. Have a base case (e.g., target length reached, end of input). The `append`/`recurse`/`pop` sequence is the core backtracking mechanism. Pass necessary state/index into the recursive call. Be careful about making copies when adding solutions to `result`.

**9. Linked List Manipulation (Fast & Slow Pointers / Reversal)**

*   **What:** Techniques specific to linked list structures.
*   **When:**
    *   Finding the middle node.
    *   Detecting cycles (Floyd's Tortoise and Hare).
    *   Reversing a linked list (in-place).
    *   Merging sorted linked lists.
*   **Template (Cycle Detection - Floyd's Tortoise & Hare):**
    ```python
    class ListNode: # Assuming definition exists
        # ... val, next ...

    def has_cycle(head: ListNode) -> bool:
        slow = head
        fast = head

        while fast and fast.next: # Ensure fast can advance twice
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                return True # Cycle detected
        return False # Reached end (no cycle)
    ```
*   **Template (In-place Reversal):**
    ```python
    def reverse_list(head: ListNode) -> ListNode:
        prev = None
        current = head
        while current:
            next_node = current.next # Store next node
            current.next = prev      # Reverse pointer
            prev = current           # Move prev forward
            current = next_node      # Move current forward
        return prev # New head is the previous tail
    ```
*   **Key Considerations:** Linked lists require careful pointer manipulation. Draw pictures! Null checks (`if node:`, `while node:`, `if node.next:`) are critical. Keep track of `prev`, `current`, `next` pointers meticulously during reversal.

**10. Tree Traversals (Explicit Orders)**

*   **What:** Specific ways to visit nodes in a tree beyond basic BFS/DFS.
*   **When:** Problems explicitly require in-order, pre-order, or post-order processing (e.g., serializing/deserializing a BST, validating BST properties).
*   **Template (Recursive In-order Traversal - yields sorted list for BST):**
    ```python
    class TreeNode: # Assuming definition exists
        # ... val, left, right ...

    def inorder_traversal(root: TreeNode):
        result = []
        def traverse(node):
            if not node:
                return
            traverse(node.left)
            result.append(node.val) # Process node *after* left subtree
            traverse(node.right)
        traverse(root)
        return result
    ```
*   **Templates (Recursive Pre/Post-order):** Change the line `result.append(node.val)`:
    *   **Pre-order:** Process node *before* visiting children. `result.append(node.val); traverse(node.left); traverse(node.right)`
    *   **Post-order:** Process node *after* visiting *both* children. `traverse(node.left); traverse(node.right); result.append(node.val)`
*   **Key Considerations:** Understand when each order is useful. In-order for BST sort/validation. Pre-order for copying trees or prefix expressions. Post-order for deletion or postfix expressions. Recursive is usually simplest to write. Iterative versions exist using stacks but are more complex to template quickly.

**11. Binary Search on Answer**

*   **What:** Applying binary search not on the input array itself, but on the *range of possible answers* to the problem.
*   **When:**
    *   Problems asking for the **minimum/maximum value** (e.g., minimum time, maximum capacity, minimum length) that satisfies a certain **monotonic condition**.
    *   The condition function `can_achieve(value)` is checkable (often in O(N) or O(N log N)) and has a monotonic property (e.g., if you `can_achieve` it with value `X`, you can also achieve it with `X+1` or `X-1` depending on min/max).
*   **Template (Find Minimum value `X` such that `check(X)` is True):**
    ```python
    def binary_search_on_answer(params):
        low = # Minimum possible answer value
        high = # Maximum possible answer value
        best_ans = -1 # Or suitable default

        def check(mid_value):
            # Logic to determine if 'mid_value' is a possible/valid answer
            # Often involves iterating through input using 'mid_value' as a constraint
            # Returns True if valid/possible, False otherwise
            pass # Implement this check function

        while low <= high:
            mid = low + (high - low) // 2
            if check(mid):
                # 'mid' is a possible answer. Try for a potentially smaller/better one.
                best_ans = mid
                high = mid - 1 # Search in the lower half
            else:
                # 'mid' is too small/not possible. Need a larger value.
                low = mid + 1 # Search in the upper half

        return best_ans
    ```
*   **Key Considerations:** Identifying the monotonic `check` function is key. Determine the correct search space boundaries (`low`, `high`). Carefully decide whether to update `low` or `high` and whether to store the answer (`best_ans = mid`) when `check(mid)` is true or false, depending on whether you're minimizing or maximizing.

**12. Trie (Prefix Tree)**

*   **What:** A tree-like data structure for efficient prefix-based lookups. Each node represents a character, paths represent words.
*   **When:**
    *   Autocomplete / Typeahead suggestions.
    *   Spell checking.
    *   Finding words matching a prefix.
    *   IP routing (longest prefix match).
*   **Template (Basic Implementation):**
    ```python
    class TrieNode:
        def __init__(self):
            self.children = {} # char -> TrieNode
            self.is_end_of_word = False

    class Trie:
        def __init__(self):
            self.root = TrieNode()

        def insert(self, word: str):
            node = self.root
            for char in word:
                if char not in node.children:
                    node.children[char] = TrieNode()
                node = node.children[char]
            node.is_end_of_word = True

        def search(self, word: str) -> bool: # Search full word
            node = self.root
            for char in word:
                if char not in node.children:
                    return False
                node = node.children[char]
            return node.is_end_of_word

        def starts_with(self, prefix: str) -> bool: # Search prefix
            node = self.root
            for char in prefix:
                if char not in node.children:
                    return False
                node = node.children[char]
            return True
    ```
*   **Key Considerations:** Each node needs a way to store children (dict is common) and a flag indicating if a word ends there. Understand the difference between `search` (exact word match) and `starts_with` (prefix match).

**13. Basic Dynamic Programming (Top-Down Memoization)**

*   **What:** Breaking a problem into overlapping subproblems and storing the results of subproblems to avoid re-computation. Top-down starts from the main problem and uses recursion + caching.
*   **When:**
    *   Optimization problems (min/max cost, count ways).
    *   Problems where the solution can be built from solutions to smaller instances of the *same problem* (e.g., `fib(n)` depends on `fib(n-1)` and `fib(n-2)`).
    *   Overlapping subproblems are present (naive recursion would recompute same values many times).
*   **Template (Fibonacci Example):**
    ```python
    memo = {} # Use a dictionary for caching results

    def fib_memoized(n):
        if n in memo:
            return memo[n]
        if n <= 1:
            return n

        # Compute and store before returning
        memo[n] = fib_memoized(n - 1) + fib_memoized(n - 2)
        return memo[n]

    # Need to clear memo if calling multiple times for different problems or make it part of a class.
    # result = fib_memoized(target_n)
    ```
*   **Template (Generic Structure):**
    ```python
    memo = {} # Or initialize within a class __init__

    def dp_solve(state): # 'state' represents the subproblem (e.g., index, remaining capacity)
        if state in memo:
            return memo[state]

        if base_case(state): # Base case(s) for the recursion
            return base_case_value(state)

        # Compute result by making recursive calls for smaller subproblems
        # result = combine(dp_solve(next_state1), dp_solve(next_state2), ...)
        result = # ... calculation involving recursive calls ...

        memo[state] = result # Store result before returning
        return result

    # Initial call: dp_solve(initial_state)
    ```
*   **Key Considerations:** Identify the state(s) needed to define a subproblem uniquely (these become the keys in `memo`). Define the base case(s). Determine the recursive relation (how to compute the current state from smaller states). Make sure to store (`memoize`) the result before returning. Top-down is often more intuitive to derive from a pure recursive solution.

**The 'Memorization' Strategy - Don't Be an Idiot**

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
