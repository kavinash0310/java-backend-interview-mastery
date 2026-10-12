# System Design and Architecture: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Requirements and capacity trade-offs

### Q1. How do you turn vague scale statements into a capacity estimate?

**Answer:** Clarify average and peak traffic, payload size, read/write mix, retention, and growth; state assumptions before calculating.

**Example:** Estimate peak requests per second from daily volume and peak factor.

### Q2. How can Little's Law estimate in-flight requests?

**Answer:** Concurrency is approximately arrival rate multiplied by average time in the system.

**Example:** At 200 requests/s and 0.25 s average latency, estimate about 50 in-flight requests.

### Q3. Why estimate peak traffic instead of designing only for average?

**Answer:** Bursts can exhaust queues, pools, or rate limits even when daily average looks small.

**Example:** Use observed peak-to-average ratios and growth margin.

### Q4. How do RTO and RPO shape an architecture?

**Answer:** RTO constrains recovery time; RPO constrains tolerated data loss, affecting replication and backup choices.

**Example:** A near-zero RPO may require synchronous replication with availability trade-offs.

### Q5. What is a consistency requirement you should clarify in a design interview?

**Answer:** Ask which reads must reflect a recent write, for whom, and how quickly stale results are acceptable.

**Example:** A user may require immediate visibility of their own update.

### Q6. How do you distinguish availability from reliability?

**Answer:** Availability is readiness to serve at a moment or over a window; reliability includes consistent correct behavior over time.

**Example:** A fast but incorrect response is not reliable.

### Q7. What is a failure-isolation boundary?

**Answer:** A boundary limits how far a component's failure can spread by separating resources, queues, or dependencies.

**Example:** Use a separate worker pool for optional report generation.

### Q8. How should a capacity estimate state uncertainty?

**Answer:** Give ranges, expose assumptions, and identify which measurement would reduce the largest uncertainty.

**Example:** Label estimated payload size and peak factor explicitly.

### Q9. What can dominate storage estimates beyond raw payload bytes?

**Answer:** Indexes, replicas, metadata, logs, backups, and growth all add overhead.

**Example:** Apply separate multipliers for replicas and indexes.

### Q10. How does a read/write ratio affect datastore choice?

**Answer:** Read-heavy and write-heavy patterns stress different resources and may justify replicas, caching, or partitioning.

**Example:** Measure actual query patterns before selecting a database.

## Data, partitioning, and consistency

### Q11. How should a partition key be chosen?

**Answer:** It should distribute load, align with common access patterns, and preserve any required locality or ordering.

**Example:** A tenant key is convenient but can create a hot large tenant.

### Q12. What is a hot shard and how can it be mitigated?

**Answer:** One shard receives disproportionate traffic or data; split hot keys, add caching, or change partitioning when semantics allow.

**Example:** Monitor per-shard traffic rather than cluster average.

### Q13. What does replication lag mean for an API response?

**Answer:** A read replica may not yet include a committed write, so a follow-up read can show stale data.

**Example:** Route session-critical reads to the primary or use a version token.

### Q14. When can a cache create a correctness problem?

**Answer:** When a business operation assumes cached values are current or two actors update stale copies.

**Example:** Use version checks or read authoritative data for critical decisions.

### Q15. What does quorum read/write policy trade off?

**Answer:** It balances consistency, availability, and latency by requiring responses from a number of replicas.

**Example:** Define behavior under one replica failure.

### Q16. How can event sourcing help and hurt a design?

**Answer:** It preserves an event history and supports rebuilds, but adds schema evolution, projection, and operational complexity.

**Example:** Adopt it when history is a core requirement, not just for audit logging.

### Q17. What is CQRS at system scale?

**Answer:** It separates write handling from read models, often with asynchronous projections and eventual consistency.

**Example:** Tell consumers how stale the read model may be.

### Q18. Why should idempotency be implemented at the durable boundary?

**Answer:** In-memory duplicate checks disappear on restart or across instances; a database constraint or durable record survives races.

**Example:** Uniquely store `(client_id, idempotency_key)`.

### Q19. How does the transactional outbox fit a system design?

**Answer:** It closes the database/event dual-write gap by storing the event with business state and relaying it later.

**Example:** Design for duplicate relay delivery.

### Q20. When is database sharding premature?

**Answer:** When one well-indexed database with adequate vertical or read scaling still meets load and operational needs.

**Example:** Sharding adds routing, rebalancing, and cross-shard query complexity.

## Resilience and deployment choices

### Q21. How should the API deadline relate to downstream service deadlines?

**Answer:** Pass the remaining request budget so downstream work cannot outlive the caller's useful window.

**Example:** A gateway budget of 1 second leaves less than 1 second for each dependency.

### Q22. Why is an unbounded message queue not unlimited capacity?

**Answer:** It converts overload into growing delay and storage pressure rather than solving the rate mismatch.

**Example:** Monitor oldest-item age and define a backlog limit.

### Q23. What is a load-shedding priority policy?

**Answer:** It rejects or delays lower-value work first so essential operations retain capacity.

**Example:** Keep payments ahead of background analytics.

### Q24. How can bulkheads be applied in a system design?

**Answer:** Separate pools, queues, or capacity budgets so one dependency or workload cannot consume all resources.

**Example:** Use distinct worker pools for critical and optional jobs.

### Q25. What is a failure domain?

**Answer:** A component or set of components that may fail together due to shared infrastructure or dependency.

**Example:** Two instances in one Availability Zone share a failure domain.

### Q26. Why can active-active multi-region be harder than active-passive?

**Answer:** It must resolve concurrent writes, route traffic, and handle data replication conflicts across regions.

**Example:** Use it only when latency or recovery requirements justify the complexity.

### Q27. What does a circuit breaker need besides thresholds?

**Answer:** A clear fallback or failure response, bounded half-open probes, and metrics so operators understand its state.

**Example:** Do not return stale data without an explicit freshness policy.

### Q28. How do you roll out a schema change without downtime?

**Answer:** Expand schema, deploy compatible readers/writers, backfill, switch behavior, then contract only after old versions are gone.

**Example:** Keep rollback-compatible fields during the rollout.

### Q29. When is a CDN useful for backend systems?

**Answer:** When content can be cached near users or static assets dominate transfer time.

**Example:** Use cache headers and invalidation rules for mutable content.

### Q30. How should feature flags be managed after rollout?

**Answer:** Give each flag an owner, purpose, safe default, and removal date to prevent permanent branching complexity.

**Example:** Remove the flag once rollout risk has passed.

## Design exercises and quality attributes

### Q31. How would you prevent overselling limited inventory?

**Answer:** Use an atomic reservation or conditional decrement with a durable reservation ID and expiry/compensation policy.

**Example:** Update stock only when `available >= requested`.

### Q32. How would you design a webhook delivery service?

**Answer:** Persist delivery attempts, sign payloads, retry with backoff, isolate failing endpoints, and expose replay/status controls.

**Example:** Use an idempotent event ID and per-destination rate limit.

### Q33. How would you design delayed notifications?

**Answer:** Store scheduled work durably, dispatch when due, and make sending idempotent with retry/dead-letter handling.

**Example:** Use a queue with scheduled delivery or a scheduler plus durable table.

### Q34. How would you design a background job scheduler across instances?

**Answer:** Store schedules and leases durably, claim due jobs atomically, and tolerate duplicate execution.

**Example:** Use a unique run key and lease expiration.

### Q35. How would you design audit events for compliance?

**Answer:** Append immutable actor/action/time records, restrict mutation, and define retention, search, and integrity controls.

**Example:** Include correlation ID and before/after fields only as permitted.

### Q36. How would you design a scheduled export service?

**Answer:** Accept an export request, enqueue durable work, generate a file asynchronously, store it with expiry, and notify the user with an authorized download link.

**Example:** Keep status and retry state in a durable job record.

### Q37. How would you design tenant isolation?

**Answer:** Choose explicit data partition and authorization boundaries, then test cross-tenant access as a security invariant.

**Example:** Include tenant ID in queries and unique constraints where appropriate.

### Q38. How would you keep a notification system from overwhelming users?

**Answer:** Apply preferences, deduplication, batching, rate limits, and channel-specific retries.

**Example:** Group low-priority events into a digest.

### Q39. What trade-offs should you explain when choosing a database?

**Answer:** Compare consistency, query patterns, transaction needs, scaling, operations, and team familiarity.

**Example:** Tie the choice to a workload rather than trend.

### Q40. How do you present a system design answer clearly?

**Answer:** State requirements, sketch data flow, identify bottlenecks, then discuss failure handling and trade-offs.

**Example:** Call out one assumption you would verify with real traffic.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [PostgreSQL indexes](https://www.postgresql.org/docs/current/indexes.html)
- [AWS guidance: partitioning](https://docs.aws.amazon.com/whitepapers/latest/scale-performance-best-practices/partitioning.html)
- [AWS guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
