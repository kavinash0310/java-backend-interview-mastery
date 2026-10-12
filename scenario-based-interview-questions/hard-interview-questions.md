# Scenario-Based Interview Questions: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Latency, capacity, and JVM incidents

### Q1. p99 latency doubles after a JVM upgrade while average latency stays flat. How do you investigate?

**Answer:** Compare per-version JFR, GC/safepoints, CPU throttling, allocation, and request cohorts; canary rollback if the SLO is breached. Tail symptoms often point to a small slow cohort.

### Q2. CPU rises after adding a cache, but latency improves for most users. What do you check?

**Answer:** Inspect serialization, eviction, hashing, and cache synchronization; compare CPU per successful request and tail latency. Keep the cache only if total capacity remains safe.

### Q3. A service scales out but queue age keeps growing. What is your next decision?

**Answer:** Measure arrival and completion rates, partition skew, and downstream waits. If service capacity is below arrival rate, shed or defer work and fix the bottleneck.

### Q4. One tenant consumes most API capacity. How do you contain it without harming others?

**Answer:** Add per-tenant concurrency and rate budgets, identify the hot operation, and isolate expensive work. Preserve contractual priority and audit fairness.

### Q5. GC pauses are short, but requests still have long latency spikes. What evidence do you gather?

**Answer:** Correlate queue wait, safepoint time-to-enter, CPU throttling, lock contention, and downstream spans. GC duration alone may miss stop-the-world coordination or waiting.

### Q6. A new endpoint is slow only for large responses. How do you narrow the cause?

**Answer:** Compare serialization time, payload size, compression, allocations, and network transfer. Paginate or stream only after checking client contract and memory behavior.

### Q7. Container RSS exceeds the JVM heap by several gigabytes. What do you inspect?

**Answer:** Check direct buffers, thread stacks, metaspace, code cache, mapped files, native agents, and cgroup accounting. Capture Native Memory Tracking if enabled.

### Q8. A thread dump shows many BLOCKED threads but low CPU. What is the likely next step?

**Answer:** Find the monitor owner and lock acquisition path, then check whether a slow call occurs under that lock. Reduce critical-section scope and confirm with repeated dumps.

### Q9. Autoscaling triggers only after users see errors. How do you improve it?

**Answer:** Use leading signals such as queue age or saturation, retain warm headroom, and include instance readiness delay in capacity planning.

### Q10. The app has fast local tests but slow cold startup in Kubernetes. How do you diagnose?

**Answer:** Time image pull, JVM startup, class loading, bean creation, probes, and dependency waits separately. Optimize measured critical path rather than only JVM boot.

## Database and consistency incidents

### Q11. A query's plan changes after a statistics refresh and production latency worsens. What do you do?

**Answer:** Capture before/after plans and actual cardinalities, roll back risky plan inputs if possible, and inspect skew. Do not disable statistics globally.

### Q12. Database CPU reaches 100% but application CPU is low. How do you protect users first?

**Answer:** Limit expensive traffic and concurrency, identify top normalized queries and lock waits, and preserve critical operations. Avoid blindly adding app replicas.

### Q13. A write succeeds but the immediate read from a replica misses it. How do you respond?

**Answer:** Use primary or consistency-aware routing for read-after-write and expose replication lag. Do not claim the commit failed based on a stale read.

### Q14. A migration is blocking writes during peak traffic. What is the safest immediate action?

**Answer:** Assess lock state and rollback safety, stop or cancel the migration if safe, and restore write capacity. Then redesign as staged, bounded migration.

### Q15. Two users reserve the final item concurrently and both see success. How do you fix it?

**Answer:** Move the invariant into an atomic conditional update or constraint and return conflict for the loser. Add a concurrency test and idempotent retry behavior.

### Q16. A connection pool is exhausted but the database has spare CPU. What do you inspect?

**Answer:** Pool wait time, leaked connections, long transactions, lock waits, and slow clients. Increasing pool size may intensify contention rather than fix it.

### Q17. A backfill increases replica lag and slows customer reads. What do you change?

**Answer:** Throttle batch rate, shorten transactions, schedule lower-traffic windows, and monitor lag and read latency. Resume gradually with a pause control.

### Q18. An ORM change triples SQL statements but tests still pass. How do you catch it?

**Answer:** Add query-count or trace assertions around representative use cases and compare fetch plans. Functional correctness does not expose N+1 regressions.

### Q19. A hot row causes optimistic-lock retries to spiral. How do you stop amplification?

**Answer:** Limit retries and apply queueing or partitioning around the hot entity; consider a serialized command processor. Retrying every caller worsens contention.

### Q20. Data differs between primary and read model after a deploy. What evidence is needed?

**Answer:** Compare source transaction IDs, outbox offsets, consumer failures, and schema versions; pause destructive replay and reconcile from authority.

## Messaging and dependency failures

### Q21. Consumers are healthy but oldest message age keeps rising. What do you inspect?

**Answer:** Check per-partition throughput, traffic skew, downstream latency, retry volume, and max poll behavior. Healthy heartbeats do not prove processing capacity.

### Q22. A consumer reprocesses the same payment after restarting. What control prevents double charge?

**Answer:** Use a durable idempotency ledger or unique business operation key before calling the payment side effect. Reconcile ambiguous provider outcomes.

### Q23. A Kafka partition is stuck behind one poison record. How do you recover without silent loss?

**Answer:** Stop unbounded retry, capture the record and failure, quarantine or route it under an ordering policy, then repair and replay with audit.

### Q24. A downstream times out, then retries create a larger outage. What change do you make?

**Answer:** Introduce an end-to-end deadline, retry budget with jitter, bulkhead, and load shedding. Assign one layer ownership of retries.

### Q25. A circuit breaker stays open after the dependency recovers. How do you investigate?

**Answer:** Check half-open probe configuration, stale breaker instances, health criteria, and network path; use gradual probes and alert on breaker state.

### Q26. A queue is full during a traffic spike. What is the first product decision?

**Answer:** Define which work can be rejected, deferred, or degraded and protect critical flows. Then apply backpressure and communicate retry guidance.

### Q27. Messages arrive out of order after introducing retry topics. What is the recovery?

**Answer:** Confirm ordering key and partition mapping, then use sequence checks or per-key serialization. Reprocess affected entities from an authoritative event history.

### Q28. An outbox table grows despite successful publishing. What do you inspect?

**Answer:** Check relay checkpoint/marking failures, poison rows, lock contention, and cleanup policy. Keep rows until delivery is durable and auditable.

### Q29. A service mesh retries POST requests that are not idempotent. How do you stop duplicate effects?

**Answer:** Disable unsafe automatic retries or add a durable idempotency contract at the service. Mesh policy must reflect application semantics.

### Q30. Trace context disappears after a Kafka hop. Where do you look?

**Answer:** Inspect producer header injection, consumer extraction, instrumentation, and message wrapper transformations. Preserve context headers and link asynchronous spans.

## Security, cloud, and release incidents

### Q31. A production EC2 instance cannot read S3 after a role update. How do you diagnose without adding keys?

**Answer:** Check instance profile attachment, role trust, bucket and KMS policies, endpoint policy, region, and CloudTrail denial reason. Keep credential chain temporary.

### Q32. A deployment increases latency only in one Availability Zone. What signals help isolate it?

**Answer:** Compare target health, zonal traffic, network and dependency metrics, instance type, and recent capacity changes. Shift traffic only after confirming safe headroom elsewhere.

### Q33. A readiness probe passes but customer requests fail authentication. What is missing?

**Answer:** The probe checks process reachability, not end-to-end auth configuration. Add a safe synthetic check for required security wiring and inspect secrets/issuer connectivity.

### Q34. An image scanner finds a critical library in a release candidate. What is the release decision?

**Answer:** Identify exploitability and exposure, update or mitigate, rebuild the immutable artifact, and preserve evidence. Do not waive solely because tests pass.

### Q35. A rollback restores the old binary but the service still fails. What common cause do you check?

**Answer:** Database or event schema changes may not be backward-compatible, and external config may remain changed. Design expand-and-contract and rollback all release inputs.

### Q36. A cloud bill spikes after a feature launch. How do you isolate the cause?

**Answer:** Break cost by service, tag, region, request volume, data transfer, and storage; correlate with deployment and traffic. Contain runaway resources before optimizing.

### Q37. A secret appears in a debug log. What are the immediate steps?

**Answer:** Restrict log access, rotate or revoke the credential, preserve incident evidence, and remove or redact exposure where policy allows. Fix the logging path and search retention.

### Q38. One region loses connectivity to the primary database. Should you promote a replica immediately?

**Answer:** Compare RTO/RPO, replication lag, and split-brain risk; follow the failover runbook and establish write ownership before promotion.

### Q39. A new HPA doubles replicas and database connections. What guardrail is missing?

**Answer:** Cap app concurrency or connection pools and scale on useful demand signals. Autoscaling must account for downstream capacity and reserved headroom.

### Q40. An alert storm begins during a regional incident. How do you keep responders focused?

**Answer:** Group by shared cause, page on user-facing SLO and critical saturation, and suppress only verified duplicates. Keep a timeline and preserve actionable signals.


## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Spring transaction annotations](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)
- [Kafka documentation](https://kafka.apache.org/documentation/)
- [PostgreSQL EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)
- [AWS IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
