# Redis and Caching: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Cache design and consistency

### Q1. What invalidation race can occur when a database update and cache delete are separate?

**Answer:** A reader can reload the old database value between the write and cache deletion, then repopulate stale data.

**Example:** Use versioned keys or a carefully ordered update/invalidate strategy.

### Q2. What is a cache-aside stale-fill race?

**Answer:** A slow read started before a write can finish later and overwrite the cache with an older value.

**Example:** Include data versions or coordinate refresh with writes.

### Q3. Why add jitter to cache TTLs?

**Answer:** Random variation spreads expirations so many keys do not expire together and overload the source.

**Example:** Add a small randomized offset to the base lifetime.

### Q4. When is negative caching helpful?

**Answer:** It stores a short-lived absence result so repeated lookups for nonexistent keys do not hammer the database.

**Example:** Use a short TTL so newly created records become visible.

### Q5. What is request coalescing for a hot cache miss?

**Answer:** Concurrent callers share one refresh operation rather than each querying the origin.

**Example:** Use an in-flight future keyed by cache key.

### Q6. What is cache warming, and what is its risk?

**Answer:** Preloading likely hot values can reduce cold-start misses, but poor estimates can consume capacity and origin resources.

**Example:** Warm only measured high-value keys.

### Q7. How should cache entries be versioned across schema changes?

**Answer:** Include a version namespace or safely decode old and new formats during rollout.

**Example:** Change `profile:v1:` to `profile:v2:`.

### Q8. Why is cache consistency usually weaker than database consistency?

**Answer:** Cache updates often occur on a separate path and may be delayed, evicted, or missed.

**Example:** Define which reads may be stale and for how long.

### Q9. What is a write-through cache?

**Answer:** The write path updates the cache and backing store as a coordinated step, with ordering defined by the design.

**Example:** Do not acknowledge success if the required durable write failed.

### Q10. What is a write-behind cache's main risk?

**Answer:** A process or cache failure before asynchronous persistence can lose acknowledged updates.

**Example:** Use only when loss tolerance and recovery are explicit.

## Redis operations and data structures

### Q11. How does Redis Cluster distribute keys?

**Answer:** Keys are mapped to hash slots, and slots are assigned to cluster nodes; multi-key operations may require keys in one slot.

**Example:** Hash tags such as `{user42}` can colocate related keys.

### Q12. What is a hot key, and how can it be mitigated?

**Answer:** One key receives disproportionate traffic and overloads its shard; local caching, replication reads, or key splitting may help.

**Example:** Measure before adding suffix shards because writes then need aggregation.

### Q13. What is a big-key problem in Redis?

**Answer:** A very large value or collection can cause latency spikes during transfer, deletion, replication, or memory management.

**Example:** Use `UNLINK` for asynchronous deletion where appropriate and avoid unbounded collections.

### Q14. Why use SCAN instead of KEYS in production?

**Answer:** SCAN iterates incrementally and yields between batches, while KEYS can block a Redis server while matching the full keyspace.

**Example:** Still handle duplicates and cursor completion correctly.

### Q15. What does Redis Slow Log show?

**Answer:** Commands whose server-side execution exceeds a configured threshold; it does not include all client/network delay.

**Example:** Compare slow-log events with application latency.

### Q16. What does a Redis pipeline improve?

**Answer:** It batches commands to reduce network round trips; it does not make the batch a transaction.

**Example:** Pipeline independent reads or writes when ordering isolation is unnecessary.

### Q17. How do MULTI/EXEC and WATCH support optimistic transactions?

**Answer:** WATCH marks keys; EXEC applies queued commands only if watched keys were not changed, otherwise the client retries.

**Example:** Keep retry loops bounded under contention.

### Q18. What is the difference between RDB and AOF persistence?

**Answer:** RDB stores point-in-time snapshots; AOF records write operations for replay, with different recovery and I/O trade-offs.

**Example:** Choose based on durability and restart-time needs.

### Q19. What does Redis replication guarantee about freshness?

**Answer:** Replication is generally asynchronous, so a replica can lag behind the primary and reads may be stale.

**Example:** Do not assume read-after-write from a replica.

### Q20. What should an application monitor for Redis memory?

**Answer:** Used memory, fragmentation, evictions, hit rate, key growth, and command latency.

**Example:** Alert before the memory limit causes broad eviction.

## Locks and atomic operations

### Q21. Why should a Redis lock use a unique owner token?

**Answer:** It prevents one client from deleting a lock acquired later by another client after the first client's lease expired.

**Example:** Release only if the stored token matches yours.

### Q22. Why is a lock expiry alone insufficient for some distributed writes?

**Answer:** A paused client may resume after its lease expires and still perform a stale write; fencing tokens let the resource reject old owners.

**Example:** Use monotonically increasing fencing values at the protected resource.

### Q23. What atomic pattern can implement a counter with an expiry?

**Answer:** Run an increment and set expiry atomically, for example with Lua, so a crash cannot leave an unbounded key.

**Example:** `INCR` followed by `EXPIRE` in a script.

### Q24. When is Redis Pub/Sub unsuitable as a job queue?

**Answer:** Messages are not retained for disconnected subscribers, so consumers can miss them.

**Example:** Use a durable queue or stream when delivery and replay matter.

### Q25. How do Redis Streams differ from Pub/Sub?

**Answer:** Streams store records and support consumer groups and acknowledgment tracking, while Pub/Sub is transient broadcast.

**Example:** Use a stream when consumers need to resume pending work.

### Q26. What does `XACK` mean for a Redis Stream consumer group?

**Answer:** It marks a delivered entry as processed so it can be removed from the group's pending list.

**Example:** Acknowledge only after the business side effect succeeds.

### Q27. Why should a stream consumer inspect pending entries?

**Answer:** A consumer may crash after delivery, leaving work unacknowledged for recovery.

**Example:** Claim old pending entries after a safe timeout.

### Q28. What is a Bloom filter's trade-off?

**Answer:** It is memory-efficient and may say “possibly present” for an absent item, but a negative result is definitive under normal operation.

**Example:** Confirm positives with the source of truth.

### Q29. How can a Redis key naming convention help operations?

**Answer:** Prefixes make ownership, environment, entity type, and version easier to identify and expire safely.

**Example:** Use `prod:catalog:v2:item:123`.

### Q30. Why should Redis command access be restricted?

**Answer:** A compromised or misconfigured client with broad commands can read or alter unrelated data.

**Example:** Use network boundaries, ACL users, TLS, and least privilege.

## Failure handling and scale

### Q31. How should an API behave if Redis is only a performance cache and becomes unavailable?

**Answer:** Fall back to the source of truth with bounded concurrency and timeouts; fail explicitly if fallback would overload the database.

**Example:** A circuit breaker can cap fallback load.

### Q32. What can happen if a Redis outage sends every cache miss to the database?

**Answer:** The origin may become overloaded, causing a cache failure to become a broader outage.

**Example:** Use request limits, local cache, and controlled fallback.

### Q33. How can cache penetration be mitigated for arbitrary IDs?

**Answer:** Validate identifiers, cache misses briefly, or use a Bloom filter before database access.

**Example:** Avoid caching unbounded user-controlled keys forever.

### Q34. Why might eviction cause a sudden latency spike?

**Answer:** Many hot entries can disappear and all requests reload from the database at once.

**Example:** Monitor evictions and size the cache for the working set.

### Q35. How do you decide whether to use Redis as a source of truth?

**Answer:** Evaluate durability, persistence, replication, recovery, consistency, and data-model requirements rather than assuming in-memory means disposable.

**Example:** Critical records often belong in a durable database with Redis as a derived view.

### Q36. What does a Redis failover protect against, and what may it not protect?

**Answer:** It can restore service after node failure, but asynchronous replication can lose recent writes and clients may see brief interruption.

**Example:** Test failover and define tolerated write loss.

### Q37. How can Redis Cluster multi-key commands fail?

**Answer:** Keys in one atomic command may need to map to the same hash slot.

**Example:** Use compatible hash tags for related keys.

### Q38. What is a thundering herd after a Redis restart?

**Answer:** Many clients simultaneously refill an empty cache, creating a burst against the backing store.

**Example:** Warm gradually and use jitter or coalescing.

### Q39. Why should a Redis lock not protect a long remote operation without lease renewal?

**Answer:** The lease may expire while work is still running, allowing another owner to enter.

**Example:** Keep critical sections short or implement a robust lease and fencing design.

### Q40. Which signals help separate Redis server latency from client latency?

**Answer:** Compare command duration, network round-trip, connection-pool waits, server CPU, and slow-log data.

**Example:** Instrument client spans around Redis operations.

## References

- [Redis data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis cache-aside pattern](https://redis.io/docs/latest/develop/use-cases/cache-aside/)
- [Redis eviction](https://redis.io/docs/latest/develop/reference/eviction/)
- [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
