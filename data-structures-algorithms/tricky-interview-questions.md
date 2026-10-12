# Data Structures and Algorithms: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Array and search edge cases

### Q1. Why can binary search miss a value if low and high are inclusive but the loop uses low<high?

**Answer:** The final single candidate is skipped unless the loop or post-check handles it. State whether high is inclusive and keep update rules consistent.

### Q2. Why is (low+high)/2 unsafe for a large int array?

**Answer:** The sum can overflow before division. Use low+(high-low)/2 or a wider type after validating bounds.

### Q3. How can binary search return an arbitrary duplicate instead of the first?

**Answer:** On equality it may stop immediately. Continue left while preserving a half-open interval invariant to find the lower bound.

### Q4. Why does sliding-window sum fail when elements may be negative?

**Answer:** Removing the left element does not guarantee the sum decreases, so the shrink condition is not monotonic. Use prefix sums with a deque for constrained subarrays.

### Q5. Why must the current prefix be queried before it is added for a zero-sum subarray count?

**Answer:** Adding first counts an empty subarray at every position. Initialize prefix frequency with 0→1, query prior prefixes, then record current.

### Q6. What is the hidden assumption in the usual two-sum hash-map solution?

**Answer:** Expected constant-time hashing and distinct input positions. Query before inserting the current element to avoid pairing an item with itself.

### Q7. Why can a comparator written as a-b violate sorting requirements?

**Answer:** Subtraction can overflow, breaking sign and transitivity. Use Integer.compare or compare keys without arithmetic subtraction.

### Q8. When does a sorted array still not justify two pointers?

**Answer:** Only when the target condition changes monotonically with pointer movement. Prove each move eliminates candidates, not just that input is sorted.

### Q9. Why is coordinate compression not valid for distance-based calculations by itself?

**Answer:** It preserves order, not numeric gaps. Keep original values when costs depend on actual differences.

### Q10. How can an O(1)-space algorithm still use O(n) extra memory?

**Answer:** Recursion stack or output storage may be excluded from auxiliary space. State what the complexity count includes.

## Graph and tree traps

### Q11. Why is a visited set alone insufficient to detect a cycle in a directed graph?

**Answer:** An edge to a previously completed node is not necessarily a cycle. Track recursion-stack or three-color states for back edges.

### Q12. What does BFS guarantee when all edges have equal weight, and what if one edge differs?

**Answer:** BFS minimizes edge count only with equal costs. Use Dijkstra for nonnegative weights or an appropriate algorithm for special weights.

### Q13. Why should Dijkstra ignore a popped queue entry whose distance is stale?

**Answer:** The node may have been enqueued again with a shorter path. Processing the outdated entry can repeat work or corrupt predecessor choices.

### Q14. How much queue memory can BFS use on a star graph?

**Answer:** The queue may hold Θ(V) neighbors at once even though the graph has only V−1 edges. Space depends on frontier width, not just path depth.

### Q15. What is the edge case when finding bridges in a graph with parallel edges?

**Answer:** Skipping every edge to the parent vertex incorrectly ignores a second parallel edge. Track parent edge identity, not only parent node.

### Q16. Why can recursive DFS fail on a valid long chain?

**Answer:** Depth can be O(V) and exceed the call stack. Use an explicit stack for inputs without a safe depth bound.

### Q17. How should union-find handle repeated union operations?

**Answer:** They should be idempotent: if roots already match, do not increase component count. Use path compression and union by rank or size.

### Q18. Can ordinary union-find handle arbitrary edge deletions?

**Answer:** No, its efficient operations only merge sets. Offline reverse processing or dynamic connectivity methods are needed for deletions.

### Q19. Why can topological order be non-unique?

**Answer:** Several zero-indegree vertices may be available at a step. If uniqueness matters, detect whether the choice set ever has more than one vertex.

### Q20. What is the correct starting rule for bipartite checking?

**Answer:** Start BFS/DFS from every uncolored vertex; a single component traversal misses disconnected odd cycles.

## DP, heaps, and string algorithms

### Q21. Why does one-dimensional 0/1 knapsack iterate capacities downward?

**Answer:** Downward iteration prevents the current item from being reused in the same pass. Upward iteration instead models unbounded reuse.

### Q22. Why can greedy coin change fail for a valid set of denominations?

**Answer:** Local largest-coin choice is not generally optimal for arbitrary denominations. Use DP or prove the denomination system has the needed property.

### Q23. What does a DP state need to capture to avoid invalid merging of histories?

**Answer:** All past information that changes future legal choices or cost. Prove histories mapped to one state have equivalent futures.

### Q24. Why might a memoized recursive solution still exceed time limits?

**Answer:** State count may be exponential, or a missing state dimension causes repeated work. Count unique states and transition cost.

### Q25. How can an LCS algorithm return the right length but wrong sequence?

**Answer:** Tie-breaking or backtracking can choose inconsistent parents. Store decisions or re-evaluate transitions with deterministic tie rules.

### Q26. Why is a rolling hash match not proof that substrings are equal?

**Answer:** Hash collisions are possible. Verify candidate substrings or use a collision policy appropriate to the correctness requirement.

### Q27. What can make a min-heap solution for top K frequent values return unstable tie order?

**Answer:** The heap comparator does not define deterministic tie-breaking. Add a secondary key if output order is specified.

### Q28. Why can a heap's array representation not be printed as sorted output?

**Answer:** Only each parent-child order is guaranteed, not sibling or global order. Repeatedly poll to produce sorted order.

### Q29. When is a monotonic deque better than a heap for a sliding-window maximum?

**Answer:** The deque removes dominated and expired indices in amortized O(1) per element; a heap needs lazy deletion or indexed removal.

### Q30. Why does KMP handle a partial pattern match without moving the text index backward?

**Answer:** The prefix table preserves the longest suffix that is also a pattern prefix. Each text position is examined a bounded number of times.

## References

- [Princeton Algorithms companion site](https://algs4.cs.princeton.edu/home/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
