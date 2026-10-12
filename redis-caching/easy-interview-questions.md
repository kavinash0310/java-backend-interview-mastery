# Redis and Caching: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q03. What is Redis?

**Answer:** An in-memory data store commonly used for caching, counters, coordination, and data structures. Choose a Redis type that fits the operation.

### Q04. What is a Redis key?

**Answer:** The name used to retrieve a value. Use a predictable namespace and include the relevant identity, such as product:42.

### Q05. What is a Redis string?

**Answer:** A value that can hold text, serialized data, or integer-like content. Redis supports atomic numeric operations on integer strings.

### Q06. What is a Redis hash?

**Answer:** Fields and values stored under one key. It can represent a small object when field-level reads or updates are useful.

### Q07. When would you use a Redis list?

**Answer:** For an ordered sequence with push and pop operations. For durable queue guarantees or consumer groups, consider Streams or a broker.

### Q08. What is a Redis set?

**Answer:** A collection of unique members without guaranteed order. It supports membership checks and set operations.

### Q09. What is a sorted set?

**Answer:** Unique members associated with scores that determine order. It can support leaderboards or range queries by score.

### Q10. What is a Redis Stream?

**Answer:** An append-oriented log with IDs and consumer groups, supporting event processing, pending messages, and acknowledgement workflows.

### Q11. What is cache-aside?

**Answer:** The app checks the cache, reads the database on a miss, then stores the value. The application owns cache population and invalidation.

**Example:** Look up in Redis; on a miss query the database and set a TTL.

### Q12. What is write-through caching?

**Answer:** The write flow updates the cache alongside the source store. It can keep reads warm, but consistency and partial failures need care.

### Q13. What is write-behind caching?

**Answer:** The app writes to the cache first and persists later. It can reduce write latency but risks data loss if persistence fails.

### Q14. What is a cache hit?

**Answer:** The requested usable value is present in cache. Monitor hit rate along with latency and database load.

### Q15. What is a cache miss?

**Answer:** The needed value is absent or expired, so the app must fetch from its source or use a defined fallback.

### Q16. What is a TTL?

**Answer:** Time-to-live: how long a cached key remains valid before expiring. Choose it based on freshness needs.

### Q17. What is cache invalidation?

**Answer:** Removing or refreshing cached data when the source changes or the value becomes stale. Define which writes invalidate which keys.

### Q18. What is a cache stampede?

**Answer:** Many requests miss or expire together and recompute the same expensive value. Use coalescing, jittered TTLs, or controlled refresh.

### Q19. What is cache penetration?

**Answer:** Repeated requests for absent keys cause every request to miss. A short negative-cache entry or input validation can help.

### Q20. What is cache avalanche?

**Answer:** Many expirations or cache loss send sudden load to the source. Spread expirations and ensure fallback capacity.

### Q21. What is a negative cache entry?

**Answer:** A short-lived record that a lookup returned no result, preventing repeated source queries. Invalidate it when the record is created.

### Q22. What is an eviction policy?

**Answer:** It determines which keys Redis removes when memory is constrained. Choose based on whether the instance holds disposable cache data or important state.

### Q23. Expiry vs eviction?

**Answer:** Expiry removes a key after its TTL; eviction removes keys to free memory under policy. A key can be evicted before expiry.

### Q24. What is Redis persistence?

**Answer:** It stores data to disk using configured mechanisms so it may survive restart. Recovery point and write overhead depend on settings.

### Q25. What is Redis replication?

**Answer:** It copies data from a primary to replicas. It can aid availability or read scaling, but does not guarantee every write is synchronously durable.

### Q26. What is a Redis distributed lock?

**Answer:** A time-limited claim for coordinating processes. Correctness requires unique ownership tokens and careful expiry/release; database constraints may better protect business invariants.

### Q27. What is a Redis pipeline?

**Answer:** It sends several commands without waiting for each response before sending the next, reducing round trips. It is not a transaction.

### Q28. What is a Redis transaction?

**Answer:** MULTI/EXEC queues commands and runs them without interleaving from other clients. It does not provide relational rollback semantics for every command error.

### Q29. How should an app behave when Redis is unavailable?

**Answer:** If Redis is a cache, bypass it with database protection; if it holds essential coordination state, fail that operation safely. Alert on failure.

### Q30. Why should cache values have a TTL?

**Answer:** A TTL limits how long abandoned or stale data can remain, bounds memory, and helps recover from missed invalidation.

## References

- [Redis data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis cache-aside pattern](https://redis.io/docs/latest/develop/use-cases/cache-aside/)
- [Redis eviction](https://redis.io/docs/latest/develop/reference/eviction/)
- [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
