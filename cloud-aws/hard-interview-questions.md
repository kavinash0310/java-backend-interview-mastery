# Cloud and AWS: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Identity, network, and security

### Q1. How should an EC2 workload get temporary AWS credentials without embedded keys?

**Answer:** Attach an instance profile role with least-privilege permissions; the SDK retrieves rotating credentials. Restrict instance metadata access and avoid forwarding credentials to untrusted processes.

### Q2. Why can a VPC endpoint reduce both exposure and NAT cost?

**Answer:** It routes supported service traffic privately without traversing an internet gateway or NAT. Endpoint policies and security groups still need least-privilege review.

### Q3. How do security groups and network ACLs differ during troubleshooting?

**Answer:** Security groups are stateful and attached to resources; network ACLs are stateless subnet filters. Check both directions and ephemeral return ports.

### Q4. What is the risk of granting s3:* on all resources to one application role?

**Answer:** A compromised workload can read, overwrite, or delete unrelated buckets. Scope actions, resources, prefixes, and conditions to the workload's actual needs.

### Q5. How can cross-account access avoid long-lived credentials?

**Answer:** Let a workload assume a narrowly scoped role in the target account with an external ID or trust condition where appropriate. Audit the trust and session duration.

### Q6. Why is a private subnet not sufficient protection for an exposed service?

**Answer:** It may still receive routed traffic through load balancers, peering, or misconfigured rules. Review the entire path, security groups, routes, and identity policy.

### Q7. How should IAM policy changes be validated before production?

**Answer:** Use policy simulation and least-privilege access analysis, then deploy a canary role and monitor denied/allowed actions. Test both required and forbidden actions.

### Q8. What is the security boundary of an EC2 instance profile?

**Answer:** Processes able to access instance credentials can use that role. Isolate workloads, restrict metadata hops, and do not place mutually untrusted apps on one host.

### Q9. How can KMS key policy and IAM policy interact to deny an apparently permitted action?

**Answer:** Both the key policy/grants and caller IAM permissions must allow the use, subject to explicit denies and boundaries. Inspect the full authorization path.

### Q10. Why should CloudTrail data events be enabled selectively?

**Answer:** They provide object-level audit detail but can add cost and volume. Enable for sensitive buckets or actions and set retention and alerting intentionally.

## Compute, storage, and databases

### Q11. When is an ALB a better fit than an NLB?

**Answer:** Use ALB for HTTP-aware routing, host/path rules, and web integrations; NLB suits high-performance TCP/UDP and static IP use cases. Match protocol needs.

### Q12. How do EC2 Auto Scaling health checks create a false replacement loop?

**Answer:** A too-aggressive check may mark slow-starting instances unhealthy before warmup completes. Align grace periods, load balancer health, and application initialization.

### Q13. What does Multi-AZ RDS protect against, and what does it not provide?

**Answer:** It supports failover across availability zones, but it is not a read-scaling mechanism or backup replacement. Plan client reconnect and recovery behavior.

### Q14. How do read replicas change read-after-write semantics?

**Answer:** Replication lag can make a recent write invisible on a replica. Route critical reads to the writer or use a consistency policy.

### Q15. Why should S3 object keys avoid embedding secrets or personal data?

**Answer:** Object paths appear in logs, metrics, URLs, and audit records. Use opaque identifiers and keep sensitive metadata in controlled records.

### Q16. What does S3 versioning protect against, and what must be configured separately?

**Answer:** It retains prior object versions for recovery, but lifecycle, delete-marker handling, access control, and replication still need design.

### Q17. How should large S3 uploads use multipart upload?

**Answer:** Use bounded parallel parts, checksums, resume/abort cleanup, and lifecycle rules for incomplete uploads. Do not expose broad bucket credentials to clients.

### Q18. When is Lambda a poor fit for a long-running stateful workload?

**Answer:** When execution duration, local state, connection reuse, or predictable low latency exceeds its model. Use containers or managed compute that fit lifecycle requirements.

### Q19. Why can a serverless function exhaust database connections under burst traffic?

**Answer:** Concurrency can scale faster than the database connection limit. Use pooling/proxies, reserved concurrency, and bounded query load.

### Q20. How does an EC2 launch template improve repeatability?

**Answer:** It versions machine configuration so autoscaling launches consistent instances. Keep user data idempotent, secrets external, and AMI updates controlled.

## Messaging, scaling, and operations

### Q21. How do SQS visibility timeout and message processing duration interact?

**Answer:** The timeout must exceed normal processing or be extended while work continues; otherwise another consumer can receive the message concurrently. Make handling idempotent.

### Q22. Why is a dead-letter queue not a complete failure-handling strategy?

**Answer:** It stores failed messages but needs alarms, ownership, inspection, redrive policy, and replay safety. Poison messages require a repair path.

### Q23. How should an SQS consumer handle duplicate delivery?

**Answer:** Assume at-least-once delivery and use a durable idempotency key or business uniqueness constraint. Acknowledgment timing alone cannot guarantee exactly once.

### Q24. What is the difference between SQS Standard and FIFO operationally?

**Answer:** Standard emphasizes high throughput with possible duplicates and reordering; FIFO supports ordered message groups with deduplication constraints and lower scaling limits.

### Q25. How can an Auto Scaling policy oscillate?

**Answer:** Noisy metrics, short evaluation windows, and aggressive scale-in/out thresholds can cause repeated changes. Add cooldown/stabilization and use demand-aligned signals.

### Q26. Why should scale-out warmup include application readiness, not just instance launch?

**Answer:** Instances may need to load data, warm caches, or register targets before contributing capacity. Premature traffic can worsen tail latency.

### Q27. How do you estimate NAT gateway costs for a chatty private service?

**Answer:** Count cross-AZ traffic, data processing volume, idle uptime, and route paths. VPC endpoints for supported services can avoid some NAT data charges.

### Q28. What is the risk of depending on one availability zone for a stateful cache?

**Answer:** An AZ failure can remove both compute and local data capacity. Use a multi-AZ design or accept documented data-loss/recovery objectives.

### Q29. How should a deployment handle a changed IAM policy that is not yet propagated?

**Answer:** Use staged rollout, retry only safe authorization refreshes, and alert on AccessDenied. Avoid broad wildcard permissions as a propagation workaround.

### Q30. What operational data helps diagnose an AWS service quota bottleneck?

**Answer:** Correlate quota usage, API throttles, scaling events, CloudTrail, and regional service health. Request increases before reaching the limit.

## Reliability, cost, and architecture

### Q31. How do RTO and RPO change an AWS backup design?

**Answer:** RTO sets how quickly service must recover; RPO sets acceptable data loss. Test restore procedures against both targets, not just backup completion.

### Q32. What is the risk of assuming a cross-region replica is a complete disaster-recovery plan?

**Answer:** Replication may copy corruption or deletion and may not include dependencies, keys, DNS, or runbooks. Test isolated recovery with explicit failover criteria.

### Q33. How can a queue decouple a bursty producer from a bounded database?

**Answer:** Buffer work and process at a rate the database can handle, while monitoring age and applying backpressure. A queue cannot compensate for sustained overload.

### Q34. Why can multi-region active-active writes create conflicts?

**Answer:** Network delay and concurrent updates require conflict resolution or ownership partitioning. Choose consistency and write routing explicitly rather than assuming replicas merge safely.

### Q35. How do you choose between savings plans and on-demand for a variable workload?

**Answer:** Use on-demand for uncertain burst capacity and commitments for a stable measured baseline. Include coverage, term, flexibility, and future migration plans.

### Q36. What is a good cost guardrail for ephemeral environments?

**Answer:** Set budgets, TTL cleanup, resource tags, and quotas; alert on orphaned storage and public endpoints. Make cleanup safe for shared data.

### Q37. Why should S3 lifecycle policies be tested against recovery needs?

**Answer:** Transitions or expiration may remove data needed for restore or legal retention. Model object age, versions, backups, and retrieval time before applying.

### Q38. How do you prevent a single account compromise from exposing all environments?

**Answer:** Separate accounts, use centralized identity with least-privilege roles, enforce organization policies, and isolate production credentials and logs.

### Q39. What should an AWS Well-Architected review produce beyond a score?

**Answer:** A prioritized list of workload-specific risks, evidence, owners, and actions mapped to reliability, security, operations, performance, and cost goals.

### Q40. How can chaos testing validate an AWS recovery plan safely?

**Answer:** Inject a bounded AZ or dependency fault in a controlled environment, verify user SLOs and runbooks, and stop automatically if blast radius exceeds limits.


## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [AWS shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/)
- [AWS Auto Scaling guidance](https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/rel_adapt_to_changes_autoscale_adapt.html)
- [Amazon VPC documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
