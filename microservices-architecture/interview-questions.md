# Microservices Interview Questions

## How do microservices communicate?

Synchronous communication (usually HTTP or gRPC) is useful when a caller needs an immediate answer. Asynchronous communication (usually a broker such as Kafka) is useful when work can happen later, consumers should be decoupled, or events need to be retained and replayed.

Choose based on the interaction and failure behavior, not fashion. Synchronous calls add availability and latency dependencies; asynchronous flows add eventual consistency, retry, ordering, and duplicate-delivery concerns.

## Kafka vs other messaging services

Kafka is a distributed event log built around ordered partitions and retained records. It is useful for high-throughput event streams, replay, and multiple consumer groups reading the same history. Traditional task queues often focus on delivering a message to one worker and removing or acknowledging it after processing.

The exact comparison depends on the broker and configuration. Discuss ordering, retention, replay, throughput, delivery semantics, operations, and what happens when a consumer is unavailable.

## Explain the Saga pattern and its types

A Saga breaks a cross-service workflow into local transactions. If a later step fails, the workflow runs compensating actions for the earlier steps instead of relying on one distributed database transaction.

In choreography, services react to events and publish the next event. In orchestration, a coordinator tells each participant which step to run. Choreography reduces a central coordinator but can be harder to follow as the workflow grows. Orchestration makes the flow explicit but adds a component that must be operated and evolved.

Use a Saga when each service owns its own data and the business can tolerate eventual consistency. Design compensation carefully: a refund is a new business action, not a time machine that erases a charge.

## What is the Circuit Breaker pattern?

A circuit breaker tracks failures to a dependency. After enough failures it opens and rejects calls quickly, then allows a limited trial after a wait period. If the trial succeeds, it closes; if it fails, it stays open.

For Service A -> B -> C, if C is down, B should fail fast or return a safe fallback rather than hold many requests open. A should also have a timeout and bounded retries. Retries need backoff and jitter; otherwise they can amplify the outage.

A circuit breaker does not fix the dependency. It limits how much the failure spreads while recovery happens.

## REST, Kafka, or gRPC: which would you choose?

Use REST when broad interoperability and straightforward request-response behavior matter. Use gRPC for typed, efficient service-to-service calls and streaming when the environment supports it. Use Kafka when the producer and consumer should be decoupled in time, events need retention, or many consumers need the same facts.

A useful answer states who needs the response, how quickly, what should happen during an outage, and whether ordering or replay matters. One system may use all three for different interactions.

## How do you trace a request through 15 services?

Propagate a trace context across HTTP and message boundaries, give each operation a span, and include a correlation ID in structured logs. Use distributed tracing to see the path and time spent per service; metrics show whether the issue affects one request or the service broadly.

Do not put personal or secret data in trace attributes. Sample traces thoughtfully at high volume, while retaining errors and slow traces at a useful rate. For queued work, carry trace context in message headers.
