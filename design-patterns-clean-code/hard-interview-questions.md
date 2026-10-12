# Design Patterns and Clean Code: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Architecture boundaries and dependency direction

### Q1. How does hexagonal architecture keep domain logic independent of infrastructure?

**Answer:** The domain defines ports for required capabilities; adapters implement those ports at the edge. Dependencies point inward, so tests can replace adapters.

### Q2. When is a domain service a better home than an entity method?

**Answer:** When a business operation spans multiple aggregates or does not naturally belong to one entity. Keep invariants close to the state they protect.

### Q3. How can a modular monolith enforce architecture without network boundaries?

**Answer:** Use package/module visibility, explicit APIs, dependency checks, and separate ownership. Test that modules do not reach into each other's internals.

### Q4. What is the difference between a repository and a DAO in domain-driven design?

**Answer:** A repository presents collection-like access to domain aggregates; a DAO often exposes persistence-oriented operations. Keep domain contracts free of SQL-shaped leakage.

### Q5. How should an anti-corruption layer handle a legacy enum with unknown values?

**Answer:** Map known values to domain concepts and preserve or quarantine unknowns without inventing semantics. Log version context and design a migration path.

### Q6. Why is a shared-kernel module risky across independently deployed services?

**Answer:** Coordinated changes and version drift couple teams, and business concepts may diverge. Share stable technical contracts sparingly; duplicate small logic when ownership differs.

### Q7. How do you tell whether a class is a useful abstraction or just indirection?

**Answer:** It should capture a stable policy or variation and make change safer. If it only forwards calls or hides one line, it may add navigation cost without value.

### Q8. What does dependency inversion require of a high-level policy?

**Answer:** It should depend on abstractions it owns rather than concrete infrastructure details. Adapters implement those abstractions and translate external models.

### Q9. Why can a giant service class indicate a missing domain boundary?

**Answer:** It may coordinate many unrelated invariants and reasons to change. Split by cohesive behavior, but avoid one class per trivial method.

### Q10. How do architecture decision records reduce repeated debate?

**Answer:** They capture context, options, trade-offs, and consequences at decision time. Keep them concise, dated, and update status when the choice changes.

## Patterns and trade-offs

### Q11. When is Strategy preferable to a conditional tree?

**Answer:** Use Strategy when algorithms vary independently and new variants should be added without growing one conditional. A small stable conditional may be clearer.

### Q12. How does State differ from Strategy when both delegate behavior?

**Answer:** State represents lifecycle transitions and often changes the active behavior; Strategy selects an interchangeable algorithm without implied transitions.

### Q13. Why can Decorator be safer than subclassing for optional behavior?

**Answer:** It composes behavior at runtime without inheriting implementation assumptions. Keep wrapper order explicit because decorators can alter semantics.

### Q14. When is CQRS justified, and what complexity does it add?

**Answer:** Use separate read and write models when their workloads or shapes differ materially. It adds synchronization, duplicate representations, and operational complexity.

### Q15. Why is event sourcing a poor default for every aggregate?

**Answer:** It requires event evolution, replay, projections, and operational tooling. Choose it when history and temporal reconstruction are core requirements, not merely audit logging.

### Q16. How does a functional core simplify tests for a side-effecting workflow?

**Answer:** Pure decisions can be tested from inputs to outputs; an imperative shell performs I/O. It requires explicit boundary data and may not fit every codebase.

### Q17. When should Template Method give way to composition?

**Answer:** Prefer composition when subclasses override many hooks or inherit fragile sequencing. A set of small collaborators exposes variation more directly.

### Q18. How can an Adapter prevent vendor types from spreading across a codebase?

**Answer:** Translate vendor request/response objects at one boundary and expose a stable application port. Keep vendor-specific behavior visible and testable.

### Q19. What problem does an Idempotency Key pattern solve, and where should it live?

**Answer:** It maps repeated requests to one durable outcome. Place it at the business operation boundary with shared storage and a defined replay result.

### Q20. Why is a Singleton pattern often unnecessary in a dependency-injection application?

**Answer:** The container already manages object scope, and static global state harms tests and lifecycle. Use a singleton-scoped bean only when shared state is safe.

## Refactoring and maintainability

### Q21. How do you refactor a large method without changing behavior?

**Answer:** Add characterization tests, identify cohesive operations, extract one step at a time, and compare outputs and side effects after each change.

### Q22. What is a code smell that is not sufficient by itself to justify a rewrite?

**Answer:** Long methods or duplication may be intentional or stable. Look for change friction, repeated defects, and a concrete improvement before extracting.

### Q23. How can a strangler refactor reduce risk compared with a big-bang rewrite?

**Answer:** Move one behavior behind a stable boundary, compare results, then shift traffic incrementally. Preserve a rollback path and avoid dual-write inconsistency.

### Q24. Why should abstractions be extracted after observing variation rather than predicted?

**Answer:** Premature generalization adds concepts for hypothetical cases. Start with a clear concrete design, then extract when repeated change reveals a stable seam.

### Q25. How do you evaluate whether duplication is safer than a shared abstraction?

**Answer:** Compare how often the copies change together and whether the shared concept is stable. Independent domains can diverge; forced reuse increases coupling.

### Q26. What makes an API hard to refactor even when its implementation is clean?

**Answer:** Leaked mutable state, ambiguous nulls, side effects, and unstable errors constrain callers. Improve contracts with explicit types and compatibility tests.

### Q27. How can feature flags become a design liability?

**Answer:** Old flags leave dead branches and compound combinations. Track owner and removal date, test key states, and delete flags after rollout completes.

### Q28. Why is a broad catch-and-continue block a design smell?

**Answer:** It hides failure policy and makes invalid state appear successful. Catch at a boundary that can recover, translate, or report the error meaningfully.

### Q29. How do you reduce a dependency cycle between two modules?

**Answer:** Identify the policy that belongs in a third stable contract or invert one dependency through an interface. Avoid moving both modules into a vague shared layer.

### Q30. What is a safe review strategy for a cross-cutting refactor?

**Answer:** Define behavior checks, split the change into reviewable steps, include migration and rollback effects, and compare runtime telemetry after deployment.

## Quality, testing, and team practice

### Q31. How does mutation testing reveal weak tests?

**Answer:** It injects small behavior changes and checks whether tests fail. Surviving mutants point to unasserted behavior, though generated or equivalent mutants need judgment.

### Q32. Why are property-based tests useful for complex value objects?

**Answer:** They explore many generated inputs and verify invariants such as round-trip or ordering laws. Keep generators realistic and shrink failures to readable examples.

### Q33. How can a test suite communicate architecture boundaries?

**Answer:** Use module-level tests or dependency rules to reject forbidden imports and cycles. Tests should enforce explicit boundaries rather than package naming alone.

### Q34. What is the trade-off between mock-heavy unit tests and integration tests?

**Answer:** Mocks are fast and isolate logic but can mirror implementation assumptions; integration tests verify wiring and real semantics at higher cost.

### Q35. How should code review distinguish correctness concerns from style preference?

**Answer:** Prioritize behavior, contracts, failure modes, maintainability, and agreed conventions. Suggest style changes only when they improve consistency or clarity.

### Q36. Why should a refactoring pull request avoid unrelated formatting changes?

**Answer:** It obscures semantic changes and makes regressions harder to review. Separate mechanical formatting when possible and preserve focused diffs.

### Q37. What is a good response when a reviewer requests an abstraction you believe is premature?

**Answer:** Explain the current change patterns and costs, offer a concrete alternative, and agree on a future trigger to revisit. Treat feedback as design discussion.

### Q38. How can static analysis become noisy and lose developer trust?

**Answer:** Uncurated rules create false positives and ignored warnings. Start with high-value checks, baseline legacy debt, and assign ownership for exceptions.

### Q39. Why should a performance optimization include a representative benchmark and correctness test?

**Answer:** Micro-optimizations can change semantics or lose on realistic data. Measure the actual workload and keep behavioral coverage.

### Q40. What evidence should support a cleanup proposal during an incident?

**Answer:** Show the failure path, scope, expected risk reduction, and safe rollout. Avoid broad rewrites while restoring service unless they directly mitigate the issue.


## References

- [Refactoring.Guru design patterns catalog](https://refactoring.guru/design-patterns/catalog)
- [Spring Framework Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Java Object-Oriented Programming concepts](https://docs.oracle.com/javase/tutorial/java/concepts/)
