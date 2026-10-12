# Cloud and AWS: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Identity and network security

### Q1. IAM role trust policy vs permissions policy?

**Answer:** The trust policy says who may assume the role; the permissions policy says what the role may do afterward.

**Example:** Check both when an EC2 role cannot access S3.

### Q2. How do STS temporary credentials improve security?

**Answer:** They expire automatically and avoid storing long-lived access keys on an instance or in an image.

**Example:** Use an instance profile to obtain them.

### Q3. What is an IAM permissions boundary?

**Answer:** A maximum-permission guardrail for a user or role; it does not grant permissions by itself.

**Example:** The identity policy and boundary must both allow an action.

### Q4. How can a resource policy differ from an identity policy?

**Answer:** A resource policy is attached to the target resource and can name principals; identity policies attach to users or roles.

**Example:** S3 bucket policy can restrict access to a particular role.

### Q5. What is an AWS Organizations SCP?

**Answer:** An organization-level permission guardrail that limits what member accounts can do, without granting access.

**Example:** Use it to block unapproved regions or services.

### Q6. Security group vs network ACL?

**Answer:** Security groups are stateful and attached to resources; network ACLs are stateless subnet-level filters.

**Example:** For a stateless ACL, allow return traffic explicitly.

### Q7. How does a private subnet reach a public AWS service without NAT?

**Answer:** A VPC endpoint provides private service connectivity for supported services.

**Example:** Use an S3 gateway endpoint for private S3 routes.

### Q8. What does a NAT Gateway provide?

**Answer:** Outbound IPv4 connectivity from private subnets to public destinations; it does not permit unsolicited inbound connections.

**Example:** Place it in a public subnet with a route from private subnets.

### Q9. How can VPC Flow Logs help investigate connectivity?

**Answer:** They record network flow metadata that can show accepted or rejected traffic between interfaces.

**Example:** They do not capture packet contents.

### Q10. Why use multiple Availability Zones for an application tier?

**Answer:** It reduces dependence on one zone, provided traffic routing and stateful dependencies also tolerate the failure.

**Example:** Test health checks and failover, not just instance placement.

## Compute, storage, and databases

### Q11. ALB vs NLB: how do their use cases differ?

**Answer:** ALB operates at HTTP-aware layer 7 with routing rules; NLB handles layer 4 traffic with high-throughput TCP/UDP behavior.

**Example:** Choose based on protocol and routing needs.

### Q12. What can cause an Auto Scaling group to replace healthy-looking instances?

**Answer:** Health-check type, grace period, launch template, or scaling policy may mark them unhealthy or overshoot desired capacity.

**Example:** Inspect activity history and health-check source.

### Q13. Why use an EC2 launch template?

**Answer:** It versions instance configuration such as AMI, type, storage, and user data for repeatable scaling.

**Example:** Pin the template version in a rollout.

### Q14. S3 versioning vs replication?

**Answer:** Versioning keeps multiple object versions in a bucket; replication copies objects to another bucket or Region based on rules.

**Example:** Replication is not a substitute for retention or access controls.

### Q15. How does S3 lifecycle transition help cost control?

**Answer:** It moves eligible objects to lower-cost storage classes or expires them according to retention rules.

**Example:** Check retrieval delay and cost before moving active data.

### Q16. What is the RDS Multi-AZ standby primarily for?

**Answer:** It improves availability through managed failover; a standby is generally not a read-scaling target.

**Example:** Use a read replica when read scaling is the goal.

### Q17. RDS read replica vs Multi-AZ?

**Answer:** Read replicas serve read scaling and can have replication lag; Multi-AZ supports availability failover.

**Example:** Some engines support promotion, but it needs a recovery plan.

### Q18. What is the risk of relying only on automated database backups?

**Answer:** Retention, region, deletion protection, and restore time may not meet recovery requirements.

**Example:** Test point-in-time restore and measure RTO/RPO.

### Q19. How should Lambda handle a downstream dependency timeout?

**Answer:** Set bounded timeouts, limit retries, and consider queue-based buffering to avoid retry amplification.

**Example:** The function timeout must leave time for cleanup and response.

### Q20. What creates a Lambda cold start?

**Answer:** A new execution environment must initialize runtime and application dependencies before processing.

**Example:** Keep initialization lean and measure the actual runtime and workload.

## Messaging and operations

### Q21. What does an SQS visibility timeout do?

**Answer:** It temporarily hides a received message while a consumer processes it; after expiry it can be delivered again.

**Example:** Set it longer than normal processing or extend it safely.

### Q22. Why is SQS delivery generally treated as at-least-once?

**Answer:** A message can be delivered again due to retries or distributed processing behavior, so consumers should be idempotent.

**Example:** Use a unique event or business key.

### Q23. What is an SQS dead-letter queue for?

**Answer:** It isolates messages that repeatedly fail so they can be inspected and handled separately.

**Example:** Set a redrive policy and alarm on DLQ depth.

### Q24. What does FIFO SQS add compared with a standard queue?

**Answer:** It supports ordered message groups and deduplication features within documented constraints, with different throughput behavior.

**Example:** Use a message group ID to order one customer's work.

### Q25. How does SNS fanout with SQS decouple subscribers?

**Answer:** SNS publishes once to multiple queues, allowing each consumer to process independently and at its own rate.

**Example:** Give each subscriber a separate queue.

### Q26. What is an SNS subscription filter policy?

**Answer:** It lets a subscriber receive only messages whose attributes match its filter.

**Example:** Filter events by event type or region.

### Q27. How can CloudWatch alarms reduce noise?

**Answer:** Use sustained thresholds, missing-data treatment, and actionable dimensions tied to service health.

**Example:** Alert on a burn rate rather than every short CPU spike.

### Q28. What does CloudTrail answer that CloudWatch metrics do not?

**Answer:** CloudTrail records account API activity and actor context; metrics summarize numeric behavior over time.

**Example:** Use CloudTrail to investigate who changed an IAM policy.

### Q29. How can KMS key policy prevent an IAM permission from working?

**Answer:** KMS authorization evaluates the key policy and IAM policies; a missing allow in the key policy can block use.

**Example:** Check key policy and grants along with role policy.

### Q30. Why enable deletion protection for critical resources?

**Answer:** It reduces accidental deletion risk but does not replace backups or access controls.

**Example:** Combine protection with tested recovery procedures.

## Cost, resilience, and architecture

### Q31. What is an AWS budget alert useful for?

**Answer:** It warns when actual or forecast spend reaches a defined threshold, giving teams time to investigate.

**Example:** Set separate budgets for projects or environments.

### Q32. How can data transfer charges surprise an architecture?

**Answer:** Cross-AZ, cross-Region, internet egress, and service paths may incur charges not visible in compute pricing.

**Example:** Map traffic flows and estimate transfer volume.

### Q33. What is a Well-Architected review meant to uncover?

**Answer:** Risks across operational excellence, security, reliability, performance, cost, and sustainability pillars.

**Example:** Use it to frame trade-offs, not as a one-time certification.

### Q34. How do you design an AWS service for AZ failure?

**Answer:** Distribute stateless capacity and required data services across zones, then verify routing and failover behavior.

**Example:** Run a controlled failover exercise.

### Q35. What does an RTO define?

**Answer:** The target maximum time to restore service after a disruption.

**Example:** A four-hour RTO requires tested recovery within that window.

### Q36. What does an RPO define?

**Answer:** The target maximum amount of data loss measured in time.

**Example:** A 15-minute RPO requires recovery points no more than 15 minutes apart.

### Q37. How can a backup be useless despite succeeding?

**Answer:** It may be unreadable, incomplete, unavailable in the needed region, or too slow to restore.

**Example:** Practice restoring into an isolated environment.

### Q38. When is serverless a poor fit?

**Answer:** When workload duration, latency, local state, execution limits, or cost profile conflicts with the service's constraints.

**Example:** Compare measured total cost, not just idle cost.

### Q39. Why separate production and nonproduction AWS accounts?

**Answer:** It limits blast radius, enables distinct guardrails and billing, and reduces accidental cross-environment access.

**Example:** Use organization controls and role-based access.

### Q40. How can one AWS account assume a role in another account?

**Answer:** The target role trusts a source principal and grants limited permissions; the source identity also needs permission to call AssumeRole.

**Example:** Use an external ID for a third-party cross-account integration where appropriate.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [AWS shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/)
- [AWS Auto Scaling guidance](https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/rel_adapt_to_changes_autoscale_adapt.html)
- [Amazon VPC documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
