# Spring Framework: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Container internals and lifecycle

### Q1. How can a BeanFactoryPostProcessor trigger premature creation of beans?

**Answer:** If it is instantiated as a non-static @Bean or injects ordinary beans, those beans may be created before all definitions and proxy infrastructure are ready. Register it early and keep it independent. The former can modify bean definitions before instantiation; the latter can wrap or modify created beans. Using the wrong lifecycle stage causes premature creation or missed definitions.

### Q2. Why can injecting a prototype bean into a singleton fail to create a new instance per use?

**Answer:** The prototype is resolved once when the singleton is built. Use ObjectProvider, a scoped proxy, or a factory when each operation needs a fresh object.

### Q3. How can circular dependencies be hidden by setter injection yet remain a design problem?

**Answer:** Early references may resolve some singleton cycles, but constructor cycles cannot and proxying complicates them. Break the dependency with a clearer boundary or event.

### Q4. Why should BeanFactoryPostProcessor implementations avoid ordinary bean injection?

**Answer:** They run while the container is still building definitions; eager bean creation can violate ordering and instantiate infrastructure too soon. Use static @Bean methods when defining them.

### Q5. How does FactoryBean differ from a bean that implements a factory pattern itself?

**Answer:** Spring exposes the FactoryBean product by default and the factory through an ampersand-prefixed name. Lifecycle and type prediction must account for both.

### Q6. What is the risk of calling a bean during ApplicationContext refresh?

**Answer:** The bean may be only partially initialized or trigger premature singleton creation. Prefer lifecycle callbacks or application-ready events for work needing the full context.

### Q7. How can conditional bean configuration become order-dependent?

**Answer:** Conditions evaluate the definitions visible at that processing stage; poorly ordered auto-configurations may miss an intended bean. Use explicit ordering and narrow conditions.

### Q8. Why can a scoped proxy solve request-scope injection into a singleton?

**Answer:** The singleton receives a stable proxy that resolves the actual request-scoped target on each invocation. Calls outside an active request still fail.

### Q9. What does @Lazy on an injection point change, and what does it not fix?

**Answer:** It injects a lazy-resolution proxy, delaying creation. It can defer a cycle or startup cost but does not correct ownership or runtime access outside scope.

### Q10. How do you debug unexpected bean overrides in a large context?

**Answer:** Inspect bean-definition names, condition reports, configuration imports, and override settings. Give definitions unique names and make intended replacement explicit.

## AOP proxies and transaction semantics

### Q11. Why does self-invocation bypass a proxy-based @Transactional method?

**Answer:** The call uses this directly and never crosses the proxy. Move the transactional boundary to another bean or use an explicit programmatic transaction.

### Q12. How do final classes or methods affect class-based AOP proxies?

**Answer:** CGLIB cannot override final methods and generally cannot subclass final classes. Use an interface proxy or redesign the target so interceptable methods are non-final.

### Q13. What determines the transaction manager used when several exist?

**Answer:** Qualifier, explicit transactionManager attribute, and bean selection rules determine it. Name the manager on critical transaction boundaries and test configuration.

### Q14. Why can a checked exception leave a transaction committed by default?

**Answer:** Default rollback rules commonly roll back for unchecked exceptions and Errors, not every checked exception. Configure rollbackFor or translate the failure deliberately.

### Q15. How does REQUIRES_NEW affect connection-pool pressure?

**Answer:** It suspends the outer transaction and starts another, often holding both connections simultaneously. Under load this can exhaust a small pool; model nesting and size it.

### Q16. Why can transaction advice order change behavior with retries?

**Answer:** If retry wraps transaction, each attempt may start a fresh transaction; if transaction wraps retry, retries may reuse a failed transaction. Put the boundary where the desired isolation occurs.

### Q17. What does propagation NESTED require from the transaction manager?

**Answer:** It typically uses savepoints within an existing JDBC transaction. Support depends on the manager and resource; it is not equivalent to an independent commit.

### Q18. Why can an @Transactional method appear to work in a unit test but not in production?

**Answer:** The test proxy, thread, transaction manager, or call path may differ. Verify invocation crosses the proxy and transaction boundaries exist in the running application.

### Q19. How should a transaction boundary interact with an asynchronous event listener?

**Answer:** An async listener runs on another thread and does not inherit the caller's transaction. Use an after-commit phase or an outbox if durable delivery matters.

### Q20. What happens when a transactional method catches and suppresses a persistence exception?

**Answer:** The provider or transaction may already be marked rollback-only, so commit can fail with an unexpected rollback. Do not hide persistence failures as successful results.

## Configuration and extension points

### Q21. When should you choose an ImportSelector over component scanning?

**Answer:** Use an ImportSelector when configuration inclusion depends on conditions or metadata and should be explicit. Scanning is simpler for ordinary application components.

### Q22. Why should configuration properties be validated at startup?

**Answer:** It fails fast on missing or inconsistent values before traffic arrives. Use typed binding and constraints, and avoid scattering raw environment lookups.

### Q23. How can a custom ConversionService cause surprising startup behavior?

**Answer:** It may be needed while configuration or bean definitions are being created, before ordinary dependencies are ready. Register converters through supported infrastructure configuration.

### Q24. What is the difference between an application event and a durable message?

**Answer:** Spring events are in-process and lost on crash; a broker or persisted outbox provides durable delivery and cross-process communication.

### Q25. Why can an event listener cause a transaction to perform unexpected work?

**Answer:** Synchronous listeners run on the publisher's thread and may execute inside its transaction. Keep handlers short or explicitly choose after-commit/asynchronous behavior.

### Q26. How do you support multiple implementations of an extension point without relying on bean order?

**Answer:** Inject a qualified collection or map, define an explicit priority, and make selection rules deterministic. Do not depend on incidental component-scan ordering.

### Q27. What is the cost of using @Lookup method injection?

**Answer:** Spring overrides the method to resolve a bean at call time, coupling the class to container behavior and complicating testing. A provider is often clearer.

### Q28. Why should a custom BeanPostProcessor be registered early and narrowly?

**Answer:** It must be available before target beans are created, and broad wrapping can affect infrastructure unexpectedly. Limit its types and test proxy interactions.

### Q29. How do profiles differ from conditional properties for feature selection?

**Answer:** Profiles select environment-specific bean sets; properties express configuration and feature choices more granularly. Avoid using profiles as a substitute for every feature flag.

### Q30. How would you make optional integration configuration disappear cleanly when its library is absent?

**Answer:** Use class-presence conditions without eagerly referencing absent types, isolate configuration classes, and test both dependency-present and absent classpaths.

## Testing and production concerns

### Q31. How can a test accidentally instantiate the full Spring context while intending a unit test?

**Answer:** Annotations such as SpringExtension or @SpringBootTest load context infrastructure. Use plain construction for unit tests and narrow slices only when container behavior matters.

### Q32. Why can @DirtiesContext make a suite much slower?

**Answer:** It evicts cached contexts, forcing expensive rebuilds. Fix shared mutable test state or use isolated configuration rather than clearing the context indiscriminately.

### Q33. How do you test a proxy-based feature rather than merely test the target class?

**Answer:** Obtain the bean from an application context and invoke it through the managed reference. Assert observable transaction, security, cache, or advice behavior.

### Q34. What makes an integration test's @Transactional rollback misleading?

**Answer:** Work on another thread or through an external service is outside the test transaction. Clean up external state explicitly and assert committed behavior where relevant.

### Q35. How do you prevent bean-name collisions when composing modules?

**Answer:** Use explicit names, narrow scan packages, and module-specific configuration; fail on accidental overrides. Keep auto-configuration opt-in or conditional.

### Q36. Why is exposing the entire ApplicationContext as a service locator a coupling problem?

**Answer:** Consumers can resolve arbitrary dependencies at runtime, hiding dependencies from constructors and tests. Inject the specific collaborator or a narrow provider interface.

### Q37. How can you observe startup bean-creation time without adding logs to every component?

**Answer:** Use application startup instrumentation or a buffering ApplicationStartup implementation and inspect startup steps. Compare cold starts under the same environment.

### Q38. What is the trade-off of lazy initialization for a production service?

**Answer:** It lowers startup work but shifts bean failures and initialization latency to first use. Readiness should reflect whether required components are actually usable.

### Q39. How should graceful shutdown interact with in-flight requests?

**Answer:** Stop accepting new work, allow active requests a bounded drain interval, then close resources. Set the server and orchestrator termination grace periods coherently.

### Q40. Why can reflective framework behavior fail after modularization or native compilation?

**Answer:** Strong module boundaries or closed-world analysis limit reflection discovery. Register hints or use supported extension APIs, and test the packaged runtime.


## References

- [Spring IoC and Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Spring bean scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html)
- [Spring Java configuration and @Bean](https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html)
- [Spring AOP proxying](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
