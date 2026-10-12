# Microservices Architecture: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q07. What is a microservice?

**Answer:** A service organized around a business capability and deployed independently. The team takes responsibility for its operation and data behavior.

### Q08. What is a monolith?

**Answer:** An application packaged and deployed as one unit. A well-structured monolith can be easier to run than a premature set of services.

### Q09. Monolith vs microservices trade-off?

**Answer:** A monolith simplifies local calls and deployment but may become harder to change as teams grow. Microservices support independent ownership but add network and operational complexity.

### Q10. What is a service boundary?

**Answer:** It defines which behavior and data belong to a service and what it exposes to others. A strong boundary follows business ownership and change.

### Q11. What is a bounded context?

**Answer:** A domain-model boundary where terms and rules have a clear meaning. Different contexts may use the same word differently.

### Q12. Why should each microservice own its data?

**Answer:** It can then change its schema and rules independently. Other services should use published APIs or events, not write directly to its tables.

### Q13. What is service discovery?

**Answer:** It helps a service find another service's current network location, using a registry, platform DNS, or cloud routing.

### Q14. What is client-side load balancing?

**Answer:** The caller selects a destination among service instances. It needs current endpoint information and a way to avoid unhealthy targets.

### Q15. What is server-side load balancing?

**Answer:** A load balancer forwards requests to available instances and gives clients a stable address.

### Q16. Why set timeouts on service calls?

**Answer:** They bound how long a request waits for a slow dependency. Without them, stuck calls occupy threads and connections.

### Q17. What is a retry storm?

**Answer:** A surge of repeated calls when many clients retry a failing dependency. Use bounded retries, backoff, jitter, and a retry budget.

### Q18. Why should retries be limited?

**Answer:** Retries consume capacity and may repeat side effects. Retry only transient failures and safe-to-repeat operations.

### Q19. What is a bulkhead pattern?

**Answer:** It isolates resource pools or concurrency limits so one failing dependency cannot consume all capacity.

### Q20. What is a fallback?

**Answer:** A defined alternative response when a dependency is unavailable. It should preserve correctness and show when data may be stale or incomplete.

### Q21. What is eventual consistency?

**Answer:** Related services or replicas may temporarily disagree but are expected to converge as updates propagate. The product must tolerate the delay.

### Q22. Why are distributed transactions difficult?

**Answer:** Independent services cannot share one local database transaction. Network failures can leave different outcomes, so workflows often use compensations or an outbox.

### Q23. What is the transactional outbox pattern?

**Answer:** The service writes its business change and an event row in one local transaction. A publisher sends the event later, avoiding a dual-write gap.

### Q24. What is an inbox or deduplication table?

**Answer:** It records processed message IDs so a consumer can detect redelivery. Commit the dedupe record and business update atomically.

### Q25. What is a backend-for-frontend?

**Answer:** A server component tailored to a client experience, such as mobile or web. It composes APIs without becoming an unowned general gateway.

### Q26. What is centralized configuration?

**Answer:** Shared management for service settings, often with environment-specific values. Access, versioning, rollout, and secret handling still matter.

### Q27. Why use correlation IDs?

**Answer:** They let operators connect logs and spans for one request across service boundaries. Propagate consistently rather than generating unrelated IDs at each hop.

### Q28. What is a sidecar pattern?

**Answer:** A sidecar is a helper process deployed alongside a service to handle a cross-cutting task such as proxying traffic or collecting telemetry. It adds another component to operate, so use it when shared infrastructure behavior is valuable.

### Q29. When should you split a service?

**Answer:** When ownership, scaling, deployment, or change boundaries provide a clear benefit greater than network and operations costs.

### Q30. What is a service contract?

**Answer:** The operations or events a service exposes, including data shape and behavior. A stable contract lets the owner evolve implementation safely.

## References

- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: Circuit breaker](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/circuit-breaker.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Martin Fowler: Microservices](https://martinfowler.com/articles/microservices.html)
