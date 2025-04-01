**The 'Memorization' Strategy**

It's not about rote memorization like some chump reciting multiplication tables. It's about *internalizing the mechanics*.

1.  **Diagnose the Core Problem:** What are they *really* asking? Is it about finding a pair in sorted data? Optimizing subarray checks? Shortest path? Exploring possibilities? Finding the top K? Needing fast lookups?
2.  **Keyword Association (Initial Hint):** "Sorted array" -> Two Pointers. "Contiguous subarray" / "substring" -> Sliding Window. "Shortest path" / "fewest steps" / "level order" -> BFS. "Permutations" / "combinations" / "any path" / "connectivity" -> DFS/Backtracking. "Top K" / "median" / "scheduling" -> Heap. "Counts" / "duplicates" / "fast lookup needed" -> Hash Map.
3.  **Recall the Skeleton Key:** Visualize the core template structure (loops, data structures, main operations).
4.  **Adapt and Combine:** This is where you earn your paycheck. How does the *specific* problem change the template?
    *   What state needs tracking in the window/map/queue/recursion?
    *   What's the exact condition for shrinking the window or backtracking?
    *   How are neighbors defined (grid, graph, implicit state)?
    *   Do you need multiple patterns? (e.g., Sliding Window *with* a Hash Map, BFS *on* an implicit graph).
5.  **Code It Clean & Handle Edges:** Use good names. Think about empty input, `k=0`, target not found, single-element arrays. Don't look like a rookie.
