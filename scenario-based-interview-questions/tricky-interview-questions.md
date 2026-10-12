# Scenario-Based Interview Questions: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Java and runtime trick scenarios

### Q1. A method returns from try, but finally changes a field. Which value does the caller observe?

**Answer:** The return expression was evaluated before finally, so the caller gets that value; the field reflects the later mutation.

### Q2. Your stream count is correct but a peek counter is zero. Is the stream broken?

**Answer:** No. A sized stream may optimize count and elide intermediate stages. Side effects inside peek are not guaranteed.

### Q3. A synchronized counter misses increments. What hidden issue do you suspect?

**Answer:** Not every access uses the same monitor, or some path bypasses synchronization. Establish one happens-before discipline for all access.

### Q4. An Integer comparison passes on small values but fails on larger ones. Why?

**Answer:** == compares references; wrapper caching can make small values appear identical. Use value equality or primitives.

### Q5. A virtual-thread app has rising latency despite many threads. What finite resource do you inspect?

**Answer:** DB connections, remote quotas, semaphores, sockets, and carrier pinning. Cheap threads do not remove downstream limits.

### Q6. Two classes have the same name but a cast fails after plugin reload. What is likely?

**Answer:** They were defined by different class loaders. Type identity includes the defining loader.

### Q7. A process has free heap but is killed by its container. Why?

**Answer:** RSS includes native memory, stacks, direct buffers, and mapped files beyond Xmx. Check cgroup usage and native tracking.

### Q8. A Future times out but the HTTP server continues. Why?

**Answer:** The future timeout may not cancel underlying I/O. Configure client deadlines and propagate cancellation.

### Q9. A class fails on first use, then later reports NoClassDefFoundError. Why?

**Answer:** Static initialization may first throw ExceptionInInitializerError; the class is then marked erroneous for later use.

### Q10. A final field references a list that another thread changes. Is the containing object immutable?

**Answer:** No. Final fixes the reference, not list contents. Copy and expose an immutable snapshot.

## SQL and persistence trick scenarios

### Q11. A NOT IN query returns zero rows after import. What value do you look for?

**Answer:** A NULL in the subquery can make comparisons UNKNOWN. Rewrite as NOT EXISTS or exclude nulls.

### Q12. A LEFT JOIN drops customers with no orders. Where is the bug?

**Answer:** A WHERE predicate on the orders side filters null-extended rows. Move it to ON if unmatched customers must remain.

### Q13. Two transactions both pass a capacity SELECT and overbook. Why did READ COMMITTED not prevent it?

**Answer:** The check and insert are separate operations with no serialized invariant. Use a constraint, lock, or atomic update.

### Q14. saveAndFlush returns, but commit later fails. Is that impossible?

**Answer:** No. Deferred constraints or serialization checks can fail at commit. Commit is the success boundary.

### Q15. A page endpoint returns fewer unique roots after fetching a collection. Why?

**Answer:** SQL limit applies to multiplied child rows, then ORM deduplicates. Page root IDs first.

### Q16. A JDBC update is invisible in a loaded JPA entity. Why?

**Answer:** The persistence context retains its identity-mapped object; external SQL bypasses it. Refresh/clear and invalidate caches.

### Q17. A unique constraint allows duplicate rows with missing optional values. Is it broken?

**Answer:** Many databases treat NULLs as distinct for uniqueness. Define the rule with NOT NULL or a partial index.

### Q18. A query uses an index but is still slow. What next?

**Answer:** Check actual rows, heap page visits, selectivity, and buffers; scattered lookups can cost more than a scan.

### Q19. A read-only transaction sees different results in two SELECTs. Why?

**Answer:** Under READ COMMITTED each statement can see a new snapshot. Use stronger isolation if needed.

### Q20. A generated entity ID changes while it is in a HashSet. Why can contains fail?

**Answer:** hashCode may have changed since insertion, so lookup searches a different bucket.

## HTTP and distributed-system trick scenarios

### Q21. The client timed out, but a payment completed. What should its retry do?

**Answer:** Reuse the idempotency key and retrieve the existing outcome instead of creating another charge.

### Q22. A DELETE returns 404 on retry. Did it violate idempotence?

**Answer:** No. The intended resource state remains absent even though the response status differs.

### Q23. A JWT signature is valid but audience is wrong. Should it pass?

**Answer:** No. Validate that the token targets this API; signature alone is insufficient.

### Q24. A mesh retries POST after connection reset. What can go wrong?

**Answer:** The first request may have committed before reset, so retry duplicates effects. Require durable idempotency or disable the retry.

### Q25. Kafka lag is low, but results are stale. What metric is missing?

**Answer:** Oldest-message age or end-to-end processing time; record count alone misses delays.

### Q26. An outbox event appears twice after relay restart. Is the relay necessarily broken?

**Answer:** No. Publish-before-mark crash causes at-least-once duplicates. Consumers deduplicate by event ID.

### Q27. A cache is flushed and the database spikes. Why?

**Answer:** Cold misses arrive together and overload the origin. Coalesce reads, rate-cap, and warm gradually.

### Q28. Health is UP during a login outage. What might the check omit?

**Answer:** It may only test process or database. Monitor the critical auth path safely.

### Q29. Cross-region failover works but users see old data. What was missing?

**Answer:** An explicit RPO/lag policy and read routing. A promoted replica may be stale.

### Q30. An alert says no errors after collectors fail. Is that recovery?

**Answer:** Not proven. Check last-sample age, collector errors, and independent synthetic signals.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Spring transaction annotations](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)
- [Kafka documentation](https://kafka.apache.org/documentation/)
- [PostgreSQL EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)
- [AWS IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
