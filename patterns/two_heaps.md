# Two Heaps

**Description:**
The Two Heaps pattern is a technique used to efficiently solve problems involving partitioning a collection of numbers into two sets or finding median-like elements (like the actual median, or k-th smallest/largest). It utilizes two priority queues (heaps):

1.  A **Max-Heap:** To store the smaller half of the numbers. The largest element in this heap (the root) is the largest among the smaller half.
2.  A **Min-Heap:** To store the larger half of the numbers. The smallest element in this heap (the root) is the smallest among the larger half.

The core idea is to maintain these heaps such that they are balanced (differ in size by at most 1) and all elements in the max-heap are less than or equal to all elements in the min-heap. This setup allows quick access to the middle element(s).

*Note: Since Python's `heapq` module only implements min-heaps, the max-heap is typically simulated by inserting negated values into a min-heap.*

**When to use the pattern:**
-   **Finding the Median in a Data Stream:** The canonical use case. As numbers arrive one by one, maintain the median efficiently.
-   **Finding the k-th Smallest/Largest Element Dynamically:** Similar to the median problem, the heaps can help track the k-th element as the dataset changes.
-   **Scheduling Problems:** E.g., "Maximize Capital" where you need to pick projects you can afford (min-heap based on cost) and then pick the most profitable among affordable ones (max-heap based on profit).
-   **Maintaining two balanced sets:** Problems where elements need to be partitioned based on a threshold that is related to the median or rank.
-   Sliding Window Median.

**Template(s):**
-   **Median Finder from Data Stream:**

    ```python
    import heapq

    class MedianFinder:
        def __init__(self):
            """
            Initializes the MedianFinder.
            'small_half_max_heap': Stores the smaller half (using negated values for max-heap behavior).
            'large_half_min_heap': Stores the larger half (standard min-heap).
            """
            self.small_half_max_heap = []  # Stores negated values
            self.large_half_min_heap = []

        def addNum(self, num: int) -> None:
            """Adds a number to the data structure."""

            # 1. Add to Max Heap (small half) first (by convention)
            # Push negated value onto the max-heap simulation
            heapq.heappush(self.small_half_max_heap, -num)

            # 2. Balance Values: Ensure max(small_half) <= min(large_half)
            # If the largest element in small_half (smallest negated value)
            # is greater than the smallest element in large_half, swap them.
            if (self.small_half_max_heap and self.large_half_min_heap and
                    (-self.small_half_max_heap[0]) > self.large_half_min_heap[0]):
                # Pop largest from small_half (smallest negated value)
                val_to_move = -heapq.heappop(self.small_half_max_heap)
                # Push it onto large_half
                heapq.heappush(self.large_half_min_heap, val_to_move)

            # 3. Balance Sizes: Ensure heaps differ by at most 1 in size
            # If small_half has more than one extra element
            if len(self.small_half_max_heap) > len(self.large_half_min_heap) + 1:
                # Pop largest from small_half (smallest negated value)
                val_to_move = -heapq.heappop(self.small_half_max_heap)
                 # Push it onto large_half
                heapq.heappush(self.large_half_min_heap, val_to_move)
            # If large_half has more elements than small_half
            elif len(self.large_half_min_heap) > len(self.small_half_max_heap):
                # Pop smallest from large_half
                val_to_move = heapq.heappop(self.large_half_min_heap)
                # Push its negation onto small_half
                heapq.heappush(self.small_half_max_heap, -val_to_move)


        def findMedian(self) -> float:
            """Returns the median of all elements seen so far."""

            # If sizes are equal (even number of elements total)
            if len(self.small_half_max_heap) == len(self.large_half_min_heap):
                # Median is the average of the two middle elements
                # Check for empty case first
                if not self.small_half_max_heap:
                    return 0.0 # Or raise error / return None
                # Remember to negate the max-heap root
                return (-self.small_half_max_heap[0] + self.large_half_min_heap[0]) / 2.0
            else: # Odd number of elements total
                # The extra element is always in the small_half_max_heap by our balancing logic
                # Median is the root of the larger heap (small_half)
                 # Remember to negate the max-heap root
                return float(-self.small_half_max_heap[0])

    # Example Usage:
    # mf = MedianFinder()
    # mf.addNum(1)
    # mf.addNum(2)
    # print(mf.findMedian()) # Output: 1.5
    # mf.addNum(3)
    # print(mf.findMedian()) # Output: 2.0
    ```

**Key Considerations & Screw-ups to Avoid:**
-   **Heap Type Simulation:** Crucially remember that Python's `heapq` is a min-heap. To simulate a max-heap for the smaller half, you *must* store and retrieve negated values. Forgetting negation is a very common error.
-   **Balancing Logic - Values:** Ensure the invariant `max(small_half) <= min(large_half)` is maintained. This usually involves checking the roots after an initial insertion and potentially swapping them.
-   **Balancing Logic - Sizes:** After ensuring the value invariant, re-balance the sizes so `abs(len(max_heap) - len(min_heap)) <= 1`. Decide which heap should hold the extra element if the total count is odd (the template keeps it in `small_half_max_heap`). Transfer the *root* element from the larger heap to the smaller one.
-   **Order of Operations:** Typically: 1. Add to one heap. 2. Balance values between heap roots. 3. Balance sizes. The exact order might slightly vary, but these steps are needed.
-   **Median Calculation:** Correctly identify the median based on heap sizes. If sizes are equal, average the roots (negating the max-heap root). If sizes differ, the median is the root of the larger heap (negating if it's the max-heap).
-   **Edge Cases:** Handling the very first element, the second element, and cases where one or both heaps might be empty during intermediate steps (especially during value balancing).
-   **Floating Point Precision:** Median calculation might involve division, resulting in floats. Ensure appropriate handling.

**Runtime:**
-   **Time Complexity:**
    -   Adding a number (`addNum`): Involves pushing onto a heap (O(log N)) and potentially popping/pushing between heaps for balancing (also O(log N)). Total time is **O(log N)** per number added, where N is the total number of elements seen so far.
    -   Finding the median (`findMedian`): Involves accessing the root(s) of the heap(s), which takes **O(1)** time.
-   **Space Complexity:** **O(N)**, as the heaps store all the numbers added to the stream.