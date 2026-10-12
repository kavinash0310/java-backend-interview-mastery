# Choosing and Combining Design Patterns

Start with the problem that changes, not with a pattern name. A pattern is a reusable trade-off, not a checklist or a goal. Keep object-level patterns (such as Strategy) separate from architecture choices (such as modular monolith or microservices); they answer questions at different scales.

## A quick decision process

1. Name the pressure: object creation, an unstable algorithm, an external API, an event flow, a state machine, or a failure boundary.
2. Choose the smallest design that makes the pressure explicit. A function, interface, or composition may be enough.
3. Compare the cost: extra types, indirection, state, runtime work, operational ownership, and test burden.
4. Keep the pattern at the boundary where it solves the problem; avoid spreading its vocabulary through unrelated code.
5. Revisit it when the original variation or failure mode disappears.

## Patterns that are easy to confuse

| If you need… | Consider… | Difference to keep in mind |
|---|---|---|
| a family of compatible products | Abstract Factory | Creates related products together; Factory Method chooses one product through an overridable hook. |
| readable construction with optional values | Builder | The builder assembles one object; it is not a family factory. |
| a compatible wrapper around an old API | Adapter | Adapter translates an existing interface; Bridge separates two dimensions designed to vary independently. |
| extra behavior around an object | Decorator | Decorator composes behavior while retaining the contract; Proxy controls access to a subject. |
| a simpler entry point to a subsystem | Facade | Facade simplifies a group of components; it does not automatically provide authorization or transaction guarantees. |
| interchangeable algorithms | Strategy | Caller selects a policy; State delegates behavior based on current lifecycle state. |
| shared algorithm steps with a few extension hooks | Template Method | The base class fixes the sequence; Strategy uses composition to swap a whole behavior. |
| local notifications | Observer | In-process subscribers receive callbacks; a brokered pub/sub system adds persistence, delivery, and network semantics. |
| database persistence boundary | DAO or Repository | DAO emphasizes storage operations; Repository presents domain-oriented collection/use-case operations. The distinction depends on the application. |
| transient remote failures | Timeout, Retry, Circuit Breaker, Bulkhead | Timeout bounds waiting; Retry repeats eligible work; Breaker stops calls to an unhealthy dependency; Bulkhead caps isolated in-flight work. |
| long cross-service business workflow | Saga | Uses local commits and compensations; it does not provide one global ACID transaction. |
| separate read and write models | CQRS | Can be used without Event Sourcing; Event Sourcing stores state changes as the source of truth. |

## Patterns that commonly work together

- A Spring application often combines Dependency Injection with Strategy and Proxy-based transactions. Spring proxy behavior has practical limits, including self-invocation; check the [Spring proxy reference](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html).
- A reliable event publication flow often combines a local transaction, Transactional Outbox, at-least-once delivery, and idempotent consumers. The [AWS outbox guide](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html) explains the dual-write problem.
- A service call may use a deadline, a small retry budget for transient errors, a circuit breaker, and an isolated concurrency limit. These address different failure modes; combining them requires one end-to-end latency budget.
- A Saga often uses orchestration or choreography, durable workflow state, idempotent steps, and compensating actions. See [Microsoft’s Saga guidance](https://learn.microsoft.com/en-us/azure/architecture/patterns/saga).
- CQRS may use a transactional outbox to update a read projection reliably, but the added model and eventual consistency should be justified by workload needs. See [Microsoft’s CQRS guidance](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## Patterns to avoid applying automatically

- **Singleton everywhere:** turns dependencies into global state and complicates tests. Prefer explicit injection; use a singleton only when its lifecycle and shared state are truly intended.
- **Abstract factories with one product family:** adds types without giving a real variation point.
- **Retry every exception:** amplifies overload and can duplicate side effects. Retry only eligible transient failures, with a bounded deadline and idempotent operation.
- **CQRS or Event Sourcing for ordinary CRUD:** introduces projection lag, replay, and schema-evolution obligations. Start with a straightforward transactional model.
- **A generic repository for every table:** may hide query cost and domain meaning. Prefer explicit operations that reflect the use case.
- **A pattern-shaped class without a problem:** names do not improve design by themselves; measure clarity against the added indirection.

## Interview answer template

Explain a pattern in this order: the problem and forces, the roles and flow, a concrete example, why it fits, the main trade-off, failure or concurrency behavior, and one simpler alternative. Mention what the pattern does not guarantee.

[Pattern catalog](README.md) · [Course home](../README.md)