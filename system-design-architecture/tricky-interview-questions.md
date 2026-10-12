# System Design and Architecture: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Requirements and capacity pitfalls

### Q1. Why is sizing from average requests per second dangerous?

**Answer:** Peak bursts, tenant skew, and retries create higher demand. Estimate peak and tail capacity with explicit assumptions.

### Q2. Can adding a queue solve sustained overload?

**Answer:** No. If arrivals exceed processing capacity, backlog grows without bound. A queue absorbs bursts but needs admission or capacity planning.

### Q3. Why can autoscaling from CPU lag behind queue overload?

**Answer:** CPU may stay low while workers wait on I/O, and new instances take time to warm. Scale on queue age and reserve headroom.

### Q4. Why is a p99 latency target meaningless without window and population?

**Answer:** Routes, time windows, and request classes produce different percentiles. Define eligible requests and aggregation period.

### Q5. How should you estimate storage for a write-heavy event system?

**Answer:** Include replicas, indexes, compaction, retention, backups, and metadata, not only payload bytes.

### Q6. Why can a partition key that hashes evenly today become hot later?

**Answer:** One tenant or entity may grow disproportionately. Measure skew and plan key splitting or isolation.

### Q7. Does using more replicas always increase read capacity?

**Answer:** Only if reads can be distributed and lag is acceptable; coordination, network, and hot partitions may remain.

### Q8. Why can data be stale if every database transaction is strongly consistent?

**Answer:** Caches, replicas, or async projections can serve older versions. Specify end-to-end consistency.

### Q9. How do you determine whether a feature needs strong consistency?

**Answer:** Identify the user-visible invariant and failure cost, then compare stale-read harm against coordination latency and availability.

### Q10. Why should a capacity model include retry traffic?

**Answer:** Failures can increase attempts as capacity falls. Model amplification and enforce a retry budget.

## Scalability and resilience traps

### Q11. Can a global token bucket scale linearly across regions without coordination?

**Answer:** Not exactly. Local buckets allow aggregate overshoot; strict accounting requires coordination or partitioned quotas.

### Q12. Why can cache hit ratio improve while the database remains overloaded?

**Answer:** Hot misses, stampedes, skew, or expensive uncached queries can dominate. Segment metrics by operation and origin load.

### Q13. What is the danger of stale data as fallback for every endpoint?

**Answer:** Some decisions require current authorization, balance, or inventory. Define stale-safe endpoints and maximum staleness.

### Q14. Why can a circuit breaker make recovery slower?

**Answer:** If thresholds or half-open probes are too strict, traffic may stay away after recovery. Use gradual probes and monitor transitions.

### Q15. Can exponential backoff alone prevent a thundering herd?

**Answer:** No. Clients can synchronize. Add jitter, concurrency bounds, and server-directed Retry-After.

### Q16. How can bulkhead isolation fail if all pools share one thread scheduler?

**Answer:** The common scheduler can still saturate and block every class. Isolate execution resources as well as connections.

### Q17. Why can multi-region active-active violate uniqueness?

**Answer:** Concurrent regions may accept the same key before replication converges. Partition ownership or coordinate uniqueness.

### Q18. What should happen when a dependency is slow but returns success?

**Answer:** Enforce deadlines and concurrency limits; slow success can exhaust resources. Track latency and saturation, not only errors.

### Q19. Why can read repair increase load during a storage incident?

**Answer:** Repair writes add work to an already stressed system and can amplify the outage. Bound repair concurrency and prioritize user-facing reads.

### Q20. Can a load balancer make a single-threaded bottleneck scale horizontally?

**Answer:** Only if requests are independent and state is externalized; hot keys or shared dependencies may remain serial.

## Consistency and design traps

### Q21. Why does an outbox prevent lost publication but not duplicate messages?

**Answer:** The relay may publish then crash before marking sent. Consumers need stable event IDs and idempotent effects.

### Q22. Can a saga provide atomic visibility to all readers?

**Answer:** No. Steps commit independently and intermediate state may be visible. Model pending states and compensation explicitly.

### Q23. Why is exactly-once delivery an imprecise guarantee?

**Answer:** Transport guarantees stop at a boundary; external effects may repeat. State the scope and make business processing idempotent.

### Q24. Why should a cache key include tenant even if authorization runs first?

**Answer:** Shared cache entries can cross tenant boundaries if scope is omitted. Authorization cannot fix a wrongly keyed value.

### Q25. Why can a design that guarantees globally increasing IDs become a cross-region bottleneck?

**Answer:** A single ordered allocator needs coordination across regions. If global order is unnecessary, partition IDs by region or use sortable IDs with weaker ordering.

### Q26. Why can read-your-writes require routing state?

**Answer:** A later request may hit a lagging replica. Carry a version token or route to primary until replication catches up.

### Q27. What is the risk of a distributed lock without fencing?

**Answer:** An expired owner can continue writing after a new owner acquires the lease. The resource must reject stale tokens.

### Q28. When is denormalization dangerous in a write-heavy system?

**Answer:** Each update must synchronize more copies and can partially fail. Denormalize for measured reads with a repair path.

### Q29. Why can a monotonic ID generator bottleneck a multi-region design?

**Answer:** Global coordination limits availability and throughput. Use partitioned IDs if global ordering is unnecessary.

### Q30. How should architecture handle uniqueness across shards?

**Answer:** Route the key to one shard, use coordination, or change the invariant. Cross-shard checks need explicit consistency.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [PostgreSQL indexes](https://www.postgresql.org/docs/current/indexes.html)
- [AWS guidance: partitioning](https://docs.aws.amazon.com/whitepapers/latest/scale-performance-best-practices/partitioning.html)
- [AWS guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
