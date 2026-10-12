# Kafka Interview Questions

## Explain Kafka architecture. Where would you use it?

A Kafka cluster stores records in topics, and each topic is split into partitions. Brokers host partition replicas. Producers write records; consumers read them and track progress with offsets. A consumer group divides partitions among its members, so each partition is handled by one member of that group at a time.

Kafka fits durable event streams, audit or activity feeds, and asynchronous integration where consumers benefit from replay. It is not automatically the right choice for a quick one-to-one request that needs an immediate answer.

A key usually determines the partition. Ordering is guaranteed within a partition, not across the whole topic. See the [Kafka documentation](https://kafka.apache.org/documentation/).

## A Kafka consumer processes the same message twice. How do you handle it?

Assume duplicate delivery can happen. Give each event a stable ID and make the consumer's side effect idempotent, for example by recording the event ID with a unique constraint in the same database transaction as the update. Commit the Kafka offset only after durable processing.

Kafka transactions can provide strong guarantees for Kafka-to-Kafka processing, but an external database does not automatically join that transaction. Design the database write and offset strategy around retries and process crashes.

## One partition gets much more traffic than the others. What would you do?

Check the key distribution first. A hot key sends records to one partition and limits parallelism for that key. If the business requires ordering for that key, adding partitions alone will not spread that key's ordered stream.

Possible fixes include choosing a better partition key, splitting a hot entity into subkeys when ordering rules allow it, or separating exceptionally hot traffic. Measure consumer lag and per-partition throughput before and after the change.

## What should happen when a message keeps failing?

Classify the error. Retry transient failures with bounded attempts, backoff, and jitter. Send a poison message to a dead-letter topic with its original key, headers, error details, and attempt count so it can be investigated or replayed after correction.

Do not retry permanent validation errors forever. Alert on dead-letter volume and make replay safe; the consumer may still see a message more than once.

## How do you preserve message ordering for one customer?

Use the customer ID as the record key so that customer's records go to the same partition. Process each partition in order and avoid concurrent work that reorders updates for that key.

This gives per-partition ordering, not global ordering. If the number of partitions or partitioning scheme changes, consider how key mapping and existing records are handled.
