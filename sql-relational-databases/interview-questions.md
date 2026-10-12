# SQL and Database Interview Questions

## INNER JOIN vs LEFT JOIN

An INNER JOIN returns rows that match on both sides. A LEFT JOIN keeps every row from the left table and fills the right-side columns with NULL when there is no match.

For example, joining customers to orders with LEFT JOIN still shows customers who have never placed an order. A filter on the right table in the WHERE clause can accidentally remove those unmatched rows; put the condition in the join when unmatched left rows must remain.

## How do indexes improve query performance? What types are common?

An index is an additional data structure that helps the database find rows without scanning the whole table. Common forms include B-tree indexes for equality and range searches, composite indexes across multiple columns, unique indexes for enforcing uniqueness, and specialized text or spatial indexes where supported.

Indexes use disk space and make inserts, updates, and deletes more expensive. A composite index is ordered by its leading columns, so column order and the actual query predicates matter. Use the query plan and real workload; adding an index to every column is not a strategy.

## Explain ACID

- Atomicity: a transaction's changes succeed together or roll back together.
- Consistency: constraints and application rules remain satisfied.
- Isolation: concurrent transactions are kept from interfering beyond the chosen level.
- Durability: committed changes survive a failure according to the database's guarantees.

A transfer that debits one account and credits another should not leave only one side committed.

## What are transaction isolation levels?

Isolation controls what one transaction may observe from concurrent transactions. The common SQL levels are READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, and SERIALIZABLE; exact behavior and implementation vary by database. Stronger isolation can reduce anomalies but may increase locking, retries, or reduced throughput.

Explain the anomaly the application must avoid, then choose the weakest level that safely handles it. Do not assume that two databases implement each named level identically.

## How do you optimize a slow SQL query?

Start with the actual query plan and representative parameters. Check whether the database scans too many rows, chooses a poor join order, sorts a large result, or uses an index poorly. Look at row counts, selectivity, stale statistics, lock waits, and whether the query returns more columns or rows than the endpoint needs.

Then make one targeted change, such as a suitable composite index, a simpler predicate, pagination, or a query that avoids repeated lookups. Compare latency and resource use under realistic load. A query that used to take 50 ms and now takes 10 seconds also calls for checking recent data growth, plan changes, deployments, and database contention.

For database CPU at 100%, check top queries and wait events first. For a table with hundreds of millions of rows, consider partitioning only when its key and maintenance pattern fit the workload; partitioning is not a substitute for a sound query and index.

## Primary key vs UNIQUE key

A primary key identifies each row and cannot be NULL. A table has one primary key constraint, which may contain multiple columns. A table can have multiple UNIQUE constraints; NULL handling depends on the database. Both can be backed by an index, depending on the database implementation.

Use the primary key for stable row identity and UNIQUE constraints for business rules such as one account per email address.

## DELETE vs TRUNCATE

DELETE removes rows and can use a WHERE clause. TRUNCATE removes all rows as a bulk operation and commonly resets identity counters, but its transactional and trigger behavior varies by database. Both are different from DROP, which removes the table definition itself.

Check your database's behavior before relying on rollback, triggers, or identity values.

## WHERE vs HAVING

WHERE filters rows before grouping. HAVING filters groups after GROUP BY and can refer to aggregate results.

~~~sql
SELECT department_id, COUNT(*) AS headcount
FROM employee
WHERE active = true
GROUP BY department_id
HAVING COUNT(*) > 5;
~~~

Filter individual rows with WHERE when possible; reserve HAVING for group-level conditions.

## Find the second-highest salary

First decide whether ties count as one rank. For the second-highest distinct salary:

~~~sql
SELECT DISTINCT salary
FROM employee
WHERE salary IS NOT NULL
ORDER BY salary DESC
OFFSET 1 ROW
FETCH NEXT 1 ROW ONLY;
~~~

Syntax for limiting rows varies by database. A window function such as DENSE_RANK is a good choice when you need to return all employees tied at that salary.

## Two transactions update the same row. How do you handle it?

Use optimistic locking with a version column when collisions are rare; one update wins and the other sees a conflict and can retry safely. Use pessimistic locking when conflicts are frequent and the operation must serialize. Keep transactions short, handle deadlocks, and make retries idempotent.

For inventory, a conditional update such as “decrement only where quantity is still positive” can enforce the invariant atomically.
