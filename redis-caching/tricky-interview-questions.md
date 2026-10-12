# Redis and Caching: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Cache semantics and expiration

### Q1. Does updating a key always preserve its Redis TTL?

**Answer:** No. Some update commands clear expiration unless an expiry-preserving option is used. Check command semantics and reapply TTL atomically.

### Q2. Can an expired Redis key remain in memory briefly?

**Answer:** Expiration is enforced lazily and by active sampling; a key is treated as absent to commands while reclamation timing may differ.

### Q3. Why is a cache hit not proof that the value is fresh?

**Answer:** The cache may contain stale data within its TTL or after a failed invalidation. Freshness must match the domain's requirement.

### Q4. What race occurs in cache-aside after a database update?

**Answer:** A reader can load old data before commit and write it into cache after invalidation. Use versions or coordinated invalidation.

### Q5. Why can a local refresh lock still allow a database stampede across pods?

**Answer:** The lock is process-local, so each replica may refresh at once. Use shared coalescing, TTL jitter, or origin rate limits.

### Q6. Can TTL alone prevent stale authorization after permission revocation?

**Answer:** No. TTL defines a stale window that may be unacceptable. Use invalidation/version checks or the authoritative source.

### Q7. Does Redis LRU eviction remove the globally least-recently-used key exactly?

**Answer:** No. Redis uses an approximate sampling policy for efficiency. Expect approximate behavior.

### Q8. What happens if a cache key is reused after serialized schema changes?

**Answer:** Old payload may fail or be misinterpreted. Version key namespaces or payload envelopes and migrate gradually.

### Q9. Why can negative caching hide a newly created record?

**Answer:** A cached not-found remains until expiry. Use a deliberate short TTL or invalidate on creation.

### Q10. Can an atomic SET still overwrite a fresher cache value with stale data?

**Answer:** Yes. Atomicity applies to one command, not whether its source version is current. Compare versions or use conditional scripts.

## Commands, scripts, and cluster traps

### Q11. Why is KEYS dangerous on a large production keyspace?

**Answer:** It scans the whole keyspace in one blocking command. Use SCAN in bounded batches and tolerate duplicates or concurrent changes.

### Q12. Does SCAN return each matching key exactly once?

**Answer:** No. It can return duplicates and is not a snapshot. Make scan processing idempotent.

### Q13. Can MULTI/EXEC roll back every command that fails at runtime?

**Answer:** No. Commands execute in the transaction; a runtime error does not undo the other executed commands. Validate inputs first.

### Q14. Why can EXPIRE key 0 emit a delete event instead of an expiration event?

**Answer:** A non-positive timeout deletes the key immediately; with keyspace notifications enabled, Redis reports a del event rather than an expired event. Expiration notifications are also best-effort.

### Q15. What do Redis TTL results -1 and -2 mean, and why can cleanup code confuse them?

**Answer:** -1 means the key exists without expiry; -2 means the key is missing. Treat them separately or cleanup may leave permanent keys or misreport absence.

### Q16. What is the risk of putting every tenant under one hash tag?

**Answer:** It concentrates them in one slot and creates a hot shard. Use tags only for keys needing same-slot atomicity.

### Q17. Why can pipelining preserve throughput but not atomicity?

**Answer:** It batches network round trips, but other clients' commands can interleave. Use a transaction or Lua where required.

### Q18. Why can a Lua script cause latency for unrelated clients?

**Answer:** Scripts execute atomically and block command processing. Keep them short and avoid unbounded loops.

### Q19. Does PUBLISH guarantee a disconnected subscriber receives a message later?

**Answer:** No. Pub/Sub is ephemeral; use Streams or a durable broker for replay.

### Q20. Why can one huge Redis command create a latency spike?

**Answer:** Large scans or payload operations block other work. Bound payload size and operation scope.

## Locks, queues, and failure

### Q21. Why is deleting a Redis lock by key alone unsafe?

**Answer:** The lease may have expired and another client may own the key. Compare the unique token and delete atomically.

### Q22. Does a lock TTL stop its owner from continuing a critical section after expiry?

**Answer:** No. The old client can continue. Use fencing tokens enforced by the protected resource.

### Q23. Why can increment-then-expire leave a permanent rate-limit key?

**Answer:** The process may crash between INCR and EXPIRE. Use an atomic script or command combination.

### Q24. Can Redis rate limiting use INCR without atomic expiry handling?

**Answer:** The count may persist forever or reset incorrectly. Set expiry atomically and define window boundary semantics.

### Q25. What happens if an RPOP worker crashes after receiving a queue item?

**Answer:** The item may be lost because it was removed. Use a processing list/ack protocol or a durable stream.

### Q26. Why can a Streams consumer group show pending entries while consumers are healthy?

**Answer:** A handler may have processed but failed to acknowledge, or a prior owner died. Inspect pending age and reclaim safely.

### Q27. Does a Redis replica always contain the latest acknowledged write after failover?

**Answer:** Replication is often asynchronous, so recent writes can be missing. Choose durability based on acceptable data loss.

### Q28. Why can a fast Redis recovery still overload the database?

**Answer:** A cold cache causes a concentrated miss burst. Coalesce reads, throttle origin traffic, and warm critical keys gradually.

### Q29. Can a Redis transaction provide atomicity with a payment API call?

**Answer:** No. It cannot commit the external payment. Use a durable workflow, idempotency, and reconciliation.

### Q30. Why should a queue consumer acknowledge only after its database side effect commits?

**Answer:** A crash before commit after acknowledgment loses the effect. Commit first and handle duplicate delivery.

## References

- [Redis data types](https://redis.io/docs/latest/develop/data-types/)
- [Redis cache-aside pattern](https://redis.io/docs/latest/develop/use-cases/cache-aside/)
- [Redis eviction](https://redis.io/docs/latest/develop/reference/eviction/)
- [Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
