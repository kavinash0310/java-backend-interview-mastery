# SQL and Relational Databases: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Plans, indexes, and query shape

### Q1. How does column order in a multicolumn B-tree index affect usable predicates?

**Answer:** The leading columns determine the contiguous key range; predicates on later columns alone may not seek efficiently. Match index order to common filters and ordering.

### Q2. When can a partial index outperform a full index?

**Answer:** When queries repeatedly target a selective predicate such as active rows, a smaller index reduces storage and write cost. The planner must prove the query implies the predicate.

### Q3. Why can an index-only scan still visit table pages?

**Answer:** The visibility map may not show tuples as all-visible, so MVCC visibility checks require heap access. Vacuum and workload patterns affect this benefit.

### Q4. How can stale statistics cause a bad join order?

**Answer:** Wrong cardinality estimates distort the cost model, causing poor join methods or order. Refresh statistics and inspect estimates versus actual row counts.

### Q5. Why can EXPLAIN ANALYZE itself be dangerous on a write query?

**Answer:** It executes the statement. Wrap carefully in a transaction and roll back only when side effects are safe; triggers or external effects may not be reversible.

### Q6. How should you diagnose a query that is fast alone but slow under load?

**Answer:** Inspect lock waits, pool queueing, cache misses, I/O, and plan changes under concurrency. Isolated latency omits resource contention.

### Q7. What is a sargable predicate, and why does it matter?

**Answer:** It can use an index search condition without transforming every row. Avoid wrapping indexed columns in functions unless a matching expression index exists.

### Q8. When can a parameterized query get a poor generic plan?

**Answer:** A generic plan may ignore value-specific selectivity for skewed parameters. Compare custom and generic plans and use database-specific plan controls only with evidence.

### Q9. How do BRIN and B-tree indexes suit different data distributions?

**Answer:** BRIN summarizes physically correlated ranges with low storage; B-tree indexes individual keys and support selective lookup. Choose based on correlation, selectivity, and update pattern.

### Q10. When can a selective index still cause many heap reads?

**Answer:** If matching index entries point to scattered pages, the engine may perform many random heap fetches. Compare buffers and actual rows; a bitmap or sequential scan can be cheaper. Each insert or update must maintain indexes, increasing WAL, storage, and contention. Index only access paths that justify their ongoing write cost.

## Isolation, locking, and correctness

### Q11. What anomaly can repeatable read still permit in some database implementations?

**Answer:** Standard repeatable read may allow write skew because transactions read the same snapshot then update disjoint rows. Use serializable isolation or explicit constraints/locks.

### Q12. How should an application handle a serialization failure?

**Answer:** Retry the entire transaction from a clean boundary with bounded jitter, because the database intentionally aborted one concurrent schedule.

### Q13. Why does SELECT FOR UPDATE not protect rows that do not yet exist?

**Answer:** There is no row to lock, so concurrent transactions can both observe absence and insert. Use a unique constraint, serializable isolation, or a suitable key-range lock.

### Q14. What is write skew, and how can a database constraint prevent it?

**Answer:** Two transactions each observe a valid global condition and update different rows so the combined result violates it. Encode the invariant as a constraint or serialize access.

### Q15. How can lock ordering reduce deadlocks in a transfer operation?

**Answer:** Always lock accounts in a stable key order, then update both in one transaction. Consistent ordering removes a common circular wait.

### Q16. Why is a transaction timeout not a substitute for query optimization?

**Answer:** It bounds how long a session holds resources but may still cause repeated failure and rollback work. Find the slow plan and lock path.

### Q17. What is the difference between optimistic conflict detection and pessimistic row locking?

**Answer:** Optimistic checks a version at commit and retries conflicts; pessimistic reserves a row before work. Choose based on contention and acceptable retry cost.

### Q18. How can a unique constraint make an application-level check-then-insert race safe?

**Answer:** The constraint arbitrates concurrent inserts atomically. Handle the duplicate-key result as an expected conflict rather than trusting a prior SELECT.

### Q19. Why can a long-running read transaction cause storage bloat in MVCC databases?

**Answer:** It may hold an old snapshot that prevents cleanup of dead row versions. Keep transactions short and inspect idle-in-transaction sessions.

### Q20. When does SERIALIZABLE isolation need application retry logic?

**Answer:** The database may abort one transaction to preserve serial equivalence. Treat serialization errors as transient only when the whole operation is safe to retry.

## Data modeling and advanced SQL

### Q21. How do you enforce non-overlapping time ranges in a relational schema?

**Answer:** Use a database exclusion constraint or an equivalent locking/serialization design. A pre-insert overlap query alone races under concurrent writers.

### Q22. When should a partial unique index represent a business rule?

**Answer:** Use it when uniqueness applies only to rows matching a predicate, such as one active subscription per user. Keep the predicate aligned with domain semantics.

### Q23. Why are nullable columns tricky in unique constraints?

**Answer:** Databases differ in how multiple NULLs are treated for uniqueness, and NULL means unknown rather than equal. Define the desired rule explicitly with constraints or indexes.

### Q24. How can window functions replace a self-join for ranking within groups?

**Answer:** Use ROW_NUMBER, RANK, or DENSE_RANK partitioned by group and ordered by deterministic keys. Tie behavior differs, so select the correct ranking function.

### Q25. What does a recursive CTE need to avoid infinite traversal?

**Answer:** A clear termination predicate and often cycle detection or a visited-path representation. Limit depth defensively when data can be malformed.

### Q26. Why is keyset pagination more stable than offset pagination during inserts?

**Answer:** It resumes from an ordered key instead of skipping a moving number of rows. It still needs deterministic ordering and a policy for changing sort keys.

### Q27. How should you model a money amount to avoid invalid cross-currency arithmetic?

**Answer:** Store amount and currency together and enforce currency-aware operations. Decimal precision alone cannot make USD and EUR values add meaningfully.

### Q28. What is a safe strategy for a zero-downtime column type change?

**Answer:** Add a compatible new column, dual-write or backfill in batches, compare values, switch readers, then remove the old column after rollback risk passes.

### Q29. Why can a soft-delete flag complicate uniqueness and foreign keys?

**Answer:** Deleted rows still occupy keys and remain referenced unless rules are designed for active records. Consider partial indexes and explicit lifecycle semantics.

### Q30. How can generated columns or expression indexes help without duplicating application logic?

**Answer:** They let the database index a deterministic derived value. Keep expressions stable, immutable where required, and consistent with query predicates.

## Performance and operations

### Q31. A table has hundreds of millions of rows. What do you check before partitioning it?

**Answer:** Confirm the partition key matches pruning predicates, estimate per-partition size, and plan uniqueness, foreign keys, retention, and migration operations.

### Q32. How can partitioning make a query slower?

**Answer:** If pruning cannot eliminate partitions or too many partitions are planned, planning and execution overhead can rise. Verify actual partition pruning in the plan.

### Q33. Why can a connection pool be saturated while database CPU is low?

**Answer:** Connections may wait on locks, network, slow clients, or oversized transactions. Inspect wait events and pool acquisition latency instead of adding connections blindly.

### Q34. How do you reduce impact of a large backfill on production queries?

**Answer:** Use small committed batches, rate limits, indexed key ranges, and pause controls. Monitor replica lag, lock waits, WAL, and user-facing latency.

### Q35. What does replication lag mean for a read-after-write API?

**Answer:** A replica may not yet contain the committed write. Route read-after-write to the primary or use a consistency token/wait policy.

### Q36. Why should database failover tests include client retry behavior?

**Answer:** Connections break during promotion and retries can duplicate writes or stampede the new leader. Use bounded reconnects and idempotent operation semantics.

### Q37. How do you tell lock contention from slow disk I/O?

**Answer:** Inspect database wait events, lock graphs, query timings, and storage latency. A query duration alone cannot distinguish blocked time from execution time.

### Q38. What is the operational cost of an unused index?

**Answer:** It consumes disk and cache, adds write work, and must be maintained. Confirm with usage counters over representative workload and account for infrequent queries.

### Q39. Why should a schema migration be tested against production-sized data?

**Answer:** Locks, table rewrites, index build time, WAL, and replica lag scale with data volume. Test duration and rollback behavior using realistic copies.

### Q40. How should a slow-query alert avoid noisy one-off reports?

**Answer:** Aggregate by normalized query, track rate and tail latency against service impact, and sample plans safely. Avoid capturing sensitive literal parameters.


## References

- [PostgreSQL SQL command reference](https://www.postgresql.org/docs/current/sql.html)
- [PostgreSQL table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
