# Design Patterns and Clean Code: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Principles and abstraction traps

### Q1. Does dependency inversion mean every class should have an interface?

**Answer:** No. Add abstractions at meaningful boundaries where variation or testing needs justify them; interfaces for every class add indirection.

### Q2. Can a class obey Single Responsibility while having many methods?

**Answer:** Possibly, if those methods change for one cohesive reason. Count alone is not the test; identify distinct actors and change causes.

### Q3. Why can extracting a shared utility increase coupling?

**Answer:** It can force independent domains to share one policy and release cycle. Reuse only when the concept and change direction are genuinely common.

### Q4. Does Open-Closed mean existing code should never be edited?

**Answer:** No. It means structure likely variation points so common extensions do not require risky changes; correcting the abstraction can still require edits.

### Q5. Can a subclass satisfy the method signatures but violate Liskov Substitution?

**Answer:** Yes. It may strengthen preconditions, weaken postconditions, or break invariants. Behavioral contracts matter beyond type compatibility.

### Q6. Why can a strategy pattern be worse than a switch?

**Answer:** A stable small set of cases may be clearer in one switch. Strategy pays off when algorithms vary or grow independently.

### Q7. What does composition fail to solve if every collaborator shares mutable global state?

**Answer:** It still has hidden coupling and race risks. Explicit ownership and dependencies matter more than class shape.

### Q8. Why is a domain model with setters for every field not necessarily encapsulated?

**Answer:** Callers can create invalid combinations without enforcing invariants. Expose operations that preserve business rules.

### Q9. Can an immutable object contain a mutable list safely?

**Answer:** Only if it copies input and does not expose the internal list or mutable elements. An unmodifiable view alone is insufficient.

### Q10. Why can a “generic” abstraction be less reusable than duplicated code?

**Answer:** It may encode assumptions that differ across callers, forcing flags and hooks. Small duplication can preserve clear local behavior.

## Pattern selection and trade-offs

### Q11. When is CQRS overkill for a CRUD service?

**Answer:** When reads and writes have similar models and scale; separate models add synchronization and maintenance without solving a real bottleneck.

### Q12. Does Event Sourcing automatically provide an audit trail that meets compliance needs?

**Answer:** No. Event completeness, immutability, access, retention, PII erasure, and tamper evidence still need design.

### Q13. Why can a Singleton be dangerous even if the container guarantees one instance?

**Answer:** Shared mutable state can race and tests can leak state across contexts. Singleton lifecycle does not mean thread-safe behavior.

### Q14. When is a Decorator chain difficult to reason about?

**Answer:** When wrapper order changes semantics or hides side effects. Name, order, and test the composition explicitly.

### Q15. Why can an Adapter become a second domain model?

**Answer:** If it leaks vendor details or accumulates business rules, the boundary is misplaced. Keep translation focused and ownership clear.

### Q16. What is the difference between Factory and Abstract Factory in practical terms?

**Answer:** A simple factory creates one family/product decision; Abstract Factory groups creation of related products. Avoid the heavier abstraction without multiple families.

### Q17. How can a Command pattern help with retries but also create a danger?

**Answer:** It packages an operation and metadata, but replay can duplicate side effects unless the command is idempotent and versioned.

### Q18. Why does a Repository abstraction sometimes make complex queries harder?

**Answer:** A leaky generic CRUD API cannot express domain-specific performance or projections. Keep useful query semantics explicit instead of hiding SQL.

### Q19. How can a generic Repository abstraction hide a transaction boundary?

**Answer:** If callers cannot tell whether operations are atomic or lazy, abstraction hides important semantics. Document unit-of-work behavior and expose transaction ownership.

### Q20. Can a Builder pattern guarantee an immutable object?

**Answer:** Only if the final object copies mutable inputs and exposes no mutators. Builder mutability does not imply product mutability.

## Refactoring and code review traps

### Q21. Why is replacing a legacy module with a clean rewrite risky even when tests pass?

**Answer:** Tests may miss undocumented behavior and data migration paths. Compare live behavior, migrate incrementally, and preserve rollback options.

### Q22. Should duplicate code always be extracted before a second use?

**Answer:** No. First determine whether the behavior and reasons to change are actually the same; premature shared code creates coupling.

### Q23. What should happen before refactoring a method with no tests?

**Answer:** Add characterization tests around current outputs and side effects, then make small changes. Otherwise behavior changes are hard to distinguish from cleanup.

### Q24. Why can a feature flag make code harder after rollout?

**Answer:** Old paths and combinations remain, increasing test and maintenance state space. Assign an owner and removal date.

### Q25. Can a fluent API be less readable than simple methods?

**Answer:** Yes, if order is significant, side effects are hidden, or errors appear late. Optimize for discoverable contract rather than terseness.

### Q26. Why should a code review reject a broad catch block even if it prevents an error page?

**Answer:** It can mask failed invariants and report success for corrupt work. Catch only where there is a safe recovery or translation.

### Q27. How can a method be made testable without injecting every value as a dependency?

**Answer:** Pure calculations can accept data directly; inject only external collaborators and time/randomness where control materially improves tests.

### Q28. Why is an abstraction based on current call sites sometimes unstable?

**Answer:** Call sites may share syntax but differ in policy and future change. Model the domain concept, not superficial code similarity.

### Q29. What is the risk of using reflection to avoid an explicit dependency?

**Answer:** It hides coupling and may fail under module/native restrictions. Prefer a clear port or extension API.

### Q30. How should a team decide whether to accept a small performance optimization that obscures code?

**Answer:** Require representative measurements and compare maintenance cost, correctness risk, and workload impact. Keep the simpler version without evidence.

## References

- [Refactoring.Guru design patterns catalog](https://refactoring.guru/design-patterns/catalog)
- [Spring Framework Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Java Object-Oriented Programming concepts](https://docs.oracle.com/javase/tutorial/java/concepts/)
