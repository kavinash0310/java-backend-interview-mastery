# Apache Kafka and Messaging: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Partitions, ordering, and throughput

### Q1. What determines ordering in Kafka?

**Answer:** Order is guaranteed within a partition; records are ordered by append position in that partition, not across partitions.

**Example:** Use the same key for records that require per-entity order.

### Q2. What happens when consumer count exceeds partition count?

**Answer:** Some consumers in the group receive no partition and remain idle.

**Example:** Scale partitions if parallelism and key distribution permit it.

### Q3. Why can increasing partition count change key-based behavior?

**Answer:** The partitioner may map existing keys differently after the partition count changes, affecting ordering across the old and new layout.

**Example:** Treat partition expansion as a data-ordering decision.

### Q4. How can a poor message key create a hot partition?

**Answer:** A highly skewed or constant key routes most records to one partition.

**Example:** Use a key with enough distribution while preserving required ordering.

### Q5. What is the trade-off in choosing more partitions?

**Answer:** More partitions raise potential parallelism but increase broker metadata, file, replication, and operational overhead.

**Example:** Set count from throughput and consumer needs, not an arbitrarily large number.

### Q6. How can producer batching improve throughput?

**Answer:** The producer buffers records into batches per partition, reducing request overhead and improving compression.

**Example:** Tune `batch.size` and `linger.ms` against latency needs.

### Q7. Why can compression help Kafka throughput?

**Answer:** Smaller batches reduce network and disk bytes, often at additional CPU cost.

**Example:** Benchmark compression type with representative payloads.

### Q8. What does a Kafka partition leader do?

**Answer:** It handles reads and writes for that partition; replicas follow its log and may become leader after failure.

**Example:** Monitor under-replicated partitions and leader changes.

### Q9. What do `min.insync.replicas` and `acks=all` combine to protect?

**Answer:** They require acknowledgments from the in-sync replica set and can reject writes when too few replicas remain.

**Example:** The producer must handle the resulting availability failure.

### Q10. What is an unclean leader election trade-off?

**Answer:** Allowing an out-of-sync replica to lead can restore availability faster but may lose records it had not replicated.

**Example:** Choose based on durability requirements.

## Producer delivery and transactions

### Q11. What does producer idempotence prevent?

**Answer:** It prevents duplicate records caused by producer retries within Kafka's supported producer session semantics.

**Example:** Enable compatible settings and still handle business-level duplicate events.

### Q12. What does a Kafka transaction cover?

**Answer:** It can atomically write records to Kafka partitions and commit consumed offsets for Kafka-to-Kafka processing.

**Example:** It does not automatically include a relational database transaction.

### Q13. What is `read_committed` isolation for a consumer?

**Answer:** It hides records from aborted transactions and reads only committed transactional output.

**Example:** Use it when consuming transactional producer records.

### Q14. Why does `acks=all` not mean every configured replica acknowledged?

**Answer:** It waits for all current in-sync replicas; the ISR size can be smaller than the replication factor.

**Example:** Combine with `min.insync.replicas` to require a minimum.

### Q15. How can an outbox pattern bridge a database transaction and Kafka publish?

**Answer:** Write business data and an outbox event in one database transaction, then relay the event to Kafka.

**Example:** Consumers should remain idempotent because relays can publish again.

### Q16. Why can a producer timeout leave the send result ambiguous?

**Answer:** The broker may have stored the record but the acknowledgment was lost, so a retry can duplicate without idempotence.

**Example:** Use idempotent production and downstream deduplication for business effects.

### Q17. How does acks=0 differ from acks=1?

**Answer:** With acks=0 the producer does not wait for broker acknowledgment; with acks=1 it waits for the leader's acknowledgment.

**Example:** Choose based on loss tolerance and latency.

### Q18. What should a producer do when the broker rejects a record as too large?

**Answer:** Treat it as a permanent configuration or payload issue, not an endless transient retry.

**Example:** Validate size limits and reduce or externalize large payloads.

### Q19. How can schema compatibility protect rolling deployments?

**Answer:** It ensures old and new producers/consumers can coexist under a declared compatibility policy.

**Example:** Add optional fields before making consumers require them.

### Q20. Why are Kafka message schemas not a substitute for validation?

**Answer:** A structurally valid message can still violate business invariants or contain values the service cannot process.

**Example:** Validate domain rules at the consumer boundary.

## Consumers, offsets, and rebalances

### Q21. When should a consumer commit offsets manually?

**Answer:** After the side effect is durably complete, if at-least-once processing is required.

**Example:** Commit after database transaction success.

### Q22. What duplicate window exists when committing after processing?

**Answer:** If the consumer crashes after the side effect but before offset commit, the record is delivered again.

**Example:** Make the side effect idempotent or store a processed-event ID.

### Q23. What loss window exists when committing before processing?

**Answer:** A crash after commit but before side effect can skip the record permanently.

**Example:** Do not commit early when processing must not be lost.

### Q24. What does `max.poll.interval.ms` protect?

**Answer:** It bounds how long a consumer may go without polling before the group considers it unresponsive and rebalances.

**Example:** Keep processing bounded or move long work to another controlled executor.

### Q25. How can slow processing trigger a rebalance loop?

**Answer:** The consumer may exceed its poll interval, lose ownership, then repeat work after reassignment.

**Example:** Tune processing duration and polling strategy from measured records.

### Q26. Why should a rebalance listener finish quickly?

**Answer:** Blocking partition assignment or revocation delays the whole group and can cause further churn.

**Example:** Persist needed state and release resources promptly.

### Q27. How can a consumer preserve order while retrying one failed record?

**Answer:** Pause the partition or route work through an ordered retry mechanism so later records do not overtake it.

**Example:** The choice may reduce throughput for that partition.

### Q28. What should happen to a poison record?

**Answer:** Use bounded retries, record diagnostics, and route it to a dead-letter path with enough metadata for safe replay.

**Example:** Do not block a partition forever without an operational plan.

### Q29. How should dead-letter records be replayed safely?

**Answer:** Fix the cause, preserve original key and headers, deduplicate business effects, and replay at a controlled rate.

**Example:** Record the original topic, partition, and offset.

### Q30. Why can a consumer group have high lag even when average CPU is low?

**Answer:** A hot partition, blocked I/O, downstream throttling, or uneven key distribution can bottleneck one worker.

**Example:** Inspect lag per partition and consumer assignment.

## Operations and architecture

### Q31. What does log compaction retain?

**Answer:** It keeps the latest value per key over time, while tombstones mark keys for deletion subject to cleanup.

**Example:** Do not expect it to preserve every historical event.

### Q32. What are Kafka tombstones used for in compacted topics?

**Answer:** A null-valued record marks a key for deletion during compaction after the configured retention delay.

**Example:** Consumers need to interpret the tombstone as a delete.

### Q33. Why can retention delete records a slow consumer still needs?

**Answer:** Kafka retention is based on time or size, not whether each consumer has processed the data.

**Example:** Monitor lag against retention and provide a recovery plan.

### Q34. What is the difference between consumer lag and message loss?

**Answer:** Lag measures how far a consumer trails the log end; it does not by itself imply records are lost.

**Example:** Check offsets, retention, and processing results.

### Q35. How can rack awareness improve Kafka resilience?

**Answer:** Replica placement across racks or failure zones reduces correlated loss from one infrastructure failure.

**Example:** Verify the broker's rack metadata and replica placement.

### Q36. Why monitor under-replicated partitions?

**Answer:** Replicas not caught up reduce fault tolerance and can make leader failure more damaging.

**Example:** Alert on sustained under-replication.

### Q37. When would Kafka be a poor fit for a synchronous request?

**Answer:** When the caller needs an immediate strongly consistent response or the operational overhead exceeds the decoupling value.

**Example:** A simple local database transaction may be more appropriate.

### Q38. How should an event represent a schema evolution?

**Answer:** Use explicit event type/version, compatible optional additions, and a migration window for consumers.

**Example:** Keep semantic meaning stable across versions.

### Q39. Why include event ID and occurred-at time?

**Answer:** They support deduplication, tracing, and understanding event order or age.

**Example:** Use a stable unique ID generated with the event.

### Q40. How can a Kafka consumer scale safely with an external database?

**Answer:** Bound concurrency by partition ownership and database capacity, use idempotent writes, and monitor lag and connection waits.

**Example:** More consumers than partitions will not increase partition parallelism.

## References

- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Kafka protocol and partitioning](https://kafka.apache.org/40/design/protocol/)
- [Kafka design documentation](https://kafka.apache.org/40/design/design/)
