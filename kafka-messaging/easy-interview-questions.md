# Apache Kafka and Messaging: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q06. What is a Kafka broker?

**Answer:** A Kafka server that stores partitions and serves produce and fetch requests. A cluster normally has multiple brokers.

### Q07. What is a Kafka topic?

**Answer:** A named stream of records, split into partitions so data can be stored and processed in parallel.

### Q08. What is a Kafka record?

**Answer:** A message containing a key, value, timestamp, and optional headers. The key can influence partition choice.

### Q09. What does a Kafka producer do?

**Answer:** It publishes records to topic partitions. It can choose a partition directly or use the record key.

### Q10. What does a Kafka consumer do?

**Answer:** It reads records from assigned partitions and tracks progress with offsets. The app defines what counts as processed.

### Q11. What is a Kafka consumer group?

**Answer:** Consumers cooperate in a group to process a topic. Within a group, one partition is assigned to at most one consumer at a time.

### Q12. What is a Kafka offset?

**Answer:** A record's position within a partition. Consumers use offsets to resume after restart or rebalance.

### Q13. What does committing an offset mean?

**Answer:** It records a group's progress. Commit after the intended work is durable enough that restart will not lose it.

### Q14. Auto commit vs manual commit?

**Answer:** Auto commit records offsets periodically; manual commit lets the app choose when processing is complete, making delivery behavior easier to reason about.

### Q15. What is a consumer rebalance?

**Answer:** A change in partition ownership when a consumer joins, leaves, or membership changes. Processing should tolerate a pause and reassignment.

### Q16. What is consumer lag?

**Answer:** The gap between the latest partition offset and a group's progress. Growing lag can signal insufficient processing capacity or a slow dependency.

### Q17. What is a partition leader?

**Answer:** The replica that handles reads and writes for a partition. Other replicas can follow and take over if the leader fails.

### Q18. What is replication factor?

**Answer:** The number of replicas kept per partition. More replicas can improve availability but consume storage and network capacity.

### Q19. What does ISR mean?

**Answer:** The in-sync replica set contains replicas sufficiently caught up with the leader. Acks settings can use this set to control durability.

### Q20. What does acks=all mean?

**Answer:** The producer waits for acknowledgments from the leader and current in-sync replicas, subject to topic and broker settings. This may increase latency.

### Q21. What is producer idempotence?

**Answer:** It prevents duplicates from producer retries within supported producer semantics. It does not make arbitrary database side effects exactly once.

### Q22. What does at-least-once delivery mean?

**Answer:** A record is delivered one or more times, so duplicates are possible. Make side effects idempotent or deduplicate.

### Q23. What does at-most-once delivery mean?

**Answer:** A record is processed zero or one time, so failure can lose work. It may suit data where occasional loss is acceptable.

### Q24. What is a dead-letter topic?

**Answer:** A separate topic for records that failed the retry policy. Include metadata to investigate and replay safely.

### Q25. Why not retry a bad message forever?

**Answer:** A permanently invalid record can block progress and consume capacity. Use bounded retries, then route it for investigation.

### Q26. What is log compaction?

**Answer:** It retains the latest record for each key under configured conditions rather than every old value, useful for changelog-like topics.

### Q27. What is retention in Kafka?

**Answer:** It determines how long or how much data a topic keeps before old segments can be removed. Match it to replay and storage needs.

### Q28. What is a tombstone record?

**Answer:** A keyed record with a null value can mark deletion in a compacted topic. Consumers interpret it according to the event contract.

### Q29. What is schema evolution?

**Answer:** Changing a message structure while producers and consumers may run different versions. Compatibility rules help old and new code coexist.

### Q30. What is a schema registry used for?

**Answer:** It stores or serves message schemas and may enforce compatibility. A schema does not replace validation of business meaning.

## References

- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Kafka protocol and partitioning](https://kafka.apache.org/40/design/protocol/)
- [Kafka design documentation](https://kafka.apache.org/40/design/design/)
