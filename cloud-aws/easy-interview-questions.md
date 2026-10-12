# Cloud and AWS: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is cloud computing?

**Answer:** On-demand computing resources delivered over a network, often with usage-based cost. Customers still own secure configuration and application behavior.

### Q2. What is IaaS?

**Answer:** Infrastructure such as virtual machines, networks, and storage that the customer configures and operates.

### Q3. What is PaaS?

**Answer:** A managed platform or runtime that lets teams focus more on application code, with provider control of more infrastructure.

### Q4. What is SaaS?

**Answer:** A complete application operated by a provider and accessed by users or software.

### Q5. What is an AWS Region?

**Answer:** A geographic area containing multiple Availability Zones. Choose based on latency, service availability, compliance, and cost.

### Q6. What is an Availability Zone?

**Answer:** An isolated location within a Region. Spreading workloads across zones can reduce a single-zone failure's impact.

### Q7. What is the shared responsibility model?

**Answer:** AWS secures the cloud infrastructure; customers secure what they deploy and configure in the cloud. The split varies by service.

### Q8. What is IAM?

**Answer:** AWS Identity and Access Management controls identities and permissions for resources. Grant only required actions and scope.

### Q9. What is an IAM policy?

**Answer:** A set of permission statements allowing or denying actions on resources under conditions. Review action and resource scope.

### Q10. What is a security group?

**Answer:** A stateful virtual firewall associated with resources such as EC2 instances. Allow only needed network traffic.

### Q11. What is a VPC?

**Answer:** A logically isolated AWS network where you define address ranges, subnets, routes, and security controls.

### Q12. Public vs private subnet?

**Answer:** A public subnet has a route to an internet gateway; a private subnet does not route directly to the internet.

### Q13. What is an EC2 instance?

**Answer:** A resizable virtual server. Choose type, storage, networking, and identity settings to fit the application.

### Q14. What is AWS Lambda?

**Answer:** It runs event-triggered code without managing server instances. It has runtime, concurrency, and execution constraints.

### Q15. What is Amazon S3?

**Answer:** Object storage for files, backups, and static assets. Control access with identity, bucket policies, encryption, and public-access settings.

### Q16. What is Amazon RDS?

**Answer:** A managed relational database service. Teams still design schema, queries, and availability requirements.

### Q17. What is DynamoDB?

**Answer:** A managed NoSQL key-value and document database. Model keys around the access patterns the app must serve.

### Q18. What is an Application Load Balancer?

**Answer:** It distributes HTTP/HTTPS traffic to targets and can route based on request details. Health checks avoid unhealthy targets.

### Q19. What is Auto Scaling?

**Answer:** It adjusts compute capacity based on demand or health. Policies should allow new instances time to become ready.

### Q20. What is an SQS queue?

**Answer:** A managed message queue that decouples producers and consumers. Consumers should tolerate redelivery and use visibility timeouts.

### Q21. What is Amazon SNS?

**Answer:** A managed publish-subscribe service that can fan out a message to queues or other subscribers.

### Q22. SQS vs SNS?

**Answer:** SQS holds messages for consumers to poll; SNS distributes published messages to subscribers. They can be combined for fan-out.

### Q23. What is CloudWatch?

**Answer:** It collects AWS metrics, logs, and alarms. Define alarms around user impact and actionable thresholds.

### Q24. What is the AWS Well-Architected Framework?

**Answer:** Guidance for reviewing workloads across operational excellence, security, reliability, performance, cost, and sustainability.

### Q25. What is a managed service?

**Answer:** A provider handles some operational tasks, such as patching or infrastructure availability. Customers still configure access, data, and app reliability.

### Q26. What is a NAT Gateway?

**Answer:** It lets private-subnet resources initiate outbound IPv4 connections without accepting unsolicited inbound internet connections.

### Q27. What is AWS KMS?

**Answer:** A managed service for cryptographic keys used to encrypt and decrypt data. Key policies and grants control use.

### Q28. What is AWS Secrets Manager?

**Answer:** It stores and can rotate secrets such as database credentials. Apps should retrieve secrets through an authorized identity.

### Q29. How do you control AWS cost?

**Answer:** Measure use, tag resources, right-size and scale capacity, and remove unused resources. Balance cost with reliability and performance.

### Q30. What is an AWS IAM role?

**Answer:** An identity with permissions that a trusted user or service can assume temporarily.

**Example:** Attach an instance role so EC2 can access S3 without stored keys.

### Q31. What is an instance profile?

**Answer:** A container for an IAM role that can be attached to an EC2 instance.

**Example:** The instance receives temporary role credentials.

### Q32. What is least privilege in IAM?

**Answer:** Grant only the actions and resources required for the task.

**Example:** Allow read access to one S3 bucket prefix instead of all buckets.

### Q33. What is an S3 bucket policy?

**Answer:** A resource-based policy that controls access to a bucket or its objects.

**Example:** Use it to limit access to a known role.

### Q34. What is an S3 lifecycle rule?

**Answer:** A rule that transitions or expires objects based on age or other filters.

**Example:** Move older logs to a lower-cost storage class.

### Q35. What is a VPC endpoint?

**Answer:** A private connection from a VPC to a supported AWS service without routing that traffic through the public internet.

**Example:** An S3 gateway endpoint can keep S3 traffic on AWS networking.

### Q36. What is a load balancer health check?

**Answer:** A repeated check used to decide whether a target should receive traffic.

**Example:** Remove an unhealthy EC2 target from rotation.

### Q37. What is a Multi-AZ database deployment?

**Answer:** A database configuration that keeps a standby in another Availability Zone for availability.

**Example:** The service can fail over after an AZ issue.

### Q38. What is an AWS VPC security group?

**Answer:** A stateful virtual firewall controlling inbound and outbound traffic for associated resources.

**Example:** Allow app servers to reach the database port.

### Q39. What is AWS CloudTrail?

**Answer:** A service that records account activity and API events for audit and investigation.

**Example:** Use it to check who changed a security group.

### Q40. What is an AWS route table?

**Answer:** A set of rules that directs subnet network traffic to destinations.

**Example:** A private subnet route may send outbound traffic to a NAT gateway.

## References

- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [IAM roles for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
- [AWS shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/)
- [AWS Auto Scaling guidance](https://docs.aws.amazon.com/wellarchitected/2024-06-27/framework/rel_adapt_to_changes_autoscale_adapt.html)
- [Amazon VPC documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
