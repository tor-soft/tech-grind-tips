# Heap (Priority Queue)

**Description:**
A Heap is a specialized tree-based data structure that satisfies the heap property. In a **Min-Heap**, for any given node C, if P is a parent node of C, then the value (or key) of P is less than or equal to the value of C. In a **Max-Heap**, the value of P is greater than or equal to the value of C. Heaps are commonly implemented using arrays for efficiency.

A **Priority Queue** is an abstract data type that operates like a regular queue but where each element has a "priority". Elements with higher priority are served before elements with lower priority. Heaps are a very common and efficient way to implement priority queues. The Heap pattern involves using a Min-Heap or Max-Heap to efficiently keep track of the smallest or largest elements among a dynamic set of values.

Python's `heapq` module provides an implementation of the heap queue algorithm, also known as the priority queue algorithm. By default, `heapq` implements a **Min-Heap**.

**When to use the pattern:**
- **Finding Top K Elements:** Finding the K largest or K smallest elements in a collection (e.g., Top K Frequent Elements, Kth Largest Element in an Array).
- **Finding the Kth Smallest/Largest Element:** Similar to Top K, but only need the specific Kth element.
- **Median Finding in a Stream:** Maintaining the median of a constantly growing list of numbers (often uses two heaps - a min-heap and a max-heap).
- **K-way Merge:** Merging K sorted lists or arrays efficiently.
- **Scheduling Problems:** Problems involving prioritizing tasks based on some criteria (e.g., shortest processing time first, meeting scheduling based on end times).
- **Graph Algorithms:** Used as a core component in algorithms like Dijkstra's (shortest path) and Prim's (Minimum Spanning Tree) to efficiently retrieve the next node/edge with the minimum weight/distance.
- **Any problem requiring efficient retrieval of the minimum or maximum element** from a changing collection.

**Template(s):**
- **Finding Top K Largest Elements (using Min-Heap):**

  ```python
  import heapq

  def find_top_k_largest(nums: list[int], k: int) -> list[int]:
      """
      Finds the K largest elements using a Min-Heap of size K.

      Args:
          nums: List of numbers.
          k: The number of largest elements to find.

      Returns:
          A list containing the K largest elements (order not guaranteed).
      """
      if k <= 0:
          return []

      min_heap = [] # Will store the K largest elements seen so far

      for num in nums:
          if len(min_heap) < k:
              # If heap size is less than K, just add the element
              heapq.heappush(min_heap, num)
          elif num > min_heap[0]: # Compare with the smallest element in the heap (root of min-heap)
              # If the current number is larger than the smallest in the heap,
              # remove the smallest and add the current number.
              heapq.heapreplace(min_heap, num) # More efficient than pop followed by push
              # Or:
              # heapq.heappop(min_heap)
              # heapq.heappush(min_heap, num)

      # The heap now contains the K largest elements
      return list(min_heap)

  # Example: find_top_k_largest([3, 2, 1, 5, 6, 4], 2) -> Returns [5, 6] (order may vary)
  ```

- **Finding Kth Smallest Element (using Max-Heap):**
  *(Note: Python's `heapq` is a min-heap, so we store negated values to simulate a max-heap)*

  ```python
  import heapq

  def find_kth_smallest(nums: list[int], k: int) -> int | None:
      """
      Finds the Kth smallest element using a Max-Heap of size K.

      Args:
          nums: List of numbers.
          k: The position (1-based) of the smallest element to find.

      Returns:
          The Kth smallest element, or None if k is invalid.
      """
      if k <= 0 or k > len(nums):
          return None

      # Use a max-heap (simulate by negating values) to keep track of the K smallest elements seen so far
      max_heap = []

      for num in nums:
          neg_num = -num # Negate to use min-heap as max-heap
          if len(max_heap) < k:
              heapq.heappush(max_heap, neg_num)
          elif neg_num > max_heap[0]: # Current negated number is 'larger' (means original num is smaller)
              # If the current number is smaller than the largest in the heap (negated root), replace
              heapq.heapreplace(max_heap, neg_num)

      # The root of the max-heap (negated) is the Kth smallest element
      return -max_heap[0] # Negate back to get the original value

  # Example: find_kth_smallest([3, 2, 1, 5, 6, 4], 3) -> Returns 3 (1st=1, 2nd=2, 3rd=3)
  ```

- **Building a Heap (Heapify):**

  ```python
  import heapq

  def build_heap(data: list):
      """
      Transforms a list into a min-heap, in-place, in linear time.
      """
      heapq.heapify(data) # data is now a valid min-heap

  # Example:
  # my_list = [9, 3, 2, 6, 7, 1]
  # build_heap(my_list)
  # print(my_list) # Output will be a heap representation, e.g., [1, 3, 2, 6, 7, 9]
  # Smallest element is always at my_list[0]
  # print(heapq.heappop(my_list)) # -> 1
  # print(my_list) # -> [2, 3, 9, 6, 7] (heap property maintained)
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Min-Heap vs. Max-Heap:** Python's `heapq` is fundamentally a min-heap. To simulate a max-heap, store negated values (or use custom comparison objects/tuples `(-value, original_value)`). Don't forget to negate back when retrieving the final result. Choosing the wrong type (min vs max) for the problem (e.g., using a max-heap to find K smallest elements efficiently) leads to incorrect or inefficient solutions.
- **Heap Size Management:** In Top K problems, ensure the heap size doesn't exceed K. Use `len(heap)` checks before potentially popping/pushing.
- **`heapify` vs. `heappush` Loop:** `heapq.heapify(list)` builds a heap in O(N) time. Pushing N elements one by one using `heapq.heappush` takes O(N log N) time. Use `heapify` if you have all elements upfront.
- **Heap Operations Complexity:** Remember the complexities:
    - `heappush`: O(log K) or O(log N)
    - `heappop`: O(log K) or O(log N)
    - `heapreplace`: O(log K) or O(log N)
    - `heapify`: O(N)
    - Accessing min/max (root): O(1) (`heap[0]`)
    *(K is the heap size, N is the total number of elements processed)*
- **Custom Objects:** If storing objects or tuples, ensure the first element of the tuple is the one used for comparison, or implement the comparison methods (`__lt__`, `__gt__`, etc.) if using custom classes. E.g., `heapq.heappush(heap, (priority, item_data))`.
- **0-based vs 1-based `k`:** Pay attention to whether the problem asks for the Kth element using 1-based indexing (common) or 0-based indexing. Adjust `k` accordingly if needed.
- **Modifying Heap Directly:** Avoid modifying elements within the heap array directly without calling heap functions, as this can violate the heap property. Use `heappop` and `heappush` or `heapreplace` to update.

**Runtime:**
- **Building Heap:** O(N) using `heapify`, O(N log N) using repeated `heappush`.
- **Adding/Removing Element:** O(log K) where K is the current size of the heap.
- **Getting Min/Max Element:** O(1) (accessing the root `heap[0]`).
- **Top K Elements:** O(N log K), where N is the total number of elements and K is the number of elements to find. Each element is processed once, and heap operations take O(log K).
- **Kth Smallest/Largest Element:** O(N log K) using the heap approach. Can also be done in O(N) average time using QuickSelect, but the heap method is often simpler to implement during interviews.
- **K-way Merge:** O(N log K), where N is the total number of elements across all lists, and K is the number of lists. The heap stores at most K elements.