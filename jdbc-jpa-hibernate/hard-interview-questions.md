# JDBC, JPA and Hibernate: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Persistence context and entity identity

### Q1. When does a JPA query trigger an automatic flush?

**Answer:** With AUTO flush mode, the provider may flush pending changes before a query whose results could be affected. Do not assume SQL waits until commit.

### Q2. Why can two Java objects for one row create confusing equality behavior?

**Answer:** One may be detached while another is managed; object identity differs despite the same database identity. Define equals/hashCode carefully and avoid mutable generated IDs as hash keys.

### Q3. How should equals/hashCode work for a Hibernate entity with a generated identifier?

**Answer:** Use a stable business key if one exists, or a carefully tested identifier strategy that remains stable across persistence transitions. Never use mutable fields casually.

### Q4. What happens to a managed entity after a bulk JPQL update?

**Answer:** Bulk DML bypasses the persistence context, leaving in-memory entities stale. Flush before it and clear or refresh affected entities afterward.

### Q5. Why can flush() succeed but commit still fail?

**Answer:** Deferred constraints, serialization conflicts, or commit-time resource errors may be detected only at transaction completion. Treat commit as the true success boundary.

### Q6. What does merge() return, and why is continuing to use the argument risky?

**Answer:** merge copies state into and returns a managed instance; the input remains detached. Use the returned object for further managed changes.

### Q7. How can Open Session in View hide an inefficient API design?

**Answer:** Lazy loads may execute during JSON serialization after the service boundary, causing N+1 queries and unpredictable transactions. Return explicit DTO projections.

### Q8. Why can a detached entity update overwrite fields changed by another transaction?

**Answer:** Merging stale state may copy old values across the entity. Use version columns, narrow updates, or command methods that change only intended fields.

### Q9. What is the difference between remove() and orphanRemoval?

**Answer:** remove deletes an entity explicitly; orphanRemoval deletes a child removed from its owning relationship when flushed. Use it only when ownership truly implies lifecycle.

### Q10. How should an entity listener avoid surprising database access?

**Answer:** Keep lifecycle callbacks deterministic and lightweight; hidden I/O or service lookup makes persistence side effects hard to order and test.

## Relationships, fetch plans, and queries

### Q11. Why can a fetch join on a collection break pagination?

**Answer:** The join multiplies root rows, so database limits apply to joined rows rather than unique entities. Page root IDs first, then fetch the graph separately.

### Q12. How can an entity graph affect a query without changing the JPQL text?

**Answer:** It declares a fetch plan hint for associations, but providers may choose implementation details. Verify generated SQL and row counts.

### Q13. When is batch fetching better than a large fetch join?

**Answer:** Batch fetching loads related rows in chunks, avoiding both N+1 round trips and row multiplication. Tune batch size against DB parameter and memory limits.

### Q14. Why can a bidirectional association serialize recursively?

**Answer:** Each entity references the other, producing cycles and accidental lazy traversal. Map to DTOs and define API shape independently of persistence mapping.

### Q15. How does owning-side configuration affect a relationship update?

**Answer:** The owning side writes the foreign key or join table; changing only the inverse side may not persist the association. Keep both object sides consistent in helpers.

### Q16. What is the risk of cascading REMOVE from a shared reference entity?

**Answer:** Deleting one owner may delete shared data still needed elsewhere. Cascade only along true aggregate ownership boundaries.

### Q17. How do projection queries reduce persistence overhead?

**Answer:** They select only fields needed for a read, avoiding entity hydration and unexpected lazy fetches. Keep write models separate when invariants require managed entities.

### Q18. Why can a native query return inconsistent results after pending entity changes?

**Answer:** It may execute before pending state is flushed, depending on flush mode/provider behavior. Flush explicitly or use a query path with defined synchronization.

### Q19. What is keyset pagination for JPA, and what index does it need?

**Answer:** Use a stable ordered key and a predicate after the last seen value; index the same leading sort/filter columns. Add a unique tie-breaker.

### Q20. How do you avoid an N+1 when mapping a page to nested DTOs?

**Answer:** Fetch required associations deliberately through projection, entity graph, or batch strategy, then inspect SQL count on a realistic page size.

## Transactions, locking, and caches

### Q21. How does @Version prevent lost updates, and what does the caller do after conflict?

**Answer:** The update includes the old version; zero matched rows signals an optimistic conflict. Reload and re-evaluate the business action rather than blindly replay stale state.

### Q22. Why can pessimistic locking still deadlock?

**Answer:** Transactions may lock multiple rows in different orders or hold locks while calling remote systems. Lock narrowly, order consistently, and keep transactions short.

### Q23. What is the difference between first-level and second-level cache consistency?

**Answer:** The persistence context is per unit of work; second-level cache spans sessions and needs provider-coordinated invalidation. External SQL writes can make it stale.

### Q24. Why is query-cache performance sensitive to table updates?

**Answer:** Cached result sets are invalidated based on affected query spaces, so write-heavy data can churn entries. Cache stable, repeated reads only after measurement.

### Q25. How can a transaction annotation fail on a private or self-invoked method?

**Answer:** Proxy-based interception may not see private methods or calls that do not cross the proxy. Put the boundary on an externally invoked service method.

### Q26. What does REQUIRES_NEW mean when an outer transaction later rolls back?

**Answer:** The inner transaction can commit independently, so its changes remain. Use it only when that independence is a deliberate business rule.

### Q27. Why can a read-only transaction improve intent but not guarantee zero writes?

**Answer:** It is generally a hint to frameworks/providers, not a universal database prohibition. Enforce write rules with database permissions or explicit controls where needed.

### Q28. How should an optimistic-lock conflict be handled in an inventory reservation?

**Answer:** Re-read available quantity and retry or reject according to business policy; never apply the same stale decrement unconditionally.

### Q29. What is the risk of using a second-level cache for frequently changing authorization data?

**Answer:** Stale grants or revocations can create a security gap. Require bounded staleness and invalidation guarantees or keep authorization reads uncached.

### Q30. Why can JDBC work inside a JPA transaction still use a separate connection?

**Answer:** Connection acquisition and transaction manager configuration must share the same resource. Verify the same DataSource and transaction manager are enlisted.

## Mapping and production diagnostics

### Q31. How do you detect hidden lazy loads in a REST response?

**Answer:** Count SQL statements in integration tests or inspect ORM statistics and traces while serializing representative responses. Fail tests on unexpected query growth.

### Q32. Why should a collection-valued DTO projection be paged at the root level?

**Answer:** Joining multiple collections creates a Cartesian product and duplicate roots. Fetch the root page, then load bounded associated data in a second query.

### Q33. What causes MultipleBagFetchException, and what is a safer response than changing every List to Set?

**Answer:** Fetching multiple bag collections can create ambiguous duplicate handling. Split the fetch into queries or use a purpose-built read projection.

### Q34. How can batch inserts be defeated by identifier generation?

**Answer:** Some ID strategies require a database round trip per entity and prevent JDBC batching. Choose a compatible strategy and inspect actual SQL batching.

### Q35. What does hibernate.default_batch_fetch_size trade?

**Answer:** It groups lazy association loads into fewer queries but may fetch unused rows and consume memory. Tune from observed access patterns.

### Q36. Why are schema auto-update settings risky in production?

**Answer:** They do not provide a reviewed migration plan and may apply unsafe or incomplete DDL. Use versioned migrations and validate schema drift at startup.

### Q37. How do you diagnose an ORM query regression after a framework upgrade?

**Answer:** Compare generated SQL, bind parameters, execution plans, fetch counts, and provider settings with the same dataset. Upgrade may alter query or batching behavior.

### Q38. What is the danger of exposing entity classes directly as request DTOs?

**Answer:** Client-controlled fields can modify persistence state, lazy loading leaks data access, and schema changes affect the API. Map an explicit input model.

### Q39. How should a database-generated default be represented in managed entity state?

**Answer:** Refresh or use generated-value mapping where supported; otherwise in-memory state may differ until reloaded. Test insert behavior on the target database.

### Q40. Why can a test pass with H2 but fail under PostgreSQL locking?

**Answer:** Dialect behavior, DDL, isolation, and lock semantics differ. Run critical persistence tests against the production engine using containers or a dedicated test database.


## References

- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
- [Jakarta Persistence EntityManager API](https://jakarta.ee/specifications/persistence/3.2/apidocs/jakarta.persistence/jakarta/persistence/entitymanager)
- [Hibernate ORM User Guide](https://docs.hibernate.org/orm/6.6/userguide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA reference](https://docs.spring.io/spring-data/jpa/reference/)
