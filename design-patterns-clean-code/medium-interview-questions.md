# Design Patterns and Clean Code: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Applying design principles

### Q1. How can you tell whether a class violates Single Responsibility?

**Answer:** Look for unrelated reasons to change or collaborators that serve distinct jobs, then separate responsibilities around cohesive behavior.

**Example:** An invoice class should not also send email and render PDFs.

### Q2. How does Open/Closed guide adding a new payment type?

**Answer:** Put variation behind a stable contract so a new implementation can be added without editing a long conditional in every caller.

**Example:** Inject a `PaymentMethod` strategy.

### Q3. What is a practical Liskov Substitution test?

**Answer:** Ask whether callers relying on the base contract still behave correctly with every subtype, including edge cases and exceptions.

**Example:** A subtype must not reject inputs the base type promises to accept.

### Q4. When can an interface be too broad?

**Answer:** When implementations have to provide irrelevant methods or callers depend on capabilities they do not need.

**Example:** Split read-only and write repository contracts when clients differ.

### Q5. How does Dependency Inversion differ from dependency injection?

**Answer:** Dependency Inversion is a design principle about high-level policy depending on abstractions; injection is one way to supply dependencies.

**Example:** A constructor can inject a port defined by the application layer.

### Q6. Why can inheritance increase coupling?

**Answer:** Subclasses depend on base behavior and protected state, so a base change can alter every subclass.

**Example:** Prefer composition when behavior varies independently.

### Q7. What does high cohesion look like in a service?

**Answer:** Its methods and data contribute to one clear business responsibility.

**Example:** A pricing component calculates prices rather than coordinating email and persistence.

### Q8. How can you reduce temporal coupling?

**Answer:** Make required setup and ordering explicit or encapsulate them in one operation.

**Example:** Return a configured client from a factory instead of requiring callers to call init first.

### Q9. What is a stable abstraction?

**Answer:** A contract whose meaning changes less often than the implementations behind it.

**Example:** Define a payment port around business actions, not vendor SDK methods.

### Q10. When does DRY create the wrong abstraction?

**Answer:** Two similar lines may change for different reasons, so forcing them together can make future changes harder.

**Example:** Duplicate a tiny mapping until shared behavior is genuinely stable.

## Pattern trade-offs

### Q11. When is CQRS worth its added complexity?

**Answer:** When read and write models have meaningfully different scale or shape needs and the team can operate eventual consistency.

**Example:** Do not add separate models to a simple CRUD service without evidence.

### Q12. What is the Unit of Work pattern responsible for?

**Answer:** Tracking changes within a business operation and coordinating persistence, often through a transaction boundary.

**Example:** A persistence context acts like a unit of work.

### Q13. How can the Null Object pattern hide a real failure?

**Answer:** A no-op implementation can make a missing required dependency look successful if used where failure should be visible.

**Example:** Use it only when “do nothing” is a valid business behavior.

### Q14. When does the Factory pattern improve design?

**Answer:** When construction rules vary or are complex enough to separate from callers.

**Example:** A factory can select a parser based on file type.

### Q15. What problem does the Command pattern solve?

**Answer:** It represents an operation as data so it can be queued, logged, retried, or undone where appropriate.

**Example:** A command object can carry a request ID and payload.

### Q16. How can a Decorator become hard to debug?

**Answer:** Many nested wrappers can obscure behavior and order, especially when each adds hidden side effects.

**Example:** Name and instrument wrappers clearly.

### Q17. Why can a Singleton pattern complicate testing?

**Answer:** Global state survives between tests and hides dependencies, making isolation and parallel execution harder.

**Example:** Prefer container-managed singleton services with explicit dependencies.

### Q18. When should an Adapter be placed at an architectural boundary?

**Answer:** When translating an external or legacy interface into the application's own language and contract.

**Example:** Keep vendor-specific DTOs inside the adapter.

### Q19. How does a Facade differ from a Gateway?

**Answer:** A facade simplifies access to a subsystem in code; a gateway often mediates traffic across a network boundary.

**Example:** A checkout facade can coordinate local domain services.

### Q20. What is the main risk in applying a pattern by name?

**Answer:** The pattern can add indirection without solving a real problem, making the design harder to follow.

**Example:** Start with the change pressure and choose the simplest fitting structure.

## Refactoring and code quality

### Q21. How do you refactor safely when behavior is poorly tested?

**Answer:** Add characterization tests around current behavior, change one small step, and compare outputs before restructuring further.

**Example:** Capture edge cases before replacing the implementation.

### Q22. What is a characterization test?

**Answer:** A test that records what existing code currently does, including quirks, before a refactor.

**Example:** Use it to preserve behavior while improving structure.

### Q23. How can a code review separate correctness from style?

**Answer:** Check behavior, failure modes, and contracts first; leave purely stylistic preferences to formatters or team conventions.

**Example:** Ask for evidence when a review comment is subjective.

### Q24. What is a useful refactoring boundary in a legacy service?

**Answer:** A seam where input/output can be controlled, such as a dependency interface or transaction boundary.

**Example:** Wrap a vendor API before rewriting business logic.

### Q25. How should technical debt be prioritized?

**Answer:** Rank by user or delivery impact, frequency, risk, and cost to pay down; avoid treating all cleanup as urgent.

**Example:** Track a small debt item alongside feature work.

### Q26. What is a code smell versus a defect?

**Answer:** A smell suggests maintainability risk; a defect is observable incorrect behavior.

**Example:** A long method is a smell, while a wrong total is a defect.

### Q27. How can you reduce a long conditional without adding many classes?

**Answer:** Use guard clauses, a lookup table, or a small strategy only when variations are genuinely independent.

**Example:** A status-to-handler map can replace a repeated switch.

### Q28. Why are comments sometimes a sign to improve code instead?

**Answer:** A comment may explain an unclear name or complex branch that could be made explicit in code.

**Example:** Rename `x` to `retryCount` before documenting it.

### Q29. How should a method communicate failure?

**Answer:** Use a clear return type or exception contract that callers can handle without guessing.

**Example:** Return `Optional` for absence; throw for unexpected failure.

### Q30. What makes a code example interview-ready?

**Answer:** It shows the important trade-off and edge case while keeping incidental setup out of the explanation.

**Example:** State complexity and why the selected structure fits.

## Architecture and testability

### Q31. What does hexagonal architecture mean in practical terms?

**Answer:** Keep domain rules inside and connect them to external systems through ports and adapters.

**Example:** A database adapter implements an application repository port.

### Q32. How does dependency direction protect business rules?

**Answer:** Infrastructure depends inward on application contracts, so database or framework changes do not define domain behavior.

**Example:** The domain should not import Spring annotations.

### Q33. What is a domain service?

**Answer:** A stateless operation that expresses domain behavior spanning multiple entities when it does not naturally belong to one entity.

**Example:** A transfer service can enforce rules across two accounts.

### Q34. When is a value object preferable to primitive fields?

**Answer:** When a value has validation or behavior that should stay consistent across the model.

**Example:** Represent money as amount plus currency, not as a bare double.

### Q35. What is an aggregate boundary used for?

**Answer:** It defines which invariants are enforced atomically through one root and transaction.

**Example:** Change child entities through the aggregate root when they share an invariant.

### Q36. What is an anti-corruption layer?

**Answer:** A translation boundary that prevents an external model or legacy system from shaping the internal domain.

**Example:** Map partner status codes into internal states.

### Q37. How can you test architecture boundaries?

**Answer:** Add automated checks that restrict package dependencies or enforce module APIs.

**Example:** Fail the build if domain code imports infrastructure packages.

### Q38. What is a modular monolith's key design requirement?

**Answer:** Modules need explicit interfaces and controlled dependencies even though they deploy together.

**Example:** Do not let every module query another module's tables directly.

### Q39. Why use an immutable command object?

**Answer:** It captures the request's intent as a stable value and is easier to validate, log, or pass between layers.

**Example:** A `CreateOrder` command contains customer ID and line items.

### Q40. How do you decide whether a behavior belongs in an entity or service?

**Answer:** Put invariant-preserving behavior with the state it governs; use a service for coordination across objects or external ports.

**Example:** An order can enforce its own valid state transitions.

## References

- [Refactoring.Guru design patterns catalog](https://refactoring.guru/design-patterns/catalog)
- [Spring Framework Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Java Object-Oriented Programming concepts](https://docs.oracle.com/javase/tutorial/java/concepts/)
