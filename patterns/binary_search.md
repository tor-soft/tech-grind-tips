# Binary Search

**Description:**
Binary Search is an efficient algorithm for finding an item from a **sorted** list of items. It works by repeatedly dividing the search interval in half. If the value of the search key is less than the item in the middle of the interval, narrow the interval to the lower half. Otherwise, narrow it to the upper half. Repeat this process until the value is found or the interval is empty.

A powerful extension is "Binary Search on the Answer Space", where you don't search for an element in a given array, but rather search for a potential *answer* within a range of possible values, using a check function to determine if a candidate answer is feasible.

**When to use the pattern:**
- Searching for an element in a **sorted** array or list.
- Finding the smallest/largest element that satisfies a certain condition in a sorted array (e.g., first occurrence, last occurrence, first element greater than target).
- Searching in a rotated sorted array (requires modification of the standard binary search).
- Finding a peak element in an array (where elements increase then decrease).
- Optimizing solutions where you need to find a minimum/maximum value that satisfies a condition, and the condition exhibits a monotonic property (if a value `x` works, then `x+1` also works, or if `x` works, then `x-1` also works). This is "Binary Search on the Answer Space". Examples:
    - Minimum time to complete tasks given constraints.
    - Maximum possible value under certain limitations (e.g., largest subarray sum not exceeding X).
    - Finding square roots or performing division efficiently.
    - Problems solvable with a brute-force check, where increasing the input parameter monotonically affects the check result (e.g., check if it's possible to achieve result `k`).

**Template(s):**
- **Standard Binary Search (finding target):**

  ```python
  def binary_search(arr: list[int], target: int) -> int:
      """
      Standard binary search to find the index of a target in a sorted array.

      Args:
          arr: A sorted list of integers.
          target: The integer to search for.

      Returns:
          The index of the target if found, otherwise -1.
      """
      left, right = 0, len(arr) - 1

      while left <= right:
          # Calculate mid point carefully to avoid potential overflow
          mid = left + (right - left) // 2

          if arr[mid] == target:
              return mid # Target found
          elif arr[mid] < target:
              # Target is in the right half
              left = mid + 1
          else: # arr[mid] > target
              # Target is in the left half
              right = mid - 1

      return -1 # Target not found

  # Example Usage:
  # arr = [-1, 0, 3, 5, 9, 12], target = 9
  # print(binary_search(arr, target)) # Output: 4
  # arr = [-1, 0, 3, 5, 9, 12], target = 2
  # print(binary_search(arr, target)) # Output: -1
  ```

- **Binary Search for Lower Bound (First Occurrence / First element >= target):**

  ```python
  def lower_bound(arr: list[int], target: int) -> int:
      """
      Finds the index of the first element that is >= target.

      Args:
          arr: A sorted list of integers.
          target: The target value.

      Returns:
          The index of the lower bound. If all elements are smaller,
          returns len(arr).
      """
      left, right = 0, len(arr) # Note: right is len(arr), not len(arr) - 1
      result = len(arr) # Default if target is larger than all elements

      while left < right: # Note: condition is left < right
          mid = left + (right - left) // 2
          if arr[mid] >= target:
              # Found a potential candidate, try searching in the left half
              # to find an earlier one. Store this index.
              result = mid
              right = mid # Note: right = mid, not mid - 1
          else: # arr[mid] < target
              # Candidate must be in the right half
              left = mid + 1

      return result

  # Example Usage:
  # arr = [1, 2, 2, 2, 4, 5], target = 2 -> returns 1 (index of first 2)
  # arr = [1, 2, 2, 2, 4, 5], target = 3 -> returns 4 (index of 4, the first element >= 3)
  # arr = [1, 2, 4, 5], target = 6 -> returns 4 (len(arr))
  ```
  *(Note: Upper bound / last occurrence requires slight variations)*

- **Binary Search on Answer Space:**

  ```python
  def check(candidate_answer: int, params) -> bool:
      """
      Helper function to check if a 'candidate_answer' is feasible
      given the problem constraints 'params'.
      This function's logic is highly problem-specific.
      It must typically exhibit monotonicity:
      - If check(x) is True, then check(x+1) is True (or False) consistently.
      - OR If check(x) is True, then check(x-1) is True (or False) consistently.

      Returns:
          True if candidate_answer is feasible, False otherwise.
      """
      # ... problem-specific implementation ...
      # Usually involves iterating through input using the candidate_answer
      # and checking if constraints are met. Often O(N) or O(N log N).
      pass # Replace with actual check logic


  def binary_search_on_answer(min_possible_answer: int, max_possible_answer: int, params) -> int:
      """
      Template for binary searching on the answer space.
      Finds the boundary value (min feasible or max feasible) for the answer.

      Args:
          min_possible_answer: The smallest possible valid answer.
          max_possible_answer: The largest possible valid answer.
          params: Additional parameters needed by the check function.

      Returns:
          The optimal answer (e.g., minimum feasible value).
      """
      best_answer = -1 # Or max_possible_answer + 1, depending on search direction

      left, right = min_possible_answer, max_possible_answer

      while left <= right:
          mid = left + (right - left) // 2

          if check(mid, params):
              # This candidate answer 'mid' works.
              # Store it as a potential best answer.
              best_answer = mid
              # Try to find a better answer:
              # - If finding MINIMUM feasible: search left -> right = mid - 1
              # - If finding MAXIMUM feasible: search right -> left = mid + 1
              right = mid - 1 # Example: finding minimum feasible
              # left = mid + 1 # Example: finding maximum feasible
          else:
              # This candidate answer 'mid' doesn't work.
              # Adjust search space accordingly:
              # - If finding MINIMUM feasible: need larger value -> left = mid + 1
              # - If finding MAXIMUM feasible: need smaller value -> right = mid - 1
              left = mid + 1 # Example: finding minimum feasible
              # right = mid - 1 # Example: finding maximum feasible

      return best_answer

  # Example problem: Minimized Maximum Division Sum
  # Given an array nums and an integer k, divide the array into k non-empty
  # continuous subarrays. Find the minimum possible value for the largest
  # sum among these k subarrays.
  # 'check(max_sum_limit)' would see if it's possible to divide nums into k
  # subarrays such that NO subarray sum exceeds max_sum_limit.
  # min_possible_answer = max(nums)
  # max_possible_answer = sum(nums)
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Sorted Input:** Standard binary search *requires* the search space (array) to be sorted. If not sorted, the logic breaks down. For "search on answer", the *monotonicity* of the `check` function is the equivalent requirement.
- **Integer Overflow:** Calculating `mid = (left + right) // 2` can overflow if `left` and `right` are very large positive numbers. Use `mid = left + (right - left) // 2` instead.
- **Loop Termination Condition:** Use `while left <= right` for standard search where the element could be at `left == right`. Use `while left < right` for lower/upper bound variations where the pointers might converge differently. Understand the implication of each.
- **Pointer Updates:** Ensure `left` and `right` are updated correctly. `left = mid + 1` and `right = mid - 1` are standard. For bound searches or answer space searches, you might use `right = mid` or `left = mid` depending on whether `mid` itself is a potential answer you want to keep in the search space. Getting these wrong leads to infinite loops or incorrect results.
- **Off-by-One Errors:** Boundary conditions (`left=0`, `right=len(arr)-1` vs `right=len(arr)`) and pointer updates (`mid`, `mid+1`, `mid-1`) are common sources of errors. Trace simple examples.
- **Handling Not Found:** Decide how to handle the case where the target is not found (return -1, exception, specific index like lower/upper bound).
- **Answer Space Range:** For "Binary Search on Answer", carefully determine the valid range [`min_possible_answer`, `max_possible_answer`]. If the range is too small, the correct answer might be missed. If too large, it might be less efficient but usually still correct.
- **Check Function Correctness:** For "Binary Search on Answer", the `check` function is the most crucial part and its correctness and monotonicity are essential.

**Runtime:**
- **Time Complexity:** **O(log N)**, where N is the number of elements in the search space (the array size or the range of possible answers). This is because the search space is halved in each iteration.
    - For "Binary Search on Answer", the complexity is **O(P * log A)**, where A is the range of possible answers (`max_possible_answer - min_possible_answer`) and P is the complexity of the `check` function (often O(N) or O(N log N), where N is the size of the original input array/data).
- **Space Complexity:** **O(1)** for the iterative implementation, as it only uses a few variables for pointers and midpoints. A recursive implementation would use O(log N) space on the call stack.