# Two Pointers

**Description:**
The Two Pointers pattern involves using two index variables (pointers) that iterate through a data structure, typically a sorted array or a linked list, in a coordinated way. The pointers might move towards each other, away from each other, or in the same direction at different speeds. This technique optimizes traversal by avoiding nested loops, often reducing time complexity from O(N²) to O(N). Key variations include pointers starting at opposite ends, pointers starting at the beginning but moving at different speeds (fast/slow), and read/write pointers for in-place modifications.

**When to use the pattern:**
- **Sorted Arrays:**
    - Finding pairs that sum to a target value (e.g., Two Sum II).
    - Finding triplets/quadruplets that sum to a target value (often combined with a loop).
    - Searching for elements satisfying a condition based on their relative positions (e.g., container with most water).
    - Removing duplicates in-place from a sorted array.
    - Squaring elements of a sorted array and producing a sorted result.
- **Palindrome Checking:** Using pointers from both ends moving inwards to compare characters/elements.
- **Linked Lists:**
    - Detecting cycles (Fast & Slow Pointers).
    - Finding the middle element (Fast & Slow Pointers).
    - Finding the k-th element from the end.
- **General Array/String Manipulation:**
    - Reversing an array or string in-place.
    - Partitioning an array (e.g., Dutch National Flag problem, separating 0s, 1s, and 2s).
    - Comparing two strings where one might have special characters (like backspaces).
    - Problems where you need to compare or process elements based on their distance or relative order from either end or from a common start.

**Template(s):**
- **Opposite Ends (e.g., searching for a pair sum in a sorted array):**

  ```python
  def two_pointers_opposite_ends(arr: list[int], target: int): # Return type depends on problem
      """
      Template for two pointers starting at opposite ends of a sorted array.
      Example: Find indices of two numbers that sum to target.

      Args:
          arr: A sorted input array.
          target: The target sum.

      Returns:
          Indices of the pair, or an indicator that no pair exists.
      """
      if len(arr) < 2:
          return None # Or [], or [-1, -1] etc.

      left = 0
      right = len(arr) - 1

      while left < right: # Use '<' to avoid using the same element twice
          current_sum = arr[left] + arr[right]

          if current_sum == target:
              # Found the pair
              return [left, right] # Or values: [arr[left], arr[right]]
          elif current_sum < target:
              # Sum is too small, need a larger value, move left pointer right
              left += 1
          else: # current_sum > target
              # Sum is too large, need a smaller value, move right pointer left
              right -= 1

      # Pair not found
      return None # Or [], or [-1, -1] etc.

  # Example Usage:
  # arr = [2, 7, 11, 15], target = 9
  # print(two_pointers_opposite_ends(arr, target)) # Output: [0, 1]
  ```

- **Same Direction (Slow/Fast or Read/Write) (e.g., Remove duplicates from sorted array):**

  ```python
  def two_pointers_same_direction(arr: list[int]) -> int:
      """
      Template for two pointers starting at the beginning, often for in-place modification.
      Example: Remove duplicates from sorted array in-place, return new length.

      Args:
          arr: A sorted input array.

      Returns:
          The length of the array after removing duplicates.
          The input `arr` is modified in-place up to this length.
      """
      if not arr:
          return 0

      # 'write_ptr' indicates the position where the next unique element should be written.
      write_ptr = 1 # Start from the second position (index 1)

      # 'read_ptr' iterates through the array to find unique elements.
      for read_ptr in range(1, len(arr)):
          # If the current element is different from the previous unique element
          if arr[read_ptr] != arr[write_ptr - 1]:
              # Write the unique element to the 'write_ptr' position
              arr[write_ptr] = arr[read_ptr]
              # Move the 'write_ptr' forward
              write_ptr += 1

      return write_ptr # The length of the unique part of the array

  # Example Usage:
  # nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
  # k = two_pointers_same_direction(nums)
  # print(k) # Output: 5
  # print(nums[:k]) # Output: [0, 1, 2, 3, 4]
  ```

- **Fast & Slow Pointers (e.g., Linked List Cycle Detection):**

  ```python
  class ListNode: # Definition for singly-linked list.
      def __init__(self, x):
          self.val = x
          self.next = None

  def has_cycle(head: ListNode) -> bool:
      """
      Template for fast and slow pointers in a linked list.
      Example: Detect if a linked list has a cycle.

      Args:
          head: The head of the linked list.

      Returns:
          True if there is a cycle, False otherwise.
      """
      if not head or not head.next:
          return False

      slow = head
      fast = head

      while fast is not None and fast.next is not None:
          slow = slow.next      # Moves 1 step
          fast = fast.next.next # Moves 2 steps

          if slow == fast:
              # Pointers met, cycle detected
              return True

      # Fast pointer reached the end, no cycle
      return False

  # Example Usage requires setting up a linked list with a cycle.
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Input Requirements:** Many Two Pointer techniques (especially opposite ends) require the input array to be **sorted**. Forgetting to sort (or if the problem implies it's already sorted) is a common mistake.
- **Pointer Initialization:** Ensure pointers start at the correct positions (`0`, `len(arr)-1`, `head`, etc.).
- **Loop Condition:** The `while` loop termination condition is critical. `left < right` is common for opposite ends to avoid processing the same element twice or crossing over. `fast and fast.next` is common for linked lists to prevent null pointer exceptions.
- **Pointer Movement:** Decide precisely when and which pointer(s) to move. Moving the wrong pointer or forgetting to move a pointer can lead to infinite loops or incorrect results.
- **Handling Duplicates:** If the problem requires skipping duplicates (e.g., in 3Sum), remember to add extra checks and loops to advance pointers past identical consecutive elements.
- **Edge Cases:** Test with empty inputs, single-element inputs, inputs where the condition is met at the very beginning or end, and inputs where the condition is never met.
- **In-Place Modification:** For read/write pointer patterns, be careful not to overwrite values prematurely. Ensure the `write_ptr` only advances when a valid element is placed.
- **Off-by-One Errors:** Double-check all index calculations and boundary conditions.

**Runtime:**
- **Time Complexity:** Typically **O(N)** for arrays and linked lists, as each pointer traverses the data structure at most once. If sorting is required first, the overall time complexity becomes **O(N log N)** dominated by the sort.
- **Space Complexity:** Usually **O(1)** because the pointers use constant extra space. If sorting is done not in-place, it might require O(log N) or O(N) space. If the result needs to be stored in a new list (e.g., squaring a sorted array), it might be O(N).