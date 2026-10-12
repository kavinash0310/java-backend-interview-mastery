# Real-World Backend Projects: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Domain and data design traps

### Q1. A payment provider times out after charging. How should the service decide whether to retry?

**Answer:** Use a stable provider reference and reconcile the outcome first. A timeout is ambiguous, not a confirmed failure.

### Q2. A booking endpoint checks availability and then inserts a reservation. Why can it double-book?

**Answer:** Concurrent requests can both pass. Enforce the invariant atomically with a constraint or conditional update.

### Q3. A DTO exposes a JPA entity. What subtle issue can occur during JSON serialization?

**Answer:** Lazy getters can trigger hidden queries or expose internal fields/cycles. Map to an explicit response model.

### Q4. A Redis key uses only order ID in a multi-tenant project. What can leak?

**Answer:** A cross-tenant cache collision can return another tenant's data. Scope keys by tenant and test isolation.

### Q5. A money field uses double and rounds only at display time. What is wrong?

**Answer:** Binary floating point may already have lost decimal precision. Use decimal or integer minor units with currency.

### Q6. A shared event has no schema/version context. Why can future consumers break?

**Answer:** Meaning or shape can change without migration context. Use compatible evolution and document semantic changes.

### Q7. A soft-deleted user still blocks username reuse. Is that necessarily a bug?

**Answer:** It depends on policy. Use a partial unique constraint or intentionally reserve names.

### Q8. Why can email be a poor permanent primary key?

**Answer:** It changes, normalizes differently, or may be reused. Use a stable internal ID and enforce email uniqueness separately.

### Q9. A project stores a current balance and every ledger entry. Which should be authoritative?

**Answer:** Define the ledger as source of truth and reconcile derived balance; two mutable authorities can silently drift.

### Q10. A generated ID is used in entity hashCode before persistence. What surprise follows?

**Answer:** ID assignment changes hash behavior, so a HashSet may not find the entity.

## Workflow and integration traps

### Q11. An outbox relay publishes then crashes before marking the row sent. What does the consumer need?

**Answer:** Idempotent processing keyed by event ID; duplicate publication is expected.

### Q12. A saga refund fails after payment succeeds. What should the project show?

**Answer:** A durable partial state and retry/manual recovery, not a false rollback. Compensation is a business action that can fail.

### Q13. A job is acknowledged before its database write. What loss window exists?

**Answer:** A crash after acknowledgment loses the result. Persist the effect first or use a transactional integration.

### Q14. Two app instances process the same scheduled job. What protects correctness?

**Answer:** A durable claim/lease plus idempotent handler. Do not rely on an in-memory singleton.

### Q15. A cache invalidation is sent before SQL commit. What race can repopulate stale data?

**Answer:** A reader can reload old DB state before commit. Publish after commit via outbox or compare versions.

### Q16. A retry creates a second order after a 504. Which contract is missing?

**Answer:** A durable idempotency key tied to request fingerprint and stored outcome.

### Q17. A webhook arrives twice and out of order. How should the handler decide what to apply?

**Answer:** Deduplicate provider event IDs and compare entity sequence/version; ignore stale updates only when semantics allow.

### Q18. A migration adds NOT NULL to a large live table. What rollout trap exists?

**Answer:** Backfill and lock duration can block traffic. Add compatible structure, backfill in batches, validate, then enforce.

### Q19. A Kafka message can replay after 30 days. Which assumption matters for deduplication?

**Answer:** Records must cover the replay horizon or handlers must be naturally idempotent.

### Q20. A downstream rate-limits requests. Why can local retries violate the limit?

**Answer:** Replicas retry independently and synchronize. Share retry budgets or throttle with jitter and Retry-After.

## Project quality and delivery traps

### Q21. A load test reports 10,000 requests per second on a laptop. What should the README claim?

**Answer:** Only report tested setup, workload, and caveats; do not present it as production capacity.

### Q22. Unit tests pass, but there are no production-database tests. What can hide?

**Answer:** Dialect, locking, migration, and constraint behavior may differ. Test critical paths against the target engine.

### Q23. A sample compose file contains a production secret in an environment variable. What now?

**Answer:** Remove it, rotate if real, document the variable name, and use local ignored secrets.

### Q24. A PR builds an image, then the pipeline rebuilds before deploy. What reproducibility issue arises?

**Answer:** Deployed bytes may differ from tested bytes. Promote the immutable digest built and verified once.

### Q25. An architecture diagram omits the outbox and worker. What misunderstanding follows?

**Answer:** Readers miss consistency and failure paths. Show durable ownership and message flow, not only synchronous calls.

### Q26. A project has a circuit breaker but no timeout. Why might it not open?

**Answer:** Hung calls may never complete to count as failures. Bound calls and observe slow-call behavior.

### Q27. How do you know a performance fix did not shift work to the database?

**Answer:** Measure end-to-end latency and resource use across app, DB, and cache under representative load.

### Q28. A project uses latest tags for dependencies and images. What is hard to reproduce?

**Answer:** The same source may resolve different artifacts. Pin versions and record digests.

### Q29. Health is UP but there is no queue-age alert. What operational risk remains?

**Answer:** Background work may silently fall behind while process health is green. Expose freshness and backlog age.

### Q30. A portfolio project claims exactly-once processing. What question should you ask?

**Answer:** Exactly once across which boundary? Require scope and external-side-effect idempotency.

## References

- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Spring Petclinic sample application](https://github.com/spring-projects/spring-petclinic)
- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
