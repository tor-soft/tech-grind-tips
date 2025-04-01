# Sliding Window

**Description:**
The Sliding Window pattern is used to perform operations on a specific *window* (a contiguous sub-array or sub-string) of a linear data structure like an array or a string. The window "slides" over the data, element by element. As it slides, the window size might be fixed or variable. The key idea is efficiency: instead of recomputing the result for each window from scratch, we leverage the result from the previous window by adding the contribution of the new element entering the window and removing the contribution of the element leaving the window.

**When to use the pattern:**
- Problems involving contiguous sub-arrays or sub-strings.
- Finding the maximum or minimum value (sum, average, count) of a sub-array/sub-string of a fixed size `k`.
- Finding the longest or shortest sub-array/sub-string that satisfies a certain condition (e.g., sum >= target, contains at most `k` distinct characters).
- Problems where you need to check for a condition within a "window" of elements as you iterate through the sequence.
- Checking for the existence of a sub-array/sub-string with a specific property (e.g., finding an anagram/permutation of a smaller string within a larger string).
- Keywords: "sub-array", "sub-string", "contiguous", "fixed size k", "longest", "shortest", "maximum/minimum within a range".

**Template(s):**
- **Fixed Size Window:**

  ```python
  def fixed_sliding_window(arr: list[int], k: int) -> list: # Return type depends on problem
      """
      Template for a fixed-size sliding window.
      Example: Find the sum of all subarrays of size k.

      Args:
          arr: The input array.
          k: The fixed size of the window.

      Returns:
          A list containing the result for each window position (e.g., sums).
      """
      if len(arr) < k or k <= 0:
          return [] # Or appropriate empty/error value

      results = []
      current_window_sum = 0 # Or other metric

      # Initialize the first window
      for i in range(k):
          current_window_sum += arr[i]
          # Perform other initial window calculations if needed

      results.append(current_window_sum) # Store result for the first window

      # Slide the window
      for window_end in range(k, len(arr)):
          # Add the element entering the window
          current_window_sum += arr[window_end]
          # Remove the element leaving the window
          window_start_element = arr[window_end - k]
          current_window_sum -= window_start_element

          # Update results based on the current window's state
          results.append(current_window_sum)
          # Or update max/min: e.g., max_sum = max(max_sum, current_window_sum)

      return results # Or the single max/min value etc.

  # Example Usage: Find max sum subarray of size 3
  # arr = [2, 1, 5, 1, 3, 2], k = 3
  # Max sum would be 9 (from [5, 1, 3])
  ```

- **Variable Size Window (Dynamic Size based on Condition):**

  ```python
  def variable_sliding_window(arr: list[int], condition_func) -> int: # Return type depends on problem
      """
      Template for a variable-size sliding window.
      Example: Find the length of the smallest subarray whose sum is >= target.

      Args:
          arr: The input array.
          condition_func: A function or logic defining the window's condition.
                          Often involves a target value.

      Returns:
          A result like min/max length, count, etc.
      """
      window_start = 0
      min_length = float('inf') # Or max_length = 0, or count = 0 etc.
      current_window_state = 0 # e.g., current_sum, character counts map, etc.

      for window_end in range(len(arr)):
          # --- Expand the Window ---
          # Add the current element to the window and update state
          current_element = arr[window_end]
          current_window_state += current_element # Adjust based on actual state (e.g., add to sum, update map)

          # --- Shrink the Window ---
          # While the condition is met (or violated, depending on problem),
          # try to shrink the window from the left
          while condition_func(current_window_state): # e.g., while current_sum >= target:
              # Update the overall result (e.g., find min length)
              current_length = window_end - window_start + 1
              min_length = min(min_length, current_length)

              # Remove the element at window_start from the window state
              start_element = arr[window_start]
              current_window_state -= start_element # Adjust based on actual state (e.g., subtract from sum, update map)

              # Move the window start forward
              window_start += 1

      # Handle cases where no window satisfied the condition
      if min_length == float('inf'):
          return 0 # Or -1, or appropriate default

      return min_length # Or other calculated result

  # Example Usage: Smallest subarray sum >= 7
  # arr = [2, 1, 5, 2, 3, 2], target = 7
  # Smallest length is 2 (from [5, 2])
  # condition_func would check if current_window_sum >= 7
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Off-by-One Errors:** Be extremely careful with window boundary indices (`window_start`, `window_end`), window size calculation (`window_end - window_start + 1`), and loop ranges.
- **Window Update Logic:** Ensure you correctly add the `window_end` element's contribution *and* correctly remove the `window_start` element's contribution when sliding (fixed size) or shrinking (variable size). Forgetting the removal step is common.
- **Variable Window Shrinking Condition:** The `while` loop condition for shrinking the variable window is critical and problem-dependent. Determine if you shrink *while* the condition is met (e.g., finding the minimum length) or *while* it's violated (e.g., keeping the window valid for maximum length).
- **Initialization:** Correctly initialize window state (sum, counts, etc.) and the result variable (e.g., `min_len = float('inf')`, `max_len = 0`, `count = 0`).
- **Edge Cases:** Consider empty input (`arr = []`), window size `k` being invalid (e.g., `k <= 0` or `k > len(arr)`), and cases where the condition for the variable window is never met.
- **Data Structure for State:** Choose the right structure to maintain the window's state efficiently (e.g., a simple sum, a hash map/dictionary for character counts, a deque). The complexity of updating the state affects the overall runtime.
- **Fixed vs. Variable:** Don't confuse the logic. Fixed windows always move one step at a time after initialization. Variable windows expand `window_end` and conditionally shrink `window_start`.

**Runtime:**
- **Time Complexity:** Typically **O(N)**, where N is the total number of elements in the input array/string. Although there might be nested loops (especially in the variable size template), each element is processed by the `window_end` pointer exactly once, and each element is processed by the `window_start` pointer at most once. Therefore, the total number of operations is proportional to N, *assuming* the operations to update the window state and check the condition take O(1) or amortized O(1) time (like hash map lookups/updates).
- **Space Complexity:** Usually **O(1)** if the window state requires only a few variables (e.g., current sum, count). Can be **O(K)** or **O(M)** where K is the window size or M is the number of distinct elements in the window (e.g., alphabet size) if you need auxiliary storage like a hash map or frequency array to track the contents of the window (e.g., for character counts or distinct elements).