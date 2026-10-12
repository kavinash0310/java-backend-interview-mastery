# SQL and Relational Databases: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is a relational database?

**Answer:** It stores related data in tables of rows and columns, with constraints describing valid relationships and values.

### Q2. What is a row?

**Answer:** One record in a table, such as one order or customer.

### Q3. What is a column?

**Answer:** A named attribute shared by table rows, such as order_date or total_amount.

### Q4. What does NULL mean in SQL?

**Answer:** A missing or unknown value, not zero or an empty string. Test it using IS NULL.

### Q5. Why does value = NULL not work as expected?

**Answer:** SQL comparison with NULL is unknown rather than true. Write IS NULL or IS NOT NULL.

### Q6. What does GROUP BY do?

**Answer:** It combines rows with the same selected values into groups so aggregate functions can summarize them.

### Q7. What is an aggregate function?

**Answer:** It calculates over rows, such as COUNT, SUM, AVG, MIN, or MAX.

### Q8. What does COUNT(*) count?

**Answer:** It counts every row in the group, including rows with NULL in columns.

### Q9. What does DISTINCT do?

**Answer:** It removes duplicate result rows for selected columns. It does not change stored data.

### Q10. ORDER BY vs GROUP BY?

**Answer:** ORDER BY sorts returned rows; GROUP BY creates groups for aggregation. They solve different parts of a query.

### Q11. What is a subquery?

**Answer:** A query nested inside another SQL statement. It can supply a value or row set to the outer query.

### Q12. What is a common table expression?

**Answer:** A named temporary result introduced with WITH for one statement. It can improve readability and can be recursive.

### Q13. UNION vs UNION ALL?

**Answer:** UNION combines results and removes duplicates; UNION ALL keeps duplicates and may avoid deduplication work.

### Q14. What does EXISTS check?

**Answer:** Whether a subquery returns at least one row. It often tests whether related data exists.

### Q15. What is a foreign key?

**Answer:** A constraint requiring a column or columns to reference a valid key in the same or another table, helping prevent orphan relationships.

### Q16. What is a composite key?

**Answer:** A key made from more than one column. Together the columns identify a row or enforce uniqueness.

### Q17. What is normalization?

**Answer:** Organizing data to reduce unnecessary duplication and update anomalies, usually by separating facts into related tables.

### Q18. What is denormalization?

**Answer:** Deliberately storing duplicated or precomputed data to improve a measured read workload. It adds consistency and update work.

### Q19. What is a database view?

**Answer:** A named query that presents selected rows or columns as a table-like result. Materialized views, where supported, store results.

### Q20. What is a transaction in SQL?

**Answer:** A unit of database work that commits as a whole or rolls back, helping keep related changes consistent.

### Q21. What is a primary key?

**Answer:** A column or column set that uniquely identifies each row and cannot be null.

**Example:** `orders.id` can be the primary key.

### Q22. Primary key vs unique constraint?

**Answer:** Both enforce uniqueness; a table has one primary key, while it may have multiple unique constraints. NULL behavior varies by database.

**Example:** Use a unique constraint for an email address.

### Q23. What is an index?

**Answer:** A data structure that can help the database find rows without scanning the whole table.

**Example:** Index a frequently filtered customer_id column.

### Q24. What is the downside of adding many indexes?

**Answer:** Indexes use storage and add work to inserts, updates, and deletes.

**Example:** Index only columns that support real query patterns.

### Q25. What is a composite index?

**Answer:** An index containing more than one column, useful when queries filter or sort by those columns in a compatible order.

**Example:** An index on `(customer_id, created_at)` can help a customer's date range query.

### Q26. What does EXPLAIN show?

**Answer:** The database's planned steps for executing a query, including scans, joins, and estimated costs.

**Example:** Use EXPLAIN before changing indexes.

### Q27. What is a full table scan?

**Answer:** The database reads table rows to evaluate a query instead of using a selective index path.

**Example:** It can be fine for a small table or a query returning most rows.

### Q28. What is a JOIN?

**Answer:** It combines related rows from tables using a join condition.

**Example:** Join orders to customers on customer_id.

### Q29. INNER JOIN vs LEFT JOIN?

**Answer:** INNER JOIN keeps matching pairs; LEFT JOIN keeps every left row and fills missing right-side values with NULL.

**Example:** Keep customers with no orders using LEFT JOIN.

### Q30. WHERE vs HAVING?

**Answer:** WHERE filters rows before grouping; HAVING filters groups after aggregation.

**Example:** Use HAVING COUNT(*) > 1 for duplicate groups.

### Q31. What is a transaction isolation level?

**Answer:** A setting that controls which concurrent changes a transaction may observe.

**Example:** Choose based on consistency needs and contention.

### Q32. What is a dirty read?

**Answer:** Reading another transaction's uncommitted changes; common isolation levels prevent it.

**Example:** The other transaction may still roll back.

### Q33. What is a deadlock in a database?

**Answer:** Transactions wait on locks held by each other, so the database aborts one to make progress.

**Example:** Retry a deadlock victim safely when appropriate.

### Q34. What is an execution plan?

**Answer:** The operations the database chooses to read and combine data for a query.

**Example:** Compare estimated and actual row counts when supported.

### Q35. What is a database constraint?

**Answer:** A rule the database enforces to keep data valid, such as NOT NULL or FOREIGN KEY.

**Example:** Use a check constraint for a nonnegative quantity.

### Q36. What is a CHECK constraint?

**Answer:** A rule that requires a column value or row to satisfy a Boolean condition.

**Example:** `CHECK (quantity >= 0)`

### Q37. DELETE vs TRUNCATE?

**Answer:** DELETE removes selected rows and can use a WHERE clause; TRUNCATE removes all rows with database-specific behavior.

**Example:** Use DELETE when only some rows should be removed.

### Q38. What is a CTE?

**Answer:** A named query expression that makes a larger SQL statement easier to read and organize.

**Example:** `WITH recent AS (...) SELECT ...`.

### Q39. What is a window function?

**Answer:** It computes a value across related rows while keeping each row in the result.

**Example:** `ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC)`.

### Q40. How do you find the second-highest distinct salary?

**Answer:** Rank distinct salaries in descending order and select rank two.

**Example:** `DENSE_RANK() OVER (ORDER BY salary DESC)` handles ties.

## References

- [PostgreSQL SQL command reference](https://www.postgresql.org/docs/current/sql.html)
- [PostgreSQL table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
