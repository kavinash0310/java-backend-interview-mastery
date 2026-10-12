# GoF Behavioral Patterns

The eleven classic behavioral patterns organize communication, algorithms, state changes, and responsibilities between objects. Use them to make a changing behavior or collaboration explicit.

Each entry covers the intent, a focused Java example, when it helps, trade-offs or pitfalls, and a short memory hook. Examples are illustrative fragments; imports, framework setup, and domain types are omitted where they are not the point. The examples target Java 17 unless a framework is named.

## 1. Chain of Responsibility

**Intent:** Pass a request through handlers; each handler can process it, reject it, or pass it to the next handler.

**Java example:**

```java
interface Handler { boolean handle(Request r); }
final class AuthHandler implements Handler {
    private final Handler next;
    AuthHandler(Handler next) { this.next=next; }
    public boolean handle(Request r) {
        if (!r.authenticated()) return false;
        return next==null || next.handle(r);
    }
}
```

**Use it when:** Use for ordered validation, authentication filters, middleware, or fallback resolution where handlers can be composed.

**Trade-offs and pitfalls:** Handlers stay small and order is configurable. A request may reach no handler, many handlers, or fail with little visibility unless the chain is instrumented.

**Remember:** Each handler decides: handle, stop, or pass along.

## 2. Command

**Intent:** Wrap an operation and its parameters as an object so it can be queued, logged, retried, scheduled, or undone.

**Java example:**

```java
interface Command { void execute(); }
final class ReserveStock implements Command {
    private final Inventory inventory; private final Sku sku;
    ReserveStock(Inventory i,Sku s) { inventory=i; sku=s; }
    public void execute() { inventory.reserve(sku); }
}
final class Worker {
    void run(Command c) { c.execute(); }
}
```

**Use it when:** Use for job queues, user actions, retries, audit trails, or undoable operations.

**Trade-offs and pitfalls:** Separates the invoker from the receiver and makes operations first-class. Adds command classes and does not make a non-idempotent action safe to retry.

**Remember:** Turn a request into an object.

## 3. Interpreter

**Intent:** Represent a small language's grammar as objects and evaluate or interpret those expressions.

**Java example:**

```java
interface Expr { int eval(); }
record NumberExpr(int value) implements Expr {
    public int eval() { return value; }
}
record AddExpr(Expr left,Expr right) implements Expr {
    public int eval() { return left.eval()+right.eval(); }
}
Expr total=new AddExpr(new NumberExpr(2),new NumberExpr(3));
int answer=total.eval();
```

**Use it when:** Use for small, stable grammars such as rules, filters, or simple expressions.

**Trade-offs and pitfalls:** Makes grammar nodes extensible and visible. One class per grammar rule becomes unwieldy for a large language; parser generators or AST visitors are better there.

**Remember:** Grammar rules become expression objects.

## 4. Iterator

**Intent:** Provide sequential access to a collection without exposing how the collection stores its elements.

**Java example:**

```java
final class Playlist implements Iterable<String> {
    private final List<String> tracks;
    Playlist(List<String> tracks) { this.tracks=List.copyOf(tracks); }
    public Iterator<String> iterator() { return tracks.iterator(); }
}
for (String track : new Playlist(List.of("Intro","Finale"))) {
    System.out.println(track);
}
```

**Use it when:** Use for collections, trees, streams of records, or custom traversal order while keeping representation private.

**Trade-offs and pitfalls:** Separates traversal from storage and supports multiple traversal policies. Iterators can become invalid if the collection mutates during iteration.

**Remember:** Traversal protocol, hidden representation.

## 5. Mediator

**Intent:** Centralize interactions among a set of collaborating objects so they do not depend directly on one another.

**Java example:**

```java
interface ChatRoom { void publish(User from,String text); }
final class User {
    private final String name; private final ChatRoom room;
    User(String n,ChatRoom r) { name=n; room=r; }
    void send(String text) { room.publish(this,text); }
    String name() { return name; }
}
```

**Use it when:** Use when peer-to-peer dependencies create a web of coordination, such as UI widgets, chat rooms, or workflow participants.

**Trade-offs and pitfalls:** Reduces direct coupling between colleagues. The mediator may become a god object or hide a complex event flow.

**Remember:** Colleagues talk through a coordinator.

## 6. Memento

**Intent:** Capture and restore an object's state without exposing its internal representation.

**Java example:**

```java
final class Editor {
    private String text="";
    void append(String s) { text+=s; }
    Snapshot save() { return new Snapshot(text); }
    void restore(Snapshot s) { text=s.text; }
    static final class Snapshot {
        private final String text;
        private Snapshot(String t) { text=t; }
    }
}
```

**Use it when:** Use for undo/redo, checkpoints, or rollback of in-memory editor/workflow state.

**Trade-offs and pitfalls:** Keeps encapsulation and makes restoration straightforward. Snapshots can consume substantial memory and may need versioning.

**Remember:** Save a point-in-time value, restore it later.

## 7. Observer

**Intent:** Let one subject notify multiple subscribers when its state or an event changes.

**Java example:**

```java
final class PriceFeed {
    private final List<Consumer<BigDecimal>> listeners=new CopyOnWriteArrayList<>();
    void subscribe(Consumer<BigDecimal> c) { listeners.add(c); }
    void unsubscribe(Consumer<BigDecimal> c) { listeners.remove(c); }
    void publish(BigDecimal price) { listeners.forEach(c -> c.accept(price)); }
}
```

**Use it when:** Use for in-process events, UI updates, domain notifications, or decoupled reactions to state changes.

**Trade-offs and pitfalls:** Publishers do not need to know concrete subscribers. Notification order, slow listeners, reentrancy, and failures need a policy.

**Remember:** One subject, many listeners.

## 8. State

**Intent:** Change an object's behavior when its internal state changes by delegating state-specific behavior to state objects.

**Java example:**

```java
interface OrderState { OrderState pay(); }
final class Pending implements OrderState {
    public OrderState pay() { return new Paid(); }
}
final class Paid implements OrderState {
    public OrderState pay() { throw new IllegalStateException("already paid"); }
}
final class Order {
    private OrderState state=new Pending();
    void pay() { state=state.pay(); }
}
```

**Use it when:** Use when transitions and allowed actions vary meaningfully across a finite set of states.

**Trade-offs and pitfalls:** Makes transitions explicit and avoids large conditional blocks. Adds classes and can be excessive for a small, stable state machine.

**Remember:** State chooses the behavior.

## 9. Strategy

**Intent:** Put interchangeable algorithms behind one contract and select one at runtime.

**Java example:**

```java
interface ShippingCost { BigDecimal calculate(Order o); }
final class StandardShipping implements ShippingCost {
    public BigDecimal calculate(Order o) { return new BigDecimal("5.00"); }
}
final class ExpressShipping implements ShippingCost {
    public BigDecimal calculate(Order o) { return new BigDecimal("15.00"); }
}
final class Checkout {
    BigDecimal total(Order o,ShippingCost s) { return o.subtotal().add(s.calculate(o)); }
}
```

**Use it when:** Use when an algorithm varies by policy, customer, configuration, or runtime input.

**Trade-offs and pitfalls:** Removes branching from the caller and makes each algorithm testable. Callers need a selection policy, and many tiny strategies can be needless indirection.

**Remember:** Choose an algorithm; keep its caller stable.

## 10. Template Method

**Intent:** Define the invariant steps of an algorithm in a base method while subclasses supply selected steps.

**Java example:**

```java
abstract class ImportJob {
    public final void run(InputStream in) {
        validate(in);
        List<Row> rows=read(in);
        save(rows);
    }
    protected abstract void validate(InputStream in);
    protected abstract List<Row> read(InputStream in);
    protected abstract void save(List<Row> rows);
}
```

**Use it when:** Use when several workflows share a fixed sequence but differ in a few controlled steps.

**Trade-offs and pitfalls:** Avoids duplicated workflow code. Inheritance tightly couples subclasses to the base class and makes step variation less flexible than composition.

**Remember:** Base class owns the sequence; subclass fills the hooks.

## 11. Visitor

**Intent:** Add operations over a stable object structure by moving each operation into a visitor.

**Java example:**

```java
interface Shape { <R> R accept(ShapeVisitor<R> v); }
record Circle(double radius) implements Shape {
    public <R> R accept(ShapeVisitor<R> v) { return v.circle(this); }
}
record Rectangle(double width,double height) implements Shape {
    public <R> R accept(ShapeVisitor<R> v) { return v.rectangle(this); }
}
interface ShapeVisitor<R> {
    R circle(Circle c);
    R rectangle(Rectangle r);
}
```

**Use it when:** Use when the element types are stable but new operations are added often, such as printing, metrics, or export over an AST.

**Trade-offs and pitfalls:** Keeps operations together and avoids type-switch logic. Adding a new element type requires changing every visitor.

**Remember:** New operation across stable types; new type changes all visitors.

---

Reference: [GoF pattern catalog (publisher page)](https://www.informit.com/store/design-patterns-elements-of-reusable-object-oriented-software-9780201633610). The patterns are summarized in original wording with small Java examples; consult the linked source for fuller treatment.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)