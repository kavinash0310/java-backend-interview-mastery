# JDBC, JPA and Hibernate: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Persistence context and lifecycle

### Q1. What is the identity guarantee of a persistence context?

**Answer:** Within one persistence context, one database identity maps to one managed entity instance.

**Example:** Loading the same entity twice usually returns the same managed Java object.

### Q2. What happens when EntityManager.flush() runs?

**Answer:** Pending changes are synchronized to SQL statements, but the transaction is not necessarily committed.

**Example:** A constraint error may appear at flush before method return.

### Q3. Why can a query trigger an automatic flush?

**Answer:** The provider may flush pending changes first so query results are consistent with the persistence context.

**Example:** A JPQL query can surface a database error earlier than commit.

### Q4. What does merge() return for a detached entity?

**Answer:** It returns a managed instance with copied state; the passed object itself may remain detached.

**Example:** Use `managed = entityManager.merge(detached)`.

### Q5. Why can merging a partial detached entity overwrite fields?

**Answer:** The provider may copy its state over the managed representation, including missing/default values depending on mapping and workflow.

**Example:** Prefer loading the managed entity and applying only requested changes.

### Q6. How does bulk JPQL update differ from changing managed entities?

**Answer:** It executes directly in the database and bypasses normal dirty checking and entity callbacks, leaving the persistence context potentially stale.

**Example:** Clear or refresh affected managed entities after bulk operations.

### Q7. Why can `IDENTITY` ID generation inhibit JDBC insert batching?

**Answer:** The provider may need each insert executed immediately to obtain its generated key.

**Example:** Benchmark sequence or pooled strategies where supported by the database.

### Q8. What is JDBC batching in Hibernate?

**Answer:** The provider groups compatible SQL statements into batches to reduce round trips.

**Example:** Configure a batch size and avoid identity generation constraints.

### Q9. Why should entity constructors avoid database or network work?

**Answer:** Entities are persistence objects and may be created by the provider; side effects make loading and testing unpredictable.

**Example:** Keep entity construction limited to valid state.

### Q10. How should equals/hashCode be designed for JPA entities?

**Answer:** Avoid mutable fields; account for generated IDs and proxy behavior, or use a stable immutable business key when valid.

**Example:** Test equality before and after persistence.

## Relationships and fetching

### Q11. What is the difference between cascade and orphan removal?

**Answer:** Cascade propagates selected entity operations; orphan removal deletes a child removed from its owning relationship.

**Example:** Use orphan removal only when the child cannot exist independently.

### Q12. Why keep both sides of a bidirectional relationship synchronized in Java?

**Answer:** The in-memory graph should match the owning side that controls the foreign key.

**Example:** An `addItem` helper sets both `order` and `order.items`.

### Q13. What is a fetch join, and what pagination caveat can it have?

**Answer:** It loads a relationship in the same query; joining a collection can multiply rows and make database pagination incorrect or inefficient.

**Example:** Page parent IDs first, then fetch their collections separately.

### Q14. How do entity graphs help compared with changing a mapping to EAGER?

**Answer:** They specify a use-case fetch plan without making every load of the entity fetch the same relationships.

**Example:** Use a graph for the order-details endpoint only.

### Q15. What is batch fetching in Hibernate?

**Answer:** It loads several related entities in a smaller number of `IN` queries instead of one query per entity.

**Example:** Tune batch size based on database limits and page size.

### Q16. Why is EAGER loading not a universal fix for N+1?

**Answer:** It can fetch too much data or create joins for queries that do not need the relationship, and may still issue secondary selects.

**Example:** Choose fetch plans per use case.

### Q17. What can happen if JSON serialization walks a lazy bidirectional relationship?

**Answer:** It may trigger extra SQL, fail after the persistence context closes, or recurse indefinitely.

**Example:** Return DTOs with deliberately selected fields.

### Q18. How does `@OrderColumn` affect a collection mapping?

**Answer:** It stores list positions in a database column, so reordering can cause additional updates.

**Example:** Use it only when list position is part of persisted meaning.

### Q19. Why can a many-to-many mapping become awkward as the model grows?

**Answer:** The join table may later need attributes such as status or created time, requiring an explicit association entity.

**Example:** Model the link as `Membership` when it has behavior or data.

### Q20. When should a relationship be mapped as unidirectional?

**Answer:** When the use cases do not need navigation in both directions; simpler mappings can reduce accidental loading and synchronization work.

**Example:** A child may store its parent ID without exposing a parent collection.

## Transactions, locking, and caches

### Q21. What does @Version do on a JPA entity?

**Answer:** It adds optimistic concurrency checking so an update fails if another transaction changed the row first.

**Example:** Use an integer version field annotated with `@Version`.

### Q22. How should an optimistic-lock failure be handled?

**Answer:** Reload current state, explain the conflict or recompute the operation, and retry only if business semantics allow it.

**Example:** Do not silently overwrite the newer value.

### Q23. What is the effect of a pessimistic write lock?

**Answer:** The database prevents conflicting updates to the locked row until the transaction completes.

**Example:** Keep the transaction short to reduce lock waits.

### Q24. What does the first-level cache guarantee?

**Answer:** Managed entities are cached within one persistence context and identity is reused there; it is not a cross-request cache.

**Example:** A new transaction usually gets a different context.

### Q25. Why can a second-level cache become stale?

**Answer:** External SQL or another application may update the database without invalidating the provider's cached entry.

**Example:** Define cache regions and invalidation rules carefully.

### Q26. What does the JPA query cache store?

**Answer:** It generally caches query result identifiers or metadata, while entity state may still come from the second-level cache.

**Example:** It is useful only when query results are reused and invalidation is manageable.

### Q27. What is the Open Session in View trade-off?

**Answer:** It allows lazy loads during response rendering but can hide query costs and extend persistence access beyond service logic.

**Example:** Prefer explicit data fetching and DTO mapping for predictable APIs.

### Q28. How can @Transactional rollback behavior differ for checked exceptions?

**Answer:** Spring defaults commonly roll back on unchecked exceptions and Errors unless rollback rules are customized.

**Example:** Declare `rollbackFor` when a checked business exception must roll back.

### Q29. Why might a transaction commit even though a method later throws?

**Answer:** The exception may be caught, checked without rollback configuration, or thrown outside the transactional proxy boundary.

**Example:** Inspect proxy entry, rollback rules, and transaction logs.

### Q30. How do nested repository calls usually share a transaction?

**Answer:** With REQUIRED propagation, they join the active transaction associated with the current thread and manager.

**Example:** Avoid starting independent transactions by default.

## Mapping, queries, and migration

### Q31. When should you use a DTO projection instead of loading an entity?

**Answer:** When a read needs only selected columns or a stable response shape, projections reduce data transfer and accidental graph loading.

**Example:** Select `id`, `name`, and `status` for a summary screen.

### Q32. What is the difference between JPQL and SQL table naming?

**Answer:** JPQL refers to entity names and Java attributes; native SQL refers to physical database tables and columns.

**Example:** `select o from Order o` uses the entity model.

### Q33. Why parameterize native SQL?

**Answer:** Binding values separately prevents injection and can improve statement reuse.

**Example:** Use named parameters rather than concatenating user input.

### Q34. How can pageable sorting become unsafe?

**Answer:** A client-provided property may not map to a valid or allowed sort field and can create costly queries.

**Example:** Whitelist supported sort names.

### Q35. What is schema validation at application startup checking?

**Answer:** It compares mapped entity expectations with the database schema; it does not replace versioned migrations.

**Example:** Use a migration tool to change schema deliberately.

### Q36. How should entity schema changes be deployed with old app instances still running?

**Answer:** Use an expand-and-contract sequence: add compatible schema first, deploy readers/writers, migrate data, then remove old fields later.

**Example:** Keep both versions working during rolling deployment.

### Q37. Why avoid storing large binary blobs in ordinary entity tables by default?

**Answer:** They can inflate row reads, backups, and cache pressure; use object storage when access patterns suit it.

**Example:** Store an object key and metadata in the relational database.

### Q38. How can SQL logging create a production risk?

**Answer:** Bind values may contain personal data or secrets, and verbose logs can be expensive.

**Example:** Use sanitized, bounded SQL diagnostics.

### Q39. What does Hibernate statistics help measure?

**Answer:** It can report query, entity, collection, and cache counts that reveal unexpected ORM behavior.

**Example:** Compare query counts for a representative endpoint.

### Q40. How do you prove that an N+1 fix worked?

**Answer:** Capture SQL or query-count metrics for a fixed dataset and verify both query count and returned behavior.

**Example:** Test a page containing several parent entities.

## References

- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
- [Jakarta Persistence EntityManager API](https://jakarta.ee/specifications/persistence/3.2/apidocs/jakarta.persistence/jakarta/persistence/entitymanager)
- [Hibernate ORM User Guide](https://docs.hibernate.org/orm/6.6/userguide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA reference](https://docs.spring.io/spring-data/jpa/reference/)
