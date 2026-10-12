# Data Structures and Algorithms: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Proofs, complexity, and array techniques

### Q1. How do you prove a two-pointer algorithm is correct rather than just test examples?

**Answer:** State an invariant, show each move cannot discard a valid answer, and prove termination. Use sorted order or another explicit property to justify each step.

### Q2. Why can an O(n log n) solution beat an O(n) solution in practice?

**Answer:** Constants, memory locality, branch behavior, and implementation overhead matter. Benchmark representative input sizes while retaining the asymptotic analysis.

### Q3. How do negative values break the usual sliding-window sum technique?

**Answer:** The window sum is no longer monotonic as endpoints move, so shrinking may discard a valid range. Use prefix sums with a monotonic deque or another invariant.

### Q4. How do you find the shortest subarray with sum at least K when numbers may be negative?

**Answer:** Build prefix sums and maintain increasing prefix indices in a deque; discard dominated prefixes and test the front. Complexity is O(n).

### Q5. What is the difference between amortized analysis and average-case analysis?

**Answer:** Amortized analysis bounds a sequence of operations in the worst case using potential or aggregate cost; average-case relies on a probability model.

### Q6. When is coordinate compression useful, and what information does it preserve?

**Answer:** It maps ordered distinct values to dense ranks, preserving comparisons but not numeric distances. Use it for Fenwick trees or segment trees over sparse coordinates.

### Q7. How do you prevent overflow in a prefix-sum algorithm?

**Answer:** Choose a wider type based on maximum n and value magnitude, and validate multiplication before addition. int overflow can silently corrupt comparisons.

### Q8. Why does a monotonic stack solve next-greater-element queries in linear time?

**Answer:** Each index is pushed and popped at most once. The stack maintains candidates whose answers have not yet been resolved.

### Q9. How would you find the maximum sum of a fixed-length window when values stream in?

**Answer:** Keep a rolling sum, subtract the outgoing value, add the incoming value, and track the maximum. Use O(1) extra state.

### Q10. When is a difference array more efficient than updating every range element?

**Answer:** For many offline range additions, mark +delta at the start and -delta after the end, then prefix-sum once. Each range update becomes O(1).

## Trees, graphs, and union-find

### Q11. Why does Dijkstra fail with negative edge weights even if the graph has no negative cycle?

**Answer:** It finalizes a node under the assumption that no later path can improve it; a negative edge can violate that assumption. Use Bellman-Ford or another suitable method.

### Q12. How does Bellman-Ford detect a reachable negative cycle?

**Answer:** Relax all edges V−1 times, then check whether any reachable edge can still relax. Guard unreachable distances to avoid overflow or false positives.

### Q13. What does a topological ordering tell you, and how can you detect a cycle while building one?

**Answer:** A directed acyclic graph has an order where every edge points forward. Kahn's algorithm detects a cycle if fewer than V vertices are emitted.

### Q14. When would you use Tarjan's algorithm for strongly connected components?

**Answer:** Use its DFS low-link values to partition a directed graph into maximal mutually reachable groups in O(V+E), without transposing the graph.

### Q15. How does union-find's inverse-Ackermann bound arise, and what are its practical assumptions?

**Answer:** Path compression plus union by rank/size makes operations near constant amortized time. It supports connectivity merges, not arbitrary edge deletions.

### Q16. Why can a minimum spanning tree be unique even when edge weights tie?

**Answer:** Ties do not guarantee multiple MSTs; the graph structure may still force one. Uniqueness can be reasoned about by equal-weight alternatives across cut or cycle choices.

### Q17. How would you identify bridges in an undirected graph?

**Answer:** Use DFS discovery times and low-link values; a tree edge is a bridge when the child's low value exceeds the parent's discovery time. Handle the parent edge correctly.

### Q18. Why can BFS find shortest paths in an unweighted graph but not a weighted one?

**Answer:** Each edge has equal cost, so first discovery uses the fewest edges. With nonnegative weights, use Dijkstra or a specialized 0-1 BFS.

### Q19. How do you handle duplicate edges and self-loops in graph algorithms?

**Answer:** Define whether the graph is simple or multigraph, normalize adjacency, and ensure degree/count logic matches that model. Self-loops can affect cycle and topological checks.

### Q20. What is a bidirectional search trade-off for shortest paths?

**Answer:** It can reduce explored nodes dramatically when branching is high and the target is known, but needs reverse traversal and careful meeting/termination logic.

## Strings, heaps, and range structures

### Q21. How does KMP avoid rescanning text after a mismatch?

**Answer:** The prefix-function table retains the longest suffix that is also a pattern prefix, so matching resumes from that border. Preprocessing and search are O(n+m).

### Q22. When is a rolling hash useful, and how do you control collision risk?

**Answer:** It gives fast substring fingerprints but collisions are possible. Use a large modulus or double hash, then verify candidate matches when correctness is critical.

### Q23. Why can a trie use more memory than a hash map for prefix search?

**Answer:** Each prefix node and child structure has overhead, especially for sparse alphabets. Compress unary paths or use a radix trie when memory matters.

### Q24. How do you find the kth-largest element without sorting the full array?

**Answer:** Maintain a min-heap of size k for O(n log k), or use selection for expected O(n) when mutation and worst-case behavior are acceptable.

### Q25. What is the difference between a sparse table and a segment tree?

**Answer:** Sparse tables answer idempotent static range queries in O(1) after preprocessing; segment trees support updates in O(log n) and broader associative operations.

### Q26. How can a Fenwick tree support order-statistic search?

**Answer:** Maintain frequencies and binary-lift over prefix sums to find the smallest index reaching a target rank. Counts must remain nonnegative for monotonic prefixes.

### Q27. Why does a heap not provide sorted iteration?

**Answer:** It only guarantees the root is extreme and each parent respects its children. Repeated extraction gives sorted order but costs O(n log n).

### Q28. How do you merge k sorted streams efficiently?

**Answer:** Keep one head per stream in a min-heap, emit the smallest, then add its next item. Complexity is O(N log k) with O(k) state.

### Q29. Why might a Bloom filter return a false positive but not a false negative?

**Answer:** Multiple inserted keys share bits, so a query may see every bit set by other keys. Bits only transition to set; deletion requires a counting variant.

### Q30. When would an interval tree be preferable to sorting intervals once?

**Answer:** Use one when queries ask repeatedly which intervals overlap a range while the set changes. For a one-time offline query, sorting or a sweep may be simpler.

## Dynamic programming and advanced design

### Q31. How do you choose a DP state that is sufficient but not redundant?

**Answer:** Define exactly what history affects future choices, then prove two histories with the same state have identical remaining options and costs.

### Q32. When does memoization use more memory than bottom-up dynamic programming?

**Answer:** It retains visited states and recursion overhead; bottom-up can reuse rolling rows when transitions need only a bounded prior window.

### Q33. How do you reconstruct a solution from a dynamic-programming table?

**Answer:** Store predecessor decisions or re-evaluate transitions while walking backward from the optimum. Track tie-breaking explicitly if output must be deterministic.

### Q34. Why can a greedy algorithm pass many tests but still be wrong?

**Answer:** Local choices need an exchange or matroid-style proof that an optimal solution can be transformed to include the choice. Counterexamples reveal missing conditions.

### Q35. What is the difference between 0/1 and unbounded knapsack loop direction?

**Answer:** For 0/1, iterate capacity downward so each item is used once; upward iteration allows the current item to be reused.

### Q36. How can bitmask DP reduce a subset problem to O(n·2^n)?

**Answer:** Represent chosen elements as an n-bit state and transition by adding one unused choice. It is practical only for small n due to exponential growth.

### Q37. When is meet-in-the-middle a good alternative to exponential search?

**Answer:** Split n items into halves, enumerate each in O(2^(n/2)), then combine sorted summaries. It trades memory for a lower exponential exponent.

### Q38. How do you use a segment tree to answer range minimum queries with point updates?

**Answer:** Store each interval's minimum; update one leaf and recompute ancestors, while queries combine O(log n) covered nodes.

### Q39. Why is an LRU cache usually implemented with both a hash map and a doubly linked list?

**Answer:** The map finds nodes in O(1) expected time; the list moves or evicts least-recent items in O(1).

### Q40. What should an algorithm interview answer include beyond its final code?

**Answer:** State assumptions, invariant, correctness argument, time and space bounds, edge cases, and a small trace; then discuss alternatives when constraints change.


## References

- [Princeton Algorithms companion site](https://algs4.cs.princeton.edu/home/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
