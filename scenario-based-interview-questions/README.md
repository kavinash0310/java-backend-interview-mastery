# Module 21 — Scenario-Based Interview Questions

> **Module guide:** Practice diagnosing realistic backend incidents and explaining a structured path from symptoms to mitigation and prevention.

## Learning outcomes

- Ask for impact, scope, and evidence before proposing a fix.
- Separate immediate mitigation from root-cause correction.
- Explain how monitoring and prevention reduce recurrence.

## Subtopic links

- [Latency and capacity incidents](#latency-and-capacity-incidents)
- [Data and dependency failures](#data-and-dependency-failures)
- [Resilience and incident response](#resilience-and-incident-response)

## Topic coverage

### Latency and capacity incidents

- Slow REST APIs
- High database load
- High traffic and capacity limits
- CPU saturation and memory leaks

### Data and dependency failures

- Redis failures
- Kafka duplicate messages
- Kafka consumer lag
- Distributed transaction failures
- Concurrent updates and lost writes

### Resilience and incident response

- Service outages
- Retry storms and cascading failures
- Data consistency problems
- Production incident analysis
- Mitigation, root cause, and follow-up actions

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `scenario-based-interview-questions/latency-and-capacity-incidents/` — latency and capacity incidents question pages
- `scenario-based-interview-questions/data-and-dependency-failures/` — data and dependency failures question pages
- `scenario-based-interview-questions/resilience-and-incident-response/` — resilience and incident response question pages

Use descriptive lowercase kebab-case filenames, for example `scenario-based-interview-questions/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [Beginner-friendly Q&A for quick revision](production-scenarios.md)
- [Production scenario practice](production-scenarios.md)

## Related modules

- [Module 02: Multithreading and Concurrency](../multithreading-concurrency/README.md)
- [Module 03: JVM and Memory Management](../jvm-memory-management/README.md)
- [Module 09: SQL and Relational Databases](../sql-relational-databases/README.md)
- [Module 11: Redis and Caching](../redis-caching/README.md)
- [Module 12: Apache Kafka and Messaging](../kafka-messaging/README.md)
- [Module 19: Logging, Monitoring and Observability](../logging-monitoring-observability/README.md)
- [Module 20: System Design and Architecture](../system-design-architecture/README.md)
