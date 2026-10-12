# Online Assessment Practice: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Java output and type puzzles

### Q1. What does 1 << 32 evaluate to for an int in Java?

**Answer:** It evaluates to 1. For int shifts, only the low five bits of the distance are used, so 32 behaves like a shift by zero.

### Q2. Why does short value = 1; value += 1; compile without a cast?

**Answer:** Compound assignment includes an implicit narrowing conversion back to the left-hand type. The equivalent value = value + 1 requires an explicit cast.

### Q3. What does int i=0; print(i++ + ++i) evaluate to?

**Answer:** 2. Post-increment yields 0, then pre-increment yields 2.

### Q4. Why might Integer values 127 compare true with == while 128 values do not?

**Answer:** Wrapper caching guarantees only a small range. Use equals for value comparison.

### Q5. What happens when null is concatenated with a string?

**Answer:** It becomes the text null; calling a method on the null reference instead throws NullPointerException.

### Q6. What does casting 3.9 to int do?

**Answer:** It truncates toward zero rather than rounding.

### Q7. What does integer expression 1/2 evaluate to?

**Answer:** 0 due to integer division. Use floating operands or a decimal type.

### Q8. Why can an empty catch block make a test pass unexpectedly?

**Answer:** It suppresses failure and continues with invalid state. Assert or propagate expected failures.

### Q9. What does a return in finally do if try already returned?

**Answer:** The finally return overrides the earlier result. Avoid returning from finally.

### Q10. What is the size of Arrays.asList(new int[]{1,2}) and why?

**Answer:** It is one: the primitive int array is treated as a single varargs element. Use an integer array or box elements to create two list entries.

## Algorithm and data-structure traps

### Q11. For sorted [1,2,2,2,4], what does ordinary binary search guarantee for target 2?

**Answer:** Only some matching index, not first or last. Use lower_bound or upper_bound logic for boundaries.

### Q12. Why does two-sum check the map before inserting the current value?

**Answer:** It prevents using the same array position twice; insertion afterward enables future matches.

### Q13. For prefix-sum counting of zero-sum subarrays, what initial frequency is required?

**Answer:** Initialize prefix zero with count one so a subarray beginning at index zero is counted.

### Q14. Why does sliding-window sum break when values can be negative?

**Answer:** The sum is not monotonic as the window changes. Use prefix sums or another proven invariant.

### Q15. What is the complexity of merging k sorted lists with a min-heap?

**Answer:** O(N log k) time and O(k) heap space for N total values.

### Q16. Why can DFS visited=true on first encounter miss a directed cycle?

**Answer:** A visited node may be completed, not on the active path. Track recursion-stack or three-color state.

### Q17. What bug appears if Dijkstra uses a negative edge?

**Answer:** Greedy finalization can be wrong because a later path may reduce a finalized distance. Use Bellman-Ford or another valid algorithm.

### Q18. What does a stable sort preserve?

**Answer:** Original relative order for comparator ties. It does not make nondeterministic input order deterministic.

### Q19. How do you find the first non-repeating character after frequency counting?

**Answer:** Count in one pass, then scan original order for count one. O(n) time and O(alphabet) space.

### Q20. Why is comparator (a,b)->a-b unsafe for large integers?

**Answer:** Subtraction can overflow and reverse order. Use Integer.compare.

## SQL, HTTP, and reasoning traps

### Q21. Why does WHERE value = NULL not find null rows?

**Answer:** SQL uses three-valued logic; the result is UNKNOWN. Use IS NULL.

### Q22. What happens if a right-table WHERE filter follows a LEFT JOIN?

**Answer:** Unmatched rows are filtered out, so the result can act like an inner join for that condition.

### Q23. Why can NOT IN with a nullable subquery return no rows?

**Answer:** NULL makes comparisons UNKNOWN. Prefer NOT EXISTS or explicitly exclude nulls.

### Q24. Does DELETE always reset an identity sequence?

**Answer:** No. DELETE and TRUNCATE differ by database; check identity, trigger, constraint, and transaction semantics.

### Q25. Can HTTP PUT be retried after a lost response?

**Answer:** It is idempotent by intended semantics if implemented consistently. Use If-Match when concurrent writes matter.

### Q26. Why should GET not delete a record?

**Answer:** GET is safe and may be prefetched or crawled. Use an unsafe method for state changes.

### Q27. What is printed by System.out.println(1.0 / 0.0)?

**Answer:** Positive infinity. Floating-point division follows IEEE 754; integer division by zero instead throws ArithmeticException.

### Q28. Why can 202 not be treated as completed success?

**Answer:** It means accepted for processing, not finished. Poll a status resource or receive completion.

### Q29. How should you approach an unfamiliar timed coding problem?

**Answer:** Clarify constraints, give a correct baseline, identify the bottleneck, then prove an optimized approach and test edges.

### Q30. What does Arrays.binarySearch return when the key is absent?

**Answer:** It returns a negative insertion-point encoding, not simply -1. Decode it as -(insertion point)-1; duplicates may match any occurrence.

## References

- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [PostgreSQL SQL documentation](https://www.postgresql.org/docs/current/sql.html)
- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
