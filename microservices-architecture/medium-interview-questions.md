# Microservices Architecture: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Boundaries and communication choices

### Q1. What signals suggest two candidate microservices belong in one boundary?

**Answer:** They change together, share invariants, or require frequent synchronous calls and coordinated releases.

**Example:** Keep tightly coupled checkout rules together until a real independent scaling or ownership need appears.

### Q2. How can a service boundary follow a bounded context?

**Answer:** Keep a service's model and language aligned with one business capability instead of sharing one universal domain model.

**Example:** “Customer” may mean different things in billing and support.

### Q3. When is a modular monolith a better starting point than microservices?

**Answer:** When independent deployment is not yet valuable and one team can preserve clear internal boundaries with simpler operations.

**Example:** Split later when ownership, scaling, or release pressure justifies the network cost.

### Q4. How can an API gateway become a bottleneck or failure point?

**Answer:** Too many business rules, synchronous dependencies, or a single underscaled gateway can centralize latency and failure.

**Example:** Keep gateway logic focused on routing and edge policy.

### Q5. When should service-to-service calls use gRPC?

**Answer:** When typed contracts, efficient binary transport, or streaming matter and all clients can support the protocol.

**Example:** Use REST when broad browser or external interoperability is more important.

### Q6. What is the main trade-off of asynchronous messaging?

**Answer:** It reduces temporal coupling but introduces eventual consistency, retries, duplicate delivery, and harder tracing.

**Example:** Tell users when a result is pending rather than pretending it is complete.

### Q7. Why should a service not expose its database schema as its API?

**Answer:** Internal storage changes would become external contract changes, tightly coupling consumers to implementation.

**Example:** Expose business operations and stable resource/event fields.

### Q8. What is consumer-driven contract testing?

**Answer:** Consumers publish expectations that providers verify, catching incompatible API changes before deployment.

**Example:** Test required response fields and status behavior.

### Q9. How can a backend-for-frontend differ from an API gateway?

**Answer:** A BFF shapes APIs for one client experience; a gateway usually provides shared routing and edge policies.

**Example:** A mobile BFF may aggregate smaller payloads for a mobile screen.

### Q10. When can service discovery through DNS be sufficient?

**Answer:** When the platform provides stable service names and clients can handle address refresh and connection changes.

**Example:** Use cluster DNS rather than a custom registry for a simple orchestrated environment.

## Resilience and traffic control

### Q11. How should timeout values relate across a call chain?

**Answer:** A downstream timeout should fit within the remaining caller deadline, with time left for cleanup and response handling.

**Example:** A 2-second request cannot safely allow three sequential 2-second calls.

### Q12. Why use one end-to-end deadline instead of independent call timeouts?

**Answer:** Per-hop limits can accumulate beyond the user's budget and waste resources after the caller has given up.

**Example:** Pass remaining deadline through each downstream request.

### Q13. What is the danger of retries at several layers?

**Answer:** Retries multiply: a single user request can create many downstream attempts and worsen overload.

**Example:** Choose one retry owner and bound attempts globally.

### Q14. When should a retry use jitter?

**Answer:** When many clients may retry together; random delay spreads the load spike.

**Example:** Use exponential backoff with full or bounded jitter.

### Q15. What should a circuit breaker's half-open state do?

**Answer:** Allow a small number of probe calls to test recovery before reopening or closing the circuit.

**Example:** Limit probes so a recovering dependency is not flooded.

### Q16. How does a bulkhead differ from a circuit breaker?

**Answer:** A bulkhead limits resource sharing; a circuit breaker stops calls based on failure behavior.

**Example:** Use separate connection pools for critical and optional dependencies.

### Q17. What is load shedding in a service?

**Answer:** Rejecting lower-priority requests under pressure to preserve capacity for essential operations.

**Example:** Return 503 or a controlled degraded response when a queue is full.

### Q18. What is a concurrency limit protecting against?

**Answer:** It bounds simultaneous work so downstream pools, memory, or CPU do not become saturated.

**Example:** Limit concurrent report generation separately from API reads.

### Q19. When is a fallback dangerous?

**Answer:** If it returns misleading stale data or calls another unhealthy dependency, it can hide or amplify the original failure.

**Example:** Define what degraded answer is acceptable to users.

### Q20. How can a service avoid cascading failure during dependency outage?

**Answer:** Use bounded timeouts, limited retries, circuit breaking, bulkheads, and a clear degraded or failed response.

**Example:** Do not queue unlimited work for a dependency that is down.

## Distributed data and workflows

### Q21. Why is a distributed transaction across services difficult?

**Answer:** Each service owns a separate resource and may fail independently; a single ACID commit is usually unavailable across them.

**Example:** Coordinate local transactions with a saga or event workflow.

### Q22. What is saga choreography's main operational challenge?

**Answer:** The workflow is implicit across event publishers and subscribers, so state and failure paths can be harder to see.

**Example:** Add correlation IDs and an explicit workflow state model.

### Q23. What is saga orchestration's main trade-off?

**Answer:** A coordinator makes flow visible but can become a central dependency and needs its own durable state.

**Example:** Persist each step and outcome rather than keeping state only in memory.

### Q24. What makes a compensating action different from a rollback?

**Answer:** It is a new business operation that semantically counteracts a completed step; it may not restore the exact prior world.

**Example:** Refunding a captured payment is not the same as erasing the payment event.

### Q25. How does the transactional outbox avoid a dual-write gap?

**Answer:** It writes domain data and an event row in the same database transaction; a relay publishes later.

**Example:** A relay can resend, so consumers still deduplicate.

### Q26. Why can an outbox publisher still deliver duplicates?

**Answer:** It may publish successfully and crash before recording that publication, then publish again after restart.

**Example:** Use event IDs and idempotent consumers.

### Q27. What is an inbox table for?

**Answer:** It records processed event IDs in the same transaction as the business effect to suppress repeated delivery.

**Example:** Enforce a unique constraint on consumer and event ID.

### Q28. How can a service maintain read-your-writes behavior with asynchronous projections?

**Answer:** Return the committed result directly or route a follow-up read to the write model until the projection catches up.

**Example:** Include a version or consistency token in the response.

### Q29. Why should a service own data migrations for its own database?

**Answer:** The owning team understands invariants and can coordinate schema rollout with its code and consumers.

**Example:** Publish a contract change before removing stored fields.

### Q30. What should an event contain if consumers need to rebuild state?

**Answer:** Stable identity, event type/version, business key, occurrence time, and the data required by the consumer contract.

**Example:** Avoid requiring consumers to query another service's private tables.

## Observability and operations

### Q31. What is a distributed trace sampling strategy?

**Answer:** A rule for selecting which traces to retain, often preserving errors or slow traces while sampling routine traffic.

**Example:** Tail sampling can decide after observing the completed trace.

### Q32. Why is trace context propagation across Kafka important?

**Answer:** It links producer and consumer work even though processing is separated in time.

**Example:** Carry trace headers in record headers.

### Q33. How can you identify a noisy neighbor service?

**Answer:** Compare per-tenant or per-caller traffic, resource consumption, queueing, and error rates without using unbounded metric labels.

**Example:** Use logs or traces for high-cardinality identifiers.

### Q34. What is a service-level objective for a dependency?

**Answer:** A measurable target such as availability or latency over a time window that informs operational decisions.

**Example:** Use a p95 latency target for a critical request path.

### Q35. How should a team use an error budget?

**Answer:** Treat allowed unreliability as a planning signal: when spent quickly, prioritize reliability work or slow risky releases.

**Example:** Define the policy before an incident.

### Q36. Why can synchronous fan-out make latency unpredictable?

**Answer:** The caller waits for multiple dependencies; the slowest sequential or parallel branch can dominate response time.

**Example:** Set a total deadline and avoid unnecessary calls.

### Q37. What is request hedging, and what risk does it introduce?

**Answer:** It sends a duplicate request after a delay to another replica to reduce tail latency, but increases load and requires idempotency.

**Example:** Use only for safe, read-only calls under a measured policy.

### Q38. How do you roll out a breaking service contract safely?

**Answer:** Deploy consumers that accept old and new forms first, then change producers, observe, and remove old support later.

**Example:** Use expand-and-contract for event fields.

### Q39. What is a service dependency map useful for?

**Answer:** It helps identify the blast radius and owners when a dependency fails or latency rises.

**Example:** Generate it from traces and keep ownership metadata current.

### Q40. How should teams decide whether to split a service?

**Answer:** Compare independent change rate, ownership, scaling, reliability, and data boundaries with added network and operational cost.

**Example:** Do not split solely because one code package is large.

## References

- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: Circuit breaker](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/circuit-breaker.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Martin Fowler: Microservices](https://martinfowler.com/articles/microservices.html)
