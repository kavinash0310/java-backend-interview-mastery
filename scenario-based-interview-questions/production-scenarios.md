# Production Scenario Practice: 40 Easy Q&A

Use a calm sequence: clarify impact, inspect evidence, mitigate safely, find the cause, and prevent recurrence. Adapt each answer to the system and your own experience.

### Q1. Several threads access a HashMap. What could go wrong?

**Answer:** Concurrent updates can be lost or observed inconsistently because HashMap does not coordinate mutation. Use a concurrent map for independent operations or a lock for a multi-step invariant.

**Example:** See the [Concurrency guide](../multithreading-concurrency/interview-questions.md).

### Q2. Two payment requests arrive together and charge twice. What do you do?

**Answer:** Give the operation an idempotency key and claim it atomically in durable storage before charging. A process-local lock cannot coordinate multiple instances.

**Example:** See the [API design guide](../system-design-architecture/interview-questions.md).

### Q3. Thread count spikes and CPU usage rises. How do you investigate?

**Answer:** Compare thread dumps, executor queues, CPU profiles, and request volume to see whether threads are running, blocked, or retrying. Stop unbounded thread creation.

### Q4. Two requests update the same database row. How do you prevent lost updates?

**Answer:** Use a version field for optimistic locking or a database lock when conflicts are frequent and the critical section is short. Report or retry conflicts rather than overwriting silently.

### Q5. Startup grows from 15 seconds to two minutes. What is your first move?

**Answer:** Compare startup timing across bean creation, scanning, migrations, and external calls. Determine whether the delay is CPU, network, or waiting before changing settings.

### Q6. A circular dependency appears after deployment. How do you resolve it?

**Answer:** Trace the responsibilities and extract a smaller collaborator or move orchestration into a third service. Lazy injection can hide a cycle without fixing design.

### Q7. LazyInitializationException appears only in production. Why?

**Answer:** Code accesses a lazy relationship after the persistence context closes. Load required data inside the transaction with a deliberate query or return a DTO.

### Q8. A transaction partly updates data although rollback was expected. What do you check?

**Answer:** Check whether the failure is checked, caught, or outside the proxy, and confirm writes use the same transaction manager. Spring defaults differ by exception type.

### Q9. The application throws OutOfMemoryError. What evidence do you collect?

**Answer:** Identify heap versus native or thread exhaustion from the error, capture a dump if safe, and compare trends and GC logs. Look for retained objects or unbounded resources.

### Q10. A downstream service is down and callers begin timing out. How do you contain impact?

**Answer:** Set timeouts and concurrency limits, stop futile retries, and isolate the dependency. Return a fallback only when the operation allows it.

### Q11. A Kafka record is processed twice and creates duplicate work. How do you fix it?

**Answer:** Assume redelivery is possible and make side effects idempotent, often by recording a processed message ID in the same transaction as the business change.

### Q12. One Kafka partition is much busier than others. What do you inspect?

**Answer:** Check key distribution for a hot key and compare per-partition traffic. Change partitioning only with a plan for ordering and migration.

### Q13. A Kafka message fails repeatedly. What should the consumer do?

**Answer:** Separate transient from permanent failures, retry with a bounded policy, then route poison messages to a dead-letter path with diagnostic metadata.

### Q14. A database query slows from 50 ms to 10 seconds. How do you debug it?

**Answer:** Compare execution plans, row counts, parameter values, locks, and recent schema or data changes. Test with representative parameters before adding an index.

### Q15. Database CPU reaches 100% at peak. How do you respond?

**Answer:** Find expensive queries and waits, reduce unnecessary work, and protect the database with limits or valid caching. Scale after identifying the bottleneck.

### Q16. A table grows to hundreds of millions of rows. How do you keep it usable?

**Answer:** Use selective indexes and bounded queries, define retention/archive policy, and consider partitioning if measured access patterns support it. Avoid huge in-memory result sets.

### Q17. An API receives 100,000 requests per minute. What do you scale first?

**Answer:** Measure traffic mix and bottlenecks, then scale stateless app capacity and downstream limits together. Add caching, queueing, or autoscaling only where evidence supports it.

### Q18. Clients abuse an API. What controls do you add?

**Answer:** Rate-limit by authenticated identity, set request-size limits, and return 429 with retry guidance where appropriate. Monitor for false positives.

### Q19. Redis goes down. What should happen?

**Answer:** If it is a cache, use a controlled database fallback; if it holds essential coordination state, fail affected operations safely. Protect the database from fallback overload.

### Q20. An EC2 instance needs S3 without stored credentials. What do you configure?

**Answer:** Attach an instance profile with a least-privilege IAM role so the SDK obtains temporary credentials. Scope access to the needed bucket and actions.

### Q21. A release causes latency to rise. What is a safe response?

**Answer:** Compare version-level latency, errors, traces, and dependency metrics; stop or roll back if user impact is clear. Preserve evidence for the root-cause review.

### Q22. Kafka consumer lag grows with no errors. What do you check?

**Answer:** Measure processing time, incoming rate, partition assignment, and downstream latency. Add consumers only if there are enough partitions and capacity downstream.

### Q23. The database connection pool is exhausted. How do you diagnose it?

**Answer:** Check active, idle, pending connections and query/transaction duration. Find leaks or long-held transactions before increasing pool size.

### Q24. A thread-local user ID appears on another request. What is the likely cause?

**Answer:** A pooled thread was reused without clearing its ThreadLocal. Clear request context in finally or use framework-managed propagation.

### Q25. A schema migration locks a busy table. What do you change?

**Answer:** Use expand-and-contract, check the database's online DDL behavior, and deploy compatible steps. Test lock duration with realistic data.

### Q26. A popular cache key expires and database traffic spikes. What helps?

**Answer:** Coalesce concurrent misses so one request refreshes while others wait briefly or use stale data. Add TTL jitter and cap fallback concurrency.

### Q27. A background job runs twice after adding a replica. How do you fix it?

**Answer:** Use a durable scheduler or atomic lease and make the job safe to retry. A process-local timer cannot coordinate a cluster.

### Q28. Browser calls fail with CORS but curl works. What do you inspect?

**Answer:** Check the preflight response for allowed origin, method, and headers. Configure CORS for intended origins; it is not an authentication fix.

### Q29. Readiness checks fail during a slow but healthy startup. How do you adjust them?

**Answer:** Use a startup probe or appropriate initial delay and keep readiness false until traffic is safe. Do not weaken liveness until stuck processes persist.

### Q30. New and old service versions disagree on an event field. How do you restore compatibility?

**Answer:** Make the schema additive, deploy consumers accepting both forms, then roll out producers and remove old fields after compatibility is confirmed. Check contracts in CI.

### Q31. A release makes error rates rise. What do you do first?

**Answer:** Confirm scope and impact, compare with the release timeline, then roll back or disable the change if that is the safest mitigation.

**Example:** Preserve logs and metrics before the rollback.

### Q32. A user reports one failed payment but logs show success. How do you investigate?

**Answer:** Trace by payment or request ID across the API, provider callback, and database before retrying anything.

**Example:** Check whether a timeout hid a provider success.

### Q33. A queue is growing quickly. What do you check?

**Answer:** Compare arrival and processing rates, consumer health, retries, and downstream bottlenecks.

**Example:** Scale consumers only if the downstream system can handle more load.

### Q34. A deploy starts failing readiness probes. What do you inspect?

**Answer:** Check startup logs, dependency readiness, probe path and timing, and recent configuration or image changes.

**Example:** Use a startup probe if initialization legitimately takes longer.

### Q35. An API returns 500 only for one customer. How do you debug?

**Answer:** Reproduce with that request context safely, compare data shape and tenant configuration, and inspect correlated logs.

**Example:** Mask sensitive fields when capturing an example.

### Q36. A retry creates a duplicate order. What design change helps?

**Answer:** Give each client operation an idempotency key and enforce uniqueness at the durable boundary.

**Example:** Return the original order result for a repeated key.

### Q37. A database pool is exhausted but database CPU is low. What do you check?

**Answer:** Look for long-held connections, slow transactions, blocked queries, and leaked connections.

**Example:** Compare active connections with request traces.

### Q38. A dashboard shows high average latency but normal p95. What could explain it?

**Answer:** Check aggregation windows, sample counts, outliers, and whether the dashboard uses the same endpoint and time range.

**Example:** A few extreme values can move an average.

### Q39. A customer sees another user's data. What is the immediate response?

**Answer:** Contain exposure, preserve evidence, notify the responsible incident and security teams, and identify affected records and requests.

**Example:** Do not reproduce using real customer data unnecessarily.

### Q40. A scheduled job skipped a run. How do you respond?

**Answer:** Check scheduler state, ownership/leader election, last-run metadata, and job logs; rerun only if the operation is safe.

**Example:** Use an idempotent job key to prevent duplicate effects.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Spring transaction annotations](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)
- [Kafka documentation](https://kafka.apache.org/documentation/)
- [PostgreSQL EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)
- [AWS IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
