# Production Scenario Practice

Each prompt below has one canonical answer in the subject guide where the relevant concept is explained. Use the link, then practice saying the response in your own words without reading it.

## Java and concurrency

- [Several threads access a HashMap](../multithreading-concurrency/interview-questions.md#what-can-go-wrong-if-many-threads-share-a-hashmap)
- [A race condition creates duplicate payments](../system-design-architecture/interview-questions.md#api-idempotency-and-duplicate-requests)
- [Thousands of threads cause a CPU spike](../multithreading-concurrency/interview-questions.md#thousands-of-threads-are-created-and-cpu-usage-spikes-what-do-you-do)
- [Several threads update one database record](../multithreading-concurrency/interview-questions.md#several-threads-update-the-same-database-record)

## Spring and persistence

- [Startup grows from 15 seconds to two minutes](../spring-framework/interview-questions.md#startup-time-rose-from-15-seconds-to-two-minutes-how-would-you-investigate)
- [A circular dependency appears after deployment](../spring-framework/interview-questions.md#what-is-a-circular-dependency-and-how-do-you-fix-it)
- [LazyInitializationException appears in production](../jdbc-jpa-hibernate/interview-questions.md#the-api-works-locally-but-fails-in-production-with-lazyinitializationexception-why)
- [A transaction partly commits despite an expected rollback](../spring-boot/interview-questions.md#why-was-data-only-partly-rolled-back)
- [The application throws OutOfMemoryError](../jvm-memory-management/interview-questions.md#the-application-suddenly-throws-outofmemoryerror-how-would-you-debug-it)

## Microservices and Kafka

- [Service C is down in an A -> B -> C call chain](../microservices-architecture/interview-questions.md#what-is-the-circuit-breaker-pattern)
- [A slow service affects the rest of the system](../microservices-architecture/interview-questions.md#what-is-the-circuit-breaker-pattern)
- [A Kafka consumer processes a message twice](../kafka-messaging/interview-questions.md#a-kafka-consumer-processes-the-same-message-twice-how-do-you-handle-it)
- [One Kafka partition is much busier than the rest](../kafka-messaging/interview-questions.md#one-partition-gets-much-more-traffic-than-the-others-what-would-you-do)
- [A message fails on every retry](../kafka-messaging/interview-questions.md#what-should-happen-when-a-message-keeps-failing)

## Database, system design, and cloud

- [A query slows from 50 ms to 10 seconds](../sql-relational-databases/interview-questions.md#how-do-you-optimize-a-slow-sql-query)
- [Database CPU reaches 100% at peak](../sql-relational-databases/interview-questions.md#how-do-you-optimize-a-slow-sql-query)
- [A table grows to hundreds of millions of rows](../sql-relational-databases/interview-questions.md#how-do-you-optimize-a-slow-sql-query)
- [An API receives 100,000 requests per minute](../system-design-architecture/interview-questions.md#an-api-receives-100000-requests-per-minute-how-would-you-scale-it)
- [Users abuse an API](../system-design-architecture/interview-questions.md#design-a-rate-limiter)
- [Redis crashes unexpectedly](../redis-caching/interview-questions.md#redis-crashes-how-should-the-application-behave)
- [An EC2 instance needs S3 access without stored keys](../cloud-aws/interview-questions.md#an-ec2-instance-needs-secure-access-to-s3-how-do-you-avoid-stored-credentials)
- [A release increases latency](../logging-monitoring-observability/scenario-questions.md#the-api-slowed-after-a-deployment-what-is-a-safe-response)

