# Spring Boot: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q05. What is a Spring Boot starter?

**Answer:** A dependency bundle for a common application capability. It gives a project a compatible starting set of libraries.

### Q06. What is auto-configuration?

**Answer:** It registers useful beans based on libraries present and application settings, and backs off when an application supplies a matching bean.

### Q07. Can you disable a particular auto-configuration?

**Answer:** Yes. Boot supports excluding an auto-configuration class. First check whether ordinary configuration can express the setup more clearly.

### Q08. What does @SpringBootApplication combine?

**Answer:** Configuration, component scanning, and auto-configuration enablement in one common application annotation.

### Q09. What is a Spring Boot application entry point?

**Answer:** Usually a main method calling SpringApplication.run, which creates the application context and starts the configured app.

### Q10. application.properties vs application.yml?

**Answer:** Both express external configuration. YAML supports nesting; properties is simple key=value text. Choose one consistent format.

### Q11. How do profiles help in Spring Boot?

**Answer:** They allow environment-specific beans or settings to be selected. Avoid putting production passwords in committed profile files.

### Q12. What is configuration precedence?

**Answer:** When several sources define a setting, Boot applies an order so higher-priority sources can override lower-priority defaults. Check the reference for your Boot version.

### Q13. What is configuration properties binding?

**Answer:** It maps external settings into a typed Java object, making related configuration easier to validate and discover.

### Q14. How can Boot validate configuration at startup?

**Answer:** Bind settings into a configuration-properties class and apply validation constraints so invalid configuration fails early.

### Q15. What is a CommandLineRunner?

**Answer:** A callback that runs after the application context starts and receives command-line arguments. It is useful for short startup tasks.

### Q16. What is a Spring Boot Actuator endpoint?

**Answer:** It exposes operational information or actions through a management interface. Enable and expose only endpoints appropriate for the deployment.

### Q17. What are health and readiness checks?

**Answer:** Health reports whether the app or dependency is functioning; readiness says whether it should receive traffic now. A running process may not be ready.

### Q18. Liveness vs readiness?

**Answer:** Liveness asks whether a process should restart; readiness asks whether it should receive requests. A temporary dependency outage should not cause a restart loop.

### Q19. How do you add a REST endpoint in Spring Boot?

**Answer:** Create a controller, map an HTTP route, accept typed input, delegate business work to a service, and return a clear response.

### Q20. What is a controller in Spring MVC?

**Answer:** It handles web requests and maps them to application behavior. @RestController is common when returning JSON data.

### Q21. What is request validation?

**Answer:** It checks incoming data against constraints before business logic uses it. Return a consistent client error without exposing internals.

### Q22. What does @Valid do on a request body?

**Answer:** It asks the validation framework to check configured constraints on the bound object. A global handler can turn failures into a useful 400 response.

### Q23. Filter vs interceptor?

**Answer:** A servlet filter runs around servlet processing; a HandlerInterceptor runs within Spring MVC around handler execution and can access handler details.

### Q24. What is a message converter?

**Answer:** It converts HTTP bodies between formats such as JSON and Java objects. Spring MVC selects one using media types and configuration.

### Q25. What is content negotiation?

**Answer:** How client and server agree on a representation format, often using Accept and Content-Type headers. Document supported formats.

### Q26. How do you set a downstream call timeout?

**Answer:** Configure timeout on the HTTP client and its pool, then handle timeout failures at the service boundary. A controller timeout alone may not stop a blocked call.

### Q27. How do you handle graceful shutdown?

**Answer:** Stop accepting new work, let in-flight requests finish within a limit, and close resources. Test with the container's termination timeout.

### Q28. Why should Actuator endpoints be secured?

**Answer:** They can reveal system information or enable management actions. Expose the minimum set and restrict access.

### Q29. How do you troubleshoot a Boot startup failure?

**Answer:** Read the first meaningful cause, then check configuration binding, missing dependencies, bean creation, and external access. Reproduce with the same profile.

### Q30. What is a Spring Boot fat JAR?

**Answer:** It packages the application and runtime dependencies so it can be launched with java. It does not include the JVM itself.

## References

- [Spring Boot auto-configuration](https://docs.spring.io/spring-boot/reference/using/auto-configuration.html)
- [Spring Boot externalized configuration](https://docs.spring.io/spring-boot/reference/features/external-config.html)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
- [Spring Boot web applications](https://docs.spring.io/spring-boot/reference/web/servlet.html)
