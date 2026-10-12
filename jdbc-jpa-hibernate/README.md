# Module 10 — JDBC, JPA and Hibernate

> **Module guide:** Connect Java persistence code to relational databases, understand ORM behavior, and avoid common query and consistency problems.

## Learning outcomes

- Explain how the persistence context tracks entity changes.
- Choose relationship and fetch strategies deliberately.
- Find and address N+1 queries and concurrent-update conflicts.

## Subtopic links

- [Persistence foundations](#persistence-foundations)
- [Entities and relationships](#entities-and-relationships)
- [Queries, caching, and performance](#queries-caching-and-performance)
- [Consistency and locking](#consistency-and-locking)

## Topic coverage

### Persistence foundations

- JDBC fundamentals
- ORM concepts and mapping
- Hibernate and JPA roles
- Spring Data JPA repositories

### Entities and relationships

- Entity lifecycle and persistence context
- Entity relationships and ownership
- Lazy versus eager fetching
- Cascade and orphan removal

### Queries, caching, and performance

- JPQL and native queries
- Dirty checking and flush behavior
- First-level and second-level cache
- N+1 query problem and fetch strategies

### Consistency and locking

- Optimistic and pessimistic locking
- Persistence and transaction boundaries
- Pagination and batch operations
- Common ORM pitfalls

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `jdbc-jpa-hibernate/persistence-foundations/` — persistence foundations question pages
- `jdbc-jpa-hibernate/entities-and-relationships/` — entities and relationships question pages
- `jdbc-jpa-hibernate/queries-caching-and-performance/` — queries, caching, and performance question pages
- `jdbc-jpa-hibernate/consistency-and-locking/` — consistency and locking question pages

Use descriptive lowercase kebab-case filenames, for example `jdbc-jpa-hibernate/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [JPA and Hibernate interview questions](interview-questions.md)

## Related modules

- [Module 09: SQL and Relational Databases](../sql-relational-databases/README.md)
- [Module 15: Testing](../testing/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
