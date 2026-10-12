# Spring Framework: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Injection and bean lifecycle

### Q1. When is setter injection preferable to constructor injection?

**Answer:** Use it for a genuinely optional dependency or reconfiguration point; required dependencies should usually be constructor parameters.

**Example:** Mark an optional setter dependency explicitly rather than hiding a required collaborator.

### Q2. How does Spring resolve multiple beans of the same type?

**Answer:** It considers qualifiers, primary candidates, bean names, and other injection metadata; ambiguity should be resolved explicitly.

**Example:** Use `@Qualifier("fastClient")` at the injection point.

### Q3. What does injecting a prototype bean into a singleton actually do?

**Answer:** Spring resolves the prototype when it creates the singleton, so the singleton keeps that one injected instance. To obtain a fresh instance per operation, inject an `ObjectProvider<PrototypeBean>` and call `getObject()` when needed, or use a scoped proxy/lookup method. Prototype scope does not make shared mutable state thread-safe.

**Example:** Inject `ObjectProvider<TaskState>` and call `getObject()` when needed.

### Q4. What is a scoped proxy used for?

**Answer:** It lets a longer-lived bean hold a proxy that resolves a shorter-lived target in the current scope.

**Example:** A singleton can use a request-scoped object through a scoped proxy.

### Q5. What is the difference between BeanFactoryPostProcessor and BeanPostProcessor?

**Answer:** The former changes bean definitions before instances are created; the latter can inspect or wrap bean instances during creation.

**Example:** A post-processor may create an AOP proxy around a bean.

### Q6. What is a FactoryBean in Spring?

**Answer:** It is a bean whose `getObject()` returns another object managed or exposed by the container.

**Example:** Use `&beanName` when you need the FactoryBean itself.

### Q7. What distinguishes full `@Configuration` from lite configuration?

**Answer:** Full configuration classes are enhanced so calls between `@Bean` methods can return the container-managed singleton; lite mode does not intercept those calls.

**Example:** Use full configuration when one `@Bean` method calls another.

### Q8. Why can a bean constructor run before all proxies are applied?

**Answer:** Proxies are commonly created after initialization, so invoking advised behavior from a constructor can bypass advice.

**Example:** Move work to a lifecycle callback after construction, or a separate bean.

### Q9. How do `@PostConstruct` and `InitializingBean` differ?

**Answer:** Both provide initialization hooks; `@PostConstruct` avoids coupling the bean to Spring's interface, while `InitializingBean` is Spring-specific.

**Example:** Keep initialization idempotent and fast.

### Q10. What happens to a Spring bean if initialization throws?

**Answer:** The container fails bean creation and usually fails context startup if the bean is required.

**Example:** Validate configuration before serving traffic.

## AOP and transactions

### Q11. What is the difference between JDK dynamic proxies and class-based proxies?

**Answer:** JDK proxies implement interfaces; class-based proxies subclass the target class and cannot override final methods.

**Example:** A concrete-class injection may require a class-based proxy.

### Q12. What happens when a CGLIB proxy advises a final method?

**Answer:** A subclass proxy cannot override a final method, so advice cannot wrap calls to that method.

**Example:** Keep advised methods overridable or use another supported proxying approach.

### Q13. How does advice ordering affect nested aspects?

**Answer:** The configured order determines which advice runs first on entry and last on exit, so transaction, security, and logging boundaries can differ.

**Example:** Set explicit order only when nesting behavior matters.

### Q14. What does transaction propagation REQUIRED do?

**Answer:** It joins an existing transaction or creates one if none exists.

**Example:** A service call can participate in its caller's transaction.

### Q15. What does REQUIRES_NEW change?

**Answer:** It suspends any current transaction and starts an independent one, which may commit even if the outer transaction rolls back.

**Example:** Use carefully for audit records that must survive outer rollback.

### Q16. Why might a checked exception not roll back a Spring transaction by default?

**Answer:** Default rollback rules generally roll back for unchecked exceptions and Errors, not every checked exception.

**Example:** Specify `rollbackFor` when checked failures must trigger rollback.

### Q17. What is transaction isolation configured to protect against?

**Answer:** It controls which concurrent database changes a transaction can observe, with stronger isolation often trading throughput or concurrency.

**Example:** Choose based on anomalies the business operation cannot tolerate.

### Q18. What happens if an @Transactional method is called from a non-Spring object?

**Answer:** No Spring proxy is involved, so annotation-driven transaction behavior may not be applied.

**Example:** Call the operation through a Spring-managed bean.

### Q19. What does readOnly=true on @Transactional mean?

**Answer:** It is a hint to transaction infrastructure and providers; it is not a universal guarantee that writes will be rejected.

**Example:** Use database permissions or validation when writes must be prohibited.

### Q20. How can transaction-bound events avoid publishing before commit?

**Answer:** Use a transaction synchronization or `@TransactionalEventListener` phase so handling occurs after commit.

**Example:** Publish an external message with an outbox when durable delivery is required.

## Container configuration and extensibility

### Q21. What is conditional bean registration?

**Answer:** A bean definition is added only when a condition such as a class, property, or missing bean matches.

**Example:** A default can use `@ConditionalOnMissingBean`.

### Q22. When should a component be registered with @Bean instead of @Component?

**Answer:** Use `@Bean` when constructing third-party objects or when configuration must be explicit; use component scanning for application-owned classes.

**Example:** Configure a vendor client with `@Bean`.

### Q23. What is a BeanDefinition?

**Answer:** Metadata that describes how the container should create and configure a bean before the instance exists.

**Example:** It includes class, scope, constructor arguments, and properties.

### Q24. How can Spring profiles create environment-specific wiring?

**Answer:** Profiles can include or exclude bean definitions and configuration for selected environments.

**Example:** Keep behavior changes explicit and avoid profile-only business logic.

### Q25. How should a Spring application represent configuration that can be invalid?

**Answer:** Bind related settings to a typed configuration object and validate it at startup.

**Example:** Require a positive timeout and nonblank endpoint.

### Q26. What is method validation in Spring?

**Answer:** A proxy-based feature that checks constraints on method parameters or return values when enabled.

**Example:** Validate a service method's input range.

### Q27. How does an application event differ from a direct method call?

**Answer:** It decouples publisher from listeners, but listeners still run within the same application and delivery is not automatically durable.

**Example:** Use a broker when the event must survive process failure.

### Q28. Are Spring application event listeners asynchronous by default?

**Answer:** Typically they run synchronously in the publisher's thread unless asynchronous execution is configured.

**Example:** A slow listener can extend the request that publishes the event.

### Q29. What makes an application context hierarchy useful?

**Answer:** Parent and child contexts can share some infrastructure while isolating subsets of beans, though modern Boot apps rarely need complex hierarchies.

**Example:** Use it only when distinct context boundaries are actually required.

### Q30. How can you make a Spring bean safe for concurrent requests?

**Answer:** Keep it stateless or protect mutable shared state with appropriate concurrency controls.

**Example:** Store request data in method locals, not singleton fields.

## Testing and production behavior

### Q31. How can a Spring test replace one bean with a fake?

**Answer:** Register a test configuration or use the supported bean override mechanism so the test context injects a controlled implementation.

**Example:** A fake payment gateway makes a service test deterministic.

### Q32. Why can a test pass alone but fail in a suite?

**Answer:** Shared mutable state, static caches, time assumptions, execution order, or leaked context configuration can affect later tests.

**Example:** Run the failing test with its neighbors and inspect state cleanup.

### Q33. What does the Spring test context cache reuse?

**Answer:** It reuses contexts with matching configuration to reduce test startup cost; too many distinct configurations can reduce cache hits.

**Example:** Keep test configuration focused and consistent.

### Q34. How should an application handle a bean that depends on an unavailable optional service?

**Answer:** Separate optional capability health from core readiness, and define a clear degraded behavior.

**Example:** Do not mark the whole service unready for a nonessential recommendation provider.

### Q35. How can you troubleshoot a bean creation exception efficiently?

**Answer:** Read the deepest cause first, then follow the dependency chain and inspect recent configuration or classpath changes.

**Example:** A missing property may appear beneath several wrapper exceptions.

### Q36. What is circular dependency resolution a signal of?

**Answer:** It often reveals coupled responsibilities or unclear object construction order, even if the container can resolve some setter cycles.

**Example:** Extract a coordinator or move shared behavior to a third bean.

### Q37. Why should initialization hooks avoid long blocking work?

**Answer:** They delay readiness and can make startup or shutdown exceed orchestration timeouts.

**Example:** Move refreshable work to a managed background task if safe.

### Q38. What is the risk of exposing a raw target instead of its proxy?

**Answer:** Calls may bypass transactions, security, caching, or other advice attached to the proxy.

**Example:** Inject the managed bean rather than constructing its implementation yourself.

### Q39. How can an AOP proxy affect type checks or serialization?

**Answer:** The proxy may have a different runtime class or exposed interfaces than the target.

**Example:** Prefer interface-based contracts and avoid relying on concrete proxy class identity.

### Q40. When would AspectJ weaving be considered instead of proxies?

**Answer:** When advice must apply to self-invocation or non-Spring objects and the added build/runtime complexity is justified.

**Example:** Most service-layer concerns can stay with proxy-based AOP.

## References

- [Spring IoC and Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Spring bean scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html)
- [Spring Java configuration and @Bean](https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html)
- [Spring AOP proxying](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
