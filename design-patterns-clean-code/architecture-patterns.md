# Architecture Patterns and Styles

These patterns describe larger application structure and service boundaries. They sit above object-level GoF patterns: an architecture can use several design patterns, but it is not one large GoF pattern.

Each entry covers intent, a Java-oriented sketch, when it helps, trade-offs, and a practical reminder. The examples are illustrative; framework setup and external types may be omitted.

## 1. Layered Architecture

**Intent:** Divide an application into layers with clear responsibilities, commonly presentation, application, domain, and persistence.

**Java example:**

```java
// Typical request path
@RestController class OrderController { /* HTTP */ }
@Service class OrderService { /* use case */ }
@Repository interface OrderRepository { /* persistence */ }
```

**Use it when:** Use for conventional business applications where a clear vertical request flow is easy to understand and deploy.

**Trade-offs and pitfalls:** Familiar and approachable. Layers can become pass-through boilerplate, and careless dependencies can make every change cross the whole stack.

**Remember:** Organize by responsibility; prevent layer leakage.

## 2. Hexagonal Architecture (Ports and Adapters)

**Intent:** Keep application/domain logic at the center and connect it to databases, HTTP, messaging, or vendors through ports and adapters.

**Java example:**

```java
interface PaymentPort { PaymentResult charge(Money amount); }
final class CheckoutUseCase {
    private final PaymentPort payments;
    CheckoutUseCase(PaymentPort payments) { this.payments=payments; }
}
final class BankApiAdapter implements PaymentPort {
    public PaymentResult charge(Money amount) { /* call provider */ return null; }
}
```

**Use it when:** Use when business behavior must be tested without infrastructure or when external systems are likely to change.

**Trade-offs and pitfalls:** Protects the core from framework and vendor choices. Too many tiny interfaces can obscure a small application.

**Remember:** Core owns ports; outside adapters implement them.

## 3. Onion / Clean Architecture

**Intent:** Keep domain rules independent of frameworks, databases, and delivery mechanisms by making source dependencies point inward.

**Java example:**

```java
// Inward dependency:
interface SaveOrder { void save(Order order); } // core-owned port
final class PlaceOrderUseCase {
    private final SaveOrder orders;
    PlaceOrderUseCase(SaveOrder orders) { this.orders=orders; }
}
// An outer JDBC adapter implements SaveOrder.
```

**Use it when:** Use for domain-heavy systems where long-lived business rules should survive framework or storage changes.

**Trade-offs and pitfalls:** Makes business policies easier to isolate and test. More boundaries and mapping can slow simple CRUD work.

**Remember:** Source code points toward policy, not infrastructure.

## 4. Modular Monolith

**Intent:** Deploy one application while keeping capabilities inside explicit modules with owned APIs and data boundaries.

**Java example:**

```java
// orders module exposes a use-case API
public interface Orders {
    OrderSummary place(PlaceOrder command);
}
// billing internals remain package-private in the billing module.
```

**Use it when:** Use when one deployable unit is valuable but teams need boundaries that make later scaling or extraction possible.

**Trade-offs and pitfalls:** Keeps deployment and local transactions simpler than microservices. Requires discipline to prevent cross-module shortcuts and shared-table coupling.

**Remember:** One deployment, many real boundaries.

## 5. Microservices Architecture

**Intent:** Split a system into independently deployable services organized around business capabilities, each owning its behavior and data.

**Java example:**

```java
// A request crosses service contracts, not shared classes:
OrderApi -> InventoryApi
OrderEvents -> PaymentConsumer
```

**Use it when:** Use when independent deployment, scaling, or team ownership outweighs distributed-systems overhead.

**Trade-offs and pitfalls:** Enables independent releases and scaling. Adds network failure, observability, data consistency, deployment, and local-development costs.

**Remember:** Independent ownership costs more than method calls.

## 6. Service-Oriented Architecture (SOA)

**Intent:** Compose business capabilities as services exposed through explicit contracts, often for integration across large enterprise systems.

**Java example:**

```java
interface CustomerLookup {
    CustomerProfile find(CustomerNumber id);
}
// Several business applications call through a governed service contract.
```

**Use it when:** Use for enterprise integration where multiple systems need stable, reusable capabilities and governance matters.

**Trade-offs and pitfalls:** Can reduce point-to-point integration and standardize contracts. Shared central middleware and broad service reuse can create coupling and bottlenecks.

**Remember:** Services integrate systems through contracts; governance is part of the design.

## 7. Event-Driven Architecture

**Intent:** Publish facts about state changes so interested consumers can react asynchronously.

**Java example:**

```java
record OrderPlaced(String orderId,Instant occurredAt) {}
// Order service publishes OrderPlaced;
// billing and notification consumers react independently.
```

**Use it when:** Use when producers should not synchronously coordinate every downstream reaction or when asynchronous fan-out is useful.

**Trade-offs and pitfalls:** Reduces direct runtime coupling and supports independent consumers. Adds eventual consistency, duplicate delivery, ordering, schema evolution, and replay concerns.

**Remember:** Publish facts; consumers own their reaction.

## 8. Broker Architecture

**Intent:** Use a broker intermediary to receive, route, and deliver messages among independent producers and consumers.

**Java example:**

```java
producer.publish("orders.created",event);
broker.route("orders.created",subscribers);
consumer.onMessage(event);
```

**Use it when:** Use when messaging endpoints should be decoupled from one another and routing, buffering, or protocol mediation is required.

**Trade-offs and pitfalls:** Centralizes delivery capabilities and can improve decoupling. The broker becomes an operational dependency and may add latency or a failure domain.

**Remember:** Broker mediates delivery, not business correctness.

## 9. Pipe-and-Filter

**Intent:** Process data through a sequence of independent transformations, where each filter consumes and emits a defined data shape.

**Java example:**

```java
Stream<String> valid = lines
    .map(this::parse)
    .filter(this::isValid)
    .map(this::normalize);
```

**Use it when:** Use for ETL, import/export, compilers, and streaming transformations that can be composed.

**Trade-offs and pitfalls:** Filters are independently testable and reusable. Intermediate representations, ordering, error propagation, and buffering need design.

**Remember:** Small transformations connected by explicit data.

## 10. Microkernel / Plugin Architecture

**Intent:** Keep a minimal core stable and add optional capabilities through well-defined plugin contracts.

**Java example:**

```java
interface PaymentPlugin {
    boolean supports(Currency currency);
    PaymentResult pay(PaymentRequest request);
}
final class PaymentCore {
    private final List<PaymentPlugin> plugins;
    PaymentCore(List<PaymentPlugin> plugins) { this.plugins=List.copyOf(plugins); }
}
```

**Use it when:** Use for products with optional extensions or customer-specific capabilities that should not be compiled into one large conditional.

**Trade-offs and pitfalls:** Supports extensibility and keeps a small core. Plugin discovery, versioning, isolation, and compatibility become part of the platform.

**Remember:** Stable core, explicit extension contract.

## 11. Client–Server

**Intent:** Separate clients that request capabilities from servers that provide them over a communication protocol.

**Java example:**

```java
HttpRequest request=HttpRequest.newBuilder(uri)
    .GET().build();
HttpResponse<String> response=client.send(request,
    HttpResponse.BodyHandlers.ofString());
```

**Use it when:** Use for networked applications with centralized data/services and multiple clients.

**Trade-offs and pitfalls:** Centralizes shared behavior and data. Network partitions, latency, versioning, authentication, and server capacity become explicit concerns.

**Remember:** The network boundary changes reliability assumptions.

## 12. Database per Service

**Intent:** Give each service exclusive ownership of its data store or schema so other services interact through contracts rather than shared tables.

**Java example:**

```java
// Order service owns OrderRepository.
// Billing owns PaymentRepository.
// Cross-service workflow uses an event or API, not a join across both databases.
```

**Use it when:** Use when services need independent schema evolution, ownership, and deployment.

**Trade-offs and pitfalls:** Reduces database coupling and supports service autonomy. Cross-service queries and transactions become harder and often need projections or Sagas.

**Remember:** Data ownership follows service boundaries.

## 13. Service Mesh

**Intent:** Move common service-to-service networking concerns such as identity, retries, telemetry, and traffic policy into a dedicated infrastructure layer.

**Java example:**

```java
// Application calls a stable service DNS name.
// A sidecar or node proxy applies mTLS and telemetry policy.
```

**Use it when:** Use when a large service fleet needs consistent network policy independent of each language/runtime.

**Trade-offs and pitfalls:** Can standardize cross-cutting network behavior. Adds proxies, control-plane operations, configuration complexity, and another place policies can conflict.

**Remember:** Infrastructure can manage transport policy; applications still own semantics.

---

For cloud/backend pattern combinations, also see the [distributed backend catalog](distributed-backend-patterns.md) and [pattern selection guide](pattern-selection-guide.md).

References: [Microsoft Cloud Design Patterns](https://learn.microsoft.com/azure/architecture/patterns) and [Azure Architecture Center](https://learn.microsoft.com/azure/architecture/).

[Design Patterns module](README.md) · [Course home](../README.md)