# System Design and Architecture: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Requirements, estimates, and data

### Q1. How do you turn a vague scale requirement into a capacity estimate?

**Answer:** Clarify peak and average traffic, payload sizes, read/write ratio, retention, and SLOs; estimate storage, bandwidth, and concurrency with explicit assumptions.

### Q2. Why should a design include a growth and skew estimate, not only average load?

**Answer:** Hot tenants or burst traffic can overload one partition despite modest totals. Model p95/p99 demand and test key distribution.

### Q3. How do you choose a partition key for a multi-tenant service?

**Answer:** Balance locality and query patterns against skew and hot tenants. Include a resharding strategy and avoid a key that concentrates a large tenant.

### Q4. When is normalization preferable to denormalizing a read model?

**Answer:** Normalize when transactional correctness and update simplicity dominate; denormalize for measured read paths where extra consistency machinery is justified.

### Q5. How should a design represent idempotency for a financial command?

**Answer:** Persist a unique request key, canonical request hash, status, and result with a uniqueness constraint. Define behavior for same key with a different payload.

### Q6. What data-retention assumptions affect storage sizing?

**Answer:** Retention duration, indexes, replicas, backups, compaction, and growth rates all matter. Include deletion/legal holds and restore copies rather than counting live rows alone.

### Q7. How do you decide whether a read replica is sufficient for scale?

**Answer:** Estimate read load, replica lag tolerance, and query mix; replicas do not help write bottlenecks and may make read-after-write stale.

### Q8. What is the difference between a transactional database partition and a distributed shard?

**Answer:** Partitioning organizes data within a database system; sharding distributes ownership across independent database nodes and adds routing and cross-shard complexity.

### Q9. How should a design handle a user changing a sort key during keyset pagination?

**Answer:** The cursor can skip or repeat rows if ordering fields change. Use immutable tie-breakers or snapshot/version semantics and document consistency.

### Q10. Why should requirements state an acceptable staleness window?

**Answer:** It determines whether caches, replicas, and asynchronous projections are valid. Without it, teams cannot choose consistency mechanisms rationally.

## Scale and resilience patterns

### Q11. How do you calculate whether a queue can drain after a traffic burst?

**Answer:** Compare incoming rate with sustained processing rate and calculate backlog age/recovery time. If processing capacity never exceeds arrivals, the queue only postpones failure.

### Q12. Why is a global rate limiter harder than a per-instance token bucket?

**Answer:** Global state needs coordination and adds latency/failure coupling; local buckets allow aggregate overshoot. Choose exactness based on abuse risk and scale.

### Q13. How do you design graceful degradation for a recommendation dependency?

**Answer:** Define which requests can omit recommendations, use a bounded timeout/cache, and expose degraded behavior in metrics. Never degrade authorization or core data integrity.

### Q14. When does a bulkhead protect a system better than adding more replicas?

**Answer:** It isolates scarce resources so one dependency or tenant cannot consume all capacity. Replicas alone can multiply pressure on the same bottleneck.

### Q15. How should retries be budgeted in a deep service graph?

**Answer:** Use one end-to-end deadline and limit attempts by operation, with jitter and no nested amplification. Measure retry volume as part of capacity.

### Q16. What is the role of a circuit breaker during a partial regional outage?

**Answer:** It stops repeated calls to unhealthy paths and permits controlled recovery probes. Pair it with routing and failover semantics; it cannot move data safely by itself.

### Q17. How does a cache affect capacity during a cold start or failover?

**Answer:** Hit rate collapses and the origin receives a surge. Warm strategically, coalesce misses, and cap origin concurrency before cache restoration.

### Q18. Why can autoscaling make a database bottleneck worse?

**Answer:** New app instances add connections and concurrent queries faster than the DB can handle them. Scale from end-to-end capacity and cap connection demand.

### Q19. How do you design admission control for a saturated API?

**Answer:** Reject or defer lower-priority work before queues grow unbounded, with clear status and retry guidance. Preserve capacity for critical traffic.

### Q20. What must a multi-region write design specify beyond failover DNS?

**Answer:** Write ownership, conflict policy, replication lag, consistency, data residency, and recovery point. DNS alone does not resolve concurrent writes.

## Consistency, evolution, and failure modes

### Q21. How do you choose between synchronous commit and an outbox event for order processing?

**Answer:** Commit order state locally, then publish through an outbox when eventual downstream updates are acceptable. Use synchronous coordination only when user-visible invariants require it.

### Q22. What is the dual-write problem, and why does a retry not solve it?

**Answer:** Database and broker commits can diverge if one succeeds and the other fails. Retries can duplicate one side; use an outbox, idempotency, or reconciliation.

### Q23. How should a schema migration support rolling old and new application versions?

**Answer:** Add compatible structures first, deploy code that reads both, backfill, switch writes/reads, then remove old structures after verification.

### Q24. What consistency guarantee does a read-your-writes session require?

**Answer:** Route or token the user's subsequent reads so they observe at least their committed version. A stale replica may violate this even if globally eventual.

### Q25. When should a system use a saga instead of a distributed transaction?

**Answer:** Use a saga when services own independent stores and eventual compensation is acceptable. A saga does not provide atomic visibility or guaranteed compensation.

### Q26. How do you prevent a poison message from blocking a partition forever?

**Answer:** Bound retries, isolate the message with metadata, alert, and preserve order semantics if later records depend on it. Replay after fixing the cause.

### Q27. Why should an idempotency store retain results beyond the maximum client retry window?

**Answer:** If a delayed retry arrives after expiration, it can repeat a committed operation. Retention should cover the business and transport retry horizon.

### Q28. What is a reconciliation job's role in an eventually consistent workflow?

**Answer:** It compares authoritative records with derived or external state and repairs detectable gaps. Make it rate-limited, observable, and safe to rerun.

### Q29. How can a distributed system handle clock skew for expiration and ordering?

**Answer:** Use database/server time or logical sequence where correctness requires ordering; allow bounded skew for leases and tokens. Never assume synchronized wall clocks are exact.

### Q30. Why is a cache invalidation topic not necessarily a source of truth?

**Answer:** Events may be duplicated, delayed, or lost during retention gaps. Rebuild from authoritative data and make invalidation consumers recoverable.

## Design exercises and trade-offs

### Q31. Design an audit log that remains trustworthy after an application bug.

**Answer:** Append immutable records with actor, action, object, timestamp, and correlation; restrict modification and separate retention. Use tamper-evident storage if required by threat model.

### Q32. Design an upload service for multi-gigabyte files without buffering them in the API process.

**Answer:** Issue short-lived scoped multipart upload URLs, verify checksums and completion, scan asynchronously, and track state in durable metadata.

### Q33. How would you support a webhook consumer that is slow or intermittently unavailable?

**Answer:** Persist deliveries, retry with bounded backoff, sign requests, expose replay status, and make receiver processing idempotent. Isolate each endpoint's queue.

### Q34. Design a search index that can be rebuilt after corruption.

**Answer:** Treat the database/event log as source of truth, use versioned bulk indexing, track checkpoints, and atomically switch aliases after validation.

### Q35. How do you prevent a reservation system from overselling under high contention?

**Answer:** Enforce an atomic conditional update or row lock with a database constraint, then publish downstream changes from an outbox. Load-test the hot inventory key.

### Q36. Design a short-link service that supports deletion and abuse reporting.

**Answer:** Use a unique mapping with indexed lookup, safe redirect policy, rate limits, lifecycle state, and audit. Decide cache invalidation and key reuse semantics.

### Q37. How would you design per-customer event ordering without one global bottleneck?

**Answer:** Partition by customer or aggregate key and process sequentially within partitions. Monitor skew and isolate oversized tenants with explicit routing.

### Q38. What trade-off does CQRS introduce in a dashboard with strict freshness needs?

**Answer:** A separate read model can scale queries but may lag. Expose freshness and use synchronous updates or primary reads for operations requiring current state.

### Q39. How do you compare a monolith and microservice design under an interview time limit?

**Answer:** State domain boundaries, deployment needs, team ownership, failure costs, and data consistency. Recommend the simplest architecture meeting measured constraints.

### Q40. What should an architecture decision include when evidence is uncertain?

**Answer:** List assumptions, alternatives, reversible choices, success metrics, and a revisit trigger. Prefer a small experiment when the cost of being wrong is high.


## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [PostgreSQL indexes](https://www.postgresql.org/docs/current/indexes.html)
- [AWS guidance: partitioning](https://docs.aws.amazon.com/whitepapers/latest/scale-performance-best-practices/partitioning.html)
- [AWS guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
