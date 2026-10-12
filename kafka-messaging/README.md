# Module 12 — Apache Kafka and Messaging

> **Module guide:** Design event-driven flows with Kafka producers, consumers, partitions, delivery semantics, retries, and schema evolution.

## Learning outcomes

- Explain partition ordering and consumer-group scaling.
- Choose an offset and retry strategy that matches delivery requirements.
- Design consumers to tolerate redelivery and schema change.

## Subtopic links

- [Kafka architecture](#kafka-architecture)
- [Producers and consumers](#producers-and-consumers)
- [Reliability and event contracts](#reliability-and-event-contracts)

## Topic coverage

### Kafka architecture

- Brokers, topics, and partitions
- Replication and leader/follower roles
- Message keys, ordering, and partitioning

### Producers and consumers

- Producer acknowledgments and retries
- Consumers, consumer groups, and rebalancing
- Offsets and commit strategies
- Consumer lag and backpressure

### Reliability and event contracts

- At-most-once, at-least-once, and exactly-once semantics
- Idempotent processing and deduplication
- Retries and dead-letter topics
- Kafka transactions
- Schema evolution and compatibility
- Kafka with Spring Boot
- Event-driven architecture

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `kafka-messaging/kafka-architecture/` — kafka architecture question pages
- `kafka-messaging/producers-and-consumers/` — producers and consumers question pages
- `kafka-messaging/reliability-and-event-contracts/` — reliability and event contracts question pages

Use descriptive lowercase kebab-case filenames, for example `kafka-messaging/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [40-question hard-level Q&A for advanced interview practice](hard-interview-questions.md)
- [Kafka interview questions](interview-questions.md)

## Related modules

- [Module 13: Microservices Architecture](../microservices-architecture/README.md)
- [Module 17: Docker, Kubernetes and CI/CD](../docker-kubernetes-cicd/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
- [Module 22: Real-World Backend Projects](../real-world-backend-projects/README.md)
