# Production Debugging and Observability Questions

## How would you debug a difficult production issue?

Start with user impact and scope: which endpoint, version, region, and users are affected? Compare error rate and latency with a healthy baseline, then check recent deployments and dependencies. Use logs for a specific request, metrics for the broader pattern, and traces for time spent across services.

Make one reversible mitigation at a time, such as rolling back, reducing traffic, or disabling a feature flag. Preserve evidence and communicate the current impact and next update to the people handling the incident. After recovery, write down the cause and add a test, alert, or guardrail that would catch it earlier.

## Following a request across services

For the canonical answer on tracing across many services, see [How do you trace a request through 15 services?](../microservices-architecture/interview-questions.md#how-do-you-trace-a-request-through-15-services). This module's production investigation guide focuses on incident triage and safe mitigation.

## The API slowed after a deployment. What is a safe response?

Compare latency, errors, and resource use by version. Check whether the new release changed query count, payload size, connection use, or downstream call patterns. If user impact is growing and rollback is safe, route traffic back to the known-good version using the deployment platform's rollback or canary controls.

Check database and message compatibility before rollback, especially if the release applied a schema change. Keep the mitigation small, confirm recovery in metrics, and then investigate the root cause.
