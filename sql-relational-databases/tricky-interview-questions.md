# SQL and Relational Databases: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## NULL, joins, and aggregation traps

### Q1. Why can NOT IN return no rows when the subquery contains NULL?

**Answer:** Comparisons with NULL evaluate UNKNOWN under three-valued logic, so the predicate is not true. Use NOT EXISTS or filter nulls.

### Q2. What is the difference between COUNT(*) and COUNT(column)?

**Answer:** COUNT(*) counts rows; COUNT(column) ignores NULL values in that column.

### Q3. Why can a WHERE condition on the right table undo a LEFT JOIN?

**Answer:** Unmatched rows have NULL right columns and get filtered out. Put the condition in ON when unmatched left rows must remain.

### Q4. Can a comparison to NULL find null values?

**Answer:** No. Equality with NULL yields UNKNOWN; use IS NULL or IS NOT DISTINCT FROM where supported.

### Q5. Why can COUNT after joining two one-to-many tables be too large?

**Answer:** The joins multiply child rows per parent. Aggregate each side first or count distinct keys if that matches the requirement.

### Q6. Does GROUP BY treat NULL values as equal groups?

**Answer:** SQL grouping generally places nulls together, even though NULL=NULL is not true in a predicate.

### Q7. Why can HAVING remove groups that WHERE could not?

**Answer:** WHERE filters rows before grouping; HAVING filters aggregated groups afterward. Put row predicates early unless aggregation is needed.

### Q8. What does a scalar subquery do if it returns multiple rows?

**Answer:** It raises a cardinality error in standard behavior; use a join, aggregate, or deterministic row selection.

### Q9. Why can SELECT DISTINCT still return two rows that look identical?

**Answer:** They may differ in another selected column or collation/type. DISTINCT applies to the complete selected tuple.

### Q10. Why is NOT EXISTS often safer than NOT IN for anti-joins?

**Answer:** NOT EXISTS is not poisoned by a NULL in the subquery result. Ensure the correlation predicate is correct and indexed.

## Transactions and concurrency traps

### Q11. Can two transactions both pass a count-below-capacity check and then overbook?

**Answer:** Yes, if they read concurrently and insert independently. Enforce capacity with a lock, serializable transaction, or atomic update.

### Q12. Does a sequence guarantee gap-free numbering?

**Answer:** No. Rollbacks and concurrent allocation can leave gaps. Use sequences for identifiers, not gapless business numbering.

### Q13. Why can a read-only transaction observe changing data across statements?

**Answer:** Under READ COMMITTED each statement may see a new snapshot. Choose isolation based on required repeatability.

### Q14. Can a local transaction be atomic across two independent database connections?

**Answer:** Not by ordinary local transaction management. Use distributed coordination or redesign with an outbox or saga.

### Q15. Why might SERIALIZABLE abort a transaction without a SQL syntax error?

**Answer:** The database detects an anomaly and aborts one participant to preserve correctness. Retry the whole transaction safely.

### Q16. What does SUM(amount) return for an empty input compared with COUNT(*)?

**Answer:** SUM returns NULL when there are no input rows, while COUNT(*) returns zero. Use COALESCE only when zero is the intended domain value.

### Q17. Why can deadlock happen if each transaction updates only one row?

**Answer:** Triggers, foreign-key checks, or other statements may acquire additional locks in different orders. Inspect the deadlock graph.

### Q18. Can an application retry a transaction after a deadlock without changing behavior?

**Answer:** Only if the whole operation is safe to replay and has no external effects. Retry after rollback with bounded backoff.

### Q19. Why may commit succeed while a later replica read is stale?

**Answer:** Replication can lag after the primary commit. Route read-after-write to the primary or use a consistency mechanism.

### Q20. What does autocommit mean for two JDBC statements?

**Answer:** Each may commit independently unless an explicit transaction is started. Failure in the second does not undo the first.

## Indexes and query-plan puzzles

### Q21. Why may an index on (a,b) not help much for a predicate on b alone?

**Answer:** The leading a key defines the ordered range; b-only lookup may scan much of the index. Match order to access patterns.

### Q22. Can a query use an index and still be slow?

**Answer:** Yes. It may fetch scattered rows or choose a poor plan. Compare actual row counts, buffers, and execution time.

### Q23. Why can OR conditions change an indexed query into a sequential scan?

**Answer:** Combined selectivity or access-path costs may make scanning cheaper. Inspect the plan before rewriting.

### Q24. Does applying LOWER() to an indexed column always prevent index use?

**Answer:** A plain index often cannot satisfy the transformed predicate; an expression index or normalized column may help. Verify the plan.

### Q25. Why can LIMIT 10 still scan millions of rows?

**Answer:** If filtering or ordering has no useful index, the engine may inspect many rows before finding ten matches.

### Q26. What is dangerous about EXPLAIN ANALYZE on DELETE?

**Answer:** It executes the delete. Use a transaction and roll back only when trigger or external side effects are known to be reversible.

### Q27. Can a covering index make a query slower?

**Answer:** It adds write and storage cost and can increase cache pressure. Measure whether its read benefit repays that cost.

### Q28. Why can a prepared statement get a bad plan for one parameter value?

**Answer:** A generic plan may ignore skewed selectivity. Compare parameter-specific and generic plans where supported.

### Q29. How can stale statistics affect a join?

**Answer:** Incorrect cardinality estimates can produce the wrong join order or algorithm. Compare estimated and actual rows, then refresh targeted stats.

### Q30. Why can a query's result be empty even when SUM is written with COALESCE?

**Answer:** COALESCE changes a NULL aggregate result to zero but does not create a row when GROUP BY has no groups. Handle the empty-set result shape separately.

## References

- [PostgreSQL SQL command reference](https://www.postgresql.org/docs/current/sql.html)
- [PostgreSQL table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
