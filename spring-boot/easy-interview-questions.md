# Spring Boot: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is a Spring Boot starter?

**Answer:** A dependency bundle for a common application capability. It gives a project a compatible starting set of libraries.

### Q2. What is auto-configuration?

**Answer:** It registers useful beans based on libraries present and application settings, and backs off when an application supplies a matching bean.

### Q3. Can you disable a particular auto-configuration?

**Answer:** Yes. Boot supports excluding an auto-configuration class. First check whether ordinary configuration can express the setup more clearly.

### Q4. What does @SpringBootApplication combine?

**Answer:** Configuration, component scanning, and auto-configuration enablement in one common application annotation.

### Q5. What is a Spring Boot application entry point?

**Answer:** Usually a main method calling SpringApplication.run, which creates the application context and starts the configured app.

### Q6. application.properties vs application.yml?

**Answer:** Both express external configuration. YAML supports nesting; properties is simple key=value text. Choose one consistent format.

### Q7. How do profiles help in Spring Boot?

**Answer:** They allow environment-specific beans or settings to be selected. Avoid putting production passwords in committed profile files.

### Q8. What is configuration precedence?

**Answer:** When several sources define a setting, Boot applies an order so higher-priority sources can override lower-priority defaults. Check the reference for your Boot version.

### Q9. What is configuration properties binding?

**Answer:** It maps external settings into a typed Java object, making related configuration easier to validate and discover.

### Q10. How can Boot validate configuration at startup?

**Answer:** Bind settings into a configuration-properties class and apply validation constraints so invalid configuration fails early.

### Q11. What is a CommandLineRunner?

**Answer:** A callback that runs after the application context starts and receives command-line arguments. It is useful for short startup tasks.

### Q12. What is a Spring Boot Actuator endpoint?

**Answer:** It exposes operational information or actions through a management interface. Enable and expose only endpoints appropriate for the deployment.

### Q13. What are health and readiness checks?

**Answer:** Health reports whether the app or dependency is functioning; readiness says whether it should receive traffic now. A running process may not be ready.

### Q14. Liveness vs readiness?

**Answer:** Liveness asks whether a process should restart; readiness asks whether it should receive requests. A temporary dependency outage should not cause a restart loop.

### Q15. How do you add a REST endpoint in Spring Boot?

**Answer:** Create a controller, map an HTTP route, accept typed input, delegate business work to a service, and return a clear response.

### Q16. What is a controller in Spring MVC?

**Answer:** It handles web requests and maps them to application behavior. @RestController is common when returning JSON data.

### Q17. What is request validation?

**Answer:** It checks incoming data against constraints before business logic uses it. Return a consistent client error without exposing internals.

### Q18. What does @Valid do on a request body?

**Answer:** It asks the validation framework to check configured constraints on the bound object. A global handler can turn failures into a useful 400 response.

### Q19. Filter vs interceptor?

**Answer:** A servlet filter runs around servlet processing; a HandlerInterceptor runs within Spring MVC around handler execution and can access handler details.

### Q20. What is a message converter?

**Answer:** It converts HTTP bodies between formats such as JSON and Java objects. Spring MVC selects one using media types and configuration.

### Q21. What is content negotiation?

**Answer:** How client and server agree on a representation format, often using Accept and Content-Type headers. Document supported formats.

### Q22. How do you set a downstream call timeout?

**Answer:** Configure timeout on the HTTP client and its pool, then handle timeout failures at the service boundary. A controller timeout alone may not stop a blocked call.

### Q23. How do you handle graceful shutdown?

**Answer:** Stop accepting new work, let in-flight requests finish within a limit, and close resources. Test with the container's termination timeout.

### Q24. Why should Actuator endpoints be secured?

**Answer:** They can reveal system information or enable management actions. Expose the minimum set and restrict access.

### Q25. How do you troubleshoot a Boot startup failure?

**Answer:** Read the first meaningful cause, then check configuration binding, missing dependencies, bean creation, and external access. Reproduce with the same profile.

### Q26. What is a Spring Boot fat JAR?

**Answer:** It packages the application and runtime dependencies so it can be launched with java. It does not include the JVM itself.

### Q27. What is the difference between Spring and Spring Boot?

**Answer:** Spring provides the framework; Boot adds conventions, auto-configuration, starters, and packaging to get applications running faster.

**Example:** Boot still uses the Spring Framework.

### Q28. How does Boot decide whether to auto-configure a bean?

**Answer:** Auto-configuration checks classpath, existing beans, and configuration conditions before applying defaults.

**Example:** Providing your own DataSource can make a default back off.

### Q29. What is an embedded server?

**Answer:** A web server packaged with the application so it can run as a standalone process.

**Example:** A Boot JAR can start an embedded Tomcat server.

### Q30. What is Spring Boot configuration metadata?

**Answer:** Metadata that helps IDEs offer completion and documentation for application properties.

**Example:** It can describe custom `@ConfigurationProperties` fields.

### Q31. What does @ConfigurationProperties do?

**Answer:** It binds a group of external settings to a typed Java object.

**Example:** Use a `MailProperties` class for `mail.*` settings.

### Q32. How can an application expose metrics to a monitoring system?

**Answer:** Instrument the app and expose metrics through a secured endpoint or exporter supported by the chosen monitoring stack.

**Example:** Spring Boot Actuator can expose a metrics endpoint.

### Q33. What is a profile-specific config file?

**Answer:** A configuration file activated for a particular environment or profile.

**Example:** `application-prod.yml` can hold production-specific non-secret settings.

### Q34. What is a health indicator?

**Answer:** A component that reports whether a dependency or application capability is healthy.

**Example:** A database health indicator can check connectivity.

### Q35. What is a custom health indicator?

**Answer:** A small component that reports the health of an application dependency or capability.

**Example:** A health indicator can check a required external service.

### Q36. What is a DTO?

**Answer:** A data-transfer object shaped for an API or boundary rather than directly exposing an entity.

**Example:** Return `OrderResponse` instead of a JPA entity.

### Q37. How do you handle API exceptions globally?

**Answer:** Use `@ControllerAdvice` with exception handlers to return consistent error responses.

**Example:** Map validation failures to a clear 400 response.

### Q38. What is a Filter in a Boot web app?

**Answer:** A servlet-level component that can inspect or wrap requests and responses before MVC handling.

**Example:** A filter can add a correlation ID.

### Q39. Why use a connection pool?

**Answer:** It reuses database connections rather than opening a fresh connection for every request.

**Example:** Tune pool size against database capacity and workload.

### Q40. What is Spring Boot DevTools?

**Answer:** A development-time tool that can restart the app after classpath changes and improve local feedback.

**Example:** Keep it out of production runtime packaging when not needed.

## References

- [Spring Boot auto-configuration](https://docs.spring.io/spring-boot/reference/using/auto-configuration.html)
- [Spring Boot externalized configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
- [Spring Boot web applications](https://docs.spring.io/spring-boot/reference/web/servlet.html)
