# Online Assessment Practice: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## undefined

### Q1. What is printed by this code, and why: List<String> a = new ArrayList<>(); List<?> b = a;

**Answer:** Printing b.size() is valid; adding a String through b is not because its element type is an unknown capture. The wildcard preserves safe reads without an unsafe write.

### Q2. How can you remove duplicates while preserving input order in Java?

**Answer:** Use a LinkedHashSet or stream distinct on an ordered stream. Expected O(n) time and O(n) extra space; define equality semantics for elements.

### Q3. A map key's field used by hashCode changes after insertion. What lookup result do you expect?

**Answer:** Lookup may fail because the key is now searched in a different bucket. Use immutable keys or remove and reinsert after mutation.

### Q4. How would you implement a bounded LRU cache with O(1) get and put?

**Answer:** Use a hash map plus a doubly linked list; move hits to front and evict tail at capacity. Expected O(1) operations and O(capacity) space.

### Q5. How can a Java method safely publish an immutable configuration object to worker threads?

**Answer:** Construct it fully, then assign through a final field, volatile reference, synchronized block, or thread-safe container. Do not let this escape during construction.

### Q6. What is the bug in this code: if (map.get(k)==null) map.put(k, create())?

**Answer:** Two threads can both observe absence and create competing values; null also may mean absent or mapped null. Use computeIfAbsent with a short mapping function.

### Q7. How should you count frequencies for a large array of bounded integers?

**Answer:** Use an int array indexed by value offset for O(n+range) time and O(range) space; use a map when the value domain is sparse.

### Q8. Why does parallelStream().forEach(list::add) risk corruption or lost values?

**Answer:** ArrayList is not thread-safe, and parallel workers mutate shared state concurrently. Use a collector or a thread-safe sink, preferably avoiding shared mutation.

### Q9. How can you implement a thread-safe lazy singleton without explicit locking?

**Answer:** Use the initialization-on-demand holder idiom: a static nested holder initializes once on class use. Class initialization provides safe publication.

### Q10. A CompletableFuture pipeline hangs when it calls a blocking HTTP client. What change would you make?

**Answer:** Run blocking calls on a dedicated bounded executor or use an async client. Do not starve the common ForkJoinPool.

## undefined

### Q11. Find the longest substring with at most K distinct characters.

**Answer:** Use a sliding window and frequency map; expand right and shrink until distinct count ≤ K. O(n) time, O(k) space.

### Q12. Find the number of subarrays summing to K when values may be negative.

**Answer:** Track prefix-sum frequencies; for each prefix p, add count[p−K], then increment count[p]. Expected O(n) time, O(n) space.

### Q13. Find the top K frequent values from a large array.

**Answer:** Count frequencies, then keep a size-K min-heap by frequency: O(n log k) time and O(n+k) space.

### Q14. Merge overlapping intervals and preserve the original endpoints.

**Answer:** Sort by start, then extend the last output interval when ranges overlap. O(n log n) time and O(n) output space; define touching-boundary semantics.

### Q15. Detect whether a directed graph has a cycle.

**Answer:** Use DFS colors or Kahn's algorithm; if topological processing emits fewer than V nodes, a cycle exists. O(V+E) time.

### Q16. Find shortest paths in a weighted graph with nonnegative edges.

**Answer:** Use Dijkstra with a min-priority queue and stale-entry check. O((V+E) log V) with a binary heap.

### Q17. Find the kth-smallest value in two sorted arrays without merging them.

**Answer:** Binary-search a partition on the shorter array and compare boundary values. O(log min(m,n)) time and O(1) extra space.

### Q18. How would you find the minimum number of coins for a target when denominations are arbitrary?

**Answer:** Use bottom-up DP over amounts, relaxing each coin; greedy is not generally valid. O(target·coins) time, O(target) space.

### Q19. Implement a queue using two stacks and explain amortized complexity.

**Answer:** Push to input stack; pop from output stack, transferring only when empty. Each item moves once, so amortized O(1) per operation.

### Q20. Given a binary tree, return the lowest common ancestor of two nodes.

**Answer:** Use recursive postorder: return current if it is either target; combine left/right hits, and define behavior when a target is missing. O(n) time.

## undefined

### Q21. Write SQL to return the latest row per customer with deterministic tie-breaking.

**Answer:** Use ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY created_at DESC, id DESC), then keep row_number=1. Add an index matching grouping/order needs.

### Q22. Why is NOT IN dangerous when the subquery can return NULL?

**Answer:** Three-valued logic makes comparisons UNKNOWN and can filter every candidate. Use NOT EXISTS or ensure the subquery excludes NULL.

### Q23. Given orders and payments, how do you find orders with no successful payment?

**Answer:** Use NOT EXISTS correlated on order ID and successful status. Index payment order/status columns and avoid join duplication.

### Q24. Why can SELECT COUNT(*) and SELECT * with LIMIT have different cost?

**Answer:** COUNT may scan matching rows or index entries to count them all; LIMIT can stop early when an access path finds enough rows.

### Q25. An API returns 202 for a job. What resource should the client poll?

**Answer:** Return a stable operation URL with status, timestamps, terminal result/error, and retry guidance. Ensure polling is authorized and rate-limited.

### Q26. Can a client safely retry a PUT after a timeout?

**Answer:** HTTP PUT is defined as idempotent by intent, but the implementation must replace the same resource consistently. Use version preconditions when concurrent writes matter.

### Q27. A Kafka consumer commits offset before writing to SQL. What crash bug follows?

**Answer:** A crash after commit but before SQL loses the effect because the broker will not redeliver. Commit after durable processing and make retries idempotent.

### Q28. A Kafka consumer writes SQL then crashes before committing offset. What design is needed?

**Answer:** Expect redelivery and deduplicate by event ID in the SQL transaction. Kafka offset and SQL commit are not atomic by default.

### Q29. How do you distinguish a 401 from a 403 in an API test?

**Answer:** 401 means credentials are missing or invalid; 403 means the authenticated principal lacks permission. Assert that protected side effects did not run.

### Q30. What does an index on (tenant_id, created_at) help when a query filters only by created_at?

**Answer:** The leading tenant_id is missing, so the index may not efficiently seek by created_at alone. Consider a separate index if that access path is important.

## undefined

### Q31. A solution sorts input but the prompt asks to preserve original order among ties. What must the comparator include?

**Answer:** Add a stable tie-breaker such as original index, or use a stable sort with a comparator that returns equality for ties. Verify the language/library guarantee.

### Q32. Your algorithm is O(n²) and n can be 200,000. What should you do before coding?

**Answer:** Estimate operations, identify repeated work, and seek sorting, hashing, or a monotonic structure near O(n log n) or O(n). State constraints and trade-offs.

### Q33. How do you avoid integer overflow in a product comparison?

**Answer:** Promote before multiplication to long, or compare using division with sign and zero handling. For arbitrary ranges use checked arithmetic or BigInteger.

### Q34. A recursive DFS stack-overflows on a long chain. What alternative can you use?

**Answer:** Use an explicit stack and iterative traversal, preserving enter/exit state if postorder is needed. Space remains O(V), but avoids call-stack limits.

### Q35. A solution passes examples but fails hidden tests. What edge cases do you inspect first?

**Answer:** Empty/singleton input, duplicates, negative values, boundaries, overflow, disconnected graphs, and already sorted/reversed data. Trace the invariant, not just the code.

### Q36. How would you state complexity for a hash-based algorithm honestly?

**Answer:** Expected O(n) time under reasonable hash distribution, O(n) space; mention worst-case behavior and whether adversarial keys are relevant.

### Q37. A coding prompt is ambiguous about Unicode characters. What clarification matters?

**Answer:** Ask whether units are bytes, UTF-16 code units, Unicode code points, or grapheme clusters. Java char iteration does not equal user-perceived character iteration.

### Q38. A reviewer spots a race in your correct-looking algorithm. How do you respond under interview time?

**Answer:** Identify the shared state and invariant, propose synchronization or ownership partitioning, then discuss contention and prove visibility/atomicity.

### Q39. How should you explain a dynamic-programming answer in five minutes?

**Answer:** Define state and recurrence, justify base cases and order, show one example, then give time/space and any reconstruction strategy.

### Q40. What should you do if you cannot finish coding before the timer ends?

**Answer:** Present a correct brute-force baseline, identify its bottleneck, outline the optimized invariant, and state unverified details honestly.


## References

- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [PostgreSQL SQL documentation](https://www.postgresql.org/docs/current/sql.html)
- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
