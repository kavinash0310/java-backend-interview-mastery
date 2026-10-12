# SQL and Relational Databases: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q11. What is a relational database?

**Answer:** It stores related data in tables of rows and columns, with constraints describing valid relationships and values.

### Q12. What is a row?

**Answer:** One record in a table, such as one order or customer.

### Q13. What is a column?

**Answer:** A named attribute shared by table rows, such as order_date or total_amount.

### Q14. What does NULL mean in SQL?

**Answer:** A missing or unknown value, not zero or an empty string. Test it using IS NULL.

### Q15. Why does value = NULL not work as expected?

**Answer:** SQL comparison with NULL is unknown rather than true. Write IS NULL or IS NOT NULL.

### Q16. What does GROUP BY do?

**Answer:** It combines rows with the same selected values into groups so aggregate functions can summarize them.

### Q17. What is an aggregate function?

**Answer:** It calculates over rows, such as COUNT, SUM, AVG, MIN, or MAX.

### Q18. What does COUNT(*) count?

**Answer:** It counts every row in the group, including rows with NULL in columns.

### Q19. What does DISTINCT do?

**Answer:** It removes duplicate result rows for selected columns. It does not change stored data.

### Q20. ORDER BY vs GROUP BY?

**Answer:** ORDER BY sorts returned rows; GROUP BY creates groups for aggregation. They solve different parts of a query.

### Q21. What is a subquery?

**Answer:** A query nested inside another SQL statement. It can supply a value or row set to the outer query.

### Q22. What is a common table expression?

**Answer:** A named temporary result introduced with WITH for one statement. It can improve readability and can be recursive.

### Q23. UNION vs UNION ALL?

**Answer:** UNION combines results and removes duplicates; UNION ALL keeps duplicates and may avoid deduplication work.

### Q24. What does EXISTS check?

**Answer:** Whether a subquery returns at least one row. It often tests whether related data exists.

### Q25. What is a foreign key?

**Answer:** A constraint requiring a column or columns to reference a valid key in the same or another table, helping prevent orphan relationships.

### Q26. What is a composite key?

**Answer:** A key made from more than one column. Together the columns identify a row or enforce uniqueness.

### Q27. What is normalization?

**Answer:** Organizing data to reduce unnecessary duplication and update anomalies, usually by separating facts into related tables.

### Q28. What is denormalization?

**Answer:** Deliberately storing duplicated or precomputed data to improve a measured read workload. It adds consistency and update work.

### Q29. What is a database view?

**Answer:** A named query that presents selected rows or columns as a table-like result. Materialized views, where supported, store results.

### Q30. What is a transaction in SQL?

**Answer:** A unit of database work that commits as a whole or rolls back, helping keep related changes consistent.

## References

- [PostgreSQL SQL command reference](https://www.postgresql.org/docs/current/sql.html)
- [PostgreSQL table expressions](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
