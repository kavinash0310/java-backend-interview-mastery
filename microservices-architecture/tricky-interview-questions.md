# Microservices Architecture: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Retries, timeouts, and dependency traps

### Q1. Why can a retry make a successful but timed-out POST happen twice?

**Answer:** The server may have committed before the response was lost. Use idempotency keys or make the operation naturally idempotent.

### Q2. If a downstream call times out at the client, is the server work canceled?

**Answer:** Not necessarily. The server may continue after the client gives up. Propagate deadlines and cancellation where supported.

### Q3. Why can retries multiply across three service layers?

**Answer:** Each layer may retry the other's attempts, multiplying requests during failure. Assign one retry owner and use a total attempt/deadline budget.

### Q4. Can a circuit breaker protect a service if calls have no timeout?

**Answer:** Not promptly; hung calls may not complete and count as failures. Bound connection and response time first.

### Q5. Why can a successful fallback hide a broken primary dependency?

**Answer:** User-facing errors disappear while stale data or failure rate grows. Track fallback volume and alert on primary health separately.

### Q6. Why does adding replicas sometimes worsen downstream timeouts?

**Answer:** More instances can create more concurrent calls and connections against the same limited dependency. Bound per-service concurrency.

### Q7. What is the danger of retrying a non-idempotent database command after a network error?

**Answer:** The commit outcome may be unknown, so retry can duplicate effects. Use a unique operation key and query the outcome.

### Q8. Can a service mesh safely retry every HTTP request by default?

**Answer:** No. Retrying unsafe operations can duplicate business effects. Configure by method and application idempotency contract.

### Q9. Why should an end-to-end deadline include queueing time?

**Answer:** Time spent waiting for a thread or connection consumes the caller's budget. A network-only timeout permits work to exceed the user deadline.

### Q10. How can bulkheads fail if each dependency uses a separate thread pool but shares one connection pool?

**Answer:** The shared connection pool remains the common bottleneck. Isolate every scarce resource or cap access at the shared pool.

## Events and distributed workflow gotchas

### Q11. Does an outbox guarantee exactly-once event delivery?

**Answer:** No. The relay can publish then crash before marking sent, causing duplicates. Use stable IDs and idempotent consumers.

### Q12. Can a saga compensation always restore the original state?

**Answer:** No. It is a new business action that can fail or be impossible, such as refunding after settlement. Track partial compensation and manual recovery.

### Q13. Why does choreography become hard to debug as participants grow?

**Answer:** Workflow state is distributed across event reactions, making order and recovery less visible. Add correlation, explicit state tracking, or orchestration where justified.

### Q14. Can event delivery order be assumed across service boundaries?

**Answer:** Only within the transport's documented ordering scope, often one partition/key. Consumers should validate sequence or tolerate reordering.

### Q15. What happens if an outbox relay publishes an event and crashes before checkpointing?

**Answer:** It republishes after restart. Duplicates are expected; consumer effect must be idempotent.

### Q16. Why can dual writes cause permanent inconsistency even with retries?

**Answer:** One system can commit while the other repeatedly fails or retries create duplicates. Persist intent atomically and reconcile.

### Q17. Can an event consumer use the producer's database as its read model?

**Answer:** It couples to another service's storage and schema, bypassing ownership. Consume events or call the owning API.

### Q18. Why might a successful compensation still leave an audit record?

**Answer:** Compensation is a new event, not erasure of history. Preserve original and compensating actions for traceability.

### Q19. How can a saga distinguish a failed step from a slow step?

**Answer:** A timeout is ambiguous. Query the participant using a stable command ID before compensating or retrying.

### Q20. Why can a delayed event resurrect deleted data in a projection?

**Answer:** An older update may arrive after a tombstone/delete event. Use entity versions or sequence checks and retain deletion markers.

## Discovery, data, and resilience

### Q21. Why can service discovery return an instance that has already begun shutdown?

**Answer:** Registry or load-balancer state can lag. Use readiness deregistration and graceful draining, while tolerating connection failures.

### Q22. Can a DNS TTL guarantee clients switch immediately after failover?

**Answer:** No. Clients and libraries may cache results differently and existing connections persist. Design reconnect and retry behavior.

### Q23. Why can a read replica break read-after-write across microservices?

**Answer:** The write-owning service may commit while another reads a lagging replica. Route consistency-critical reads or propagate version tokens.

### Q24. What is the risk of sharing one database schema between independently deployed services?

**Answer:** Cross-service queries and migrations couple releases and violate ownership. Restrict access and use explicit contracts.

### Q25. Why is a distributed lock a risky solution for a payment invariant?

**Answer:** Lease expiry can let stale owners act, and the payment provider does not enforce that lock. Use durable idempotency at the payment boundary.

### Q26. How can per-service rate limits protect a shared database?

**Answer:** Cap concurrent queries and prioritize workloads so one service cannot exhaust connections or CPU. Coordinate budgets with actual database capacity.

### Q27. Why should a service's health check not synchronously call every dependency?

**Answer:** It can amplify outages and mark all replicas unhealthy together. Use bounded critical checks and separate detailed diagnostics.

### Q28. Can a shared circuit breaker cause unrelated tenants to fail together?

**Answer:** Yes, if state is global across tenants or operations. Scope breakers to dependency/operation classes where isolation matters.

### Q29. Why is a cache key missing the tenant ID a data-leak bug?

**Answer:** Values can cross tenant boundaries even if API authorization is correct. Include tenant scope in keys and test cross-tenant isolation.

### Q30. What is a safe way to change a service's event schema during rolling deploy?

**Answer:** Consumers accept old and new shapes first, producers switch later, then old support is removed after adoption is measured.

## References

- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: Circuit breaker](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/circuit-breaker.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Martin Fowler: Microservices](https://martinfowler.com/articles/microservices.html)
