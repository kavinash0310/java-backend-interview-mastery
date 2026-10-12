# JDBC, JPA and Hibernate: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q04. What is JDBC?

**Answer:** Java's API for connecting to relational databases, sending SQL, and reading results. Drivers translate API calls to a database.

### Q05. What is an ORM?

**Answer:** Object-relational mapping connects program objects to rows and relationships. It saves repetitive mapping but does not remove the need to understand SQL.

### Q06. JPA vs Hibernate?

**Answer:** Jakarta Persistence defines an API and mapping specification; Hibernate is a provider that implements it and adds provider-specific features.

### Q07. What is an entity?

**Answer:** A class mapped to persistent data and identified by a primary key. Its lifecycle is managed through a persistence context.

### Q08. What does @Id mark?

**Answer:** The field or property that identifies an entity. Its mapping must match the database's key.

### Q09. What does @GeneratedValue do?

**Answer:** It requests provider-managed key generation. The strategy and database affect how IDs are allocated.

### Q10. What is a persistence context?

**Answer:** The set of managed entity instances for a persistence operation. It tracks changes and maintains one managed instance per identity within that context.

### Q11. Transient vs managed vs detached entity?

**Answer:** Transient means not managed; managed means tracked by a persistence context; detached means formerly managed but no longer attached.

### Q12. What does persist() do?

**Answer:** It makes a new entity managed so it can be inserted when the persistence context synchronizes with the database.

### Q13. What does merge() do?

**Answer:** It copies state from a detached or new entity into a managed instance and returns that managed object. The passed object itself does not become managed.

### Q14. What does remove() do?

**Answer:** It marks a managed entity for deletion when changes synchronize. The database delete may happen at flush or commit.

### Q15. What is dirty checking?

**Answer:** The provider detects changes to managed entities and writes needed updates during synchronization, often without an explicit update call.

### Q16. What is flush?

**Answer:** It synchronizes pending persistence-context changes with the database. It is not the same as committing the transaction.

### Q17. What is the first-level cache?

**Answer:** The persistence context's identity map. Repeated lookups for the same entity identity can return the same managed instance.

### Q18. What is a second-level cache?

**Answer:** An optional provider cache shared across persistence contexts. It can reduce reads but requires deliberate invalidation and consistency settings.

### Q19. What is the owning side of a relationship?

**Answer:** The side that controls the database relationship mapping, such as a foreign-key update. Keep both object references in sync in code.

### Q20. What does cascade mean in JPA?

**Answer:** It propagates selected lifecycle operations across a relationship. Use only operations that fit the domain.

### Q21. What is orphan removal?

**Answer:** It requests deletion of a child removed from a configured parent relationship. Use when that child has no independent lifecycle.

### Q22. What is JPQL?

**Answer:** A query language over entity types and fields rather than raw table and column names. The provider translates it to SQL.

### Q23. JPQL vs native SQL?

**Answer:** JPQL uses entity mappings and is more portable; native SQL uses database-specific syntax. Use native queries when there is a measured need.

### Q24. What is a named query?

**Answer:** A query declared with a stable name and referenced by that name in code, making reusable definitions explicit.

### Q25. How can JPA paginate results?

**Answer:** Use a pageable query or set a first result and maximum count. For large changing data, keyset pagination may be more stable than deep offsets.

### Q26. What is optimistic locking in JPA?

**Answer:** A version field detects that another transaction changed an entity since it was read. A conflict causes a locking failure instead of silently overwriting.

**Example:** A @Version column increments after a successful update.

### Q27. What is pessimistic locking in JPA?

**Answer:** It asks the database to lock data during a transaction to reduce conflicting updates. It can block work, so keep transactions short.

### Q28. What is the EntityManager?

**Answer:** The JPA interface for finding, persisting, removing, and querying entities. In a managed app, its lifecycle is usually container-controlled.

### Q29. Why should an EntityManager not be shared across threads?

**Answer:** A persistence context tracks mutable managed state and is not a shared concurrent workspace. Use the transaction-scoped instance for the current unit of work.

### Q30. Why can database access inside a loop be slow?

**Answer:** It can issue one query per row and create many round trips. Batch operations, joins, projections, or deliberate fetch plans can reduce calls.

## References

- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
- [Jakarta Persistence EntityManager API](https://jakarta.ee/specifications/persistence/3.2/apidocs/jakarta.persistence/jakarta/persistence/entitymanager)
- [Hibernate ORM User Guide](https://docs.hibernate.org/orm/6.6/userguide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA reference](https://docs.spring.io/spring-data/jpa/reference/)
