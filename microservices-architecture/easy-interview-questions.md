# Microservices Architecture: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is a microservice?

**Answer:** A service organized around a business capability and deployed independently. The team takes responsibility for its operation and data behavior.

### Q2. What is a monolith?

**Answer:** An application packaged and deployed as one unit. A well-structured monolith can be easier to run than a premature set of services.

### Q3. Monolith vs microservices trade-off?

**Answer:** A monolith simplifies local calls and deployment but may become harder to change as teams grow. Microservices support independent ownership but add network and operational complexity.

### Q4. What is a service boundary?

**Answer:** It defines which behavior and data belong to a service and what it exposes to others. A strong boundary follows business ownership and change.

### Q5. What is a bounded context?

**Answer:** A domain-model boundary where terms and rules have a clear meaning. Different contexts may use the same word differently.

### Q6. Why should each microservice own its data?

**Answer:** It can then change its schema and rules independently. Other services should use published APIs or events, not write directly to its tables.

### Q7. What is service discovery?

**Answer:** It helps a service find another service's current network location, using a registry, platform DNS, or cloud routing.

### Q8. What is client-side load balancing?

**Answer:** The caller selects a destination among service instances. It needs current endpoint information and a way to avoid unhealthy targets.

### Q9. What is server-side load balancing?

**Answer:** A load balancer forwards requests to available instances and gives clients a stable address.

### Q10. Why set timeouts on service calls?

**Answer:** They bound how long a request waits for a slow dependency. Without them, stuck calls occupy threads and connections.

### Q11. What is a retry storm?

**Answer:** A surge of repeated calls when many clients retry a failing dependency. Use bounded retries, backoff, jitter, and a retry budget.

### Q12. Why should retries be limited?

**Answer:** Retries consume capacity and may repeat side effects. Retry only transient failures and safe-to-repeat operations.

### Q13. What is a bulkhead pattern?

**Answer:** It isolates resource pools or concurrency limits so one failing dependency cannot consume all capacity.

### Q14. What is a fallback?

**Answer:** A defined alternative response when a dependency is unavailable. It should preserve correctness and show when data may be stale or incomplete.

### Q15. What is eventual consistency?

**Answer:** Related services or replicas may temporarily disagree but are expected to converge as updates propagate. The product must tolerate the delay.

### Q16. Why are distributed transactions difficult?

**Answer:** Independent services cannot share one local database transaction. Network failures can leave different outcomes, so workflows often use compensations or an outbox.

### Q17. What is the transactional outbox pattern?

**Answer:** The service writes its business change and an event row in one local transaction. A publisher sends the event later, avoiding a dual-write gap.

### Q18. What is an inbox or deduplication table?

**Answer:** It records processed message IDs so a consumer can detect redelivery. Commit the dedupe record and business update atomically.

### Q19. What is a backend-for-frontend?

**Answer:** A server component tailored to a client experience, such as mobile or web. It composes APIs without becoming an unowned general gateway.

### Q20. What is centralized configuration?

**Answer:** Shared management for service settings, often with environment-specific values. Access, versioning, rollout, and secret handling still matter.

### Q21. Why use correlation IDs?

**Answer:** They let operators connect logs and spans for one request across service boundaries. Propagate consistently rather than generating unrelated IDs at each hop.

### Q22. What is a sidecar pattern?

**Answer:** A sidecar is a helper process deployed alongside a service to handle a cross-cutting task such as proxying traffic or collecting telemetry. It adds another component to operate, so use it when shared infrastructure behavior is valuable.

### Q23. When should you split a service?

**Answer:** When ownership, scaling, deployment, or change boundaries provide a clear benefit greater than network and operations costs.

### Q24. What is a service contract?

**Answer:** The operations or events a service exposes, including data shape and behavior. A stable contract lets the owner evolve implementation safely.

### Q25. What is an API gateway in microservices?

**Answer:** A common entry point that routes client requests and can enforce shared edge concerns.

**Example:** It may authenticate requests and route them to a service.

### Q26. What is synchronous service communication?

**Answer:** The caller waits for a response before continuing.

**Example:** An HTTP request to fetch a profile is synchronous.

### Q27. What is asynchronous service communication?

**Answer:** The sender publishes work or an event and continues without waiting for the receiver to finish.

**Example:** A service publishes `OrderPlaced` to a broker.

### Q28. REST vs messaging?

**Answer:** REST suits direct request-response; messaging helps decouple work in time and absorb bursts.

**Example:** Choose based on latency, coupling, and delivery needs.

### Q29. What is a circuit breaker?

**Answer:** A resilience control that temporarily stops calls to a failing dependency and later tests recovery.

**Example:** It prevents every request from waiting on a known-bad service.

### Q30. What is a timeout?

**Answer:** A maximum wait for an operation before the caller gives up and handles failure.

**Example:** Set connect and response timeouts on downstream calls.

### Q31. What is exponential backoff?

**Answer:** A retry delay that grows after each failure, often with random jitter.

**Example:** Wait 100 ms, then 200 ms, then 400 ms plus jitter.

### Q32. Why can retrying every error be harmful?

**Answer:** Permanent failures repeat needlessly, and simultaneous retries can overload a service already under stress.

**Example:** Retry transient timeouts, not invalid input.

### Q33. What is a Saga?

**Answer:** A sequence of local transactions with compensating actions for failures across services.

**Example:** Cancel a reservation if a later payment step fails.

### Q34. Saga choreography vs orchestration?

**Answer:** Choreography reacts to events; orchestration uses a coordinator to direct each step.

**Example:** Use a coordinator when the workflow needs explicit visibility.

### Q35. What is a service mesh?

**Answer:** An infrastructure layer that manages service-to-service networking features such as identity, routing, and telemetry.

**Example:** It may run alongside services as proxies.

### Q36. What is a distributed trace?

**Answer:** A connected view of spans that follows one request across service boundaries.

**Example:** Propagate trace context in HTTP or messaging headers.

### Q37. What is distributed tracing?

**Answer:** Following one request across services using linked timed spans and propagated context.

**Example:** A trace can show time spent in each service.

### Q38. Why avoid shared databases between services?

**Answer:** It couples independent deployments to the same schema and lets teams bypass service ownership.

**Example:** Expose a contract or event rather than writing another service's tables.

### Q39. What is service registration?

**Answer:** The process of publishing an instance's address and health so clients or a platform can find it.

**Example:** An orchestrator can register a ready service instance.

### Q40. What is a health endpoint in a microservice?

**Answer:** A lightweight endpoint used by platforms or monitoring to check whether the service can run or receive traffic.

**Example:** Separate liveness from readiness when their meanings differ.

## References

- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: Circuit breaker](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/circuit-breaker.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Martin Fowler: Microservices](https://martinfowler.com/articles/microservices.html)
