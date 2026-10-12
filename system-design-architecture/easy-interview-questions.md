# System Design and Architecture: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. How do you start a system design interview?

**Answer:** Clarify users, use cases, scale, latency, availability, and constraints before drawing. Then outline the simplest architecture that meets them.

### Q2. Functional vs non-functional requirements?

**Answer:** Functional requirements describe what the system does; non-functional requirements describe qualities such as latency, availability, security, or cost.

### Q3. What is a capacity estimate?

**Answer:** A rough calculation of traffic, storage, and bandwidth to shape the design. State assumptions and use ranges.

### Q4. Average traffic vs peak traffic?

**Answer:** Average traffic describes normal load; peak traffic describes busy intervals the system must survive. Size bottlenecks for peak and burst patterns.

### Q5. How do you estimate storage?

**Answer:** Estimate records per day times average size and retention, then include indexes, replicas, and growth. State what overhead is included.

### Q6. What is a load balancer?

**Answer:** A load balancer distributes incoming requests among healthy application instances. It gives clients a stable entry point and can stop sending traffic to unhealthy targets.

### Q7. What is a read replica?

**Answer:** A database copy that serves reads. Replication lag can make recent writes temporarily invisible there.

### Q8. What is database sharding?

**Answer:** Splitting data across nodes using a shard key. It increases scale but complicates cross-shard queries, rebalancing, and transactions.

### Q9. Vertical vs horizontal scaling?

**Answer:** Vertical scaling adds resources to a machine; horizontal scaling adds machines or instances. Horizontal scale requires distributed application and data paths.

### Q10. What is a CDN?

**Answer:** A Content Delivery Network serves cacheable content from locations closer to users. It suits static or explicitly cacheable responses.

### Q11. When should a design use a message queue?

**Answer:** When work can happen asynchronously, consumers need independent scaling, or buffering helps. Define delivery, retry, and ordering behavior.

### Q12. How do you change a database schema safely?

**Answer:** Use expand-and-contract: add a compatible shape, deploy code supporting both, migrate data, then remove the old shape in a later release.

### Q13. Availability vs durability?

**Answer:** Availability is ability to serve requests; durability is ability to preserve committed data. A system can have one without the other.

### Q14. What is CAP theorem in practice?

**Answer:** During a network partition, a distributed data system chooses between rejecting some requests to preserve consistency or serving with possible stale data.

### Q15. How do you choose a partition key?

**Answer:** Choose a key that spreads load while grouping common related operations. A poor key creates hot partitions or expensive cross-partition queries.

### Q16. Why define a data retention policy?

**Answer:** It controls storage duration and can reduce cost, risk, and operational load. Retention must meet product, legal, and recovery needs.

### Q17. How would you design file uploads?

**Answer:** Upload to object storage using a scoped, short-lived authorization, then validate size and content before publishing. Store metadata separately.

### Q18. How would you design autocomplete?

**Answer:** Normalize and index terms, limit prefix results, and cache popular queries. Clarify whether suggestions can be slightly stale.

### Q19. How would you design booking for limited seats?

**Answer:** Claim capacity atomically in durable storage, expire abandoned holds, and make payment retries idempotent.

### Q20. What is an audit log?

**Answer:** An append-oriented record of who changed what and when. Protect it from silent edits and exclude secrets or unnecessary personal data.

### Q21. How do you support multiple tenants?

**Answer:** Enforce tenant identity in authorization and data access. Choose shared tables, schemas, or infrastructure based on isolation needs.

### Q22. How do you prevent a slow dependency from exhausting a service?

**Answer:** Bound connections and concurrency, set timeouts, and isolate calls with a bulkhead or breaker. Monitor the dependency separately.

### Q23. What is graceful degradation?

**Answer:** Keeping essential behavior available while reducing or pausing a nonessential feature. Define what can be stale, delayed, or omitted.

### Q24. What is an API rate limiter?

**Answer:** A control that caps request volume for an identity or resource over time.

**Example:** A token bucket can allow brief bursts.

### Q25. What is the token bucket algorithm?

**Answer:** Tokens refill at a set rate, and each request spends one; empty buckets reject or delay work.

**Example:** Set capacity 10 and refill 2 tokens per second.

### Q26. What is a cache in system design?

**Answer:** A faster store of reusable data that reduces load on a slower source.

**Example:** Cache popular product details near the API.

### Q27. What is cache invalidation?

**Answer:** Removing or refreshing cached data when its source changes or expires.

**Example:** Evict a product key after a successful update.

### Q28. What is a write-through cache?

**Answer:** A write updates the cache and backing store as part of the chosen write path.

**Example:** The application writes through to the database before acknowledging.

### Q29. What is a message queue?

**Answer:** A buffer that lets producers and consumers process work at different rates.

**Example:** Queue email delivery outside the checkout request.

### Q30. What is load shedding?

**Answer:** Rejecting low-priority work when a system is overloaded to preserve important functions.

**Example:** Return 503 for expensive reports while protecting checkout.

### Q31. What is a circuit breaker in system design?

**Answer:** A mechanism that stops repeated calls to an unhealthy dependency and permits a later recovery probe.

**Example:** Open the circuit after repeated timeouts.

### Q32. What is database replication?

**Answer:** Keeping copies of data on multiple database nodes, often for availability or read scaling.

**Example:** A read replica can serve selected read queries.

### Q33. What is a hot partition?

**Answer:** A partition that receives far more traffic or data than others.

**Example:** A single popular account ID may overload its shard.

### Q34. What is consistent hashing?

**Answer:** A key-placement approach that can reduce how many keys move when nodes are added or removed.

**Example:** It is useful for distributed caches.

### Q35. What is data partitioning?

**Answer:** Dividing a dataset into smaller pieces by a key or range.

**Example:** Partition event records by month.

### Q36. What is a read-after-write consistency need?

**Answer:** A user expects to see their own successful update immediately on a later read.

**Example:** Route that user's next read to the primary or session-consistent path.

### Q37. What is a soft delete?

**Answer:** Marking a record as deleted rather than physically removing it immediately.

**Example:** Keep `deleted_at` for recovery or audit needs.

### Q38. What is an outbox table?

**Answer:** A table written in the same database transaction as business data, later relayed as an event.

**Example:** It prevents a committed order from losing its publish event.

### Q39. How do you design for duplicate requests?

**Answer:** Make the operation idempotent using a client key or unique business identifier.

**Example:** Store the result for an idempotency key.

### Q40. What is a graceful shutdown in a distributed service?

**Answer:** Stop accepting new requests, finish or safely cancel in-flight work, and close dependencies within a bounded time.

**Example:** Honor the platform termination grace period.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [PostgreSQL indexes](https://www.postgresql.org/docs/current/indexes.html)
- [AWS guidance: partitioning](https://docs.aws.amazon.com/whitepapers/latest/scale-performance-best-practices/partitioning.html)
- [AWS guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
