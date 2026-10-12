# Scenario-Based Interview Questions: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](production-scenarios.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Latency, capacity, and runtime behavior

### Q1. p99 latency doubles after a JDK upgrade, but average latency is unchanged. How do you investigate?

**Answer:** Compare p50/p95/p99 by endpoint, inspect GC/JFR and thread states, and check runtime flags plus dependency versions before rollback.

**Example:** Use matched traffic windows and annotate the deployment.

### Q2. Autoscaling adds instances but queue age keeps rising. What next?

**Answer:** Check whether consumers are actually processing faster, whether a downstream system is the bottleneck, and whether scaling signals reflect backlog.

**Example:** Do not add workers if they only amplify DB pressure.

### Q3. CPU is low, yet request threads are waiting for seconds. What do you inspect?

**Answer:** Look at blocked thread stacks, connection pools, locks, DNS/network waits, and downstream spans.

**Example:** Thread dumps can distinguish blocked from runnable threads.

### Q4. A single tenant causes most database traffic. How do you mitigate without harming others?

**Answer:** Confirm tenant-level usage, apply fair limits or workload isolation, and optimize the hot access pattern.

**Example:** Use a per-tenant queue or quota if business rules allow.

### Q5. GC pause time rises after adding a feature while heap use stays stable. What do you check?

**Answer:** Inspect allocation churn, object lifetimes, collector logs, and the feature's data structures; stable occupancy does not rule out frequent collections.

**Example:** Profile allocations during representative requests.

### Q6. A process uses more CPU after introducing compression. How do you decide whether to keep it?

**Answer:** Compare network savings with CPU, p95/p99 latency, and total cost under realistic payloads.

**Example:** Use compression only where the end-to-end trade-off improves.

### Q7. A thread pool looks healthy by active count, but users see delays. What signal may be missing?

**Answer:** Measure queue wait time, task age, rejected work, and downstream wait; active count alone misses queued latency.

**Example:** Add a metric for oldest queued task.

### Q8. A large file upload causes memory spikes. What design change helps?

**Answer:** Stream data to object storage or disk with size limits instead of buffering the entire body in heap.

**Example:** Test backpressure and cleanup on client disconnect.

### Q9. A scheduled report misses its deadline only on month-end. How do you investigate?

**Answer:** Compare data volume, query plans, lock contention, and concurrent batch work with ordinary days.

**Example:** Add a workload and runtime timeline around month-end.

### Q10. A cache hit rate is high but database load still rises. What do you check?

**Answer:** Look at hot-key traffic, miss cost, write-through queries, cache evictions, and whether cached reads match the database workload.

**Example:** Break metrics down by operation class, not raw IDs.

## Data consistency and async processing

### Q11. A database save succeeds, but the next read returns old data. What do you investigate?

**Answer:** Check whether the read is routed to a lagging replica or cache and confirm the committed version.

**Example:** Use a primary read or version-aware consistency path when required.

### Q12. A transaction deadlock rate rises after a new workflow. What is your response?

**Answer:** Capture deadlock graphs, compare lock acquisition order, shorten transactions, and apply a bounded retry for victim transactions.

**Example:** Make retries idempotent and fix inconsistent lock ordering.

### Q13. A unique constraint starts rejecting valid requests after a normalization change. What do you inspect?

**Answer:** Compare canonicalization rules, existing data, collation, and the exact constraint columns.

**Example:** Backfill normalized keys before enforcing a stricter rule.

### Q14. An outbox relay publishes events twice after restarting. How do you protect consumers?

**Answer:** Use stable event IDs and idempotent consumer writes; inspect relay checkpoint timing and acknowledgment order.

**Example:** Persist the event ID with a unique constraint in the consumer transaction.

### Q15. A consumer acknowledges a message before its database transaction commits. What failure can occur?

**Answer:** A crash can lose the business effect while the message is considered complete.

**Example:** Commit the offset after durable processing or use an atomic strategy.

### Q16. A delayed retry allows later records to overtake an earlier one. Is that acceptable?

**Answer:** It depends on the key's ordering requirement; if order matters, pause that partition or design ordered retry handling.

**Example:** State the per-customer ordering guarantee explicitly.

### Q17. A backfill competes with live traffic and causes errors. How do you control it?

**Answer:** Throttle batches, monitor production saturation, use resumable checkpoints, and pause automatically on impact.

**Example:** Make the job safe to restart from its last committed cursor.

### Q18. A database migration has to add a required field to a huge table. How do you roll it out?

**Answer:** Add it nullable/default-compatible, dual-write or backfill in batches, validate, then enforce requiredness after old app versions are gone.

**Example:** Avoid a blocking one-step rewrite.

### Q19. A replayed event overwrites newer state. How can ordering be enforced?

**Answer:** Include an entity version or sequence and reject older updates at the consumer boundary.

**Example:** Compare incoming sequence with the stored version.

### Q20. An API idempotency key is reused with a different payload. What should happen?

**Answer:** Reject the reuse as a conflict by comparing a stored request fingerprint.

**Example:** Do not return the prior result for a different operation.

## Security and dependency incidents

### Q21. A JWT begins failing after signing-key rotation. What do you check?

**Answer:** Verify issuer key publication, key ID selection, JWKS cache refresh, clock skew, and overlap between old and new keys.

**Example:** Keep old public keys available until old tokens expire.

### Q22. A service works with AWS credentials locally but fails on EC2. What do you inspect?

**Answer:** Check the instance profile attachment, role trust policy, permission policy, region, and credential-provider chain.

**Example:** Use temporary role credentials rather than copying local keys.

### Q23. A certificate expires during a deployment. What is the safest response?

**Answer:** Restore valid certificate material or roll back the cert change, then verify the full chain and client trust.

**Example:** Monitor expiry ahead of time and rehearse renewal.

### Q24. A browser security report says the API allows arbitrary origins. How do you fix it?

**Answer:** Replace wildcard or reflected origin rules with an explicit allowlist and review credentialed requests.

**Example:** Test preflight and actual requests from allowed and denied origins.

### Q25. A third-party API returns 429 for your service. How do you respond?

**Answer:** Honor Retry-After, reduce concurrency, apply a shared rate limit, and queue safe work if the product permits delay.

**Example:** Avoid synchronized retries across instances.

### Q26. A secret appears in a log aggregator. What is the incident sequence?

**Answer:** Restrict access, rotate the credential, identify exposure window and consumers, remove or expire log copies where possible, and record the incident.

**Example:** Prevent recurrence with redaction tests and secret scanning.

### Q27. A dependency vulnerability has no patched version yet. What do you do?

**Answer:** Assess reachable exposure, apply vendor mitigations or disable the affected path, monitor advisories, and plan a tested upgrade.

**Example:** Document residual risk and an owner.

### Q28. A service-to-service call is authorized in staging but denied in production. What do you compare?

**Answer:** Compare workload identity, audience/scope claims, IAM or policy attachments, network path, and environment config.

**Example:** Do not widen permissions globally as a first fix.

### Q29. A user can access another tenant's object by changing its ID. How do you contain and fix it?

**Answer:** Disable or restrict the endpoint if needed, audit access, then enforce ownership in the data query and service authorization.

**Example:** Test cross-tenant denial as a regression case.

### Q30. New security headers break a client integration. How do you roll them out?

**Answer:** Identify the header and client behavior, deploy a compatible policy with report-only or staged validation, then enforce intentionally.

**Example:** Do not remove unrelated protections blindly.

## Deployment, observability, and recovery

### Q31. A blue-green switch succeeds but background workers process jobs twice. What caused it?

**Answer:** Both environments may have active consumers or schedulers; coordinate worker ownership during cutover.

**Example:** Use leases, consumer-group control, or idempotent job keys.

### Q32. A Kubernetes rollout repeatedly restarts new Pods before they become ready. What do you inspect?

**Answer:** Check startup duration, liveness timing, resource limits, termination behavior, and application logs.

**Example:** Use a startup probe for legitimately slow initialization.

### Q33. New metrics show thousands of time series after release. What is the likely design issue?

**Answer:** A high-cardinality label such as user ID, request ID, or raw URL was added.

**Example:** Remove unbounded labels and keep those identifiers in logs or traces.

### Q34. A distributed trace stops at an asynchronous worker. What do you check?

**Answer:** Confirm trace headers are copied into the message and the consumer extracts context before creating its span.

**Example:** Preserve trace context through queue metadata.

### Q35. Log volume triples without a traffic increase. How do you reduce cost safely?

**Answer:** Identify new repeated events or retry loops, sample routine logs, keep errors and counters, and fix the source.

**Example:** Check whether a debug level was enabled in production.

### Q36. A backup job reports success, but a restore fails. What control was missing?

**Answer:** Regular restore validation and end-to-end recovery tests were missing.

**Example:** Restore into an isolated environment and measure recovery time.

### Q37. A feature flag is turned off, but the error continues. What do you investigate?

**Answer:** Check whether every instance received the flag, whether cached config is stale, and whether the failure path is outside the flag.

**Example:** Verify effective flag state per version.

### Q38. A rollback reduces errors but old code cannot read new rows. What does this reveal?

**Answer:** The schema or data format change was not backward compatible with the rollback version.

**Example:** Use an expand-and-contract change and test mixed-version reads.

### Q39. A readiness probe starts failing only under peak load. What is more useful than raising its timeout?

**Answer:** Inspect the probe's dependencies, CPU throttling, request queue, and whether readiness work is too expensive.

**Example:** Keep probes lightweight and separate them from deep diagnostics.

### Q40. An incident spans three services with different clocks. How do you reconstruct order?

**Answer:** Use trace/span relationships, synchronized timestamps, request IDs, and broker offsets rather than wall-clock alone.

**Example:** Include monotonic durations within each process.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Spring transaction annotations](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)
- [Kafka documentation](https://kafka.apache.org/documentation/)
- [PostgreSQL EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)
- [AWS IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
