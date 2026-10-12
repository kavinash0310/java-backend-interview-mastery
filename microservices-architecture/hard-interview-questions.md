# Microservices Architecture: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Boundaries and service contracts

### Q1. How do you identify a service boundary when one business workflow spans several teams?

**Answer:** Start from cohesive domain capabilities and data ownership, then map change patterns and invariants. Split only when independent deployment and ownership outweigh network cost.

### Q2. When is a modular monolith a better choice than microservices?

**Answer:** When domain boundaries or operational maturity are uncertain, one deployable unit can preserve transactions and simpler debugging while enforcing module boundaries internally.

### Q3. How can an anti-corruption layer reduce migration risk?

**Answer:** It translates legacy models and semantics at the boundary, preventing old assumptions from spreading into new domains. Keep the adapter explicit and temporary when migration is the goal.

### Q4. Why should a service own its schema even if services share one database cluster?

**Answer:** Ownership allows independent evolution and prevents hidden cross-service coupling. Enforce access boundaries and publish contracts instead of letting peers query tables directly.

### Q5. How do you evolve a synchronous API while old and new clients coexist?

**Answer:** Use additive, tolerant changes, consumer contract tests, telemetry on old fields, and a planned deprecation window. Avoid changing semantic meaning under the same field name.

### Q6. What is the difference between a BFF and an API gateway?

**Answer:** A BFF shapes APIs for a specific client experience; a gateway handles shared routing or cross-cutting edge policy. They can coexist but should not duplicate business logic.

### Q7. How do you keep a service from becoming a shared utility that owns no domain?

**Answer:** Give it a clear capability, data responsibility, and accountable team; avoid extracting every common helper into a network dependency.

### Q8. Why can splitting a database before splitting the domain make the system worse?

**Answer:** It introduces distributed coordination without clear ownership or independent change. First establish boundaries and migration seams, then separate data deliberately.

### Q9. What is a strangler migration's most important routing invariant?

**Answer:** Each operation has one authoritative owner at a time, with explicit routing and data reconciliation. Avoid dual writes without consistency and rollback design.

### Q10. How should an internal API distinguish compatibility from implementation freedom?

**Answer:** Specify externally observable behavior and versioned contracts, while keeping storage and code private. Contract tests protect the former without freezing the latter.

## Resilience and communication

### Q11. How do deadlines, retries, and circuit breakers work together?

**Answer:** Propagate an end-to-end deadline, retry only safe transient failures within its budget, and open the breaker to stop futile calls. Each pattern controls a different failure mode.

### Q12. Why is a circuit breaker not a substitute for a timeout?

**Answer:** Without a timeout, a call may hang forever before the breaker records enough failures. Bound connection and response time first.

### Q13. How can a bulkhead prevent one downstream from exhausting all request capacity?

**Answer:** Assign separate bounded concurrency or pools per dependency so one saturated service cannot consume every worker or connection.

### Q14. What is retry amplification in a service call graph?

**Answer:** Independent retries at multiple layers multiply total attempts during failure. Choose a retry owner, propagate deadlines, and apply jittered budgets.

### Q15. When should a service return stale data instead of failing a read?

**Answer:** When the endpoint's consistency contract permits bounded staleness and the stale value is safe. Mark freshness and avoid stale authorization or financial decisions.

### Q16. Why is asynchronous communication not automatically more resilient than synchronous calls?

**Answer:** Queues can fill, consumers can lag, and poison events persist. Add capacity limits, delivery policy, idempotency, and operational ownership.

### Q17. How do you handle a downstream that is slow but not failing?

**Answer:** Use a deadline, bulkhead, concurrency cap, and possibly load shedding. A breaker may not trip on slow calls unless latency is part of its policy.

### Q18. What is the role of a service mesh, and what should remain in application code?

**Answer:** A mesh can standardize transport policy and telemetry, but business authorization, idempotency, and workflow semantics remain application responsibilities.

### Q19. How do you prevent cascading retries from creating a thundering herd during recovery?

**Answer:** Use jittered backoff, bounded queues, gradual ramp-up, and retry budgets. Let the recovering dependency regain capacity before restoring full traffic.

### Q20. Why should a fallback be reviewed as carefully as the primary path?

**Answer:** It may return inconsistent data or hide a broad outage. Define correctness, expiration, monitoring, and a clear condition to stop serving it.

## Distributed data and workflows

### Q21. How does the transactional outbox close the database-to-broker dual-write gap?

**Answer:** Write business state and an outbox row in one local transaction; a relay publishes rows and marks progress. Consumers still need idempotency.

### Q22. What is the difference between saga orchestration and choreography under failure?

**Answer:** Orchestration centralizes workflow state and decisions; choreography distributes reactions through events. Pick based on visibility, coupling, and compensation complexity.

### Q23. Why are compensating actions not equivalent to rolling back a distributed transaction?

**Answer:** They are new business operations that may fail or need their own compensation. Preserve audit history and design for partial completion.

### Q24. How should an idempotency key be scoped for a cross-service workflow?

**Answer:** Bind it to the initiating principal, operation, and request fingerprint, then persist outcome and expiry. Propagate a stable workflow ID to downstream steps.

### Q25. What consistency model does an outbox consumer usually provide?

**Answer:** At-least-once delivery with possible duplicates. Use an idempotent consumer or deduplication ledger keyed by event identity.

### Q26. How can a saga recover after the orchestrator crashes mid-workflow?

**Answer:** Persist state before issuing steps, make commands idempotent, and resume from durable progress. Treat timeout as ambiguous until the participant is queried.

### Q27. Why is a distributed lock a weak substitute for ownership or a database constraint?

**Answer:** Leases can expire and stale owners may continue; cross-system locks do not enforce the invariant at the resource. Prefer durable uniqueness and fencing.

### Q28. How do you choose between event-carried state and an event containing only an entity ID?

**Answer:** Carry a stable snapshot when consumers need independent replay and producer coupling is acceptable; use IDs when consumers can safely query owned APIs and freshness matters.

### Q29. What breaks if an event consumer assumes global ordering across Kafka partitions?

**Answer:** Ordering is only guaranteed within a partition. Partition by the entity requiring order or make the handler tolerate reordering with sequence checks.

### Q30. How should a service handle schema changes during a rolling deployment?

**Answer:** Use expand-and-contract: consumers accept both forms first, producers change next, then obsolete fields are removed after telemetry confirms adoption.

## Observability and operations

### Q31. How do you trace one request across asynchronous message boundaries?

**Answer:** Inject trace context into message headers and extract it in consumers, while recording a link or causal span. Avoid assuming the same thread or parent lifetime.

### Q32. Why should trace IDs not be used as metric labels?

**Answer:** They create near-unbounded cardinality and can overload metrics storage. Keep trace IDs in logs or exemplars and use bounded dimensions for metrics.

### Q33. How can a service-level objective guide load shedding?

**Answer:** Define user-facing latency/error budgets, then reject low-priority work when capacity is threatened to protect critical operations. Measure the rejected class separately.

### Q34. What is a useful dashboard for a dependency outage?

**Answer:** Show request rate, error and latency percentiles, saturation, queue depth, and downstream call outcomes with deployment markers. Include the user impact, not just host health.

### Q35. How do you distinguish a service bug from a dependency failure in traces?

**Answer:** Compare span boundaries, status, timeout, and resource saturation; sample correlated logs and version changes. Ensure instrumentation records the actual failing operation.

### Q36. Why should health checks avoid checking every downstream on every probe?

**Answer:** Probe fan-out can amplify a dependency outage and flip readiness for all replicas. Use bounded critical checks and separate diagnostic detail from routing signals.

### Q37. How can canary deployment reduce blast radius without hiding a global issue?

**Answer:** Route a controlled cohort and compare service and business metrics, while retaining a baseline. Avoid canaries that share the same overloaded dependency.

### Q38. What is the risk of logging entire distributed event payloads?

**Answer:** Payloads may contain secrets or personal data, create large costs, and expose data broadly. Log identifiers, safe fields, and schema versions instead.

### Q39. How do you measure queue-based service health beyond consumer lag?

**Answer:** Track oldest message age, processing duration, failure/retry counts, partition skew, and capacity headroom. Low lag can still hide poison or dead-letter growth.

### Q40. Why should a service own an operational runbook for dependencies it consumes?

**Answer:** It needs clear timeout, retry, fallback, and escalation behavior during failure. Shared dependency docs do not define local user impact or action thresholds.


## References

- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: Circuit breaker](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/circuit-breaker.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Martin Fowler: Microservices](https://martinfowler.com/articles/microservices.html)
