# Apache Kafka and Messaging: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Partitions, ordering, and replication

### Q1. How do you preserve per-customer ordering while scaling Kafka consumers?

**Answer:** Use customer ID as the key so all that customer's records share a partition, then keep processing ordered within that partition.

### Q2. What failure appears when one partition has much more traffic than others?

**Answer:** That partition becomes a hot shard; adding consumers cannot parallelize one partition's ordered stream. Revisit key distribution or split the workload semantics.

### Q3. Why can increasing partition count change key-based ordering assumptions?

**Answer:** Partition mapping may move keys to different partitions, so old and new records can be processed concurrently. Plan migration or preserve a stable routing scheme.

### Q4. What does the in-sync replica set protect, and what does it not guarantee?

**Answer:** It tracks replicas caught up enough for leadership under configured rules. It does not provide zero data loss under every acknowledgment and failure configuration.

### Q5. Why is unclean leader election a durability trade-off?

**Answer:** It may restore availability using a stale replica that lacks acknowledged records. Keep it disabled when losing acknowledged data is unacceptable.

### Q6. How do min.insync.replicas and acks=all work together?

**Answer:** acks=all waits for the current ISR, while min.insync.replicas rejects writes when too few replicas remain. Configure both to match durability goals.

### Q7. What is the effect of rack-aware replica assignment?

**Answer:** It spreads replicas across failure domains to reduce correlated loss. It cannot help if all racks share a larger regional failure.

### Q8. Why can a consumer group stop making progress after a rebalance?

**Answer:** Rebalances pause or revoke partition ownership; slow handlers or excessive membership churn prolong interruption. Tune timeouts and finish work before revocation.

### Q9. How does cooperative rebalancing reduce disruption?

**Answer:** It incrementally transfers only partitions that must move rather than revoking all ownership at once. Consumers still need correct revoke/assign handling.

### Q10. When should you use static group membership?

**Answer:** It can reduce rebalances during short restarts by preserving member identity, but stale members can delay recovery. Set session behavior and unique IDs carefully.

## Producer semantics and transactions

### Q11. What does producer idempotence prevent, and what duplicates can still reach your business logic?

**Answer:** It prevents duplicate log appends from producer retries within its producer identity and session. Application re-sends or consumer reprocessing can still duplicate effects.

### Q12. Why does Kafka exactly-once processing not automatically cover a SQL database write?

**Answer:** Kafka transactions atomically cover Kafka records and offsets, not an external database transaction. Use an outbox, idempotent sink, or coordinated design.

### Q13. How do Kafka transactions atomically publish output and advance input offsets?

**Answer:** Use a transactional producer to write output and send consumed offsets to the same transaction. Consumers must read committed records.

### Q14. What is the difference between acks=1 and acks=all under leader failure?

**Answer:** acks=1 confirms the leader's append; a leader can fail before replicas receive it. acks=all waits for the ISR subject to min-ISR configuration.

### Q15. Why can a producer timeout leave the application unsure whether a record was written?

**Answer:** The broker may have committed while the acknowledgment was lost. Idempotence reduces retry duplicates, but business workflows still need stable identifiers.

### Q16. How should a producer handle serialization failure differently from broker unavailability?

**Answer:** Serialization errors are usually deterministic bad input and should be quarantined or rejected; broker failures may be retried within a deadline.

### Q17. What is the risk of sharing a KafkaProducer across threads?

**Answer:** The standard producer is thread-safe, but creating many producers wastes buffers and connections. Reuse a managed producer and close it at shutdown.

### Q18. Why should a transactional ID be stable per logical producer instance but unique among live instances?

**Answer:** Stable identity enables fencing of zombie producers across restarts; duplicate live IDs can fence one another. Derive IDs from stable shard identity.

### Q19. How can schema compatibility prevent consumers from breaking during rollout?

**Answer:** Use compatible evolution rules, default or optional fields, and a producer/consumer rollout order. Validate schemas in CI and retain old-reader coverage.

### Q20. When is log compaction appropriate, and what does it not preserve?

**Answer:** It retains the latest value per key plus tombstones subject to cleanup, not every historical event. Use a separate event log when full history matters.

## Consumer processing and retries

### Q21. Why should offsets commit only after the business side effect is durable?

**Answer:** Committing first risks losing work after a crash; processing first risks duplicates if the commit fails. Choose at-least-once with idempotent effects or Kafka transactions.

### Q22. What happens if max.poll.interval.ms expires during a long handler?

**Answer:** The group may remove the consumer and reassign partitions while its work continues, allowing duplicate processing. Bound per-poll work or pause and poll safely.

### Q23. How do you preserve ordering when sending failed Kafka messages to a retry topic?

**Answer:** Retry topics can let later records overtake the failed one. Partition consistently and choose blocking retry, sequence checks, or per-key pause according to ordering needs.

### Q24. What should a dead-letter record include for useful replay?

**Answer:** Original topic, partition, offset, key, headers, failure type, attempts, timestamp, and schema/version context. Redact secrets and define replay ownership.

### Q25. Why is infinite retry unsafe for a poison message?

**Answer:** It blocks partition progress and consumes resources without changing the input. Bound attempts, isolate the record, and alert with a repair path.

### Q26. How can a consumer group scale only up to its partition count?

**Answer:** A partition is assigned to one group member at a time, so extra members sit idle. Increase partitions only with a key-ordering and migration plan.

### Q27. What is the consequence of committing the highest offset when records finish out of order?

**Answer:** It can skip unfinished lower offsets after a crash. Track contiguous completed offsets or process each partition serially.

### Q28. How should a consumer handle a schema it does not recognize?

**Answer:** Fail safely or quarantine with metadata; do not interpret unknown fields as trusted business meaning. Maintain compatibility and observable version handling.

### Q29. Why can auto-commit lose records during asynchronous processing?

**Answer:** Offsets may advance before handlers finish. Disable or control commits and persist progress only after durable processing.

### Q30. How do pause/resume APIs help control consumer backpressure?

**Answer:** Pause assigned partitions while downstream capacity is exhausted, continue polling for group liveness, then resume. Bound in-flight work per partition.

## Operations and message design

### Q31. How do you distinguish consumer lag caused by slow processing from insufficient partitions?

**Answer:** Compare per-partition lag, processing latency, CPU, downstream waits, and traffic skew. More partitions help parallelism only if work and keys distribute.

### Q32. What is the operational risk of a very large Kafka message?

**Answer:** It consumes broker, network, and consumer memory, raises latency, and complicates replication. Prefer references to large objects and enforce size limits.

### Q33. How can compacted-topic tombstones disappear before a slow consumer sees them?

**Answer:** Retention and compaction settings may remove tombstones after a configured window. Set cleanup policy and consumer recovery expectations together.

### Q34. Why should message keys be chosen as part of the domain model?

**Answer:** Keys determine partition locality, ordering, and compaction identity. A poor key causes hot partitions or merges unrelated entity histories.

### Q35. What is the trade-off between retrying in place and using a retry topic?

**Answer:** In-place retry preserves order but blocks the partition; retry topics isolate delay but can reorder events. Select based on business ordering constraints.

### Q36. How would you roll out a new topic partition count safely?

**Answer:** Verify key-ordering semantics, consumer assignment behavior, capacity, and retention; stage changes and monitor skew and rebalances.

### Q37. What should a Kafka retention policy be based on?

**Answer:** Recovery and replay requirements, storage budget, legal retention, and consumer outage duration. Retention is not a substitute for a backup policy.

### Q38. How does MirrorMaker-style replication affect ordering and offset meaning?

**Answer:** Replication can introduce lag and translate offsets, so source and target offsets are not necessarily identical. Define failover position and duplicate handling.

### Q39. Why is a schema registry not enough to guarantee a safe event contract?

**Answer:** It checks schema compatibility, not business semantics, field meaning, or ordering. Review semantic changes and consumer behavior too.

### Q40. How do you prove an event pipeline is not silently losing records?

**Answer:** Track produced, consumed, failed, and committed counts by event identity; reconcile durable source and sink totals. Alert on gaps, not only broker health.


## References

- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Kafka protocol and partitioning](https://kafka.apache.org/40/design/protocol/)
- [Kafka design documentation](https://kafka.apache.org/40/design/design/)
