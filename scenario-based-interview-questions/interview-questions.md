# Additional Production Scenarios

## How do you separate an application defect from deployment wiring when an endpoint fails only in production?

Treat it as an environment comparison, not immediately as a code defect. Start with the response status, body, request ID, and trace so you know whether the request reached the application and where it failed. Compare the deployed artifact and runtime versions with the working build, then check effective configuration and active profiles, secret/role permissions, database migration state, DNS/TLS and gateway routing, and downstream endpoints. Use logs, metrics, and traces from the failing deployment; compare one failing request with a known-good one instead of changing several settings at once. If the error began with a release and user impact is high, use the established rollback path while preserving evidence for the fix.

See the course guides for [Spring Boot configuration](../spring-boot/README.md), [JPA production failures](../jdbc-jpa-hibernate/interview-questions.md), and [production API debugging](../system-design-architecture/interview-questions.md#how-would-you-debug-a-slow-production-api).
