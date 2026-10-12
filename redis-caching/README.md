# Module 11 — Redis and Caching

> **Module guide:** Use Redis and application caches to reduce latency and load while managing freshness, eviction, and failure behavior.

## Learning outcomes

- Select cache keys, TTLs, and invalidation behavior for a workload.
- Explain cache-aside and write-through trade-offs.
- Design graceful behavior when Redis is slow or unavailable.

## Subtopic links

- [Redis foundations](#redis-foundations)
- [Caching strategies](#caching-strategies)
- [Consistency and resilience](#consistency-and-resilience)

## Topic coverage

### Redis foundations

- Redis architecture and key/value model
- Strings, hashes, lists, sets, sorted sets, and streams
- Redis persistence and replication basics

### Caching strategies

- Cache-aside pattern
- Read-through, write-through, and write-behind
- TTL, eviction policies, and key design
- Spring Boot cache integration

### Consistency and resilience

- Cache invalidation and freshness
- Cache stampede and request coalescing
- Distributed locks and their limits
- Cache consistency and failure handling

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `redis-caching/redis-foundations/` — redis foundations question pages
- `redis-caching/caching-strategies/` — caching strategies question pages
- `redis-caching/consistency-and-resilience/` — consistency and resilience question pages

Use descriptive lowercase kebab-case filenames, for example `redis-caching/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [Redis and caching interview questions](interview-questions.md)

## Related modules

- [Module 09: SQL and Relational Databases](../sql-relational-databases/README.md)
- [Module 13: Microservices Architecture](../microservices-architecture/README.md)
- [Module 20: System Design and Architecture](../system-design-architecture/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
