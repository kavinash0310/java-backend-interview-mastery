# Data Structures and Algorithms: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Arrays, strings, and windows

### Q1. How do you move zeroes to the end while preserving order?

**Answer:** Use a write index for nonzero values, then fill the remaining positions with zeroes; time O(n), extra space O(1).

**Example:** Write each nonzero to `a[write++]`, then fill from `write`.

### Q2. How do you rotate an array right by k in O(1) extra space?

**Answer:** Normalize k modulo n, reverse the whole array, then reverse the first k and remaining suffix.

**Example:** For `[1,2,3,4,5]`, k=2 gives `[4,5,1,2,3]`.

### Q3. How do you merge overlapping intervals?

**Answer:** Sort by start, then extend the last output interval when the next start is within its end; otherwise append it.

**Example:** Time O(n log n) for sorting.

### Q4. How can you compute product of array except self without division?

**Answer:** Build prefix products and multiply by a running suffix product; time O(n), excluding the output array.

**Example:** The prefix at i times suffix after i is the answer.

### Q5. How do you find the longest substring without repeated characters?

**Answer:** Use a sliding window and a map from character to its most recent index; move the left edge past duplicates.

**Example:** Time O(n) with a hash map.

### Q6. How do you test whether a string is a palindrome after deleting at most one character?

**Answer:** Move inward while characters match; at the first mismatch, test either skipping the left or right character.

**Example:** At most two range checks are needed.

### Q7. How do you group anagrams efficiently?

**Answer:** Map each word to a canonical key, such as sorted characters or a character-frequency tuple.

**Example:** Sorting each word costs O(k log k) for word length k.

### Q8. How do you find the longest common prefix of many strings?

**Answer:** Compare characters against the first string until another string differs or ends.

**Example:** The work is bounded by the total characters inspected.

### Q9. How can prefix sums answer repeated range-sum queries?

**Answer:** Build cumulative sums so a range sum is the difference between two prefix values.

**Example:** `sum(l,r)=prefix[r+1]-prefix[l]`.

### Q10. What invariant helps implement next permutation?

**Answer:** Find the rightmost ascent, swap it with the smallest larger suffix value, then reverse the suffix.

**Example:** If no ascent exists, reverse the whole sequence.

## Lists, stacks, queues, and heaps

### Q11. How do you reverse a singly linked list iteratively?

**Answer:** Keep previous, current, and next references; reverse one link at a time. Time O(n), space O(1).

**Example:** Save `next = current.next` before changing the link.

### Q12. How do you find where a linked-list cycle begins?

**Answer:** Use slow and fast pointers to find a meeting point, then move one pointer from the head; their next meeting is the cycle entry.

**Example:** Both pointers then advance one node at a time.

### Q13. How do you merge two sorted linked lists?

**Answer:** Use a dummy head and append the smaller current node until one list ends, then attach the remainder.

**Example:** Time O(n+m), extra space O(1) when reusing nodes.

### Q14. How do you remove the nth node from the end in one pass?

**Answer:** Advance a fast pointer n steps, then move fast and slow together; remove the node after slow.

**Example:** A dummy head handles deleting the first node.

### Q15. How do you design a MinStack?

**Answer:** Store each value with the minimum so far, or keep a second stack of minima; push, pop, and min are O(1).

**Example:** Push a new minimum whenever it is less than or equal to the current minimum.

### Q16. How can a queue be implemented with two stacks?

**Answer:** Push incoming values onto one stack; when the output stack is empty, transfer all values to it.

**Example:** Each item moves at most once, so amortized operations are O(1).

### Q17. How do you evaluate Reverse Polish Notation?

**Answer:** Push operands; when an operator appears, pop its operands, compute, and push the result.

**Example:** For subtraction, the first popped value is the right operand.

### Q18. How do you find the maximum of each sliding window in O(n)?

**Answer:** Maintain a deque of indexes in decreasing value order and discard indexes outside the current window.

**Example:** Each index enters and leaves the deque once.

### Q19. How do you find the next warmer day for each temperature?

**Answer:** Use a monotonic decreasing stack of unresolved indexes; resolve them when a warmer value arrives.

**Example:** Time O(n), space O(n).

### Q20. When is a heap useful for merging many sorted lists?

**Answer:** Keep the current head from each list in a min-heap; repeatedly emit the smallest and add its successor.

**Example:** For N total items and k lists, time is O(N log k).

## Trees and graphs

### Q21. How do you perform level-order traversal of a binary tree?

**Answer:** Use a queue, processing the queue size at each level before moving to the next.

**Example:** Time O(n), space O(width).

### Q22. How do you validate a binary search tree?

**Answer:** Carry an allowed lower and upper bound down recursion; each node must fall strictly within its range.

**Example:** Checking only each node against its immediate children is insufficient.

### Q23. How can you find the lowest common ancestor in a binary tree?

**Answer:** If the current node is either target, return it; combine recursive results from left and right, and the first node receiving both is the answer.

**Example:** For a BST, comparisons can guide a simpler search.

### Q24. How do you find the kth-smallest value in a BST?

**Answer:** In-order traversal visits values in sorted order; stop after visiting k nodes.

**Example:** An iterative traversal can use an explicit stack.

### Q25. How do you count islands in a grid?

**Answer:** Scan each cell; when an unvisited land cell appears, run DFS or BFS and mark its connected land.

**Example:** Time O(rows × columns).

### Q26. How do you clone a graph with cycles?

**Answer:** Use a map from each original node to its copy before visiting neighbors, then wire copied edges.

**Example:** The map prevents infinite recursion on cycles.

### Q27. How can a topological sort detect a dependency cycle?

**Answer:** Kahn's algorithm removes zero-indegree nodes; if fewer than all nodes are output, a cycle exists.

**Example:** Time O(V+E).

### Q28. How do you find the shortest path in an unweighted grid?

**Answer:** Run BFS from the start, recording distance when each valid neighbor is first visited.

**Example:** BFS is shortest by edge count when every move has equal cost.

### Q29. How does a trie support prefix search?

**Answer:** Each edge represents a character; walking the prefix reaches a node whose subtree contains matching words.

**Example:** Lookup cost is proportional to prefix length.

### Q30. When is Union-Find useful?

**Answer:** It tracks connected components under repeated unions and connectivity checks, with near-constant amortized operations using path compression and rank.

**Example:** Use it to detect whether adding an undirected edge closes a cycle.

## Search, dynamic programming, and design

### Q31. How do you find the first index where a sorted array value is at least x?

**Answer:** Use a lower-bound binary search that keeps the first valid candidate on the right side.

**Example:** Loop while `lo < hi`; move `hi = mid` when `a[mid] >= x`.

### Q32. How do you find the top k frequent values?

**Answer:** Count values, then use a size-k heap or bucket by frequency; choose based on input size and frequency range.

**Example:** A heap gives O(n log k) after counting.

### Q33. How do you merge k sorted arrays efficiently?

**Answer:** Keep one candidate per array in a min-heap and insert the next item from the array that supplied the minimum.

**Example:** Time O(N log k).

### Q34. What is the recurrence for coin change with unlimited coins?

**Answer:** For each amount, minimize `1 + dp[amount - coin]` over usable coins; unreachable amounts stay infinite.

**Example:** Time O(amount × coinCount).

### Q35. How does dynamic programming solve climbing stairs with 1- or 2-step moves?

**Answer:** Use `dp[n] = dp[n-1] + dp[n-2]`, with base cases for zero and one step.

**Example:** Keep only the previous two values for O(1) extra space.

### Q36. How can longest increasing subsequence be solved in O(n log n)?

**Answer:** Maintain the smallest tail value for each subsequence length and binary-search where each number belongs.

**Example:** The tails array is not necessarily the actual subsequence.

### Q37. How can subset sum be solved for nonnegative integers?

**Answer:** Use a boolean DP over sums, updating from high to low for each number so it is used once.

**Example:** For target T, space is O(T).

### Q38. What data structures are needed for an O(1) LRU cache?

**Answer:** Combine a hash map for key lookup with a doubly linked list for recency order.

**Example:** Move a hit to the list head; evict the tail when full.

### Q39. How do two pointers solve the container-with-most-water problem?

**Answer:** Start at both ends, compute area, then move the pointer at the shorter height because the other side cannot improve that pair's limiting height.

**Example:** Time O(n), space O(1).

### Q40. When is a monotonic stack useful?

**Answer:** When each item needs the next or previous greater/smaller item; maintain a stack whose values stay ordered.

**Example:** Next-greater-element and histogram-area problems use this pattern.

## References

- [Princeton Algorithms companion site](https://algs4.cs.princeton.edu/home/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
