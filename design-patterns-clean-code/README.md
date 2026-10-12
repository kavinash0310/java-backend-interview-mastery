# Module 14 — Design Patterns and Clean Code

> **Module guide:** Apply object-oriented design principles and patterns to keep backend code understandable, testable, and adaptable.

## Learning outcomes

- Recognize the problem a pattern solves and its costs.
- Apply SOLID as a design aid rather than a checklist.
- Refactor toward clearer boundaries without overengineering.

## Subtopic links

- [Design principles](#design-principles)
- [Creational patterns](#creational-patterns)
- [Behavioral and structural patterns](#behavioral-and-structural-patterns)
- [Code quality and evolution](#code-quality-and-evolution)
- [Low-level design practice](#low-level-design-practice)

## Topic coverage

### Design principles

- SOLID principles
- Dependency inversion and dependency injection
- Separation of concerns and composition

### Creational patterns

- Singleton
- Factory
- Builder

### Behavioral and structural patterns

- Strategy
- Observer
- Adapter
- Decorator
- Proxy

### Code quality and evolution

- Clean code and naming
- Refactoring safely
- Maintainability and extensibility
- Avoiding unnecessary abstraction


## Low-level design practice

Practice these small systems by defining the main objects and interfaces first, then explain state transitions, concurrency, and failure handling:

- **Delayed payment scheduler:** retries, duplicate triggers, idempotency, and payment status.
- **Coupon system:** eligibility, expiry, usage limits, and concurrent redemption.
- **Parking lot:** spot allocation, pricing rules, and concurrent entry/exit.
- **Vending machine:** inventory, payment, state transitions, and refunds.
- **Splitwise:** expense splitting, rounding, balances, and settlement.

For every exercise, explain why responsibilities are separated and how you would test race conditions. The [medium](medium-interview-questions.md) and [hard](hard-interview-questions.md) sets cover related design trade-offs.

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `design-patterns-clean-code/design-principles/` — design principles question pages
- `design-patterns-clean-code/creational-patterns/` — creational patterns question pages
- `design-patterns-clean-code/behavioral-and-structural-patterns/` — behavioral and structural patterns question pages
- `design-patterns-clean-code/code-quality-and-evolution/` — code quality and evolution question pages

Use descriptive lowercase kebab-case filenames, for example `design-patterns-clean-code/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [40-question hard-level Q&A for advanced interview practice](hard-interview-questions.md)
- [30-question tricky Q&A for common interview traps](tricky-interview-questions.md)


## Related modules

- [Module 01: Core Java](../core-java/README.md)
- [Module 05: Spring Framework](../spring-framework/README.md)
- [Module 15: Testing](../testing/README.md)
- [Module 20: System Design and Architecture](../system-design-architecture/README.md)
