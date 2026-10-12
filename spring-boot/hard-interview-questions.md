# Spring Boot: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Auto-configuration and startup

### Q1. How do you design auto-configuration so user-defined beans always take precedence?

**Answer:** Use narrow @ConditionalOnMissingBean conditions, avoid eager bean creation, and order configuration predictably. Add tests for both default and user-override paths.

### Q2. Why can an auto-configuration condition match in tests but not in production?

**Answer:** Classpath, properties, bean definitions, and processing order differ. Compare the condition evaluation report and effective environment from the failing deployment.

### Q3. How can a starter accidentally force a transitive dependency on every consumer?

**Answer:** Declare optional integrations with appropriate optional/runtime boundaries and separate specialized starters. Inspect the published dependency graph, not only the local build.

### Q4. What does the condition evaluation report tell you during a Boot startup failure?

**Answer:** It lists matched and unmet auto-configuration conditions and their reasons. Use it to trace configuration inputs rather than disabling broad auto-configuration.

### Q5. Why is running a startup runner before readiness risky?

**Answer:** Heavy or remote work can delay readiness and make scaling slow. Use runners for finite initialization and model required dependencies explicitly in health state.

### Q6. How do lazy initialization and startup metrics interact?

**Answer:** Lazy beans hide creation cost until a request triggers them, so total request latency can rise after readiness. Measure first-use paths and warm critical components.

### Q7. How would you investigate a Boot startup regression after adding one library?

**Answer:** Compare dependency trees and startup steps, then isolate classpath scanning, auto-config matches, and static initialization. Remove or scope the dependency if it adds no needed feature.

### Q8. What should a custom starter's tests verify beyond context startup?

**Answer:** Test activation and backoff conditions, property binding, missing optional classes, and user overrides. A single successful context test misses the main failure modes.

### Q9. Why can component scanning outside the application's base package be fragile?

**Answer:** It silently couples module boundaries to package layout and may scan unrelated classes. Prefer explicit imports or a deliberate root package.

### Q10. What compatibility risks come with changing an auto-configuration's bean name?

**Answer:** Consumers may qualify or override by name even if the type is unchanged. Treat names and conditional behavior as part of the starter's compatibility surface.

## Configuration, profiles, and secrets

### Q11. How should a service handle a required configuration value that is absent?

**Answer:** Bind into a typed configuration object, validate at startup, and fail with a safe actionable message. Do not silently substitute a production default for a secret.

### Q12. Why can environment-variable relaxed binding create unexpected configuration?

**Answer:** Different spellings and precedence sources can map to the same property. Inspect the bound value and property origin, and standardize deployment names.

### Q13. How do config imports help separate packaged defaults from runtime secrets?

**Answer:** They load external configuration by location while keeping the artifact reusable. Store secrets in a managed secret provider and restrict import access and refresh behavior.

### Q14. What is a safe way to rotate a credential without restarting every instance at once?

**Answer:** Support overlapping old and new credentials, update the secret source, verify consumers, then revoke the old key. Keep rollback and expiry steps explicit.

### Q15. Why is a profile-specific YAML file not a secret store?

**Answer:** It may be packaged in the artifact, copied into images, or exposed in source control. Use an external secret manager with access logging and rotation.

### Q16. How can configuration drift be detected across replicas?

**Answer:** Expose safe configuration fingerprints or version IDs and compare them, excluding secrets. Centralize config rollout and alert when instances disagree unexpectedly.

### Q17. What should a feature flag property include besides a boolean?

**Answer:** Ownership, default, rollout scope, expiry or review date, and a rollback plan. Remove stale flags so configuration does not become permanent branching.

### Q18. Why can @RefreshScope create consistency problems during a config update?

**Answer:** Beans may be recreated at different times across nodes and in-flight calls can see mixed values. Use versioned rollout and validate cross-property invariants.

### Q19. How do you prevent logs and Actuator endpoints from exposing bound secrets?

**Answer:** Mask sensitive properties, limit endpoint exposure and access, and audit logs. Never return raw configuration objects from a diagnostic endpoint.

### Q20. When should you use command-line arguments over environment variables?

**Answer:** Arguments suit explicit process-level overrides in controlled launches; environment variables suit container configuration. Both can leak via process inspection or diagnostics, so avoid secrets there.

## Web, health, and operations

### Q21. What is the difference between liveness and readiness, and how can a bad probe cause an outage?

**Answer:** Liveness asks whether a process should restart; readiness asks whether it should receive traffic. Making liveness depend on a shared DB can restart every replica together.

### Q22. How would you add a dependency health check without making health endpoints amplify an outage?

**Answer:** Use bounded timeouts, cache expensive checks where appropriate, and distinguish critical from optional dependencies. Avoid cascading synchronous probes on every orchestrator poll.

### Q23. Why can an Actuator endpoint be available on a separate management port yet still be exposed?

**Answer:** Network separation is not authentication. Restrict routes, firewall access, and authorize endpoints; expose only the operations needed by the platform.

### Q24. What is the risk of using the same readiness check for Kubernetes probes and load balancers?

**Answer:** Different callers have different timing and semantics; one slow dependency check may remove all instances. Keep checks fast and align thresholds with recovery behavior.

### Q25. How do you configure graceful shutdown so requests are not cut off during rollout?

**Answer:** Enable server draining, set a request completion timeout, and make orchestrator termination grace longer than the drain window. Test long-running and streaming requests.

### Q26. Why can a controller return a response after a transaction has already committed?

**Answer:** Transaction advice may surround service execution only, while serialization happens later. Avoid lazy data access in serialization and return DTOs built within the boundary.

### Q27. How should request IDs and trace context be added without leaking thread-local values?

**Answer:** Use supported tracing/context propagation and clear request-scoped state at task boundaries. Thread pools and async callbacks need explicit propagation.

### Q28. What does a production startup endpoint need to prove before traffic is routed?

**Answer:** Required configuration and critical resources are initialized and the application can serve its contract. Do not mark ready merely because the HTTP server opened a socket.

### Q29. How can Boot's graceful shutdown still lose queued background work?

**Answer:** In-memory executors may stop before draining or tasks may be canceled at the timeout. Persist important work and configure executor shutdown policies deliberately.

### Q30. Why should management endpoint exposure be reviewed after adding a dependency?

**Answer:** Starters can contribute endpoints and defaults may change with versions. Enumerate the effective endpoints and restrict them through network and security policy.

## Packaging, tests, and upgrades

### Q31. How can layered jars improve container rebuilds, and what should remain stable?

**Answer:** Separate dependencies from frequently changed application classes so image layers reuse cached bytes. Keep deterministic layer order and compare actual image rebuild time.

### Q32. What makes an AOT or native-image failure different from a normal JVM startup failure?

**Answer:** Closed-world analysis needs reachable types and reflection/resource hints at build time. Reproduce with the packaged native artifact and add explicit supported hints.

### Q33. Why should an upgrade test include an old configuration file and serialized data?

**Answer:** Defaults, binding, API behavior, and persisted formats can change even when compilation succeeds. Test real migration inputs and document deprecations.

### Q34. How do you make a Boot upgrade safe across rolling deployment?

**Answer:** Confirm Java/framework compatibility, test mixed versions against shared schemas and messages, canary one instance, and keep rollback-compatible data changes.

### Q35. What is the benefit and cost of slicing tests with @WebMvcTest or @DataJpaTest?

**Answer:** Slices start less infrastructure and isolate a layer, but mock missing collaborators and can diverge from full wiring. Keep a smaller set of end-to-end checks.

### Q36. Why can a test pass with an embedded database and fail on the production database?

**Answer:** SQL dialects, locking, constraints, isolation, and collation differ. Run critical persistence tests against the production engine, often via containers.

### Q37. How should a Boot application handle a failed readiness transition after deployment?

**Answer:** Stop routing traffic, capture startup and dependency diagnostics, and roll back or repair the deployment. Keep liveness separate so restart loops do not worsen it.

### Q38. Why is pinning a dependency version in a starter more sensitive than in an app?

**Answer:** Consumers inherit the starter's chosen transitive version, which can conflict with their platform. Align through a BOM and test supported dependency ranges.

### Q39. What is a reproducible deployment artifact, and why does it matter for rollback?

**Answer:** The same immutable artifact is promoted across environments, so rollback restores known bytes. Avoid rebuilding from mutable tags or environment-specific source.

### Q40. How do you evaluate whether startup optimization actually improved user impact?

**Answer:** Measure time to readiness and first successful request under cold deployment, plus resource use and errors. Faster process start alone may shift latency to first traffic.


## References

- [Spring Boot auto-configuration](https://docs.spring.io/spring-boot/reference/using/auto-configuration.html)
- [Spring Boot externalized configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
- [Spring Boot web applications](https://docs.spring.io/spring-boot/reference/web/servlet.html)
