# Spring Boot: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Property-source puzzles

### Q1. Which normally wins when a property exists in application.yml and an environment variable?

**Answer:** External sources such as environment variables generally override packaged configuration. Inspect the effective property origin for the exact Boot version.

### Q2. Why can @ConfigurationProperties fail early while @Value appears to fail later?

**Answer:** Typed binding and validation happen when the configuration bean is created; scattered @Value placeholders may resolve only when their bean is instantiated.

### Q3. Do profile-specific YAML lists always merge with the default list?

**Answer:** No. Higher-priority sources can replace lists rather than merge as expected. Inspect the bound result and use explicit list configuration.

### Q4. Why can a command-line argument unexpectedly override a deployment setting?

**Answer:** Boot often gives command-line properties higher precedence. Restrict or disable that source when runtime users must not override settings.

### Q5. Can @ConditionalOnProperty fail when the property appears in a file?

**Answer:** Yes. Prefix, exact name, havingValue, matchIfMissing, and source parsing determine the condition. Read the condition report and effective key.

### Q6. What is the difference between spring.config.location and spring.config.additional-location?

**Answer:** location replaces default search locations, while additional-location augments them. The former can make packaged config disappear.

### Q7. Why should a production profile not be treated as a security control?

**Answer:** Profiles select configuration, not authorization or secret handling. Validate production settings and protect credentials independently.

### Q8. Can a YAML value change type because it is quoted?

**Answer:** Yes. YAML may parse unquoted scalars as boolean or numeric values, while quotes keep strings. Use typed binding and validation.

### Q9. Why may an unknown configuration key go unnoticed?

**Answer:** Not every binding path rejects unrecognized keys. Use typed configuration and strict validation to catch typos.

### Q10. Why should a secret not be committed as an encrypted-looking property?

**Answer:** The encryption key or plaintext may leak, and Git history persists. Load it from a managed secret provider with rotation.

## Auto-configuration and startup

### Q11. Why might excluding an auto-configuration class leave its bean present?

**Answer:** Another configuration or user definition may create it. Inspect bean origins and the condition evaluation report before excluding more.

### Q12. Does @ConditionalOnMissingBean always see every user bean?

**Answer:** It evaluates definitions available at that processing point, so ordering matters. Test both default creation and user override.

### Q13. Why can a test slice pass while production auto-configuration is broken?

**Answer:** Slices exclude many auto-configurations and dependencies. Add a packaged smoke test or focused full-context test for critical wiring.

### Q14. How can the same component be registered twice?

**Answer:** Overlapping scans and explicit imports can create duplicate definitions. Keep one application scan root and verify bean names.

### Q15. Can readiness become true before asynchronous warmup finishes?

**Answer:** Yes, if warmup is detached from readiness state. Track required warmup explicitly and keep optional tasks from blocking the service.

### Q16. What happens when an ApplicationRunner throws an uncaught exception?

**Answer:** Startup can fail and the context closes. Use runners for finite required work, not endless or optional background tasks.

### Q17. Why can a server port be open while the app is not safe to receive traffic?

**Answer:** The listener may start before all application setup or dependencies are ready. Gate routing on a meaningful readiness probe.

### Q18. How can a custom FailureAnalyzer make startup debugging harder?

**Answer:** It may summarize the outer exception while hiding nested cause details. Preserve root causes and actionable context.

### Q19. Why is creating a raw thread in a @Bean method risky?

**Answer:** It escapes managed lifecycle and shutdown handling, and may start too early. Use a managed executor.

### Q20. How can a library dependency unexpectedly change Boot logging?

**Answer:** Classpath contents influence logging-system selection before normal beans load. Inspect the packaged dependency graph and early logging configuration.

## Actuator and web behavior

### Q21. If /actuator/health returns UP, does that prove login works?

**Answer:** No. It may only check process or dependencies. Use a safe synthetic check for the critical authentication flow if required.

### Q22. Why can a management endpoint be exposed even if the application route is private?

**Answer:** It may use another port or ingress/network policy. Review listeners, routes, firewalls, and security matchers.

### Q23. Does enabling forwarded-header support make X-Forwarded-* trustworthy?

**Answer:** No. Only trusted proxies should set them. Prevent clients from spoofing scheme, host, and source IP.

### Q24. Can graceful shutdown still terminate a streaming response?

**Answer:** Yes, the drain period is bounded. Align server and orchestrator grace periods, and test long-lived requests.

### Q25. Why might tests use a different server port than the packaged app?

**Answer:** Test properties, random-port configuration, and environment sources can override it. Inspect the actual test context.

### Q26. How can DevTools hide a production classloading issue?

**Answer:** It uses development-only restart behavior absent from the packaged runtime. Test the built artifact outside the IDE.

### Q27. Can a public health endpoint expose sensitive details from other Actuator endpoints?

**Answer:** Endpoint exposure and detail settings are separate. Enumerate all enabled endpoints and restrict config/environment details.

### Q28. What if the platform probes the wrong health group?

**Answer:** The app can think it is unready while the platform still routes traffic, or vice versa. Verify the exact probe path and endpoint behavior.

### Q29. Why can a local profile activate inside a container unintentionally?

**Answer:** Environment variables, command-line arguments, or imported config may set it. Validate active profiles and reject forbidden production combinations.

### Q30. Why should management traffic be isolated from public application traffic?

**Answer:** Operational endpoints can reveal or alter system state. Restrict exposure, authenticate, authorize, and expose only needed endpoints.

## References

- [Spring Boot auto-configuration](https://docs.spring.io/spring-boot/reference/using/auto-configuration.html)
- [Spring Boot externalized configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
- [Spring Boot web applications](https://docs.spring.io/spring-boot/reference/web/servlet.html)
