# Online Assessment Practice: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Java language and collections

### Q1. What does this print: `System.out.println(1 + 2 + "3")`?

**Answer:** `33`; numeric addition happens first, then string concatenation.

**Example:** `"1" + 2 + 3` would produce `123`.

### Q2. What does this loop print: `for (int i=1; i<8; i*=2) System.out.print(i);`?

**Answer:** It prints `124`; i doubles from 1 to 2 to 4, then becomes 8 and stops.

**Example:** There are three iterations, so the complexity is O(log n).

### Q3. What is the output of `List.of(1,2).add(3)`?

**Answer:** It throws `UnsupportedOperationException` because `List.of` returns an unmodifiable list.

**Example:** Create a mutable `ArrayList` when mutation is required.

### Q4. What does `computeIfAbsent` do in a Map?

**Answer:** It computes and stores a value only when the key is absent or mapped to null where supported.

**Example:** `map.computeIfAbsent(k, key -> new ArrayList<>()).add(v)`.

### Q5. What does this comparator sort by: `Comparator.comparing(User::name).reversed()`?

**Answer:** It sorts names in descending order.

**Example:** Add `thenComparing(User::id)` for deterministic ties.

### Q6. Why can `Arrays.asList(array)` surprise you?

**Answer:** It returns a fixed-size list backed by the array; setting elements works, but adding or removing does not.

**Example:** Use `new ArrayList<>(Arrays.asList(array))` for a resizable copy.

### Q7. What does `Optional.map` return when the Optional is empty?

**Answer:** An empty Optional; the mapping function is not called.

**Example:** Use `orElseGet` when a fallback should be computed lazily.

### Q8. What happens when an `int` addition overflows?

**Answer:** Java wraps to the two's-complement result without throwing an exception.

**Example:** `Integer.MAX_VALUE + 1` evaluates to `Integer.MIN_VALUE`.

### Q9. What is the complexity of `ArrayList.remove(0)`?

**Answer:** O(n), because later elements must shift left.

**Example:** Appending is amortized O(1).

### Q10. What does `StringBuilder.toString()` return?

**Answer:** A String containing the builder's current characters; later builder changes do not change that String.

**Example:** Build inside a loop, then convert once.

## Algorithms and code tracing

### Q11. What is the time complexity of this loop: `for(i=1;i<n;i*=2)`?

**Answer:** O(log n), because i doubles each iteration.

**Example:** It visits powers of two below n.

### Q12. What is the space complexity of recursive depth-first search on a chain of n nodes?

**Answer:** O(n) call-stack space in the worst case.

**Example:** An iterative DFS can move this storage to an explicit heap stack.

### Q13. How do you search a matrix whose rows and columns are sorted?

**Answer:** Start at the top-right: move left if the value is too large, down if it is too small; time is O(rows + columns).

**Example:** This works because each move eliminates one row or column.

### Q14. How do you find the longest run of identical characters in a string?

**Answer:** Scan once, tracking the current run and the best run seen; time O(n), space O(1).

**Example:** Reset the run length when the current character changes.

### Q15. How do you identify an off-by-one error in a half-open range?

**Answer:** Write the invariant and test empty, one-element, and full-range cases; `[left,right)` includes left but excludes right.

**Example:** The length is `right - left`.

### Q16. What is the key invariant in a binary-search lower bound?

**Answer:** All positions before `lo` are too small; positions at or after `hi` are valid candidates.

**Example:** Continue while `lo < hi`.

### Q17. How can a monotonic stack solve next-greater-element?

**Answer:** Pop smaller values when a larger value arrives, assigning that value as their next greater result.

**Example:** Each item is pushed and popped once: O(n).

### Q18. What does dynamic programming memoization prevent?

**Answer:** It prevents repeated evaluation of the same subproblem state.

**Example:** The cache key must include every variable that affects the result.

### Q19. How do you compare two strings for anagram equality in O(n)?

**Answer:** Count character frequencies for one string and decrement for the other, after validating compatible length/character range.

**Example:** Use a map when the alphabet is not fixed.

### Q20. What is the expected complexity of hash-map lookup, and its worst case?

**Answer:** Expected O(1) under a good hash distribution; collisions can make it slower, with implementation-specific mitigation.

**Example:** Do not claim guaranteed constant time.

## SQL, HTTP, and data reasoning

### Q21. What does `COUNT(column)` exclude compared with `COUNT(*)`?

**Answer:** `COUNT(column)` excludes rows where that column is NULL; `COUNT(*)` counts rows.

**Example:** Use `COUNT(*)` for total rows.

### Q22. What does a LEFT JOIN return when there is no right-side match?

**Answer:** The left row remains and right-side columns are NULL.

**Example:** A later WHERE filter on the right table can accidentally remove those rows.

### Q23. How can you find duplicates by email?

**Answer:** Group by email and filter groups with count greater than one.

**Example:** `SELECT email FROM users GROUP BY email HAVING COUNT(*) > 1`.

### Q24. Which HTTP status can report that a long-running operation was accepted?

**Answer:** 202 Accepted; the server should provide a way to check the operation's later status.

**Example:** A 201 Created means a resource has already been created.

### Q25. Which status means the request is authenticated but forbidden?

**Answer:** 403 Forbidden.

**Example:** 401 indicates authentication is missing or invalid.

### Q26. What does an ETag validator help a client avoid?

**Answer:** Re-downloading an unchanged representation when the server returns 304 Not Modified.

**Example:** The client sends `If-None-Match`.

### Q27. How can you find customers who have no orders?

**Answer:** Use `NOT EXISTS` with a correlated subquery, or a LEFT JOIN filtered for a NULL match.

**Example:** `SELECT c.id FROM customer c WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id=c.id)`

### Q28. What is wrong with `WHERE created_at = '2026-01-01'` for a timestamp column?

**Answer:** It may match only midnight, not all times on that date.

**Example:** Use a half-open range from start-of-day to next-day.

### Q29. Why use a prepared statement for a user-supplied search value?

**Answer:** It keeps data separate from SQL syntax and helps prevent injection.

**Example:** Bind the input rather than concatenate it.

### Q30. What does a database transaction rollback do?

**Answer:** It discards uncommitted changes in that transaction, subject to the database and external side effects involved.

**Example:** An HTTP call made inside the transaction is not undone automatically.

## Timed coding and debugging

### Q31. How would you implement a frequency count in Java?

**Answer:** Use a map and merge each value with one: `counts.merge(x, 1, Integer::sum)`.

**Example:** Expected O(n) time.

### Q32. How can you remove duplicates while preserving input order?

**Answer:** Use a `LinkedHashSet` or stream `distinct()` on an ordered stream.

**Example:** `new ArrayList<>(new LinkedHashSet<>(values))`.

### Q33. How do you sort integers descending with streams?

**Answer:** Use a reversed comparator: `values.stream().sorted(Comparator.reverseOrder())`.

**Example:** For primitive arrays, sort then reverse or box to use a comparator.

### Q34. What should a Two Sum implementation do when the same value can be used only once?

**Answer:** Check the complement in the map before inserting the current value, then store current index.

**Example:** This avoids using one element twice when target is twice that value.

### Q35. How can you find the longest balanced-parentheses prefix?

**Answer:** Scan left to right with a balance count; stop if it becomes negative, and remember the last index where it returns to zero.

**Example:** This checks a prefix; the longest valid substring problem needs a different method.

### Q36. How should a coding solution handle empty input?

**Answer:** Return the problem-defined empty result before accessing an element or deriving a first index.

**Example:** Test empty and one-item arrays explicitly.

### Q37. What is the difference between stable and unstable sorting?

**Answer:** A stable sort preserves the relative order of items with equal keys; an unstable sort may reorder them.

**Example:** Use a stable sort when equal-priority records need original order.

### Q38. How do you estimate whether O(n²) is acceptable?

**Answer:** Use input limits and expected operation cost; 10⁴ elements may imply about 10⁸ pair checks.

**Example:** Ask about constraints before choosing an algorithm.

### Q39. What is a useful first step when a hidden test fails?

**Answer:** Check boundary conditions and the exact invariant, then reproduce with the smallest failing input.

**Example:** Inspect overflow, duplicates, empty input, and ordering.

### Q40. How should you explain a medium coding solution in an interview?

**Answer:** State the approach, invariant, complexity, and edge cases before walking through code.

**Example:** Then trace one normal and one boundary input.

## References

- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [PostgreSQL SQL documentation](https://www.postgresql.org/docs/current/sql.html)
- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
