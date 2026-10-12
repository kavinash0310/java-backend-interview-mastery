# Module 14 — Design Patterns and Clean Code

> **Module guide:** Learn the classic object-oriented patterns, then connect them to Java application design, concurrency, and distributed backend systems.

## Learning outcomes

- Explain the problem a pattern solves and the cost it introduces.
- Recognize all 23 patterns in the classic Gang of Four catalog.
- Use Java examples to compare patterns that look similar.
- Apply Java application, concurrency, distributed, architecture, and DDD patterns where their trade-offs fit.
- Refactor toward clearer boundaries without overengineering.

## Full pattern catalog

The catalog covers all 23 classic GoF patterns, plus commonly used Java application, concurrency, distributed backend, architecture, and Domain-Driven Design patterns. The practical catalogs are intentionally identified separately: the phrase “design pattern” is broader than the GoF catalog.

| Catalog | Includes | Guide |
|---|---|---|
| Creational GoF (5) | Abstract Factory, Builder, Factory Method, Prototype, Singleton | [Creational patterns](creational-patterns.md) |
| Structural GoF (7) | Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy | [Structural patterns](structural-patterns.md) |
| Behavioral GoF (11) | Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor | [Behavioral patterns](behavioral-patterns.md) |
| Java application (13) | DI, Repository, DAO, Service Layer, DTO, Data Mapper, Unit of Work, Identity Map, Specification, MVC, Front Controller, Active Record, Null Object | [Java application patterns](java-application-patterns.md) |
| Distributed backend (19) | Gateway/BFF, resilience, Saga/Outbox, CQRS/Event Sourcing, caching, messaging, migration, and idempotency patterns | [Distributed backend patterns](distributed-backend-patterns.md) |
| Java concurrency (13) | Producer–Consumer, pools, futures, monitors, locks, immutability, confinement, ThreadLocal, and more | [Concurrency patterns and idioms](java-concurrency-patterns.md) |
| Architecture patterns (13) | Layered, Hexagonal, Clean/Onion, modular monolith, microservices, event-driven, broker, pipe-and-filter, and more | [Architecture patterns and styles](architecture-patterns.md) |
| Domain-Driven Design (9) | Entity, Value Object, Aggregate, Domain Service/Event/Factory, Bounded Context, Context Map, Shared Kernel | [Domain-Driven Design patterns](domain-driven-design-patterns.md) |

## Pick the right pattern

- [Pattern selection and trade-offs](pattern-selection-guide.md) compares commonly confused patterns and suggests useful combinations.
- Treat the examples as focused Java 17+ illustrations. They show the roles and collaboration; framework wiring and domain types are omitted where they would distract.
- Start with the underlying requirement. Do not add a pattern merely to make a class diagram look familiar.

## Design principles

- [SOLID and dependency inversion](#design-principles)
- Composition over inheritance
- Separation of concerns and bounded responsibilities
- Refactor only when it improves clarity or enables a real variation point

## Creational patterns

See the [five GoF creational patterns](creational-patterns.md). In this group, compare factories, builders, prototypes, and object lifetime before selecting one.

## Behavioral and structural patterns

- [Seven structural patterns](structural-patterns.md) organize object composition and access.
- [Eleven behavioral patterns](behavioral-patterns.md) organize algorithms, state, and collaboration.

## Java application patterns

See [Java application patterns](java-application-patterns.md) for patterns around dependency injection, persistence, service boundaries, and web requests. The [Spring Framework module](../spring-framework/README.md) covers framework-specific behavior such as bean scopes and proxies.

## Distributed backend patterns

See [distributed backend patterns](distributed-backend-patterns.md) for resilience, messaging, data consistency, cache, and migration patterns. For deeper system-design scenarios, continue to [System Design and Architecture](../system-design-architecture/README.md), [Microservices Architecture](../microservices-architecture/README.md), and [Kafka and Messaging](../kafka-messaging/README.md).

## Domain-Driven Design patterns

See [Domain-Driven Design patterns](domain-driven-design-patterns.md) for tactical domain building blocks and strategic boundaries. These concepts help keep business language and ownership clear in a growing Java backend.

## Architecture patterns and styles

See [architecture patterns and styles](architecture-patterns.md) for application boundaries, deployment units, service integration, and data ownership. These patterns sit above GoF object design; choose them based on team, deployment, and consistency needs.

## Java concurrency patterns

See [Java concurrency patterns and idioms](java-concurrency-patterns.md). Compare ownership and message passing with shared state and locks; the [Multithreading and Concurrency module](../multithreading-concurrency/README.md) covers Java memory and synchronization fundamentals.

## Code quality and evolution

- Name types and methods after intent rather than pattern vocabulary when that reads more clearly.
- Prefer immutability, small interfaces, and constructor injection where they simplify correctness.
- Test observable behavior and invariants, not the number of pattern classes.
- Keep a rollback path when refactoring high-risk production code.

## Low-level design practice

Practice these small systems by defining the main objects and interfaces first, then explain state transitions, concurrency, and failure handling:

- **Delayed payment scheduler:** retries, duplicate triggers, idempotency, and payment status.
- **Coupon system:** eligibility, expiry, usage limits, and concurrent redemption.
- **Parking lot:** spot allocation, pricing rules, and concurrent entry/exit.
- **Vending machine:** inventory, payment, state transitions, and refunds.
- **Splitwise:** expense splitting, rounding, balances, and settlement.

For every exercise, explain why responsibilities are separated and how you would test race conditions.

## Interview question sets

- [40-question beginner-friendly Q&A](easy-interview-questions.md)
- [40-question medium-level Q&A](medium-interview-questions.md)
- [40-question hard-level Q&A](hard-interview-questions.md)
- [30-question tricky Q&A](tricky-interview-questions.md)

## References

- [GoF book catalog, publisher page](https://www.informit.com/store/design-patterns-elements-of-reusable-object-oriented-software-9780201633610)
- [Spring dependency injection](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html)
- [Spring proxying mechanisms](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
- [Microsoft cloud design patterns](https://learn.microsoft.com/azure/architecture/patterns)
- [AWS transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)

## Related modules

- [Core Java](../core-java/README.md)
- [Spring Framework](../spring-framework/README.md)
- [Testing](../testing/README.md)
- [System Design and Architecture](../system-design-architecture/README.md)