# Design Patterns and Clean Code: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is a design pattern?

**Answer:** A reusable way to describe a common design problem and a family of solutions. It is a starting point, not code to apply automatically.

### Q2. What is the Single Responsibility Principle?

**Answer:** A class should have one focused reason to change. Invoice calculation and email delivery usually change for different reasons.

### Q3. What is the Open/Closed Principle?

**Answer:** Software should be open to useful extension while limiting changes to stable code. A strategy interface can let a new rule avoid a large conditional.

### Q4. What is the Liskov Substitution Principle?

**Answer:** A subtype should work where its base type is expected without breaking the base type's promises.

### Q5. What is the Interface Segregation Principle?

**Answer:** Clients should not depend on methods they do not use. Small role-focused interfaces are often easier than one broad interface.

### Q6. What is the Dependency Inversion Principle?

**Answer:** High-level policy should depend on abstractions rather than concrete details. Constructor injection makes those dependencies visible.

### Q7. Composition vs inheritance?

**Answer:** Composition supplies collaborators; inheritance specializes a base type. Prefer composition when behavior can vary independently or the subtype relationship is weak.

### Q8. What is encapsulation?

**Answer:** Keeping object state and its rules together behind a clear interface, preventing callers from creating invalid states directly.

### Q9. What is cohesion?

**Answer:** How closely a class's responsibilities belong together. High cohesion usually makes a class easier to understand and change.

### Q10. What is coupling?

**Answer:** How strongly one module depends on another's details. Clear interfaces and ownership boundaries can reduce unnecessary coupling.

### Q11. What does DRY mean?

**Answer:** Avoid repeating the same knowledge or rule in several places. It does not mean every similar line needs one abstraction.

### Q12. What does KISS mean?

**Answer:** Keep the solution as simple as the requirements allow. Simple code is easier to explain, test, and maintain.

### Q13. What does YAGNI mean?

**Answer:** You Aren't Gonna Need It: do not build speculative features before a real need exists.

### Q14. What is the Factory pattern?

**Answer:** It centralizes object creation when callers should not know which concrete implementation to construct.

### Q15. What is Abstract Factory?

**Answer:** It creates families of related objects through a shared interface, helping avoid incompatible combinations.

### Q16. What is the Builder pattern?

**Answer:** It assembles an object through named steps, making many optional settings easier to read.

**Example:** Order.builder().customer(id).priority(true).build();

### Q17. What is the Singleton pattern?

**Answer:** It restricts a type to one instance within a defined scope. A Spring singleton is one instance per container, not a global JVM instance.

### Q18. What is the Strategy pattern?

**Answer:** It puts interchangeable algorithms behind a common interface so the caller chooses behavior without a growing conditional.

### Q19. What is the Observer pattern?

**Answer:** It notifies interested listeners when an event occurs. It reduces direct dependency but can make order and failure paths harder to follow.

### Q20. What is the Adapter pattern?

**Answer:** It translates one interface into another expected by a caller, helping integrate a library without spreading its API.

### Q21. What is the Decorator pattern?

**Answer:** It wraps an object with the same interface and adds behavior. Multiple decorators can add features without changing the wrapped class.

### Q22. What is the Proxy pattern?

**Answer:** A stand-in that controls access or adds behavior, such as lazy loading, transactions, or remote access.

### Q23. What is the Facade pattern?

**Answer:** A simpler interface over lower-level components, reducing how much subsystem detail callers need to know.

### Q24. What is Template Method?

**Answer:** It defines an algorithm's steps in a base type and lets subclasses customize selected steps.

### Q25. What is the Command pattern?

**Answer:** It represents an action as an object, making it easier to queue, log, or undo. Operation safety still matters.

### Q26. What is a God object?

**Answer:** A class with too many unrelated responsibilities and dependencies. Split it by cohesive behavior.

### Q27. What is clean code?

**Answer:** Code that communicates intent, keeps responsibilities focused, and handles failures deliberately. Readability matters more than clever compression.

### Q28. What is refactoring?

**Answer:** Changing internal structure while preserving observable behavior. Small steps and tests reduce the risk of behavior changes.

### Q29. What is technical debt?

**Answer:** The future cost of a shortcut or design choice that makes later change harder. Track its impact and pay it down when it blocks useful work.

### Q30. When is an abstraction unnecessary?

**Answer:** When it hides a simple operation, has no real variation, or adds more indirection than value. Wait for evidence before adding it.

### Q31. What is the Repository pattern?

**Answer:** It gives domain or application code a collection-like interface to data access.

**Example:** An OrderRepository can hide SQL details.

### Q32. How can a design reduce coupling?

**Answer:** Depend on small stable contracts, keep responsibilities focused, and avoid unnecessary knowledge of other modules.

**Example:** Pass an interface into a service instead of constructing a vendor client inside it.

### Q33. What is the Unit of Work pattern?

**Answer:** It tracks changes made during one business operation and coordinates their persistence together.

**Example:** A transaction can commit a set of related entity changes.

### Q34. What is the Null Object pattern?

**Answer:** A harmless implementation of an interface that provides default behavior instead of returning null.

**Example:** A no-op notifier can implement the notification interface.

### Q35. What is CQRS?

**Answer:** Command Query Responsibility Segregation uses separate models or paths for changing data and reading it.

**Example:** Adopt it when read and write needs differ enough to justify the complexity.

### Q36. What is an anti-pattern?

**Answer:** A common approach that may look convenient but tends to cause recurring design problems.

**Example:** A giant service class can become an anti-pattern.

### Q37. What is feature envy?

**Answer:** A code smell where a method uses another object's data more than its own object's behavior.

**Example:** Move behavior closer to the data it naturally belongs with.

### Q38. What is a code smell?

**Answer:** A sign that code may be harder to understand or change, even if it still works.

**Example:** A method with many unrelated responsibilities is a smell.

### Q39. What does Tell, Don't Ask mean?

**Answer:** Ask an object to perform behavior instead of pulling out its state and making decisions elsewhere.

**Example:** Call `cart.checkout()` rather than editing its internals.

### Q40. How do you decide whether to create an interface?

**Answer:** Create one when it clarifies a meaningful boundary, variation, or test seam; avoid one-method wrappers with no purpose.

**Example:** A payment-provider boundary may justify an interface.

## References

- [Refactoring.Guru design patterns catalog](https://refactoring.guru/design-patterns/catalog)
- [Spring Framework Dependency Injection](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)
- [Java Object-Oriented Programming concepts](https://docs.oracle.com/javase/tutorial/java/concepts/)
