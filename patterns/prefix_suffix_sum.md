# Prefix Sum / Suffix Sum

**Description:**
The Prefix Sum (also known as Cumulative Sum) pattern involves pre-calculating an auxiliary array (or list) where each element `prefix_sum[i]` stores the sum of all elements in the original array from index 0 up to index `i` (inclusive). This allows for calculating the sum of any subarray `arr[j...k]` in O(1) time using the formula `prefix_sum[k] - prefix_sum[j-1]` (with careful handling of the `j=0` case).

Suffix Sum is the analogous concept but calculated from the end of the array: `suffix_sum[i]` stores the sum of elements from index `i` to the end (`n-1`). It's useful for queries involving ranges extending to the end of the array or when comparing sums of prefixes and suffixes.

**When to use the pattern:**
- **Efficient Range Sum Queries:** When you need to frequently calculate the sum of elements within various ranges (subarrays) of a static array.
- **Finding Subarrays/Substrings with Specific Sums:** Problems like finding a subarray that sums to a target `k` (often used in conjunction with a hash map storing prefix sums encountered).
- **Equilibrium Index / Pivot Index:** Finding an index where the sum of elements to the left equals the sum of elements to the right. This often involves comparing prefix and suffix sums.
- **Problems Involving Differences:** Can be extended to "difference arrays," where prefix sums on the difference array can reconstruct the original array after multiple range updates.
- **Grid/Matrix Problems:** The concept extends to 2D prefix sums for calculating the sum of submatrices in O(1) time after O(N*M) pre-calculation.
- **Comparing sums of array partitions:** E.g., finding a split point where prefix sum meets a certain condition relative to the total sum or suffix sum.

**Template(s):**
- **Calculating 1D Prefix Sum:**

  ```python
  def calculate_prefix_sum(arr: list[int]) -> list[int]:
      """
      Calculates the prefix sum array.
      prefix_sum[i] = arr[0] + ... + arr[i].
      Often padded with a 0 at the beginning for easier range queries.
      """
      n = len(arr)
      # Pad with 0 at the start: prefix_sum[0] = 0
      prefix_sum = [0] * (n + 1)
      for i in range(n):
          prefix_sum[i + 1] = prefix_sum[i] + arr[i]
      return prefix_sum

  # Example Usage:
  # arr = [1, 2, 3, 4, 5]
  # ps = calculate_prefix_sum(arr)
  # print(ps) # Output: [0, 1, 3, 6, 10, 15]
  ```

- **Using Prefix Sum for Range Sum Query:**

  ```python
  def range_sum(prefix_sum: list[int], i: int, j: int) -> int:
      """
      Calculates the sum of the original array elements from index i to j (inclusive)
      using the precomputed padded prefix sum array.
      Assumes 0-based indexing for i and j in the original array.
      """
      if i > j or i < 0 or j >= len(prefix_sum) - 1:
          # Handle invalid range if necessary, depends on problem context
          return 0 # Or raise error

      # Corresponds to original array indices i to j
      # Uses prefix_sum indices (i+1) and j
      # Sum(i..j) = Sum(0..j) - Sum(0..i-1)
      # In padded array: prefix_sum[j+1] - prefix_sum[i]
      return prefix_sum[j + 1] - prefix_sum[i]

  # Example Usage (using ps from above):
  # arr = [1, 2, 3, 4, 5]
  # ps = [0, 1, 3, 6, 10, 15]
  # print(range_sum(ps, 1, 3)) # Sum of arr[1], arr[2], arr[3] -> 2 + 3 + 4 = 9
  # Output: 9 (calculated as ps[3+1] - ps[1] = ps[4] - ps[1] = 10 - 1 = 9)
  # print(range_sum(ps, 0, 4)) # Sum of arr[0]..arr[4] -> 1+2+3+4+5 = 15
  # Output: 15 (calculated as ps[4+1] - ps[0] = ps[5] - ps[0] = 15 - 0 = 15)
  ```

- **Calculating 1D Suffix Sum:**

  ```python
  def calculate_suffix_sum(arr: list[int]) -> list[int]:
      """
      Calculates the suffix sum array.
      suffix_sum[i] = arr[i] + ... + arr[n-1].
      Can be padded at the end.
      """
      n = len(arr)
      # Pad with 0 at the end: suffix_sum[n] = 0
      suffix_sum = [0] * (n + 1)
      for i in range(n - 1, -1, -1): # Iterate backwards
          suffix_sum[i] = suffix_sum[i + 1] + arr[i]
      return suffix_sum

  # Example Usage:
  # arr = [1, 2, 3, 4, 5]
  # ss = calculate_suffix_sum(arr)
  # print(ss) # Output: [15, 14, 12, 9, 5, 0]
  # ss[0] is sum arr[0..4], ss[4] is sum arr[4..4], ss[5] is 0 (padding)
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Indexing (0-based vs 1-based):** Be extremely careful with indexing. Using a padded prefix sum array (size `n+1` with `prefix_sum[0]=0`) often simplifies range query logic (`sum(i..j) = ps[j+1] - ps[i]`) but requires careful index mapping. Non-padded arrays require handling the `i=0` case separately (`sum(0..j) = ps[j]`).
- **Off-by-One Errors:** Very common when calculating ranges or accessing the prefix/suffix sum arrays. Double-check the inclusive/exclusive nature of your ranges and the corresponding indices in the auxiliary array.
- **Data Types:** Ensure the data type used for the prefix/suffix sum array can accommodate potentially large sums to avoid integer overflow.
- **Static Arrays:** The basic prefix sum technique is most effective when the underlying array does not change frequently. If there are many updates to the array, data structures like Fenwick Trees (Binary Indexed Trees) or Segment Trees are generally more suitable.
- **Beyond Sums:** The concept can be adapted for other associative operations like prefix products, prefix XOR sums, etc. Ensure the operation allows for an efficient "inverse" to calculate range results (division for product - careful with zeros, XOR is its own inverse).
- **2D Prefix Sums:** The logic extends to 2D but requires careful calculation: `P[r][c] = A[r][c] + P[r-1][c] + P[r][c-1] - P[r-1][c-1]`. Range sum queries also become slightly more complex: `Sum(r1,c1 to r2,c2) = P[r2][c2] - P[r1-1][c2] - P[r2][c1-1] + P[r1-1][c1-1]`. Indexing and padding are even more crucial here.

**Runtime:**
- **Time Complexity:**
    - Pre-calculation (computing the prefix/suffix sum array): **O(N)**, where N is the number of elements.
    - Range Sum Query (after pre-calculation): **O(1)**.
    - 2D Pre-calculation: **O(N * M)**, where N and M are the dimensions of the matrix.
    - 2D Range Sum Query: **O(1)**.
- **Space Complexity:** **O(N)** to store the prefix or suffix sum array (or O(N*M) for 2D).