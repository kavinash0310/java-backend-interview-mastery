# Module 13 — Microservices Architecture

> **Module guide:** Design independently deployable services and reason about communication, resilience, discovery, data ownership, and consistency.

## Learning outcomes

- Choose service boundaries based on ownership and change needs.
- Set timeouts and retries without creating retry storms.
- Explain saga and outbox approaches and their consistency trade-offs.

## Subtopic links

- [Service boundaries and communication](#service-boundaries-and-communication)
- [Routing and configuration](#routing-and-configuration)
- [Resilience and observability](#resilience-and-observability)
- [Distributed data and workflows](#distributed-data-and-workflows)

## Topic coverage

### Service boundaries and communication

- Monolith versus microservices
- Service decomposition and bounded contexts
- Service-to-service communication
- Synchronous versus asynchronous communication
- Microservices security

### Routing and configuration

- API Gateway
- Service discovery
- Load balancing
- Centralized configuration

### Resilience and observability

- Timeouts and retries
- Circuit breakers and Resilience4j
- Bulkheads and rate limits
- Distributed tracing

### Distributed data and workflows

- Distributed transactions
- Saga pattern
- Transactional Outbox pattern
- Eventual consistency

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `microservices-architecture/service-boundaries-and-communication/` — service boundaries and communication question pages
- `microservices-architecture/routing-and-configuration/` — routing and configuration question pages
- `microservices-architecture/resilience-and-observability/` — resilience and observability question pages
- `microservices-architecture/distributed-data-and-workflows/` — distributed data and workflows question pages

Use descriptive lowercase kebab-case filenames, for example `microservices-architecture/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [Microservices interview questions](interview-questions.md)

## Related modules

- [Module 08: Spring Security](../spring-security/README.md)
- [Module 12: Apache Kafka and Messaging](../kafka-messaging/README.md)
- [Module 19: Logging, Monitoring and Observability](../logging-monitoring-observability/README.md)
- [Module 20: System Design and Architecture](../system-design-architecture/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
