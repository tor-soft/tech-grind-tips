# Merge Intervals

**Description:**
The Merge Intervals pattern deals with problems involving intervals, typically represented as pairs `[start, end]`. The core idea is to process these intervals, often by merging overlapping or adjacent intervals into a smaller set of consolidated intervals. This usually involves sorting the intervals based on their start times and then iterating through them, merging any interval that overlaps with the previous one.

**When to use the pattern:**
- Problems explicitly asking to merge overlapping intervals (e.g., given `[[1,3], [2,6], [8,10], [15,18]]`, return `[[1,6], [8,10], [15,18]]`).
- Problems involving scheduling conflicts or finding available time slots (e.g., checking if a person can attend all meetings, finding the maximum number of concurrent meetings).
- Problems requiring inserting a new interval into a list of non-overlapping intervals and merging if necessary.
- Calculating the total length covered by a set of intervals on a number line.
- Determining if intervals conflict with each other.
- Finding points or ranges covered by the maximum number of intervals.

**Template(s):**
- **Standard Merge Overlapping Intervals:**

  ```python
  def merge_intervals(intervals: list[list[int]]) -> list[list[int]]:
      """
      Merges overlapping intervals in a list of intervals.

      Args:
          intervals: A list of intervals, where each interval is [start, end].

      Returns:
          A list of merged, non-overlapping intervals.
      """
      if not intervals:
          return []

      # 1. Sort intervals based on the start time.
      intervals.sort(key=lambda x: x[0])

      merged_intervals = []
      # Initialize with the first interval
      merged_intervals.append(intervals[0])

      for i in range(1, len(intervals)):
          current_start, current_end = intervals[i]
          last_merged_start, last_merged_end = merged_intervals[-1]

          # 2. Check for overlap with the last interval in the merged list.
          # Overlap occurs if current_start <= last_merged_end
          if current_start <= last_merged_end:
              # 3. Merge: Update the end time of the last merged interval.
              # The new end is the maximum of the two interval ends.
              merged_intervals[-1][1] = max(last_merged_end, current_end)
          else:
              # 4. No overlap: Add the current interval as a new merged interval.
              merged_intervals.append([current_start, current_end])

      return merged_intervals

  # Example Usage:
  # intervals = [[1,3], [2,6], [8,10], [15,18]]
  # print(merge_intervals(intervals)) # Output: [[1, 6], [8, 10], [15, 18]]

  # intervals = [[1,4], [4,5]]
  # print(merge_intervals(intervals)) # Output: [[1, 5]] (merges adjacent)
  ```

**Key Considerations & Screw-ups to Avoid:**
- **Forgetting to Sort:** The algorithm fundamentally relies on intervals being sorted by their start times. Skipping this step or sorting incorrectly (e.g., by end time) will lead to incorrect results.
- **Incorrect Overlap Condition:** The overlap condition is `current_interval.start <= last_merged_interval.end`. Ensure this comparison is correct. Pay attention if adjacent intervals (e.g., `[1,2], [2,3]`) should be considered overlapping based on the problem statement (the template above *does* merge adjacent intervals).
- **Incorrect Merging Logic:** When merging, the new end time must be the `max` of the `last_merged_interval.end` and the `current_interval.end`. Simply taking the `current_interval.end` is wrong if the `last_merged_interval` already extends further.
- **Modifying Input:** Be mindful if you are allowed to modify the input list or if you need to create a new list for the result (the template creates a new list).
- **Edge Cases:** Always consider edge cases like an empty input list (`[]`) or a list with only one interval. The template handles these.
- **Interval Representation:** Ensure you handle the interval representation correctly (e.g., inclusive/exclusive ends, list vs. tuple). The template assumes inclusive `[start, end]`.

**Runtime:**
- **Time Complexity:** O(N log N), where N is the number of intervals. The dominant factor is the sorting step. The merging pass itself takes O(N) time as it iterates through the sorted intervals once.
- **Space Complexity:** O(N) in the worst case for storing the `merged_intervals` list (if no intervals merge). If sorting is done in-place, the space complexity might be O(log N) or O(N) depending on the sorting algorithm's implementation space, plus O(N) for the output. Typically considered O(N) overall due to the output list.