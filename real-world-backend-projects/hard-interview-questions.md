# Real-World Backend Projects: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Architecture and domain decisions

### Q1. How would you design a payment workflow that survives a timeout after the provider may have charged?

**Answer:** Persist a unique operation key and pending state, query or reconcile provider outcome, and make retries idempotent. Never assume timeout means failure.

### Q2. A booking system must prevent double allocation across multiple instances. Where does the invariant live?

**Answer:** Use a database constraint or atomic conditional reservation in the authoritative store. Application pre-checks are advisory and race under concurrency.

### Q3. How do you decide whether order and inventory belong in one service?

**Answer:** Map ownership, independent change, and consistency needs. If a single transaction is essential and teams are small, a modular boundary may be better than a network split.

### Q4. How would you make a file-processing project safe for very large uploads?

**Answer:** Use direct multipart object storage upload, persist metadata, validate checksums, scan asynchronously, and stream processing with bounded memory.

### Q5. How should a project represent money and currency at every API boundary?

**Answer:** Use decimal or integer minor units with an explicit currency code, validate scale, and never use binary floating point for financial totals.

### Q6. What is a practical way to keep a small project architecture reviewable?

**Answer:** Use explicit modules, one ownership path per data set, concise diagrams, and ADRs for non-obvious trade-offs. Avoid introducing infrastructure without a requirement.

### Q7. How would you partition an event pipeline for customer-level ordering?

**Answer:** Key by customer or aggregate ID, monitor skew, and define behavior for oversized customers. Ordering is guaranteed only within the selected partition scope.

### Q8. How do you separate the public API model from persistence entities in a portfolio project?

**Answer:** Use request/response DTOs and explicit mapping so database changes do not leak into the contract or expose internal fields.

### Q9. Why should a project define tenant isolation before adding tenant-specific filters?

**Answer:** Every query, cache key, job, and object path must scope by tenant. A late filter patch can leave cross-tenant data paths unprotected.

### Q10. How would you document an architecture choice that may change after a prototype?

**Answer:** Record assumption, options, decision, measurable validation, and a revisit trigger. Avoid presenting an experiment as a permanent universal rule.

## Data integrity and asynchronous workflows

### Q11. How would you publish a domain event after a SQL transaction without losing it?

**Answer:** Write an outbox row in the same transaction and relay it with at-least-once delivery. Make consumers idempotent and monitor relay lag.

### Q12. A retry may submit the same order twice. What should the service persist?

**Answer:** Store client idempotency key, request hash, current status, and response under a unique constraint. Reject same key with changed content.

### Q13. How would you implement a compensation when a downstream step fails after charging?

**Answer:** Issue a refund or void as a new idempotent business operation, track its state, and alert when compensation fails. Keep original audit history.

### Q14. How should a project handle a poison event that cannot be deserialized?

**Answer:** Quarantine raw bytes and safe metadata, alert with schema version, and continue only if ordering allows. Preserve a controlled replay procedure.

### Q15. How can you avoid lost updates when two APIs edit the same profile?

**Answer:** Add a version/ETag and require conditional updates; return a conflict when stale. Merge only fields with explicit domain semantics.

### Q16. What is the safest way to backfill a new derived column?

**Answer:** Deploy compatible writers, backfill in bounded batches, compare old/new values, switch readers, and retain rollback compatibility until verified.

### Q17. How should an inventory reservation expire if a scheduled cleanup is delayed?

**Answer:** Validate expiry in the authoritative command path and use a durable scheduled cleanup as secondary maintenance. Do not rely only on a cache TTL.

### Q18. How would you reconcile a local order with an external payment provider after outages?

**Answer:** Use stable provider references, query recent ambiguous states, compare amounts/status, and make repair actions auditable and idempotent.

### Q19. When should a project use an event log instead of only current state?

**Answer:** When replayable history or temporal audit is central. Plan schema evolution, retention, projection rebuilds, and operational tooling before committing.

### Q20. How do you keep an asynchronous read model visibly honest about lag?

**Answer:** Expose last processed offset/time or source version, label stale results, and define freshness SLOs. Do not imply synchronous consistency.

## Testing, security, and operations

### Q21. What tests prove a payment idempotency implementation under concurrency?

**Answer:** Run simultaneous same-key requests, assert one provider operation and one durable result, and test retries after ambiguous responses.

### Q22. How would you prove tenant data cannot leak through Redis?

**Answer:** Test keys include tenant scope, exercise cross-tenant reads and invalidation, and inspect serialization/API responses. Add authorization to origin queries too.

### Q23. What should a production-ready README explain beyond how to run the app?

**Answer:** Architecture, prerequisites, configuration, migrations, API examples, test commands, operational signals, known limits, and recovery steps.

### Q24. How do you show a project is secure without claiming it is production-proof?

**Answer:** Document threat model, authentication/authorization, secret handling, dependency checks, and known limitations. Provide reproducible evidence rather than broad claims.

### Q25. A service needs external API credentials in local development and cloud. How should it load them?

**Answer:** Use a local ignored secret mechanism for development and workload identity or secret manager in cloud. Share only variable names and setup guidance.

### Q26. How would you run integration tests without making them depend on a developer's local database?

**Answer:** Start isolated dependencies with Testcontainers or documented compose setup, apply migrations, and clean state. Pin versions and provide clear failure diagnostics.

### Q27. Which signals should be included in a project demo?

**Answer:** Show request volume, latency percentiles, errors, queue age, and a trace for a representative workflow. Avoid a dashboard that only displays host uptime.

### Q28. How do you safely deploy a schema change in a sample project?

**Answer:** Use versioned migrations, backward-compatible application steps, and a rollback or forward-fix plan. Test against the chosen production database engine.

### Q29. How should a project handle graceful shutdown of message consumers?

**Answer:** Stop polling, finish or safely release in-flight work, commit only completed offsets, and exit within the orchestrator's grace period.

### Q30. What belongs in an incident postmortem for a project outage?

**Answer:** Impact, timeline, contributing conditions, detection and response gaps, and owned follow-ups. Focus on system changes rather than blame.

## Performance, delivery, and interview explanation

### Q31. How do you prove a performance optimization was real?

**Answer:** Benchmark the representative path before and after with controlled data, compare latency distributions and resource cost, then validate under realistic load.

### Q32. A slow query only appears with production-sized data. How do you reproduce it safely?

**Answer:** Use anonymized shape-equivalent data, same indexes and plans, and bounded load. Avoid copying sensitive production rows into development.

### Q33. How can you explain an architecture trade-off in an interview without overselling it?

**Answer:** State constraints, alternatives, what you chose, the cost you accepted, and what evidence would change the decision.

### Q34. What makes a useful project contribution metric?

**Answer:** Tie it to user or system outcome such as error reduction, deployment time, or query latency, and explain the measurement window and attribution limits.

### Q35. How should a CI pipeline prevent publishing an untested commit?

**Answer:** Build once from the reviewed commit, run checks on that artifact, and publish only the recorded digest after required approvals.

### Q36. How do you choose a managed service versus self-hosting a component?

**Answer:** Compare operational burden, control, cost, portability, failure modes, and team expertise against actual requirements. Include exit and data-export plans.

### Q37. A project has no real traffic. How do you discuss capacity credibly?

**Answer:** Show explicit assumptions and load-test results, distinguish tested limits from estimates, and identify the next bottleneck rather than inventing production scale.

### Q38. Why should a technical demo include a failure path?

**Answer:** It shows retry, idempotency, and observability decisions, not only the happy path. Keep the failure reproducible and explain user impact.

### Q39. How do you prioritize a feature request against reliability work?

**Answer:** Compare user value, failure risk, SLO/error budget, and effort with stakeholders. Make the trade-off explicit and revisit it using evidence.

### Q40. What should you say when you do not know an answer about your own project's behavior?

**Answer:** State what you know, separate inference from verified fact, and explain how you would inspect logs, code, or a controlled test.


## References

- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Spring Petclinic sample application](https://github.com/spring-projects/spring-petclinic)
- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
