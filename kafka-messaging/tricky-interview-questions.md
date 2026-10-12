# Apache Kafka and Messaging: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Offsets, ordering, and delivery traps

### Q1. Does committing offset N mean record N has definitely been processed?

**Answer:** Offsets identify the next position to consume; correctness depends on when the app commits relative to durable processing.

### Q2. What crash bug follows if a consumer commits before writing to SQL?

**Answer:** A crash after commit but before SQL loses the effect because the record will not replay. Commit after durable processing.

### Q3. What crash bug follows if SQL commits before the Kafka offset?

**Answer:** A crash before offset commit causes redelivery. Deduplicate by stable event ID inside the SQL transaction.

### Q4. Can Kafka guarantee order across all partitions?

**Answer:** No. Ordering is per partition. Use one key/partition for the entity that needs order.

### Q5. Why can adding a consumer fail to speed up a group?

**Answer:** If consumers exceed partition count, some have no partitions; a hot partition also remains serial. Fix partitioning or processing bottleneck.

### Q6. Does producer idempotence prevent the same business event from being consumed twice?

**Answer:** No. It prevents some duplicate log appends from producer retries; application resends and reprocessing still need deduplication.

### Q7. Can auto-commit lose messages when handler work is asynchronous?

**Answer:** Yes. Offsets can advance while work is still running. Commit only contiguous durable completions.

### Q8. Why can a retry topic reorder messages for one key?

**Answer:** Later records may proceed while an earlier failure waits elsewhere. Preserve sequence or pause that key/partition.

### Q9. What does Kafka exactly-once cover with Kafka transactions?

**Answer:** It can atomically publish Kafka outputs and consumed offsets within Kafka. It does not include an external database or HTTP side effect.

### Q10. Why can a consumer process a record twice after a rebalance?

**Answer:** Old work may finish after ownership is revoked and new owner replays the uncommitted offset. Handle duplicates and revoke safely.

## Partitioning, compaction, and schemas

### Q11. Can a compacted topic serve as a complete audit log?

**Answer:** No. Compaction retains the latest value per key and can remove older records. Keep a separate immutable history for audit.

### Q12. What does a tombstone mean in a compacted topic?

**Answer:** A null-valued record marks a key for deletion; it is retained for a configured period before compaction removes history.

### Q13. Why can a new partition count break per-key ordering during rollout?

**Answer:** Key mapping can change, sending records to different partitions. Migrate routing carefully or preserve stable mapping.

### Q14. What is the difference between a partition's log end offset and high watermark?

**Answer:** The log end offset is the next append position on that replica; the high watermark is the replicated boundary visible to consumers. They can differ during replication.

### Q15. Does schema compatibility guarantee two events have the same business meaning?

**Answer:** No. Registry checks structure, not semantics. Review field meaning and consumer behavior separately.

### Q16. Why can an old consumer fail when a new enum value appears?

**Answer:** It may treat the set as closed and throw. Design unknown-value handling and validate consumers before rollout.

### Q17. What can happen if processing exceeds max.poll.interval?

**Answer:** The group may remove the consumer and reassign partitions while work continues. Bound batches and poll safely.

### Q18. Can a dead-letter topic preserve source order automatically?

**Answer:** No. It isolates failed records and may let later records through. Include source metadata and define replay order.

### Q19. Why can consumer lag look acceptable while users see stale results?

**Answer:** Lag counts records, not their age or business processing time. Track oldest-message age and end-to-end freshness.

### Q20. Does increasing replication factor automatically make old records safer?

**Answer:** Replicas must be assigned and caught up; ISR and ack settings matter. Verify partition state and durability policy.

## Operations and producer/consumer edge cases

### Q21. What does acks=all mean if the in-sync replica set has only one broker?

**Answer:** It waits for current ISR, which may be one unless min.insync.replicas rejects writes. Configure both settings.

### Q22. Why can a producer timeout fail to prove that a record was not written?

**Answer:** The broker may have appended it while the acknowledgment was lost. Retry idempotently and use stable event identity.

### Q23. Can a Kafka consumer block inside poll for a long database call?

**Answer:** Yes, but it risks exceeding poll interval and rebalances. Bound work or offload with controlled in-flight tasks and offset tracking.

### Q24. What happens if two live consumers use the same transactional ID?

**Answer:** One producer can fence the other and disrupt writes. Use a stable but unique identity per logical producer.

### Q25. Why does read_committed hide some Kafka records?

**Answer:** It excludes open or aborted transaction records to preserve transactional visibility; a long transaction can delay visibility.

### Q26. Can Kafka retain a message forever by default?

**Answer:** No. Retention or compaction cleans data. Configure replay duration and backups for recovery requirements.

### Q27. Why might a committed Kafka offset be ahead of durable SQL state?

**Answer:** The offset and database commits are independent unless coordinated. Order processing, deduplicate, and reconcile.

### Q28. What gap does a transactional outbox solve?

**Answer:** It records business state and event intent in one database transaction, avoiding an uncoordinated dual write. Relay remains often at-least-once.

### Q29. Why can increasing max.poll.records reduce reliability?

**Answer:** Larger batches may exceed processing time or memory limits. Tune to handler duration and poll interval.

### Q30. What is a safe response to a serialization poison record?

**Answer:** Quarantine payload and metadata, alert, and continue only under explicit ordering policy. Do not silently drop it.

## References

- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Kafka protocol and partitioning](https://kafka.apache.org/40/design/protocol/)
- [Kafka design documentation](https://kafka.apache.org/40/design/design/)
