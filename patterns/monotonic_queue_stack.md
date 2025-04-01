# Monotonic Stack / Queue

**Description:**
A Monotonic Stack or Monotonic Queue (often implemented using a Deque) is a data structure that maintains its elements in a strictly increasing or strictly decreasing order. As elements are pushed onto the structure, elements that violate the monotonic property are popped off, allowing efficient processing of relationships between elements (like finding the next or previous greater/smaller element).

-   **Monotonic Increasing Stack/Queue:** Elements are always in non-decreasing order from bottom to top (stack) or front to back (queue). When pushing a new element, larger elements at the top/back are popped until the property is maintained.
-   **Monotonic Decreasing Stack/Queue:** Elements are always in non-increasing order. When pushing a new element, smaller elements at the top/back are popped.

The key insight is that when an element `x` is popped because a new element `y` is being pushed, `y` is the first element to the right of `x` that violates the monotonic condition (e.g., if using a decreasing stack, `y` is the first element greater than `x` to its right).

**When to use the pattern:**
-   **Next Greater/Smaller Element:** Finding the next element to the right (or left) of each element `arr[i]` that is greater/smaller than `arr[i]`.
-   **Previous Greater/Smaller Element:** Finding the nearest element to the left of each element `arr[i]` that is greater/smaller than `arr[i]`.
-   **Largest Rectangle in Histogram:** The optimal solution uses a monotonic (increasing) stack to find the nearest smaller elements to the left and right for each bar, determining the width of the rectangle using that bar as the minimum height.
-   **Sliding Window Maximum/Minimum:** A monotonic deque (decreasing for max, increasing for min) can efficiently find the maximum/minimum element in a sliding window in O(N) total time.
-   Problems involving finding ranges or intervals defined by relative element values (e.g., number of subarrays where an element `x` is the minimum).
-   Optimizing calculations that depend on the nearest larger/smaller neighbors.

**Template(s):**
-   **Monotonic (Increasing) Stack for Next Smaller Element / Previous Smaller Element:**
    (To find "Next Greater" or "Previous Greater", use a decreasing stack - flip the comparison).

    ```python
    def monotonic_stack_next_smaller(arr: list[int]) -> list[int]:
        """
        Finds the next smaller element for each element in arr using a
        monotonic increasing stack. Stores indices in the stack.

        Args:
            arr: The input array.

        Returns:
            A list where result[i] is the index of the next smaller element
            to the right of arr[i], or -1 if none exists.
        """
        n = len(arr)
        result = [-1] * n  # Initialize results with -1 (or other indicator)
        stack = []  # Stores indices, maintains values arr[stack[top]] increasing

        for i in range(n):
            # While stack is not empty AND current element is smaller than
            # the element at the index stored at stack top
            while stack and arr[i] < arr[stack[-1]]:
                # The element arr[stack[-1]]'s next smaller element is arr[i]
                top_index = stack.pop()
                result[top_index] = i # Store index i
                # Or store value: result[top_index] = arr[i]
                # Or store distance: result[top_index] = i - top_index

            # Push the current index onto the stack
            stack.append(i)

        # Elements remaining in the stack have no smaller element to their right
        # They are already initialized to -1, so no action needed if -1 is the default

        return result

    # To find *Previous* Smaller Element, iterate from right-to-left (range(n-1, -1, -1))
    # and adjust result indexing accordingly. Or iterate left-to-right but
    # determine the *previous* smaller element when pushing onto the stack.
    # The element *already* on the stack top (before pushing i) is the candidate
    # for previous smaller element of arr[i].
    ```

-   **Monotonic (Decreasing) Deque for Sliding Window Maximum:**
    (For Sliding Window Minimum, use an increasing deque - flip the comparison).

    ```python
    import collections

    def sliding_window_maximum(arr: list[int], k: int) -> list[int]:
        """
        Finds the maximum element in each sliding window of size k using
        a monotonic decreasing deque. Stores indices in the deque.

        Args:
            arr: The input array.
            k: The size of the sliding window.

        Returns:
            A list containing the maximum value for each window.
        """
        if not arr or k <= 0:
            return []

        n = len(arr)
        results = []
        # Deque stores indices. Elements corresponding to indices are decreasing.
        deque = collections.deque()

        for i in range(n):
            # 1. Remove indices from the front that are out of the current window
            # Window is [i - k + 1, i]
            if deque and deque[0] < i - k + 1:
                deque.popleft()

            # 2. Maintain monotonic decreasing property:
            # Remove indices from the back whose elements are smaller than
            # or equal to the current element arr[i].
            while deque and arr[deque[-1]] <= arr[i]:
                 # Use '<' if strictly decreasing is required or definition changes
                deque.pop()

            # 3. Add the current index to the deque
            deque.append(i)

            # 4. The maximum element for the current window is at the front
            # Start recording results once the window has reached size k
            if i >= k - 1:
                results.append(arr[deque[0]])

        return results

    # Example Usage:
    # arr = [1, 3, -1, -3, 5, 3, 6, 7], k = 3
    # print(sliding_window_maximum(arr, k)) # Output: [3, 3, 5, 5, 6, 7]
    ```

**Key Considerations & Screw-ups to Avoid:**
-   **Stack vs. Queue:** Use a stack when the primary relationship is based on the most recently added elements (typical for next/previous greater/smaller). Use a deque (acting as a specialized queue) for sliding window problems where you need efficient additions at the back and removals from both front and back.
-   **Increasing vs. Decreasing:** Choose the correct monotonicity based on the problem (e.g., finding next *smaller* usually uses an *increasing* stack because smaller elements pop larger ones; finding next *greater* uses a *decreasing* stack). Double-check the `while` loop comparison (`<` vs `>`).
-   **Storing Indices vs. Values:** Storing indices in the stack/deque is generally more versatile, as it allows calculating distances or retrieving original values easily. Storing values might suffice for simpler problems but can be ambiguous if duplicates exist.
-   **Handling Duplicates:** Decide how strictly equal elements should be handled. Should `arr[i] == arr[stack[-1]]` cause a pop? This depends on whether you need the *strictly* next greater/smaller or if equal elements count. The templates above handle equality differently (stack pops on `<` only, deque pops on `<=`) - adjust as needed.
-   **Edge Cases:** Empty input array, window size `k` larger than array length, all elements being the same.
-   **Off-by-One Errors:** Ensure correct indexing when calculating results (e.g., storing the index of the element causing the pop vs. the index being popped) and correct window boundary checks in sliding window problems.
-   **Result Initialization:** Initialize the result array appropriately (e.g., with -1, null, or array length) to represent cases where no next/previous element is found.

**Runtime:**
-   **Time Complexity:** **O(N)**, where N is the number of elements in the input array. Although there are nested loops (the `for` loop and the `while` loop inside), each element is pushed onto and popped from the stack/deque at most once. Therefore, the total number of operations is proportional to N (amortized O(1) per element).
-   **Space Complexity:** **O(N)** in the worst case for the stack/deque (e.g., if the input array is already sorted according to the desired monotonicity, all elements might end up in the structure temporarily). For sliding window problems, the deque size is bounded by the window size `k`, so space complexity is **O(k)**.