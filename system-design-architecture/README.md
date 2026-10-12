# Module 20 — System Design and Architecture

> **Module guide:** Translate product requirements into backend architectures that meet scale, reliability, consistency, and operational needs.

## Learning outcomes

- Clarify requirements and estimate major capacity needs.
- Draw a request and data flow across system components.
- Explain trade-offs and failure modes instead of naming technologies alone.

## Subtopic links

- [Requirements and estimation](#requirements-and-estimation)
- [Scalable building blocks](#scalable-building-blocks)
- [Trade-offs and design exercises](#trade-offs-and-design-exercises)
- [HLD interview walkthrough](#hld-interview-walkthrough)

## Topic coverage

### Requirements and estimation

- Functional and non-functional requirements
- Capacity estimation
- Workload and traffic modeling

### Scalable building blocks

- Load balancing and rate limiting
- Caching
- Database scaling
- Replication and partitioning
- Message queues

### Trade-offs and design exercises

- Availability and failure handling
- Consistency trade-offs
- High-level design
- Low-level design
- Architecture review and bottleneck analysis


## HLD interview walkthrough

Work through the design in this order:

1. Clarify traffic, read/write mix, latency goals, availability, and consistency needs.
2. Define the API, data ownership, schema, and main synchronous request path.
3. Estimate capacity and identify the likely bottlenecks before choosing caches, replicas, partitions, or autoscaling.
4. Decide what must be synchronous and what can use Kafka or another queue; explain keys, ordering, offsets, retries, and dead-letter handling.
5. Cover idempotency, rate limits, timeouts, circuit breakers, and behavior when a dependency fails.
6. Close with observability, recovery, security, and the trade-offs you would revisit if traffic grew.

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `system-design-architecture/requirements-and-estimation/` — requirements and estimation question pages
- `system-design-architecture/scalable-building-blocks/` — scalable building blocks question pages
- `system-design-architecture/trade-offs-and-design-exercises/` — trade-offs and design exercises question pages

Use descriptive lowercase kebab-case filenames, for example `system-design-architecture/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [40-question hard-level Q&A for advanced interview practice](hard-interview-questions.md)
- [30-question tricky Q&A for common interview traps](tricky-interview-questions.md)
- [System design interview questions](interview-questions.md)

## Related modules

- [Module 04: Data Structures and Algorithms](../data-structures-algorithms/README.md)
- [Module 09: SQL and Relational Databases](../sql-relational-databases/README.md)
- [Module 13: Microservices Architecture](../microservices-architecture/README.md)
- [Module 19: Logging, Monitoring and Observability](../logging-monitoring-observability/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
- [Module 22: Real-World Backend Projects](../real-world-backend-projects/README.md)
