# SQL and Relational Databases: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Indexes and query plans

### Q1. How does a composite B-tree index use its column order?

**Answer:** It is most effective when a query constrains the leading columns; an index on `(tenant_id, created_at)` is not generally equivalent to the reverse order.

**Example:** Match index order to common filters and sort order.

### Q2. What is a covering index?

**Answer:** An index that contains all columns needed by a query, allowing the database to answer without fetching the table row in some plans.

**Example:** A small projection may be covered by its filter and selected columns.

### Q3. What makes a predicate non-sargable?

**Answer:** Applying a function or transformation to the indexed column can prevent an efficient index lookup.

**Example:** Prefer a range on `created_at` over `DATE(created_at) = ...`.

### Q4. When can a partial index help?

**Answer:** When queries repeatedly target a selective subset of rows, an index limited to that predicate can be smaller and faster.

**Example:** Index only active records with `WHERE status = 'ACTIVE'`.

### Q5. What does EXPLAIN ANALYZE add beyond EXPLAIN?

**Answer:** It executes the query and reports actual timings and row counts alongside the plan, so use it carefully on expensive statements.

**Example:** Compare estimated rows with actual rows.

### Q6. Why do database statistics matter to a query planner?

**Answer:** The planner estimates selectivity and row counts from statistics; stale estimates can lead to poor join or scan choices.

**Example:** Refresh statistics after major data changes.

### Q7. Why can an index be ignored even when a filter column is indexed?

**Answer:** The query may return a large fraction of rows, statistics may favor a scan, or type/collation mismatch may prevent a usable predicate.

**Example:** Inspect the chosen plan before adding another index.

### Q8. How can keyset pagination outperform OFFSET for deep pages?

**Answer:** It seeks from the last seen sort key rather than scanning and discarding an increasingly large offset.

**Example:** `WHERE id > :lastId ORDER BY id LIMIT :n`.

### Q9. When can a correlated subquery be expensive?

**Answer:** If the plan evaluates it for many outer rows, it can repeat work; a join or window function may be clearer or faster.

**Example:** Verify with an execution plan before rewriting.

### Q10. What does a window function preserve that GROUP BY does not?

**Answer:** It computes across a related set while keeping each input row in the result.

**Example:** Use `ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC)`.

## Transactions and concurrency

### Q11. What is a lost update?

**Answer:** Two transactions read the same old value and the later write overwrites the earlier change.

**Example:** Use a version column or a guarded update.

### Q12. How does optimistic locking detect a conflicting update?

**Answer:** A version or timestamp is included in the update condition; zero affected rows means another transaction changed the record.

**Example:** `UPDATE item SET qty=?, version=version+1 WHERE id=? AND version=?`.

### Q13. When is pessimistic locking appropriate?

**Answer:** When conflicts are frequent or the operation cannot safely retry after discovering a conflict, and holding locks is acceptable.

**Example:** Lock the inventory row while reserving a scarce item.

### Q14. What anomaly does repeatable read aim to prevent?

**Answer:** It prevents a transaction from seeing a different committed value for a row it has already read, though exact guarantees vary by database.

**Example:** Check the database's documented isolation semantics.

### Q15. Why can SERIALIZABLE transactions still fail?

**Answer:** The database may abort a transaction when concurrent execution cannot be serialized; the application must retry the whole transaction safely.

**Example:** Retry serialization failures with bounded backoff.

### Q16. What is a deadlock victim retry strategy?

**Answer:** Retry the complete transaction after the database aborts one participant, with a limit and idempotent effects.

**Example:** Do not retry only the final statement.

### Q17. Why should a transaction be kept short?

**Answer:** Long transactions hold locks and connections, increase contention, and can delay vacuum or cleanup in some systems.

**Example:** Do remote HTTP calls before or after, not during, a database transaction.

### Q18. How does a unique constraint help with request idempotency?

**Answer:** It enforces one durable record per idempotency key even when requests race.

**Example:** Insert the key and business result in the same transaction.

### Q19. What is a phantom read?

**Answer:** A repeated predicate query sees rows added or removed by another transaction, depending on isolation guarantees.

**Example:** Two reads of `WHERE status='OPEN'` may return different sets.

### Q20. Why can a schema migration block production writes?

**Answer:** DDL may require metadata or table locks, and some changes rewrite large tables.

**Example:** Use an expand-and-contract rollout and database-specific online operations.

## Modeling and SQL behavior

### Q21. What is a natural key, and why might a surrogate key be chosen instead?

**Answer:** A natural key comes from domain data; a surrogate key is generated for identity and can remain stable when business attributes change.

**Example:** Keep a unique constraint on the natural value even with a surrogate ID.

### Q22. When is denormalization justified?

**Answer:** When measured read needs outweigh the extra write complexity and consistency work.

**Example:** Store a product name on an order line to preserve the purchase-time description.

### Q23. How do NULL values affect a UNIQUE constraint?

**Answer:** NULL handling differs by database and constraint form; many systems allow multiple NULLs because NULL is not equal to NULL.

**Example:** Check target-database semantics before relying on it.

### Q24. Why should foreign-key columns often be indexed?

**Answer:** Joins and parent updates/deletes may need to find referencing rows efficiently; some databases do not add the index automatically.

**Example:** Index `orders.customer_id` for customer lookups.

### Q25. What is a materialized view?

**Answer:** A stored result of a query that must be refreshed, trading freshness and storage for faster reads.

**Example:** Refresh it on a schedule or through a controlled update process.

### Q26. UNION vs UNION ALL: why is one usually cheaper?

**Answer:** UNION removes duplicates and may require sorting or hashing; UNION ALL concatenates results without deduplication.

**Example:** Use UNION ALL when duplicates are allowed or impossible.

### Q27. How does a CHECK constraint complement application validation?

**Answer:** It protects stored data from every write path, including scripts and other services, while application checks give earlier user feedback.

**Example:** Enforce `quantity >= 0` in both layers when it is a core invariant.

### Q28. Why use an explicit transaction around related writes?

**Answer:** It ensures the group succeeds or fails together and gives the application a clear consistency boundary.

**Example:** Insert an order and its lines in one transaction.

### Q29. What is a database view useful for?

**Answer:** It can provide a reusable query shape or restrict which columns a consumer sees, depending on permissions and design.

**Example:** Expose a reporting view without exposing sensitive columns.

### Q30. What does `ON DELETE CASCADE` mean?

**Answer:** Deleting a referenced parent automatically deletes dependent rows; use it only when that lifecycle is intended.

**Example:** A line item may be deleted with its draft order, but not necessarily with a customer.

## Performance and operations

### Q31. How do you investigate a query that regressed after a deployment?

**Answer:** Compare plans, parameter values, row counts, statistics, indexes, and database version/configuration around the change.

**Example:** Keep the exact SQL and representative bind values.

### Q32. Why can parameterized queries still have a poor plan?

**Answer:** A reused plan may not fit every parameter's selectivity, depending on database plan caching behavior.

**Example:** Compare plans for common and skewed parameter values.

### Q33. What is connection-pool starvation from the database's perspective?

**Answer:** Application threads may wait for connections while existing connections are held by slow queries or long transactions.

**Example:** Inspect pool wait time and active connection duration.

### Q34. How can batching improve insert throughput?

**Answer:** It groups multiple statements to reduce network round trips and per-statement overhead, subject to driver and transaction limits.

**Example:** Batch 100 inserts rather than issuing 100 individual round trips.

### Q35. Why can adding an index slow a write-heavy table?

**Answer:** Each insert, delete, or indexed-column update must maintain every relevant index.

**Example:** Measure write rate and remove redundant indexes.

### Q36. What is a hot row?

**Answer:** A row updated by many transactions becomes a lock bottleneck even when the table is small.

**Example:** Sharding a counter or batching updates may reduce contention.

### Q37. How should a large table be archived?

**Answer:** Move data according to explicit retention and query needs, preserving referential integrity and recoverability.

**Example:** Use partition detach or batched archival only after testing the target database.

### Q38. What does partition pruning do?

**Answer:** The planner skips partitions whose key ranges cannot satisfy the query predicate.

**Example:** Filter on the partition key to enable pruning.

### Q39. Why should slow-query logs be sampled or bounded?

**Answer:** High-volume logging can add I/O and storage cost, potentially worsening database pressure.

**Example:** Set a threshold and retain useful query context safely.

### Q40. How do you tell whether an index helped?

**Answer:** Compare actual plan, rows and buffers read, execution time, and write overhead under representative workload.

**Example:** Use before/after measurements, not just index existence.

## References

- [PostgreSQL SQL command reference](https://www.postgresql.org/docs/current/sql.html)
- [PostgreSQL table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
