# Spring Framework: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Proxy and injection gotchas

### Q1. Why does calling an @Async method from another method on the same bean run synchronously?

**Answer:** The call bypasses the Spring proxy. Move async work to another managed bean or use an explicit task abstraction.

### Q2. Does @Primary make a bean win over an injection point that uses @Qualifier?

**Answer:** No. The explicit qualifier narrows candidates; @Primary resolves ambiguity among remaining candidates.

### Q3. Why can a @Bean method return a different instance when proxyBeanMethods=false?

**Answer:** Calls are ordinary Java method invocations without full configuration enhancement. Inject the bean instead of calling the method directly.

### Q4. Does singleton scope guarantee that a bean is thread-safe?

**Answer:** No. It controls instance count and lifetime; shared mutable fields still need safe design or synchronization.

### Q5. Why can JDK proxy injection fail when code expects a concrete implementation type?

**Answer:** The proxy may implement only interfaces and not extend the concrete class. Inject its interface or choose class-based proxying deliberately.

### Q6. Why does @Order not necessarily choose one bean for ordinary single-value injection?

**Answer:** It primarily orders collections and certain framework callbacks; use @Primary or @Qualifier to select one candidate.

### Q7. What is the difference between ObjectProvider.getIfAvailable() and getObject()?

**Answer:** The former may return null when no bean exists; getObject expects one and can fail. Choose based on whether absence is valid.

### Q8. Why can two components with distinct classes still collide in the bean registry?

**Answer:** Bean names, not Java types, identify definitions; same naming rules can produce duplicates. Set explicit names and inspect scan roots.

### Q9. Who destroys a prototype bean after Spring injects it into a singleton?

**Answer:** The container generally does not manage the prototype's full destruction lifecycle after handing it out. The consumer must close resources or use a managed scope.

### Q10. Why can a final method skip advice even if the bean is created successfully?

**Answer:** A class-based proxy cannot override it for interception. Use an interceptable method or a suitable interface boundary.

## Lifecycle and events

### Q11. Does @PostConstruct mean the whole application is ready?

**Answer:** No. It runs during bean initialization before context refresh is fully complete. Use readiness-aware lifecycle events for post-start work.

### Q12. Can a synchronous event listener affect the publisher's transaction?

**Answer:** If it executes on the publishing thread within that transaction, its exception can propagate and influence rollback. Define the boundary and error policy.

### Q13. Does @TransactionalEventListener(AFTER_COMMIT) guarantee event delivery after a crash?

**Answer:** No. It runs in-process after commit and can be lost if the process exits. Use a transactional outbox for durability.

### Q14. Why can an @Async event listener lose MDC or trace context?

**Answer:** It runs on another thread and context may not propagate. Configure executor decorators/instrumentation and clear context afterward.

### Q15. What risk comes from network calls in @PostConstruct?

**Answer:** Startup can block or fail on an external dependency before readiness. Use bounded initialization with explicit health and retry policy.

### Q16. Why can request-scoped injection fail inside a scheduled task?

**Answer:** There is no active HTTP request scope on the scheduler thread. Pass the needed values explicitly.

### Q17. Can @Lazy beans still be instantiated at startup?

**Answer:** Yes. An eager bean may request one or a startup callback may resolve it. Lazy only delays construction until first access.

### Q18. What does SmartLifecycle phase control?

**Answer:** It orders start and stop relative to other lifecycle beans; lower phases start earlier and stop later. It does not replace dependency wiring.

### Q19. Why can @DependsOn make a design harder to maintain?

**Answer:** It imposes order without showing a real object dependency. Prefer constructor injection unless initialization order itself is required.

### Q20. Why is an in-memory application event not a reliable queue?

**Answer:** It is not durable, replayable, or cross-process and may be synchronous. Use a broker or outbox when loss is unacceptable.

## Transaction annotation traps

### Q21. Can @Transactional on a private method reliably establish a transaction?

**Answer:** Proxy-based transaction advice generally applies to externally invoked methods, not private self-calls. Put the boundary on a public service method and verify proxy mode.

### Q22. If a method catches a RuntimeException, will the transaction necessarily commit?

**Answer:** Not necessarily. Nested advice or the resource may already mark it rollback-only; completion can throw UnexpectedRollbackException.

### Q23. Does a Spring transaction timeout guarantee an in-progress JDBC call stops immediately?

**Answer:** No. The timeout is enforced through transaction/resource mechanisms, and a driver call may not be interruptible. Configure statement/network timeouts and verify driver behavior.

### Q24. Does a transactional method called from a constructor use the proxy?

**Answer:** No. Constructor-time calls happen before the proxy is available. Invoke through a fully initialized managed bean.

### Q25. Why can transaction annotations on interfaces behave differently across proxy modes?

**Answer:** Annotation discovery and proxy strategy affect what metadata is visible. Put annotations consistently on service implementations and test the configured mode.

### Q26. Why is a transaction normally lost in a manually created Thread?

**Answer:** Spring transaction context is commonly thread-bound and is not automatically copied. Start a separate managed transaction in the worker.

### Q27. Does readOnly=true make database writes impossible?

**Answer:** No. It is usually a hint and not a universal database enforcement mechanism. Use permissions or explicit constraints for hard guarantees.

### Q28. Why can NESTED propagation work on one database but fail on another?

**Answer:** It depends on savepoint support in the selected transaction manager/resource. Verify actual behavior instead of assuming portability.

### Q29. How can advice order make a cache store a value from a rolled-back transaction?

**Answer:** Cache advice may run before transaction completion depending on ordering. Define the desired commit relationship and test the real proxy chain.

### Q30. What else can self-invocation bypass besides transaction advice?

**Answer:** Proxy-based caching, security, and async advice can also be skipped. Keep advised operations at externally invoked boundaries.

## References

- [Spring IoC and Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Spring bean scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html)
- [Spring Java configuration and @Bean](https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html)
- [Spring AOP proxying](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
