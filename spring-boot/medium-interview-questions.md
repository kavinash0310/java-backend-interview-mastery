# Spring Boot: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Auto-configuration and startup

### Q1. How can you find why a Boot auto-configuration matched or backed off?

**Answer:** Use the condition evaluation report or debug startup diagnostics to see matched and unmatched conditions.

**Example:** Inspect the report before adding another manual bean.

### Q2. What makes a custom auto-configuration back off cleanly?

**Answer:** Use conditional registration, especially missing-bean conditions, and avoid forcing one implementation on every application.

**Example:** Provide a default only when the user has not supplied one.

### Q3. Why should auto-configuration classes be ordered carefully?

**Answer:** Ordering can matter when one configuration depends on definitions from another, but conditions and bean dependencies are usually clearer than arbitrary ordering.

**Example:** Use documented ordering annotations only for real dependencies.

### Q4. How can dependency scanning make startup unexpectedly slow?

**Answer:** Broad component scans and classpath discovery can inspect many unrelated packages or libraries.

**Example:** Restrict scans to application-owned packages.

### Q5. What is an ApplicationStartup recorder useful for?

**Answer:** It records startup steps and timings so slow context initialization can be traced to particular phases.

**Example:** Compare startup-step output before and after a dependency change.

### Q6. What can a failed ApplicationContext refresh leave behind?

**Answer:** Resources created during startup may need destruction, and the root cause is usually in the first failing bean or condition.

**Example:** Inspect nested causes and shutdown logs.

### Q7. Why can lazy initialization hide a startup defect?

**Answer:** A bean is not created until first use, so a missing dependency or invalid setting appears during live traffic instead of startup.

**Example:** Use it selectively and exercise critical paths before readiness.

### Q8. How do command-line and application arguments differ?

**Answer:** The command line reaches the application entry point, while Boot also parses options into its Environment for configuration.

**Example:** Use `--server.port=...` to override a property.

## Configuration and application design

### Q9. How does relaxed binding map property names to Java fields?

**Answer:** Boot accepts common naming variants such as kebab case and camel case when binding configuration properties.

**Example:** `service-timeout` can bind to `serviceTimeout`.

### Q10. Why use immutable @ConfigurationProperties objects?

**Answer:** They make configuration explicit and prevent accidental changes after startup.

**Example:** A record can hold a validated set of settings.

### Q11. What is a config tree import useful for?

**Answer:** It maps files in a directory into properties, which works well with mounted secret files.

**Example:** Mount one file per setting and import the directory.

### Q12. How do profile groups differ from activating one profile?

**Answer:** A profile group activates a named set of profiles together, simplifying environment selection.

**Example:** A `production` group can include `metrics` and `cloud`.

### Q13. What risk comes from putting secrets in application.yml?

**Answer:** They can be committed, copied into build artifacts, or exposed in logs and support bundles.

**Example:** Inject secrets from a managed store at runtime.

### Q14. How should configuration precedence be debugged?

**Answer:** Inspect the effective Environment and the property sources that supplied the value; do not infer from one file alone.

**Example:** A command-line property can override packaged YAML.

### Q15. Why should @ConfigurationProperties be validated?

**Answer:** It fails fast when required settings are absent or out of range instead of allowing a later runtime failure.

**Example:** Reject a zero connection timeout during startup.

### Q16. What is the purpose of configuration metadata?

**Answer:** It helps editors document and autocomplete properties; it does not itself validate runtime values.

**Example:** Add metadata for custom properties used by a starter.

## Web, operations, and health

### Q17. What is the difference between liveness, readiness, and startup checks?

**Answer:** Liveness asks whether restart may help; readiness asks whether traffic should be sent; startup allows slow initialization before other probes begin.

**Example:** Do not make liveness fail just because a downstream service is temporarily down.

### Q18. Why should a readiness check not depend on every optional service?

**Answer:** One noncritical outage could remove all app instances from traffic and amplify the outage.

**Example:** Include only dependencies required to serve core requests.

### Q19. How can Actuator endpoints be exposed without exposing sensitive operations?

**Answer:** Expose only needed endpoints, apply network restrictions, and authenticate or authorize access.

**Example:** Keep shutdown and environment details private.

### Q20. What does the info endpoint commonly contain?

**Answer:** Safe build, version, or application metadata useful during support and deployment checks.

**Example:** Expose a commit identifier, not credentials.

### Q21. How can a custom health indicator avoid slowing readiness?

**Answer:** Use bounded timeouts and cached or lightweight checks rather than long blocking dependency calls.

**Example:** Measure readiness handler latency.

### Q22. What should happen when a REST request body fails validation?

**Answer:** Return a consistent client error with field-level messages that do not reveal internal implementation details.

**Example:** Map bean validation failures to a 400 response.

### Q23. How do servlet filters and MVC interceptors differ in placement?

**Answer:** Filters wrap the servlet request pipeline; interceptors run around handler execution after MVC has selected a handler.

**Example:** Use a filter for low-level request correlation and an interceptor for handler-specific work.

### Q24. Why configure graceful shutdown with the load balancer's drain period?

**Answer:** The platform must stop routing new requests while the app finishes in-flight work before termination.

**Example:** Make shutdown grace longer than the maximum supported request duration.

### Q25. What is the benefit of build information in Actuator?

**Answer:** It helps operators identify which artifact is running during a rollout or incident.

**Example:** Include version and commit metadata in the build.

### Q26. How should an application treat a broken actuator health endpoint?

**Answer:** Treat it as an operations defect: keep the response fast, stable, and free of secret details.

**Example:** Test health status mapping for both healthy and unhealthy dependencies.

## Packaging, testing, and upgrades

### Q27. What is the difference between an executable Boot JAR and a plain JAR?

**Answer:** The executable JAR contains Boot loader metadata and nested dependencies; a plain JAR is a normal library artifact.

**Example:** Check which artifact classifier your deployment pipeline publishes.

### Q28. Why use layered archives or images for Boot applications?

**Answer:** Stable dependencies can be cached separately from frequently changing application classes, reducing rebuild and transfer work.

**Example:** Keep dependency layers before the app layer.

### Q29. What does `spring-boot:build-image` create?

**Answer:** It uses buildpacks to produce an OCI image from the application artifact and runtime metadata.

**Example:** Pin and review the builder image used by production.

### Q30. How can `@SpringBootTest` make a test suite slow?

**Answer:** It starts a broad application context and may connect to infrastructure, increasing setup and isolation cost.

**Example:** Use a focused slice test when full wiring is unnecessary.

### Q31. When is a slice test preferable to a full context test?

**Answer:** When validating one layer, such as MVC serialization or repository behavior, without loading unrelated application components.

**Example:** Use `@WebMvcTest` for controller behavior.

### Q32. Why can changing Spring Boot versions break configuration?

**Answer:** Defaults, dependency versions, property names, and framework behavior can change across releases.

**Example:** Read the migration guide and test critical integrations.

### Q33. How should you choose a Boot upgrade test plan?

**Answer:** Run unit and integration tests, inspect deprecation notices, and validate startup, health, database, and security behavior.

**Example:** Exercise the actual production profile in a staging environment.

### Q34. Why keep the JVM version explicit in a Boot build?

**Answer:** The build tool, compiler release, runtime image, and libraries must agree on supported Java features.

**Example:** Set the release target and runtime image deliberately.

### Q35. What is an application runner useful for?

**Answer:** It runs application work after startup, such as validation or small bootstrap tasks.

**Example:** Avoid long unbounded work that delays readiness.

### Q36. How can you verify the packaged artifact rather than only IDE behavior?

**Answer:** Run the built JAR or image with production-like configuration and test its health and critical endpoints.

**Example:** Use the same artifact promoted through environments.

### Q37. What is a FailureAnalyzer in Spring Boot?

**Answer:** It turns a common startup exception into a clearer diagnostic with an actionable description.

**Example:** Look for the analyzed failure in the startup report.

### Q38. What is the difference between a startup failure and an unhealthy running app?

**Answer:** A startup failure prevents the context from becoming ready; a runtime health issue occurs after startup and should be reported through health signals.

**Example:** Track startup logs separately from live health checks.

### Q39. Why can a large classpath affect Boot auto-configuration?

**Answer:** More candidates and conditions may be inspected, and conflicting libraries can activate unexpected defaults.

**Example:** Review the dependency tree when behavior changes after adding a starter.

### Q40. How should a migration run when several app instances start together?

**Answer:** Use a migration tool with locking or a controlled deployment step so concurrent instances do not apply the same schema change unsafely.

**Example:** Separate schema migration from application startup when the rollout requires it.

## References

- [Spring Boot auto-configuration](https://docs.spring.io/spring-boot/reference/using/auto-configuration.html)
- [Spring Boot externalized configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
- [Spring Boot web applications](https://docs.spring.io/spring-boot/reference/web/servlet.html)
