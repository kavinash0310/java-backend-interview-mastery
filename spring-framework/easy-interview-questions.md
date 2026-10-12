# Spring Framework: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q07. What is Inversion of Control?

**Answer:** The framework manages object creation and wiring while application code declares what it needs. This reduces manual construction between classes.

**Example:** A service receives a repository in its constructor.

### Q08. What is an ApplicationContext?

**Answer:** Spring's central container for bean creation, dependency wiring, configuration, and application events. It builds on BeanFactory with additional application features.

### Q09. What is a Spring bean?

**Answer:** An object managed by the Spring container. It can be created through component scanning, a @Bean method, or other configuration.

### Q10. What is component scanning?

**Answer:** It searches configured packages for annotated classes and registers them as beans. Keep scan boundaries deliberate.

### Q11. Why is constructor injection commonly preferred?

**Answer:** It makes required dependencies explicit, supports final fields, and makes the class easier to instantiate in a unit test.

### Q12. What does @Autowired do?

**Answer:** It asks Spring to resolve and inject a matching dependency. With a single constructor in modern Spring, the annotation is generally optional.

### Q13. What does @Bean do?

**Answer:** It marks a method whose return value should be registered as a bean. It is useful for configuring third-party objects you cannot annotate.

### Q14. What does @Configuration do?

**Answer:** It marks a class that declares bean definitions and configuration. Its @Bean methods participate in Spring configuration processing.

### Q15. What does @Qualifier do?

**Answer:** It helps choose a particular bean when several candidates match the same type. Use a meaningful qualifier to make wiring intent clear.

### Q16. What does @Primary do?

**Answer:** It marks one bean as the preferred choice when several match a single-valued injection point. A qualifier can still choose another bean.

### Q17. What is the default Spring bean scope?

**Answer:** Singleton: one instance per bean definition in one Spring container. It is not one instance across every JVM or application context.

### Q18. What is prototype scope?

**Answer:** Spring creates a new bean instance each time it is requested from the container. Spring does not manage the full destruction lifecycle of prototypes.

### Q19. What is a Spring bean lifecycle?

**Answer:** At a high level, Spring creates the bean, injects dependencies, runs initialization callbacks, and may run destruction callbacks.

### Q20. What do @PostConstruct and @PreDestroy do?

**Answer:** They mark initialization and cleanup methods for a managed bean. Use them for lifecycle-related resource setup and release.

### Q21. What are Spring profiles?

**Answer:** Profiles let an application register or configure beans for named environments, such as local, test, or production. Keep secrets out of committed profile files.

### Q22. What is externalized configuration?

**Answer:** It means settings come from outside compiled code, such as properties or environment variables, so one artifact can run in different environments.

### Q23. What is Spring AOP?

**Answer:** It applies cross-cutting behavior such as transactions around method calls, commonly through proxies, keeping business code focused.

### Q24. What is a Spring proxy?

**Answer:** An object that wraps a target and intercepts calls to add behavior. Transactions and security commonly rely on proxy interception.

### Q25. Why can self-invocation bypass a Spring proxy?

**Answer:** A method calling another method on the same object does not pass through the external proxy, so the second method's advice may not run.

### Q26. What is a circular dependency?

**Answer:** Bean A needs B while B also needs A. Break the cycle by separating responsibilities or introducing a third collaborator.

### Q27. What are application events in Spring?

**Answer:** They let one part of an application publish an event for interested listeners. They decouple in-process reactions but are not a durable broker.

### Q28. What does @EventListener do?

**Answer:** It marks a method to handle a matching Spring application event. Make transaction and failure behavior clear for important side effects.

### Q29. What is a BeanFactory?

**Answer:** Spring's basic container abstraction for creating and wiring beans. ApplicationContext adds application features such as events and resource handling.

### Q30. What is a factory method in Spring configuration?

**Answer:** A method, often marked @Bean, that builds and returns an object for the container to manage. It can express setup that annotations cannot.

## References

- [Spring IoC and Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Spring bean scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html)
- [Spring Java configuration and @Bean](https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html)
- [Spring AOP proxying](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
