# Spring Framework: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is Inversion of Control?

**Answer:** The framework manages object creation and wiring while application code declares what it needs. This reduces manual construction between classes.

**Example:** A service receives a repository in its constructor.

### Q2. What is an ApplicationContext?

**Answer:** Spring's central container for bean creation, dependency wiring, configuration, and application events. It builds on BeanFactory with additional application features.

### Q3. What is a Spring bean?

**Answer:** An object managed by the Spring container. It can be created through component scanning, a @Bean method, or other configuration.

### Q4. What is component scanning?

**Answer:** It searches configured packages for annotated classes and registers them as beans. Keep scan boundaries deliberate.

### Q5. Why is constructor injection commonly preferred?

**Answer:** It makes required dependencies explicit, supports final fields, and makes the class easier to instantiate in a unit test.

### Q6. What does @Autowired do?

**Answer:** It asks Spring to resolve and inject a matching dependency. With a single constructor in modern Spring, the annotation is generally optional.

### Q7. What does @Bean do?

**Answer:** It marks a method whose return value should be registered as a bean. It is useful for configuring third-party objects you cannot annotate.

### Q8. What does @Configuration do?

**Answer:** It marks a class that declares bean definitions and configuration. Its @Bean methods participate in Spring configuration processing.

### Q9. What does @Qualifier do?

**Answer:** It helps choose a particular bean when several candidates match the same type. Use a meaningful qualifier to make wiring intent clear.

### Q10. What does @Primary do?

**Answer:** It marks one bean as the preferred choice when several match a single-valued injection point. A qualifier can still choose another bean.

### Q11. What is the default Spring bean scope?

**Answer:** Singleton: one instance per bean definition in one Spring container. It is not one instance across every JVM or application context.

### Q12. What is prototype scope?

**Answer:** Spring creates a new bean instance each time it is requested from the container. Spring does not manage the full destruction lifecycle of prototypes.

### Q13. What is a Spring bean lifecycle?

**Answer:** At a high level, Spring creates the bean, injects dependencies, runs initialization callbacks, and may run destruction callbacks.

### Q14. What do @PostConstruct and @PreDestroy do?

**Answer:** They mark initialization and cleanup methods for a managed bean. Use them for lifecycle-related resource setup and release.

### Q15. What are Spring profiles?

**Answer:** Profiles let an application register or configure beans for named environments, such as local, test, or production. Keep secrets out of committed profile files.

### Q16. What is externalized configuration?

**Answer:** It means settings come from outside compiled code, such as properties or environment variables, so one artifact can run in different environments.

### Q17. What is Spring AOP?

**Answer:** It applies cross-cutting behavior such as transactions around method calls, commonly through proxies, keeping business code focused.

### Q18. What is a Spring proxy?

**Answer:** An object that wraps a target and intercepts calls to add behavior. Transactions and security commonly rely on proxy interception.

### Q19. Why can self-invocation bypass a Spring proxy?

**Answer:** A method calling another method on the same object does not pass through the external proxy, so the second method's advice may not run.

### Q20. What is a circular dependency?

**Answer:** Bean A needs B while B also needs A. Break the cycle by separating responsibilities or introducing a third collaborator.

### Q21. What are application events in Spring?

**Answer:** They let one part of an application publish an event for interested listeners. They decouple in-process reactions but are not a durable broker.

### Q22. What does @EventListener do?

**Answer:** It marks a method to handle a matching Spring application event. Make transaction and failure behavior clear for important side effects.

### Q23. What is a BeanFactory?

**Answer:** Spring's basic container abstraction for creating and wiring beans. ApplicationContext adds application features such as events and resource handling.

### Q24. What is a factory method in Spring configuration?

**Answer:** A method, often marked @Bean, that builds and returns an object for the container to manage. It can express setup that annotations cannot.

### Q25. What is @Component?

**Answer:** A class-level marker that lets component scanning register the class as a Spring bean.

**Example:** Spring can discover a reusable helper marked `@Component`.

### Q26. @Service vs @Repository?

**Answer:** Both are specialized components; Service labels business logic, while Repository marks data access and can translate persistence exceptions.

**Example:** Keep SQL access in a repository.

### Q27. What is @Controller?

**Answer:** A Spring MVC component that handles web requests and commonly returns view names.

**Example:** Use `@RestController` for response bodies in REST endpoints.

### Q28. What is @RestController?

**Answer:** A controller whose handler results are written to the response body, commonly as JSON.

**Example:** A method can return a DTO directly.

### Q29. What is dependency injection?

**Answer:** The container supplies an object's dependencies instead of the object constructing them itself.

**Example:** A service receives its repository through a constructor.

### Q30. What is constructor injection's testing benefit?

**Answer:** Tests can create the class with explicit dependencies and do not need reflection to set fields.

**Example:** `new OrderService(fakeRepository)`.

### Q31. When might field injection cause trouble?

**Answer:** It hides required dependencies and makes plain unit construction harder.

**Example:** A test cannot see from the constructor what the service requires.

### Q32. What happens if two beans have the same type?

**Answer:** Spring may need a qualifier or a primary bean to resolve which one to inject.

**Example:** Use `@Qualifier("emailSender")` to choose one.

### Q33. What is lazy bean initialization?

**Answer:** The container creates a bean when it is first needed instead of during startup.

**Example:** It can reduce startup work but delay errors until first use.

### Q34. What is singleton scope in Spring?

**Answer:** One bean instance per container for that bean definition; it is not automatically one instance per JVM.

**Example:** A singleton service should avoid request-specific mutable fields.

### Q35. Why can mutable singleton state be unsafe?

**Answer:** Many requests may use the same instance concurrently, so shared fields can race.

**Example:** Keep the current user's ID in request context, not a service field.

### Q36. What is a bean post-processor?

**Answer:** A Spring extension point that can inspect or modify beans around initialization.

**Example:** Framework features may use processors to create proxies.

### Q37. What is @Transactional for?

**Answer:** It asks Spring's transaction support to wrap method execution with transaction behavior.

**Example:** If the method succeeds, the transaction can commit.

### Q38. What is the default rollback behavior commonly used by Spring?

**Answer:** By default, unchecked exceptions and Errors trigger rollback; checked exceptions usually do not unless configured.

**Example:** Set `rollbackFor` when checked failures should roll back.

### Q39. What is Spring's proxy limitation for self-invocation?

**Answer:** A method calling another method on `this` bypasses the proxy, so proxy-based advice may not run.

**Example:** Move the advised operation to another Spring bean.

### Q40. What is an application event useful for?

**Answer:** It lets components publish and handle an event inside the application without directly calling each other.

**Example:** Publish `OrderPlaced` to trigger local follow-up work.

## References

- [Spring IoC and Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Spring bean scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html)
- [Spring Java configuration and @Bean](https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html)
- [Spring AOP proxying](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
