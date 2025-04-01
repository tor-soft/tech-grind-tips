# K-way Merge

**Description:**
The K-way Merge pattern involves merging `k` sorted input lists (or arrays, streams) into a single sorted output list. While pairwise merging is possible, it's inefficient (O(N*k) where N is total elements). The standard and efficient approach utilizes a Min-Heap (Priority Queue) to keep track of the smallest element currently available from the front of each of the `k` lists. By repeatedly extracting the minimum element from the heap and adding the next element from the same list back into the heap, we can construct the final sorted list efficiently.

**When to use the pattern:**
-   Problems explicitly asking to merge `k` sorted linked lists or arrays.
-   Finding the k-th smallest element in the union of `k` sorted lists.
-   Finding the smallest range that covers at least one element from each of the `k` sorted lists.
-   Finding the k-th smallest element in a sorted matrix (can be viewed as merging `k` sorted rows or columns).
-   External sorting algorithms where chunks of data are sorted individually and then merged.
-   Merging results from distributed computations where each node returns a sorted list.

**Template(s):**
-   **Merging K Sorted Lists using a Min-Heap:**

    ```python
    import heapq

    class ListNode: # Definition for singly-linked list (if merging lists)
        def __init__(self, val=0, next=None):
            self.val = val
            self.next = next

        # Need __lt__ for heapq comparison if storing nodes directly
        def __lt__(self, other):
            return self.val < other.val

    def merge_k_lists(lists: list[list[int] | ListNode]) -> list[int] | ListNode:
        """
        Merges k sorted lists/arrays into a single sorted list/array.
        Uses a min-heap to efficiently find the next smallest element.

        Args:
            lists: A list containing k sorted lists (can be arrays or linked lists).

        Returns:
            A single sorted list (or head of linked list) containing all elements.
            (Template shows returning a list, adapt for linked lists)
        """
        # Min-heap stores tuples: (value, list_index, element_index_within_list)
        # For linked lists, store (value, list_index, node_object)
        min_heap = []

        # Initialize the heap with the first element from each non-empty list
        for i, lst in enumerate(lists):
            if lst: # Check if the list/array is not empty
                if isinstance(lst, list): # Handling array case
                    value = lst[0]
                    heapq.heappush(min_heap, (value, i, 0)) # (value, list_idx, element_idx)
                else: # Handling linked list case (assuming ListNode)
                    node = lst # Head node
                    value = node.val
                     # Push (value, list_idx, node) - Node itself carries 'next' pointer
                    heapq.heappush(min_heap, (value, i, node))


        result_list = []
        # Placeholder for linked list construction
        # dummy_head = ListNode(0)
        # current_node = dummy_head

        # While the heap is not empty
        while min_heap:
            # 1. Extract the smallest element currently in the heap
            value, list_idx, element_info = heapq.heappop(min_heap)

            # Add the extracted value to the result
            result_list.append(value)
            # For linked lists:
            # current_node.next = ListNode(value) # Or use the popped node directly if possible
            # current_node = current_node.next

            # 2. Add the next element from the *same list* back into the heap
            original_list = lists[list_idx]

            if isinstance(original_list, list): # Handling array case
                element_idx = element_info
                next_element_idx = element_idx + 1
                if next_element_idx < len(original_list):
                    next_value = original_list[next_element_idx]
                    heapq.heappush(min_heap, (next_value, list_idx, next_element_idx))
            else: # Handling linked list case
                node = element_info
                if node.next:
                    next_node = node.next
                    next_value = next_node.val
                    heapq.heappush(min_heap, (next_value, list_idx, next_node))


        return result_list # Or return dummy_head.next for linked lists

    # Example Usage (Arrays):
    # lists = [[1, 4, 5], [1, 3, 4], [2, 6]]
    # print(merge_k_lists(lists)) # Output: [1, 1, 2, 3, 4, 4, 5, 6]

    # Example Usage (Linked Lists - requires ListNode setup):
    # l1 = ListNode(1, ListNode(4, ListNode(5)))
    # l2 = ListNode(1, ListNode(3, ListNode(4)))
    # l3 = ListNode(2, ListNode(6))
    # merged_head = merge_k_lists([l1, l2, l3]) # Adapt template return
    # # Print linked list ... output: 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 -> 6
    ```

**Key Considerations & Screw-ups to Avoid:**
-   **Heap Element Structure:** The elements pushed onto the heap *must* be comparable. Since we need to know which list an element came from to push the next one, tuples like `(value, list_index, element_index)` or `(value, list_index, node)` are common. The heap automatically compares based on the first element of the tuple (the `value`). Ensure `value` is always first.
-   **Handling Custom Objects:** If merging lists of custom objects (like `ListNode`), ensure the object itself is comparable (by implementing `__lt__`) if you store the object directly in the heap, or ensure the first element of the tuple is a primitive comparable type (like the node's value). Storing `(value, index, node)` avoids needing `__lt__` on the node itself.
-   **Empty Input Lists:** The code should handle cases where the initial list of lists is empty (`lists = []`) or contains empty lists (`lists = [[], [1, 2]]`). The template checks `if lst:` before accessing the first element.
-   **Tracking Next Element:** Correctly identify and push the *next* element from the *same list* that the minimum element came from. Using `list_index` and `element_index` (for arrays) or the `node.next` pointer (for linked lists) is crucial.
-   **Memory Usage:** The heap stores at most `k` elements at any time. However, constructing the final result list requires storing all `N` elements. Be mindful if `N` is extremely large (external sorting techniques might be needed).
-   **Complexity of Heap Operations:** Remember that push and pop operations on a heap of size `k` take O(log k) time.

**Runtime:**
-   **Time Complexity:** **O(N log k)**, where `N` is the *total* number of elements across all lists, and `k` is the number of lists.
    -   Initializing the heap takes O(k log k) time (pushing the first element from each list).
    -   Each of the `N` elements must be pushed onto and popped from the heap once. Each heap operation takes O(log k) time.
    -   Therefore, the main loop dominates: N elements * O(log k) per element = O(N log k).
-   **Space Complexity:**
    -   **O(k)** for storing elements in the min-heap.
    -   **O(N)** for storing the output list. If the output can be generated as a stream or if merging linked lists in-place (more complex), the output space might be considered O(1) or related to recursion depth if applicable, but typically O(N) is required for the result itself.