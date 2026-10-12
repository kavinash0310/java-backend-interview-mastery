# Backend Project Interview Follow-ups

## When would you choose Spring Batch instead of Kafka for a large data workload?

Use Spring Batch when the work is a finite job such as importing a file, reconciling records, or rebuilding a report. Chunk processing gives a transaction boundary around groups of items, and the job repository can track execution state for restart and operational control. Kafka is a better fit for a durable, continuously arriving event stream that needs partitioned consumption, independent consumer groups, or replay.

They can work together: Kafka can signal that a file is ready, while a batch job processes that bounded file. Neither tool makes external side effects exactly once, so make writes idempotent and define how a failed or restarted step behaves. See [Spring Batch chunk processing](https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html) and the [Kafka documentation](https://kafka.apache.org/documentation/).

## How should a Spring Batch job authenticate to S3 in AWS, and what risks would you discuss?

For an AWS Batch job, set the job definition's `jobRoleArn` so the application container gets temporary credentials for its S3 calls; do not confuse it with `executionRoleArn`, which grants the container/Fargate agent permissions such as pulling an image or sending logs. For other runtimes, use an EC2 instance profile, ECS task role, or EKS workload identity. The AWS SDK credential provider chain can obtain and refresh temporary credentials. Scope the role to the specific bucket prefix and actions the job needs.

In an interview, mention the risks you checked: an overly broad role or trust policy, credentials exposed in logs or job parameters, unencrypted input/output, and incomplete audit records. For a job outside AWS, prefer federation or short-lived role credentials over a permanent access key. See [AWS Batch job roles](https://docs.aws.amazon.com/batch/latest/userguide/job_definition_parameters.html), [AWS Batch execution roles](https://docs.aws.amazon.com/batch/latest/userguide/execution-IAM-role.html), the [AWS SDK for Java credential chain](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/credentials-chain.html), [ECS task roles](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html), and [EKS workload identity](https://docs.aws.amazon.com/eks/latest/userguide/service-accounts.html).
