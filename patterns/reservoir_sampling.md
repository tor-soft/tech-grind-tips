# Reservoir Sampling

**Description:**
Reservoir Sampling is a family of algorithms for selecting a simple random sample of size `k` from a population of unknown size `n` (often a data stream) in a single pass. The key characteristic is that you don't need to know the total number of items `n` beforehand, and the memory usage is typically proportional only to the sample size `k`, not the total population size `n`. The most common variant, Algorithm R, ensures that each item in the stream has an equal probability (`k/n`) of being included in the final sample of size `k`.

**When to use the pattern:**
-   Selecting a fixed number (`k`) of random items from a very large dataset that cannot fit entirely into memory.
-   Sampling from a continuous data stream where the total number of elements is unknown until the stream ends (or potentially infinite).
-   Choosing `k` random elements from an iterator or generator without first converting it to a list (which would require storing all elements).
-   Selecting a random node from a linked list in a single pass (the case where `k=1`).
-   Any scenario where you need a uniform random sample of size `k` from `n` items (`k << n`) using O(k) memory and a single pass O(n) time.

**Template(s):**
-   **Algorithm R (Standard Reservoir Sampling):**

    ```python
    import random

    def reservoir_sample(stream, k: int) -> list:
        """
        Selects k random items from an iterable 'stream' of unknown size
        using Algorithm R.

        Args:
            stream: An iterable (list, generator, file stream, etc.) providing the items.
            k: The desired sample size.

        Returns:
            A list containing the k sampled items. Returns fewer than k items
            if the stream contains fewer than k items.
        """
        if k <= 0:
            return []

        reservoir = [] # This will hold the sample
        # Iterate through the stream using enumerate to get both index and item
        # (index i is 0-based, so we process item count i+1)
        for i, item in enumerate(stream):
            if i < k:
                # 1. Fill the reservoir initially
                reservoir.append(item)
            else:
                # 2. For subsequent items (i >= k):
                # Generate a random integer between 0 and i (inclusive).
                # The probability that this random integer is less than k is k / (i + 1).
                # (i+1 is the total number of items seen so far, including the current one)
                random_index = random.randint(0, i)

                # If the random index falls within the range [0, k-1]
                if random_index < k:
                    # Replace the element at that index in the reservoir
                    # with the current item from the stream.
                    reservoir[random_index] = item

        # The reservoir now contains a random sample of size k (or fewer if len(stream) < k)
        return reservoir

    # Example Usage:
    # data_stream = range(100) # Simulate a stream of numbers 0-99
    # sample_size = 10
    # sample = reservoir_sample(data_stream, sample_size)
    # print(f"Sample size: {len(sample)}")
    # print(f"Sample items: {sample}")
    # Output (will vary due to randomness):
    # Sample size: 10
    # Sample items: [38, 81, 73, 49, 97, 83, 71, 9, 45, 2]
    ```

**Key Considerations & Screw-ups to Avoid:**
-   **Probability Calculation:** The core idea relies on the probability `k / (i + 1)` (for the `i`-th item, 0-indexed, which is the `(i+1)`-th item overall) of replacing an element. Ensure the random number generation and comparison correctly reflect this probability. Using `random.randint(0, i)` and checking if it's `< k` achieves this.
-   **Indexing:** The random index `j` generated for replacement must be within the bounds of the reservoir, i.e., `0 <= j < k`. Ensure you're using `random.randint(0, i)` (inclusive) for the decision and then `reservoir[j]` (where `j` is the random number if `j < k`) for the replacement.
-   **1-based vs 0-based Indexing:** Be careful with the item count (`i+1`) versus the index (`i`). The probability depends on the count, while the random number generation range and reservoir access use indices.
-   **Stream Handling:** Ensure the code correctly iterates through the stream/iterator only once.
-   **Edge Cases:** Handle `k=0` (return empty list). If the stream has fewer than `k` items, the algorithm naturally returns all items seen.
-   **Random Number Generator:** Use a good quality random number generator. `random.randint` is usually sufficient for typical interviews.
-   **Understanding Uniformity (Intuition):** While a formal proof is more involved (often using induction), the intuition is that at any step `i` (where `i >= k`), any item seen so far has a `k/(i+1)` chance of being in the reservoir. For the current item `i`, it's selected if `random.randint(0, i) < k` (probability `k/(i+1)`). For an item already in the reservoir, it survives if the current item `i` *isn't* selected to replace it. The probability math ensures uniformity holds throughout the process.

**Runtime:**
-   **Time Complexity:** **O(N)**, where `N` is the total number of items in the stream. The algorithm processes each item exactly once. The random number generation and potential replacement take O(1) time per item.
-   **Space Complexity:** **O(k)**, where `k` is the desired sample size. The space used is dominated by storing the reservoir itself, which holds at most `k` items. This is independent of the total stream size `N`.