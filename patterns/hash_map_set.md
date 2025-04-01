# Hash Map / Set

**Description:**
Hash Maps (known as dictionaries or `dict` in Python) and Hash Sets (`set` in Python) are fundamental data structures that store collections of items using a technique called hashing.

-   **Hash Map (`dict`):** Stores key-value pairs. It uses a hash function to compute an index (or "hash code") for each key, allowing for very fast average-time access (insertion, deletion, lookup) to the associated value. Keys must be unique and *hashable* (immutable types like numbers, strings, tuples of immutable types).
-   **Hash Set (`set`):** Stores unique elements (no associated values). It also uses hashing to allow for very fast average-time checks for membership (`in` operator), insertion, and deletion. Elements must also be *hashable*.

The core advantage of both is the **average O(1)** time complexity for their primary operations.

**When to use the pattern:**
-   **Frequency Counting:** Quickly count the occurrences of elements in a list, characters in a string, etc. (Use Hash Map: element -> count).
-   **Checking Existence/Membership:** Efficiently determine if an element has been seen before or exists within a collection. (Use Hash Set for uniqueness, or Hash Map keys).
-   **Finding Duplicates:** Identify duplicate elements within a sequence. (Use Hash Set or Hash Map).
-   **Two Sum Problems:** Find pairs of elements in an array that sum up to a target value. (Use Hash Map: element -> index, or Hash Set to store complements).
-   **Grouping/Categorization:** Group items based on a shared property (e.g., group anagrams by their sorted representation). (Use Hash Map: property -> list of items).
-   **Caching/Memoization:** Store the results of computationally expensive function calls to avoid redundant calculations (related to Dynamic Programming). (Use Hash Map: function arguments (as key) -> result).
-   **Removing Duplicates:** Get a collection of unique elements from a sequence. (Use Hash Set).
-   **Efficient Lookups:** Any scenario where you need to quickly look up information associated with a specific identifier (key).

**Template(s):**
-   **Frequency Counting (Hash Map):**

    ```python
    from collections import Counter, defaultdict

    # Method 1: Using collections.Counter (often simplest)
    def count_frequencies_counter(items: list) -> Counter:
        """Counts frequencies using collections.Counter."""
        return Counter(items)

    # Method 2: Using collections.defaultdict
    def count_frequencies_defaultdict(items: list) -> dict:
        """Counts frequencies using collections.defaultdict."""
        counts = defaultdict(int) # Default value for new keys is 0
        for item in items:
            counts[item] += 1
        return dict(counts) # Convert back to regular dict if needed

    # Method 3: Using standard dict and get()
    def count_frequencies_get(items: list) -> dict:
        """Counts frequencies using standard dict.get()."""
        counts = {}
        for item in items:
            counts[item] = counts.get(item, 0) + 1
        return counts

    # Example: count_frequencies_xxx(['a', 'b', 'a', 'c', 'b', 'a']) -> {'a': 3, 'b': 2, 'c': 1}
    ```

-   **Checking for Duplicates (Hash Set):**

    ```python
    def has_duplicates(items: list) -> bool:
        """Checks if a list contains duplicate elements using a set."""
        seen = set()
        for item in items:
            if item in seen:
                return True
            seen.add(item)
        return False

    # Example: has_duplicates([1, 2, 3, 1]) -> True
    # Example: has_duplicates([1, 2, 3, 4]) -> False
    ```

-   **Two Sum Problem (Hash Map):**

    ```python
    def two_sum(nums: list[int], target: int) -> list[int] | None:
        """Finds indices of two numbers that sum to target using a hash map."""
        num_to_index = {} # Map: number -> its index
        for current_index, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                # Found the complement needed to reach the target
                return [num_to_index[complement], current_index]
            # Store the current number and its index for future lookups
            num_to_index[num] = current_index
        return None # No solution found

    # Example: two_sum([2, 7, 11, 15], 9) -> [0, 1] (because nums[0] + nums[1] == 9)
    ```

-   **Removing Duplicates (Hash Set):**

    ```python
    def remove_duplicates(items: list) -> list:
        """Removes duplicates while preserving original order (if needed)."""
        seen = set()
        result = []
        for item in items:
            if item not in seen:
                seen.add(item)
                result.append(item)
        return result

    # Simplified version if order doesn't matter:
    def get_unique_items(items: list) -> list:
        """Gets unique items (order not guaranteed)."""
        return list(set(items))

    # Example: remove_duplicates([1, 3, 2, 1, 5, 3]) -> [1, 3, 2, 5]
    # Example: get_unique_items([1, 3, 2, 1, 5, 3]) -> [1, 2, 3, 5] (order might vary)
    ```

**Key Considerations & Screw-ups to Avoid:**
-   **Hashable Keys/Elements:** Keys in maps and elements in sets *must* be hashable (immutable). Trying to use mutable types like lists or dictionaries directly will raise a `TypeError`. Use tuples of immutable types if needed (e.g., `my_set.add((1, 2))` is okay, `my_set.add([1, 2])` is not).
-   **Mutable Default Values:** When using `defaultdict` with mutable types like lists (`defaultdict(list)`), be aware that accessing a non-existent key creates a *new instance* of that mutable type (e.g., a new empty list).
-   **Order Guarantees:** Standard Python `dict` (since 3.7+) and `set` (since 3.6+) maintain insertion order. Do not rely on this behavior if your code needs to run on older Python versions or if the problem statement implies arbitrary order. For guaranteed order across versions, use `collections.OrderedDict` (though it's less critical now).
-   **Space Usage:** Hash maps and sets require extra space, typically O(N), where N is the number of elements stored. This can be a constraint in memory-limited scenarios.
-   **Collision Handling:** While average time complexity is O(1), hash collisions can degrade performance to O(N) in the worst case. Python's built-in implementations handle collisions well, so this is rarely an issue in typical LeetCode problems, but it's good to be aware of the theoretical possibility.
-   **Map vs. Set Choice:** Don't use a hash map (dict) when a hash set (set) suffices. If you only care about the presence or uniqueness of keys and don't need associated values, a set is more memory-efficient and semantically appropriate.
-   **Handling Non-Existent Keys:** Accessing a key that's not in a standard `dict` raises a `KeyError`. Use `key in my_dict`, `my_dict.get(key, default_val)`, or `try...except KeyError` to handle missing keys gracefully. `defaultdict` avoids this issue for key accesses that trigger the default factory.

**Runtime:**
-   **Average Time Complexity:**
    -   Insertion (`map[key] = val`, `set.add(elem)`): **O(1)**
    -   Deletion (`del map[key]`, `set.remove(elem)`): **O(1)**
    -   Lookup/Search (`map[key]`, `key in map`, `elem in set`): **O(1)**
-   **Worst Case Time Complexity (due to pathological hash collisions):**
    -   Insertion: **O(N)**
    -   Deletion: **O(N)**
    -   Lookup/Search: **O(N)**
    *(For LeetCode and typical interviews, assume O(1) average time unless explicitly stated otherwise or dealing with custom hashing scenarios.)*
-   **Space Complexity:** **O(N)**, where N is the number of key-value pairs (for maps) or elements (for sets) being stored.