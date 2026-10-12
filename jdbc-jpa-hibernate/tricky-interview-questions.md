# JDBC, JPA and Hibernate: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Entity state and flush traps

### Q1. Does calling save() always issue an INSERT immediately?

**Answer:** No. Changes may remain managed until flush or commit. SQL timing depends on ID strategy and provider behavior.

### Q2. Can flush() guarantee the transaction committed?

**Answer:** No. It sends pending SQL, but constraints or failures may still occur at commit. Commit is the durable success boundary.

### Q3. Why can an entity's equals or hashCode unexpectedly trigger lazy database loads?

**Answer:** If equality traverses a lazy association, comparing entities can initialize proxies and issue SQL. Base identity on stable scalar state, not object graphs.

### Q4. Why should you use the object returned by merge()?

**Answer:** merge copies detached state into a managed instance and returns it; the argument remains detached.

### Q5. Can edits to a detached entity be automatically tracked?

**Answer:** No. Changes are not tracked until merged or explicitly saved through a repository.

### Q6. Why might saveAndFlush() not reveal a deferred constraint error?

**Answer:** Some constraints are checked only at transaction commit. A successful flush does not guarantee final commit.

### Q7. What happens if bulk JPQL updates an entity already loaded in the persistence context?

**Answer:** The in-memory object remains stale because bulk DML bypasses entity tracking. Flush and clear or refresh as appropriate.

### Q8. Why can an entity appear updated in memory while the database still has old data?

**Answer:** The persistence context holds changed state until flush/commit. Another transaction may not see it yet.

### Q9. Can two EntityManager instances manage the same Java object at once?

**Answer:** Each context has its own identity map; sharing one mutable object across contexts creates detached-state confusion.

### Q10. Why might an entity listener observe an old generated ID?

**Answer:** The ID may be assigned at a provider-specific lifecycle point. Do not assume timing; test the chosen generation strategy.

## Associations and fetch behavior

### Q11. Why can a LEFT JOIN FETCH behave like an inner join after a WHERE condition?

**Answer:** A predicate on the fetched side removes null-extended rows. Put optional-match criteria in the join condition when supported.

### Q12. Can a collection fetch join safely paginate database rows?

**Answer:** Not generally: child rows multiply roots, so LIMIT applies before unique roots are reconstructed. Page IDs, then fetch associations separately.

### Q13. Why can two List collections fetched together trigger a Hibernate exception?

**Answer:** Bags allow duplicates without index identity, making reconstruction ambiguous. Split the fetch or use a suitable projection.

### Q14. Does changing a List association to Set automatically fix fetch performance?

**Answer:** No. It may change semantics and equality costs; SQL can still multiply rows heavily.

### Q15. What does orphanRemoval do if the child is shared elsewhere?

**Answer:** It may schedule deletion when the relationship is flushed. Use it only when the parent truly owns the child's lifecycle.

### Q16. Why might changing only the inverse side of a bidirectional relationship do nothing?

**Answer:** The owning side writes the foreign key or join table. Update both object sides consistently.

### Q17. Can a lazy association be accessed after a transaction if Open Session in View is enabled?

**Answer:** Often yes because the session remains open, but the hidden query occurs late and outside the intended service boundary.

### Q18. Why can JSON serialization produce dozens of SQL queries from one entity?

**Answer:** Getters traverse lazy relationships and trigger N+1 loads. Return DTOs and test query count.

### Q19. Does an entity graph guarantee one SQL query?

**Answer:** No. It describes fetch intent; the provider may use joins or secondary queries. Inspect generated SQL.

### Q20. Why might a page query return duplicate root entities after a fetch join?

**Answer:** One database row appears per joined child. Use distinct semantics carefully or fetch the page and associations separately.

## Identifiers, locks, and query pitfalls

### Q21. Why is a generated ID in hashCode risky before persistence?

**Answer:** The ID may change from null to assigned after insertion into a hash set, so lookup can use a different bucket.

### Q22. Does @Version prevent every lost update in a bulk update?

**Answer:** No. Bulk DML bypasses entity version checks unless the query explicitly checks and increments the version.

### Q23. Why can optimistic-lock retries repeat a non-idempotent side effect?

**Answer:** Replaying may send email or charge a provider twice. Keep side effects after commit or use an outbox/idempotency.

### Q24. Can PESSIMISTIC_WRITE lock rows that a query might insert later?

**Answer:** Not reliably across databases or isolation modes. Use uniqueness constraints or a database-specific range/serializable strategy.

### Q25. Why may a read-only transaction still dirty-check entities?

**Answer:** Read-only is often a hint and provider behavior varies. Configure flush behavior and avoid modifying those entities.

### Q26. What can happen if an entity is removed and re-persisted in one context?

**Answer:** State transitions are provider-sensitive and can produce errors or unexpected SQL. Model lifecycle as explicit commands.

### Q27. Why can findById run SQL despite a second-level cache?

**Answer:** Cache mode, region, query shape, or staleness can cause a miss or bypass. Measure SQL and cache stats.

### Q28. Can a JPQL query return state inconsistent with the database?

**Answer:** Within a context, identity-map instances may reflect earlier loaded state until refreshed. Clear or refresh after external writes.

### Q29. Why might JDBC updates fail to invalidate Hibernate cache entries?

**Answer:** The ORM does not know about external SQL changes. Evict or refresh affected state.

### Q30. Why can @OrderColumn make reordering a JPA list issue many updates?

**Answer:** The provider may update stored position values for many elements after a move. Use ordered lists only when persisted positional semantics justify the write cost.

## References

- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
- [Jakarta Persistence EntityManager API](https://jakarta.ee/specifications/persistence/3.2/apidocs/jakarta.persistence/jakarta/persistence/entitymanager)
- [Hibernate ORM User Guide](https://docs.hibernate.org/orm/6.6/userguide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA reference](https://docs.spring.io/spring-data/jpa/reference/)
