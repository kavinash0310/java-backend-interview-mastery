# Common Java Application Patterns

These patterns appear at domain, persistence, and web-application boundaries. This is a practical Java catalog: some entries are architectural patterns or idioms rather than members of the classic GoF 23.

Each entry covers the intent, a focused Java example, when it helps, trade-offs or pitfalls, and a short memory hook. Examples are illustrative fragments; imports, framework setup, and domain types are omitted where they are not the point. The examples target Java 17 unless a framework is named.

## 1. Dependency Injection (DI)

**Intent:** Supply an object's collaborators from outside instead of making the object locate or construct them itself.

**Java example:**

```java
final class InvoiceService {
    private final InvoiceRepository invoices;
    private final TaxPolicy tax;
    InvoiceService(InvoiceRepository invoices,TaxPolicy tax) {
        this.invoices=invoices; this.tax=tax;
    }
}
```

**Use it when:** Use for application services, adapters, and policies whose dependencies should be replaceable in tests or configuration.

**Trade-offs and pitfalls:** Makes dependencies visible and supports substitution. Too many injected collaborators can reveal that a class has too many responsibilities.

**Remember:** Pass dependencies in; keep construction at the composition root.

## 2. Repository

**Intent:** Expose domain-oriented collection operations while hiding persistence details.

**Java example:**

```java
interface OrderRepository {
    Optional<Order> findById(OrderId id);
    void save(Order order);
}
final class PlaceOrder {
    private final OrderRepository orders;
    PlaceOrder(OrderRepository orders) { this.orders=orders; }
}
```

**Use it when:** Use when domain or application code should express persistence in business terms rather than SQL/session details.

**Trade-offs and pitfalls:** Provides a testable boundary and keeps persistence replaceable. A generic repository can become a leaky CRUD wrapper that hides useful query semantics.

**Remember:** Domain-facing persistence boundary.

## 3. Data Access Object (DAO)

**Intent:** Encapsulate low-level database or external-storage access behind operations.

**Java example:**

```java
interface CustomerDao {
    CustomerRow findRow(long id) throws SQLException;
    int updateEmail(long id,String email) throws SQLException;
}
final class JdbcCustomerDao implements CustomerDao {
    private final DataSource dataSource;
    JdbcCustomerDao(DataSource ds) { dataSource=ds; }
    // JDBC mapping and SQL stay here.
}
```

**Use it when:** Use when persistence code needs a clear boundary, especially with JDBC or multiple data sources.

**Trade-offs and pitfalls:** Keeps SQL and row mapping out of callers. It can duplicate an ORM repository layer if both abstractions merely forward the same calls.

**Remember:** Technical data-access boundary.

## 4. Service Layer

**Intent:** Define application operations and coordinate domain rules, repositories, and transactions for a use case.

**Java example:**

```java
@Transactional // one local database transaction
void execute(Transfer t) {
    Account a=accounts.get(t.from()), b=accounts.get(t.to());
    a.withdraw(t.amount()); b.deposit(t.amount());
    accounts.save(a); accounts.save(b);
}
```

**Use it when:** Use to give API controllers and jobs a stable use-case boundary.

**Trade-offs and pitfalls:** Centralizes orchestration and authorization/transaction boundaries. An anemic service that only forwards every method may add no value.

**Remember:** A use case is an application operation.

## 5. Data Transfer Object (DTO)

**Intent:** Move a deliberate data shape across a process or layer boundary without exposing internal domain objects.

**Java example:**

```java
record CreateOrderRequest(List<LineRequest> lines,String couponCode) {}
record OrderResponse(String id,String status,BigDecimal total) {}
record LineRequest(String sku,int quantity) {}
```

**Use it when:** Use for REST payloads, messaging contracts, and data projections that differ from the internal domain model.

**Trade-offs and pitfalls:** Protects internal models and supports versioning. Mapping adds code and duplicate representations can drift.

**Remember:** DTO is a boundary shape, not the domain model.

## 6. Data Mapper

**Intent:** Move data between an in-memory domain model and a relational representation while keeping the two models independent.

**Java example:**

```java
final class UserMapper {
    User toDomain(ResultSet rs) throws SQLException {
        return new User(new UserId(rs.getLong("id")),rs.getString("email"));
    }
    void bind(PreparedStatement ps,User u) throws SQLException {
        ps.setLong(1,u.id().value()); ps.setString(2,u.email());
    }
}
```

**Use it when:** Use when the domain model should not carry SQL/ORM concerns or when database shape and object shape differ.

**Trade-offs and pitfalls:** Preserves separation and supports rich domain objects. Mapping code can be substantial and must handle nulls, relationships, and versioning.

**Remember:** Mapper translates between two models.

## 7. Unit of Work

**Intent:** Track changes made during a business transaction and persist them together at commit.

**Java example:**

```java
try (Connection c=dataSource.getConnection()) {
    c.setAutoCommit(false);
    try { updateAccount(c,from,amount); updateAccount(c,to,-amount); c.commit(); }
    catch (Exception e) { c.rollback(); throw e; }
}
```

**Use it when:** Use to group writes into one local database transaction; ORM sessions commonly provide this behavior.

**Trade-offs and pitfalls:** Coordinates atomic local persistence. Long transactions hold locks/resources and should not span remote service calls.

**Remember:** One local transaction, one commit/rollback.

## 8. Identity Map

**Intent:** Keep one in-memory object per database identity within a unit of work.

**Java example:**

```java
final class UserMap {
    private final Map<Long,User> byId=new HashMap<>();
    User get(long id,Function<Long,User> loader) {
        return byId.computeIfAbsent(id,loader);
    }
}
```

**Use it when:** Use inside an ORM/session or graph load so repeated references to one row resolve to the same object instance.

**Trade-offs and pitfalls:** Avoids duplicate loads and conflicting copies in a transaction. Requires scope, eviction, and thread-confinement rules.

**Remember:** One object instance per key per unit of work.

## 9. Specification

**Intent:** Represent a business predicate as a reusable object that can be tested and composed.

**Java example:**

```java
interface Specification<T> { boolean test(T value); }
record ActiveCustomer() implements Specification<Customer> {
    public boolean test(Customer c) { return c.active(); }
}
record And<T>(Specification<T> a,Specification<T> b) implements Specification<T> {
    public boolean test(T x) { return a.test(x)&&b.test(x); }
}
```

**Use it when:** Use for named eligibility rules, policy checks, or in-memory predicates that can be combined and tested.

**Trade-offs and pitfalls:** Makes business rules reusable and expressive. Too many tiny specifications make simple conditions difficult to trace.

**Remember:** A business rule as a composable predicate.

## 10. Model-View-Controller (MVC)

**Intent:** Separate input handling and flow (controller), application/domain state (model), and rendering (view).

**Java example:**

```java
@Controller
final class OrderPageController {
    private final OrderQueries orders;
    OrderPageController(OrderQueries orders) { this.orders=orders; }
    @GetMapping("/orders/{id}")
    String show(@PathVariable String id,Model model) {
        model.addAttribute("order",orders.find(id));
        return "order-detail";
    }
}
```

**Use it when:** Use for server-rendered web apps where request flow, model, and presentation need distinct responsibilities.

**Trade-offs and pitfalls:** Clarifies boundaries and test seams. Framework MVC often already supplies the structure; adding extra layers around every controller can be noise.

**Remember:** Controller coordinates; model represents; view presents.

## 11. Front Controller

**Intent:** Route incoming requests through one central entry point that applies shared handling before dispatch.

**Java example:**

```java
final class Dispatcher {
    void dispatch(HttpRequest request,HttpResponse response) {
        authenticate(request);
        Handler h=routes.lookup(request.path());
        h.handle(request,response);
    }
}
```

**Use it when:** Use when many web endpoints need consistent routing, authentication, exception mapping, and instrumentation.

**Trade-offs and pitfalls:** Centralizes common policy and routing. It is a critical path and can become a bottleneck or a giant conditional if responsibilities are mixed.

**Remember:** One entry point, delegated handlers.

## 12. Active Record

**Intent:** Combine row data and persistence operations in the same object.

**Java example:**

```java
final class AccountRecord {
    long id; String email;
    static AccountRecord find(DataSource ds,long id) { /* query row */ return null; }
    void save(DataSource ds) { /* insert or update row */ }
}
```

**Use it when:** Use for simple CRUD applications where the database schema and object model closely match.

**Trade-offs and pitfalls:** Fast to build and easy to understand. Persistence coupling makes rich business rules, testing, and schema changes harder.

**Remember:** The object carries data and saves itself.

## 13. Null Object

**Intent:** Provide a behaviorally safe object for an absent optional collaborator instead of scattering null checks.

**Java example:**

```java
interface AuditSink { void record(String event); }
final class NoopAuditSink implements AuditSink {
    public void record(String event) { }
}
final class Service {
    private final AuditSink audit;
    Service(AuditSink audit) { this.audit=audit; }
}
```

**Use it when:** Use when absence has a valid no-op meaning and the interface has a harmless default behavior.

**Trade-offs and pitfalls:** Simplifies callers and makes optional behavior explicit. It can hide missing configuration if a no-op should have been an error.

**Remember:** Replace null only when a safe default behavior exists.

---

Reference: [Spring dependency injection reference](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html). The patterns are summarized in original wording with small Java examples; consult the linked source for fuller treatment.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)