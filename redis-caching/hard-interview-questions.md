# Redis and Caching: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Cache correctness and consistency

### Q1. How do you prevent a cache stampede when one hot key expires?

**Answer:** Use request coalescing or a short distributed refresh lock, add TTL jitter, and serve stale data briefly where safe. Bound waiters and define failure fallback.

### Q2. Why is cache-aside invalidation harder than setting a TTL?

**Answer:** A database commit and cache delete are not atomic, so races can repopulate stale values. Use versioning, outbox-driven invalidation, or bounded TTL with clear consistency goals.

### Q3. How can a stale cache write overwrite a newer value?

**Answer:** A slow loader may finish after a newer update and write old data. Compare versions or use a conditional write script that rejects older generations.

### Q4. What does stale-while-revalidate trade?

**Answer:** It serves a known old value while refresh proceeds, improving availability and latency at the cost of bounded staleness. Define which data may tolerate it.

### Q5. How do TTL jitter and refresh-ahead complement each other?

**Answer:** Jitter spreads simultaneous expirations; refresh-ahead renews hot entries before expiry. Neither fixes a broken source of truth or unbounded refresh load.

### Q6. Why is a cache invalidation event better sent through a transactional outbox?

**Answer:** The outbox records the event with the database change, avoiding a lost publish after commit. Consumers must still handle duplicates and ordering.

### Q7. How should a cache key encode tenant, locale, and schema version?

**Answer:** Include every input that affects the representation in an unambiguous namespaced key. Avoid collisions and allow versioned invalidation without scanning all data.

### Q8. What is the risk of caching authorization decisions for too long?

**Answer:** Revoked permissions can remain effective until expiry. Use short bounded TTL, versioned invalidation, and avoid caching decisions where immediate revocation is required.

### Q9. Why should negative caching have a shorter or deliberate TTL?

**Answer:** A transient not-found can hide newly created data, while missing-key traffic can otherwise hammer the origin. Distinguish stable absence from temporary failure.

### Q10. How can a two-level local-plus-Redis cache become inconsistent?

**Answer:** Invalidating Redis does not instantly clear per-process copies. Use versioned events, short local TTLs, or bypass local cache for data needing stronger freshness.

## Redis operations and data structures

### Q11. When is a Redis hash preferable to a serialized JSON blob?

**Answer:** Use a hash for field-level updates and reads; a blob is simpler for whole-object replacement. Consider schema evolution, atomicity, and network round trips.

### Q12. How can Lua make a read-check-write operation atomic?

**Answer:** Run the conditional logic on the server as one script, passing keys and arguments explicitly. Keep scripts bounded because they block Redis command processing.

### Q13. Why can KEYS hurt a production Redis instance while SCAN is safer?

**Answer:** KEYS walks the keyspace in one blocking operation; SCAN yields incremental batches but may return duplicates and is not a snapshot.

### Q14. What does Redis Cluster's hash slot mean for multi-key operations?

**Answer:** Keys must map to the same slot for many atomic multi-key commands. Use hash tags deliberately, but avoid creating one hot slot.

### Q15. How do pipelining and transactions differ?

**Answer:** Pipelining reduces network round trips but commands may interleave; MULTI/EXEC queues a transaction on one connection. Neither alone validates application invariants.

### Q16. Why can a Redis latency spike persist after a slow command finishes?

**Answer:** Redis processes commands mostly serially per shard, so one expensive command delays others. Identify command latency and avoid large scans or huge values.

### Q17. What is the operational trade-off of key expiration at scale?

**Answer:** Expiry frees memory but synchronized TTLs can cause bursts of work and delete overhead. Spread expirations and monitor eviction and expired-key rates.

### Q18. How does a Redis Stream consumer group differ from Pub/Sub?

**Answer:** Streams persist entries and track pending deliveries for consumer groups; Pub/Sub is ephemeral and subscribers miss messages while disconnected.

### Q19. Why does a Stream pending-entry list need recovery logic?

**Answer:** Consumers can die after claiming work, leaving entries pending. Inspect and reclaim idle entries with ownership and duplicate-processing safeguards.

### Q20. What is the risk of storing an entire aggregate as one large Redis value?

**Answer:** Updates and transfers become coarse, large values raise memory and network cost, and partial field access is harder. Bound size and match data layout to access patterns.

## Locks, queues, and failure behavior

### Q21. Why is a Redis lock with only a TTL not enough to protect a slow critical section?

**Answer:** The lease may expire while the owner is still working, allowing a second owner. Use fencing tokens checked by the protected resource.

### Q22. What does a fencing token add beyond a random lock value?

**Answer:** A monotonically increasing token lets the resource reject writes from an expired older owner. A lock service alone cannot stop a stale client from acting.

### Q23. How should a client safely release a Redis lock?

**Answer:** Delete only if the stored value equals its unique token, atomically via a script. Blind DEL can remove another owner's renewed lock.

### Q24. What should an application do if Redis becomes unavailable?

**Answer:** Choose an explicit fallback: bounded origin reads, fail-fast, or degrade features. Use bulkheads and rate limits so the database is not overwhelmed.

### Q25. Why can a fallback-to-database strategy cause a second outage?

**Answer:** All cache misses surge to the origin at once. Shed load, coalesce reads, cap concurrent DB work, and recover gradually.

### Q26. How do Redis replication and failover affect acknowledged writes?

**Answer:** Replication is usually asynchronous, so a promoted replica may not contain the latest acknowledged write. Decide which data can tolerate this loss.

### Q27. What is the risk of treating Redis as the only durable store for payments?

**Answer:** Cache eviction, persistence gaps, or failover can lose critical state. Keep the authoritative financial ledger in a durable transactional database.

### Q28. When are Redis transactions insufficient for a distributed invariant?

**Answer:** MULTI/EXEC is atomic only within one Redis execution context and cannot atomically coordinate with SQL or another service. Use an outbox or saga.

### Q29. Why can a blocking queue command exhaust application connections?

**Answer:** Each blocked client holds a connection while waiting. Size pools and timeouts for blocking operations separately from short commands.

### Q30. How should a queue consumer handle a message after its side effect succeeds but acknowledgment fails?

**Answer:** Assume redelivery and make the handler idempotent using a durable processed key or business uniqueness constraint.

## Capacity, security, and tuning

### Q31. How do you estimate Redis memory beyond the raw bytes in values?

**Answer:** Include key and object overhead, allocator fragmentation, replication buffers, persistence fork copy-on-write, and headroom. Measure with realistic key distributions.

### Q32. Why can maxmemory eviction break a cache-aside assumption?

**Answer:** An entry may disappear before TTL and cause origin load; an allkeys policy may also evict operational data. Select policy and keyspace accordingly.

### Q33. What is the security risk of exposing Redis directly to an untrusted network?

**Answer:** Redis commands may read, modify, or destroy application data and can enable host-level abuse if misconfigured. Isolate it, authenticate, encrypt in transit, and restrict commands.

### Q34. How can TLS and ACLs be rolled out without interrupting Redis clients?

**Answer:** Deploy compatible credentials and trust bundles first, migrate clients with overlap, then revoke old access. Verify all clients and monitoring connections.

### Q35. Why should high-cardinality cache keys be monitored?

**Answer:** Unbounded user-controlled dimensions can grow memory until eviction or OOM. Validate key inputs, set quotas, and track keyspace growth by namespace.

### Q36. How do you choose eviction policy for a mixed Redis workload?

**Answer:** Separate cache and durable queue/session workloads where possible, then choose a policy matching access semantics. One global policy can harm unrelated data.

### Q37. When does persistence fork copy-on-write cause a memory spike?

**Answer:** RDB/AOF rewrite forks a child; writes dirty shared pages and increase private copies. Schedule persistence with memory headroom and monitor fork duration.

### Q38. Why can increasing connection-pool size reduce Redis throughput?

**Answer:** More concurrent clients add scheduling and context-switch overhead and may overload the server. Tune from observed latency and command throughput.

### Q39. How would you measure whether a cache is helping the system?

**Answer:** Track hit rate by key family alongside origin load, latency, memory, eviction, and stale-response rate. A high hit rate can still serve poor value.

### Q40. What is a safe way to roll out a new cache value format?

**Answer:** Version the namespace or envelope, support old and new readers during rollout, and expire old values gradually. Do not deserialize arbitrary untrusted payloads.


## References

- [Redis data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis cache-aside pattern](https://redis.io/docs/latest/develop/use-cases/cache-aside/)
- [Redis eviction](https://redis.io/docs/latest/develop/reference/eviction/)
- [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
