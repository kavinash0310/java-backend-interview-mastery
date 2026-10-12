# System Design Interview Questions

For each design prompt, clarify the traffic and reliability needs before naming technologies. A good answer shows the main request path, the data that must be durable, and what happens when a dependency fails.

## Design a URL shortener

Start with two endpoints: create a short code for a long URL, and redirect a code to its destination. Store code-to-URL mappings in a durable database and put a cache in front of popular reads. Generate codes with a collision-safe strategy, enforce uniqueness, and decide how custom aliases and expiration work.

A redirect is read-heavy, so cache hot codes and keep the lookup path short. Add abuse controls and analytics asynchronously so they do not slow redirects. Explain trade-offs around code generation, cache invalidation, and whether a redirect is temporary or permanent.

## Design a rate limiter

For a single process, a token bucket is easy to explain: tokens refill at a fixed rate, and each request consumes one. To enforce a limit across many instances, keep bucket state in a shared store such as Redis and update it atomically, often with a script.

Choose the key (user, API key, IP, or route), the refill rate, burst size, and response on rejection. Return HTTP 429 and, when useful, a retry hint. Protect the limiter itself with timeouts and a clear policy for Redis failure.

## API idempotency and duplicate requests

A request is idempotent when repeating it has the same intended effect as doing it once. For a payment or order-creation endpoint, accept a client-generated idempotency key and store it with a unique constraint and the completed result. A retry with the same key returns the saved result; a retry with a different key represents a new attempt.

Claim the key and create the business record in the same transaction. Define how long keys are retained, how a request in progress is handled, and what happens if an external payment provider times out after processing. Use the provider's idempotency feature too, when available.

This is more reliable than trying to prevent every network retry. See also [the duplicate-payment concurrency example](../multithreading-concurrency/interview-questions.md#duplicate-payment-requests-during-a-race).

## How do you keep working when one application instance crashes?

Keep request state outside the process, run multiple stateless instances behind a load balancer, and make health checks remove an unhealthy instance from rotation. Store durable data in shared systems, make startup and shutdown graceful, and ensure retries cannot repeat non-idempotent work.

High availability also needs capacity headroom and a recovery plan for the shared database and queue. Multiple app instances do not help if they all depend on one unprotected database.

## What is an API Gateway?

An API Gateway is a managed or application-level entry point that routes client requests to services. It commonly handles cross-cutting concerns such as TLS termination, authentication integration, rate limits, request size limits, and routing.

Keep business rules in the service that owns them. A gateway can simplify client access, but putting too much orchestration there can turn it into a bottleneck and a second monolith.

## An API receives 100,000 requests per minute. How would you scale it?

That averages about 1,667 requests per second, but the peak and request cost matter more than the average. Ask about read/write mix, payload, latency target, geographic spread, and downstream limits. Measure first, then scale stateless service instances horizontally, use a load balancer, cache safe reads, and move noncritical work to a queue.

Use connection pools and bounded concurrency so the service does not overwhelm its database. Partition or shard only after the workload shows that a single datastore is the bottleneck. Add load shedding and rate limits to protect the system under peaks.

## How would you debug a slow production API?

Compare the slow request with its normal baseline and follow its trace. Split total latency across application time, database queries, and downstream calls. Check recent deployments, traffic changes, error rates, connection pools, thread or event-loop saturation, lock waits, and query plans.

If three independent downstream calls take about ten seconds each and the response needs all three, parallel calls with strict timeouts can reduce the waiting time. Only do that when the calls are truly independent and the dependencies can handle the concurrency. Keep a fallback or partial response only if product behavior allows it.

The first goal is to locate the time, not to guess at a fix. Mitigate with a rollback or traffic control when user impact is high, then capture evidence for the permanent change.
