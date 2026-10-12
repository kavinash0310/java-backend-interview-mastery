# Cloud and AWS: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## IAM and networking traps

### Q1. Why can an EC2 role work in one subnet but fail in another?

**Answer:** Routing, endpoint policies, security groups, or metadata access may differ. Inspect credential retrieval and full network path.

### Q2. Does an instance profile contain multiple IAM roles?

**Answer:** No. It contains one role, though a role can be referenced by more than one profile.

### Q3. Why can an IAM policy appear to allow S3 while AccessDenied persists?

**Answer:** Bucket policy, KMS key policy, SCP, permission boundary, endpoint policy, or explicit deny may block it. Trace the full authorization chain.

### Q4. Can a VPC endpoint policy override an IAM explicit deny for S3?

**Answer:** No. An endpoint policy is an additional filter, not a grant that overrides explicit denies or missing identity/bucket permissions.

### Q5. Why should EC2 use temporary role credentials instead of access keys?

**Answer:** They rotate and expire automatically, reducing exposure risk. Scope the role and restrict which processes can reach instance metadata.

### Q6. What is the danger of allowing arbitrary principals to assume a cross-account role?

**Answer:** It expands trust beyond intended identities. Restrict account/principal, conditions, and session duration, and audit AssumeRole events.

### Q7. Can a security group rule block return traffic for an established connection?

**Answer:** Security groups are stateful, so return traffic is allowed for tracked connections; stateless network ACLs need both directions.

### Q8. Why can an S3 object be unreadable despite correct bucket access?

**Answer:** KMS permissions, object ownership, access points, or explicit denies may block it. Check encryption key and object-level context.

### Q9. Does an IAM role's permissions policy alone decide who can assume it?

**Answer:** No. The trust policy controls which principals may assume the role, and the caller may also need sts:AssumeRole permission.

### Q10. Why can adding a VPC endpoint fail to reduce data transfer cost?

**Answer:** Traffic may still route through NAT or use a service not covered by the endpoint. Verify actual route and flow logs.

## Storage and compute traps

### Q11. Why can an RDS failover leave an application connection pool using the old writer?

**Answer:** Existing TCP connections and DNS caches may persist after endpoint promotion. Evict stale connections, reconnect with bounded backoff, and verify the new writer.

### Q12. Can a read replica guarantee a just-written row is visible?

**Answer:** No. Replication lag can hide it. Route read-after-write to primary or use a consistency policy.

### Q13. Why is S3 versioning not a backup by itself?

**Answer:** Deletion markers, lifecycle expiration, compromised credentials, and regional failures still matter. Protect versions and test restore.

### Q14. What happens if a multipart upload is abandoned before completion?

**Answer:** Parts consume storage until aborted or cleaned by lifecycle policy. Ensure clients abort failures and configure incomplete-upload cleanup.

### Q15. Why should large files not pass through a small API container?

**Answer:** It consumes memory, bandwidth, and worker capacity. Use direct multipart object storage transfer with scoped authorization.

### Q16. Can an EC2 Auto Scaling group replace an instance because an app-level health check fails?

**Answer:** Yes, if configured to use load-balancer health; overly aggressive checks can create replacement loops. Align grace periods and startup.

### Q17. Why can a Lambda function exhaust a database connection limit after scaling?

**Answer:** Concurrent invocations create many connections. Use a proxy/pool, reserved concurrency, and database-aware limits.

### Q18. Does an ALB health check prove the backend is ready for all traffic?

**Answer:** Only if the path reflects meaningful readiness. A shallow response may pass while critical dependencies or authentication fail.

### Q19. Why can cross-AZ architecture cost more than expected?

**Answer:** Data transfer, NAT, load balancer, and replica traffic can cross zone boundaries. Measure paths and use zonal routing where appropriate.

### Q20. Can an EBS snapshot guarantee application consistency for a live database?

**Answer:** Not automatically; crash consistency may differ from application consistency. Use database-aware backup procedures and test restoration.

## Queues, failover, and cost traps

### Q21. Does SQS Standard deliver each message exactly once?

**Answer:** No. It provides at-least-once delivery and can duplicate messages. Make consumers idempotent.

### Q22. What happens if SQS visibility timeout is shorter than handler duration?

**Answer:** The message may become visible and be processed concurrently. Extend visibility or choose a longer bounded timeout.

### Q23. Can a DLQ automatically repair a poison message?

**Answer:** No. It stores it for inspection; define alarms, owner, redrive safety, and replay process.

### Q24. Does a regional replica automatically make failover safe?

**Answer:** No. Replication lag, write ownership, DNS, encryption keys, dependencies, and split-brain need a plan.

### Q25. Why can scale-in remove an AWS instance that still has active requests?

**Answer:** Without connection draining and a termination lifecycle hook, traffic can be cut off during termination. Deregister targets, drain within a deadline, then stop the instance.

### Q26. Can a VPC endpoint make an S3 bucket public?

**Answer:** No, but bucket policies and endpoint access can still be misconfigured. Restrict principals, networks, and actions independently.

### Q27. Why can NAT gateway cost rise with no increase in instance count?

**Answer:** Data transfer volume, cross-zone routing, or service calls can increase. Break usage down by route, AZ, and service.

### Q28. What do RTO and RPO mean for a restore test?

**Answer:** RTO is target recovery time; RPO is acceptable data loss. Measure actual restore and data gap, not only backup success.

### Q29. Can a budget alert stop a runaway cloud resource automatically?

**Answer:** Usually it alerts, not necessarily stops. Add explicit guardrails or automation with ownership and safe cleanup.

### Q30. Why should failover drills test clients, not just database promotion?

**Answer:** Connection pools, DNS caches, retries, and write routing can prevent apps from using the promoted resource. Test end-to-end recovery.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [AWS shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/)
- [AWS Auto Scaling guidance](https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/rel_adapt_to_changes_autoscale_adapt.html)
- [Amazon VPC documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
