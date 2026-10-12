# Spring Framework Interview Questions

## What is Dependency Injection?

Dependency Injection means an object receives the collaborators it needs instead of constructing them itself. Spring's container creates and wires these objects, which keeps classes easier to test and change.

Constructor injection is a good default: required dependencies are visible, fields can be final, and a class cannot be created in an incomplete state.

~~~java
@Service
class OrderService {
    private final PaymentClient paymentClient;

    OrderService(PaymentClient paymentClient) {
        this.paymentClient = paymentClient;
    }
}
~~~

A single constructor does not need <code>@Autowired</code> in current Spring Framework versions. Avoid field injection when you want dependencies to be explicit and testable. See [Spring constructor injection](https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html).

## @Component vs @Service vs @Repository vs @Controller

All four identify classes for component scanning. <code>@Component</code> is the general-purpose choice. <code>@Service</code> marks application or business logic; <code>@Repository</code> marks data access and participates in persistence exception translation; <code>@Controller</code> marks an MVC controller. <code>@RestController</code> combines controller behavior with response-body handling.

The specialized names tell a reader what role a class plays. They do not create different singleton behavior by themselves.

## What are Spring's default bean scopes?

The default is singleton: one bean instance per Spring container. It is not automatically one instance for the whole JVM or one per user request.

Other common scopes include prototype (a new instance when requested), request and session for web applications, and application or websocket scopes. Use request scope only for genuinely request-bound state. Stateless services are usually a good fit for singleton scope; avoid storing per-user mutable data in a singleton service.

## Spring vs Spring Boot

Spring is the framework and container: dependency injection, web support, transactions, and other building blocks. Spring Boot builds on Spring and makes a runnable application easier to configure with starters, auto-configuration, and embedded servers.

Boot does not replace Spring. It chooses sensible configuration based on the classpath and settings, while still letting the application override those choices.

## What is a circular dependency, and how do you fix it?

A circular dependency exists when A requires B to be created and B requires A. It often signals that responsibilities are tangled. Split the shared behavior into a third service, or change one dependency to an event, callback, or narrower interface. Do not reach first for lazy injection to hide the design problem.

If a cycle appears only after deployment, compare the deployed configuration and bean graph with the working environment, then reproduce it with the same profiles and property sources.

## Startup time rose from 15 seconds to two minutes. How would you investigate?

Compare startup logs and timing from a known-good release. Find which phase grew: classpath scanning, bean creation, database migration, external connection, or warm-up. Check for retries with long timeouts, new migrations, classpath growth, and slow network calls during initialization.

Measure before changing settings. Then remove unnecessary work from startup or make optional dependencies connect lazily with bounded retries. Keep readiness false until the service can safely handle traffic.

## What does SpringFactoriesLoader do?

`SpringFactoriesLoader` is a classpath extension mechanism: it finds factory implementation names declared under a factory type in `META-INF/spring.factories` resources across JARs, instantiates them, and returns them in order. Framework libraries use it to discover pluggable strategies without hard-coding every implementation. It is different from component scanning and does not itself make each result an ordinary application bean. In modern Spring Boot, auto-configuration candidates are listed in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`; `spring.factories` remains relevant to other factory contracts and compatibility. See the [SpringFactoriesLoader API](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/support/SpringFactoriesLoader.html) and [Spring Boot auto-configuration registration](https://docs.spring.io/spring-boot/reference/features/developing-auto-configuration.html).
