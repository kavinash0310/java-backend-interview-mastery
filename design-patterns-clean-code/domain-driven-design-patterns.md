# Domain-Driven Design Patterns

Domain-Driven Design (DDD) uses a shared model and explicit boundaries to keep business rules understandable. Its tactical building blocks shape code; its strategic patterns shape how teams and models relate. These are DDD patterns, separate from the classic GoF 23.

Examples are short Java 17+ illustrations. Domain types and persistence setup are omitted where the concept is the focus.

## 1. Entity

**Intent:** Model an object whose identity remains stable even when its attributes change.

**Java example:**

```java
final class Customer {
    private final CustomerId id;
    private String email;
    Customer(CustomerId id,String email) { this.id=id; this.email=email; }
    CustomerId id() { return id; }
    void changeEmail(Email next) { email=next.value(); }
}
```

**Use it when:** Use when the business distinguishes one thing from another by identity over time, such as an account, order, or customer.

**Trade-offs and pitfalls:** Supports lifecycle and behavior around identity. Equality and persistence mapping require care; mutable entities are harder to share safely.

**Remember:** Entity equality follows stable identity.

## 2. Value Object

**Intent:** Represent a concept by its values rather than identity, and keep it immutable.

**Java example:**

```java
record Money(BigDecimal amount,Currency currency) {
    Money {
        Objects.requireNonNull(amount);
        Objects.requireNonNull(currency);
        if (amount.scale()>2) amount=amount.setScale(2,RoundingMode.UNNECESSARY);
    }
}
```

**Use it when:** Use for money, address, date range, coordinates, and other concepts with meaningful validation and value equality.

**Trade-offs and pitfalls:** Encapsulates rules and is safe to share. Creating new instances can be more frequent, which is usually a smaller cost than mutable ambiguity.

**Remember:** Same values mean the same value object.

## 3. Aggregate and Aggregate Root

**Intent:** Group domain objects around consistency rules and expose state changes through one root.

**Java example:**

```java
final class Order {
    private final OrderId id;
    private final List<OrderLine> lines=new ArrayList<>();
    void add(ProductId product,int quantity) {
        if (quantity<=0) throw new IllegalArgumentException();
        lines.add(new OrderLine(product,quantity));
    }
    List<OrderLine> lines() { return List.copyOf(lines); }
}
```

**Use it when:** Use when a set of objects must maintain invariants together, such as an order and its lines or an account and ledger entries.

**Trade-offs and pitfalls:** Clarifies transactional consistency boundaries. Large aggregates create contention and make unrelated updates unnecessarily expensive.

**Remember:** One aggregate is one consistency boundary.

## 4. Domain Service

**Intent:** Represent domain logic that is meaningful but does not naturally belong to one entity or value object.

**Java example:**

```java
final class TransferPolicy {
    void transfer(Account from,Account to,Money amount) {
        from.withdraw(amount);
        to.deposit(amount);
    }
}
```

**Use it when:** Use for a domain operation spanning several entities or applying a domain policy with no natural owner.

**Trade-offs and pitfalls:** Keeps domain logic explicit and testable. Too many domain services can leave entities as passive data holders.

**Remember:** Domain rule with no natural object owner.

## 5. Domain Event

**Intent:** Represent a meaningful business fact that has already happened and may be relevant to other parts of the domain.

**Java example:**

```java
record OrderPlaced(OrderId orderId,Instant occurredAt) {}
// The aggregate records the fact; an application boundary publishes it later.
```

**Use it when:** Use when a business transition should trigger independent reactions or be recorded as part of domain behavior.

**Trade-offs and pitfalls:** Makes business events explicit and decouples reactions. Event naming, versioning, and delivery become long-lived contracts.

**Remember:** Events describe what happened, not what a caller wants.

## 6. Domain Factory

**Intent:** Create a valid domain object or aggregate when its construction requires domain rules or several coordinated steps.

**Java example:**

```java
final class OrderFactory {
    Order draft(CustomerId customer,List<CartLine> cart) {
        if (cart.isEmpty()) throw new EmptyOrder();
        return Order.fromCart(customer,cart);
    }
}
```

**Use it when:** Use when creation has meaningful business validation or would otherwise expose a complicated constructor.

**Trade-offs and pitfalls:** Centralizes valid creation. A factory that only wraps `new` adds little value.

**Remember:** Creation path guarantees a valid starting state.

## 7. Bounded Context

**Intent:** Set an explicit boundary within which a domain model and its language have one consistent meaning.

**Java example:**

```java
// "Customer" may mean different things in sales and billing.
// Use SalesCustomer and BillingAccount behind separate module APIs.
```

**Use it when:** Use when one word or model means different things to different business areas or teams.

**Trade-offs and pitfalls:** Prevents one universal model from becoming contradictory. Boundaries require translation and coordination where contexts interact.

**Remember:** One model vocabulary inside a clear boundary.

## 8. Context Map

**Intent:** Make the relationships and integration choices between bounded contexts explicit.

**Java example:**

```java
interface SalesCustomerPort { BillingCustomer toBilling(SalesCustomer c); }
final class SalesToBillingAdapter implements SalesCustomerPort {
    public BillingCustomer toBilling(SalesCustomer c) { /* translate terms */ return null; }
}
```

**Use it when:** Use when multiple domain contexts must collaborate and teams need to state who owns which contract.

**Trade-offs and pitfalls:** Makes dependencies and translation strategies visible. Maintaining mappings adds work as contracts evolve.

**Remember:** Map relationships between models, not just services.

## 9. Shared Kernel

**Intent:** Share a deliberately small subset of domain concepts and code between closely collaborating contexts.

**Java example:**

```java
// A tiny versioned library may define shared Currency and CountryCode value objects.
// Each context keeps its own Order and Customer model.
```

**Use it when:** Use only when teams have tight collaboration and the shared concepts truly have identical meaning and lifecycle.

**Trade-offs and pitfalls:** Avoids duplicated definitions. Changes require coordination and can couple release schedules.

**Remember:** Share the minimum; keep context-specific models local.

## Related patterns in this module

- [Repository and Specification](java-application-patterns.md) cover persistence boundaries and composable rules.
- [Anti-Corruption Layer](distributed-backend-patterns.md) is a common way to protect one bounded context from another model.
- [Transactional Outbox](distributed-backend-patterns.md) can publish domain events reliably after a local commit.

Reference: [Domain-Driven Design](https://www.domainlanguage.com/ddd/), from the method's originator. The patterns are summarized in original wording with Java-oriented examples.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)