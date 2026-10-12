# Apache Kafka and Messaging: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is a Kafka broker?

**Answer:** A Kafka server that stores partitions and serves produce and fetch requests. A cluster normally has multiple brokers.

### Q2. What is a Kafka topic?

**Answer:** A named stream of records, split into partitions so data can be stored and processed in parallel.

### Q3. What is a Kafka record?

**Answer:** A message containing a key, value, timestamp, and optional headers. The key can influence partition choice.

### Q4. What does a Kafka producer do?

**Answer:** It publishes records to topic partitions. It can choose a partition directly or use the record key.

### Q5. What does a Kafka consumer do?

**Answer:** It reads records from assigned partitions and tracks progress with offsets. The app defines what counts as processed.

### Q6. What is a Kafka consumer group?

**Answer:** Consumers cooperate in a group to process a topic. Within a group, one partition is assigned to at most one consumer at a time.

### Q7. What is a Kafka offset?

**Answer:** A record's position within a partition. Consumers use offsets to resume after restart or rebalance.

### Q8. What does committing an offset mean?

**Answer:** It records a group's progress. Commit after the intended work is durable enough that restart will not lose it.

### Q9. Auto commit vs manual commit?

**Answer:** Auto commit records offsets periodically; manual commit lets the app choose when processing is complete, making delivery behavior easier to reason about.

### Q10. What is a consumer rebalance?

**Answer:** A change in partition ownership when a consumer joins, leaves, or membership changes. Processing should tolerate a pause and reassignment.

### Q11. What is consumer lag?

**Answer:** The gap between the latest partition offset and a group's progress. Growing lag can signal insufficient processing capacity or a slow dependency.

### Q12. What is a partition leader?

**Answer:** The replica that handles reads and writes for a partition. Other replicas can follow and take over if the leader fails.

### Q13. What is replication factor?

**Answer:** The number of replicas kept per partition. More replicas can improve availability but consume storage and network capacity.

### Q14. What does ISR mean?

**Answer:** The in-sync replica set contains replicas sufficiently caught up with the leader. Acks settings can use this set to control durability.

### Q15. What does acks=all mean?

**Answer:** The producer waits for acknowledgments from the leader and current in-sync replicas, subject to topic and broker settings. This may increase latency.

### Q16. What is producer idempotence?

**Answer:** It prevents duplicates from producer retries within supported producer semantics. It does not make arbitrary database side effects exactly once.

### Q17. What does at-least-once delivery mean?

**Answer:** A record is delivered one or more times, so duplicates are possible. Make side effects idempotent or deduplicate.

### Q18. What does at-most-once delivery mean?

**Answer:** A record is processed zero or one time, so failure can lose work. It may suit data where occasional loss is acceptable.

### Q19. What is a dead-letter topic?

**Answer:** A separate topic for records that failed the retry policy. Include metadata to investigate and replay safely.

### Q20. Why not retry a bad message forever?

**Answer:** A permanently invalid record can block progress and consume capacity. Use bounded retries, then route it for investigation.

### Q21. What is log compaction?

**Answer:** It retains the latest record for each key under configured conditions rather than every old value, useful for changelog-like topics.

### Q22. What is retention in Kafka?

**Answer:** It determines how long or how much data a topic keeps before old segments can be removed. Match it to replay and storage needs.

### Q23. What is a tombstone record?

**Answer:** A keyed record with a null value can mark deletion in a compacted topic. Consumers interpret it according to the event contract.

### Q24. What is schema evolution?

**Answer:** Changing a message structure while producers and consumers may run different versions. Compatibility rules help old and new code coexist.

### Q25. What is a schema registry used for?

**Answer:** It stores or serves message schemas and may enforce compatibility. A schema does not replace validation of business meaning.

### Q26. What is a Kafka partition?

**Answer:** An ordered append-only slice of a topic that can be stored and consumed independently.

**Example:** Records with the same key are normally routed to the same partition.

### Q27. What is a Kafka message key?

**Answer:** A record value used by the partitioner, often to keep related records ordered together.

**Example:** Use customerId to preserve a customer's event order.

### Q28. Is Kafka ordering global across a topic?

**Answer:** No. Ordering is guaranteed within a partition, not across all partitions.

**Example:** Choose one partition for records that require a shared order.

### Q29. What is a Kafka partition assignment strategy?

**Answer:** A rule for distributing a consumer group's topic partitions among its members.

**Example:** A sticky strategy can reduce unnecessary partition movement.

### Q30. How do you respond when consumer lag keeps growing?

**Answer:** Check per-partition traffic and processing time, then fix slow work or add consumers up to the partition count.

**Example:** Measure before increasing concurrency.

### Q31. What can cause frequent consumer rebalances?

**Answer:** Unstable membership, slow polling, long processing, or session timeouts can repeatedly trigger reassignment.

**Example:** Keep polling within configured limits and inspect consumer logs.

### Q32. When is log compaction useful?

**Answer:** When consumers need the latest retained value for each key rather than every historical update.

**Example:** A compacted topic can hold current account state.

### Q33. How can a Kafka topic become a hot partition?

**Answer:** A few heavily used keys can route most records to one partition and skew traffic.

**Example:** Review key distribution before adding partitions.

### Q34. What is idempotent message processing?

**Answer:** Processing the same record more than once has the same business effect as processing it once.

**Example:** Store an event ID with a unique constraint.

### Q35. What is Kafka's exactly-once processing goal?

**Answer:** It coordinates certain Kafka reads, writes, and offsets to avoid duplicates within supported Kafka workflows.

**Example:** External database side effects still need their own transaction strategy.

### Q36. What is a Kafka producer acknowledgment?

**Answer:** A response indicating whether the broker accepted a record under the configured acknowledgment policy.

**Example:** `acks=all` waits for the in-sync replicas.

### Q37. What is a poison-pill message?

**Answer:** A record that repeatedly causes a consumer to fail during processing.

**Example:** After bounded retries, route it for investigation.

### Q38. How can you delay a retry for a Kafka record?

**Answer:** Publish it to a delay or retry mechanism with its attempt count and original metadata, then process it later.

**Example:** Bound attempts and route exhausted records for review.

### Q39. What is the consumer group coordinator?

**Answer:** The broker that manages group membership and partition assignment coordination.

**Example:** Consumers interact with it during group changes.

### Q40. Why should Kafka events be versioned carefully?

**Answer:** Producers and consumers can be deployed at different times, so old and new schemas must interoperate.

**Example:** Add optional fields with compatible defaults.

## References

- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Kafka protocol and partitioning](https://kafka.apache.org/40/design/protocol/)
- [Kafka design documentation](https://kafka.apache.org/40/design/design/)
