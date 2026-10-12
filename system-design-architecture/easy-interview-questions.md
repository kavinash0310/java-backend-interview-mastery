# System Design and Architecture: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q08. How do you start a system design interview?

**Answer:** Clarify users, use cases, scale, latency, availability, and constraints before drawing. Then outline the simplest architecture that meets them.

### Q09. Functional vs non-functional requirements?

**Answer:** Functional requirements describe what the system does; non-functional requirements describe qualities such as latency, availability, security, or cost.

### Q10. What is a capacity estimate?

**Answer:** A rough calculation of traffic, storage, and bandwidth to shape the design. State assumptions and use ranges.

### Q11. Average traffic vs peak traffic?

**Answer:** Average traffic describes normal load; peak traffic describes busy intervals the system must survive. Size bottlenecks for peak and burst patterns.

### Q12. How do you estimate storage?

**Answer:** Estimate records per day times average size and retention, then include indexes, replicas, and growth. State what overhead is included.

### Q13. What is a load balancer?

**Answer:** A load balancer distributes incoming requests among healthy application instances. It gives clients a stable entry point and can stop sending traffic to unhealthy targets.

### Q14. What is a read replica?

**Answer:** A database copy that serves reads. Replication lag can make recent writes temporarily invisible there.

### Q15. What is database sharding?

**Answer:** Splitting data across nodes using a shard key. It increases scale but complicates cross-shard queries, rebalancing, and transactions.

### Q16. Vertical vs horizontal scaling?

**Answer:** Vertical scaling adds resources to a machine; horizontal scaling adds machines or instances. Horizontal scale requires distributed application and data paths.

### Q17. What is a CDN?

**Answer:** A Content Delivery Network serves cacheable content from locations closer to users. It suits static or explicitly cacheable responses.

### Q18. When should a design use a message queue?

**Answer:** When work can happen asynchronously, consumers need independent scaling, or buffering helps. Define delivery, retry, and ordering behavior.

### Q19. How do you change a database schema safely?

**Answer:** Use expand-and-contract: add a compatible shape, deploy code supporting both, migrate data, then remove the old shape in a later release.

### Q20. Availability vs durability?

**Answer:** Availability is ability to serve requests; durability is ability to preserve committed data. A system can have one without the other.

### Q21. What is CAP theorem in practice?

**Answer:** During a network partition, a distributed data system chooses between rejecting some requests to preserve consistency or serving with possible stale data.

### Q22. How do you choose a partition key?

**Answer:** Choose a key that spreads load while grouping common related operations. A poor key creates hot partitions or expensive cross-partition queries.

### Q23. Why define a data retention policy?

**Answer:** It controls storage duration and can reduce cost, risk, and operational load. Retention must meet product, legal, and recovery needs.

### Q24. How would you design file uploads?

**Answer:** Upload to object storage using a scoped, short-lived authorization, then validate size and content before publishing. Store metadata separately.

### Q25. How would you design autocomplete?

**Answer:** Normalize and index terms, limit prefix results, and cache popular queries. Clarify whether suggestions can be slightly stale.

### Q26. How would you design booking for limited seats?

**Answer:** Claim capacity atomically in durable storage, expire abandoned holds, and make payment retries idempotent.

### Q27. What is an audit log?

**Answer:** An append-oriented record of who changed what and when. Protect it from silent edits and exclude secrets or unnecessary personal data.

### Q28. How do you support multiple tenants?

**Answer:** Enforce tenant identity in authorization and data access. Choose shared tables, schemas, or infrastructure based on isolation needs.

### Q29. How do you prevent a slow dependency from exhausting a service?

**Answer:** Bound connections and concurrency, set timeouts, and isolate calls with a bulkhead or breaker. Monitor the dependency separately.

### Q30. What is graceful degradation?

**Answer:** Keeping essential behavior available while reducing or pausing a nonessential feature. Define what can be stale, delayed, or omitted.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [PostgreSQL indexes](https://www.postgresql.org/docs/current/indexes.html)
- [AWS guidance: partitioning](https://docs.aws.amazon.com/whitepapers/latest/scale-performance-best-practices/partitioning.html)
- [AWS guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
