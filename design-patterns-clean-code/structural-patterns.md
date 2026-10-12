# GoF Structural Patterns

The seven classic structural patterns compose classes and objects into useful structures. They address API compatibility, independent variation, recursive trees, optional behavior, subsystem complexity, memory sharing, and controlled access.

Each entry covers the intent, a focused Java example, when it helps, trade-offs or pitfalls, and a short memory hook. Examples are illustrative fragments; imports, framework setup, and domain types are omitted where they are not the point. The examples target Java 17 unless a framework is named.

## 1. Adapter

**Intent:** Make an existing class usable through the interface a client expects by translating calls or data between the two.

**Java example:**

```java
interface PaymentGateway { void charge(long cents); }
final class LegacyProcessor {
    void debit(int dollars) { System.out.println("debit "+dollars); }
}
final class LegacyPaymentAdapter implements PaymentGateway {
    private final LegacyProcessor old;
    LegacyPaymentAdapter(LegacyProcessor old) { this.old=old; }
    public void charge(long cents) {
        if (cents % 100 != 0) throw new IllegalArgumentException("whole dollars only");
        old.debit(Math.toIntExact(cents / 100));
    }
}
```

**Use it when:** Use at integration boundaries: a legacy SDK, a third-party client, or a new interface over an older service.

**Trade-offs and pitfalls:** Localizes compatibility code and protects the domain from vendor details. Translation can lose information or preserve bad assumptions from the old interface.

**Remember:** Translate one interface into another.

## 2. Bridge

**Intent:** Separate an abstraction from its implementation so both can vary independently.

**Java example:**

```java
interface MessageSender { void send(String text); }
final class EmailSender implements MessageSender {
    public void send(String text) { System.out.println("email: "+text); }
}
final class SmsSender implements MessageSender {
    public void send(String text) { System.out.println("sms: "+text); }
}
abstract class Alert {
    protected final MessageSender sender;
    Alert(MessageSender s) { sender=s; }
    abstract void notifyUser(String text);
}
final class UrgentAlert extends Alert {
    UrgentAlert(MessageSender s) { super(s); }
    void notifyUser(String text) { sender.send("URGENT: "+text); }
}
```

**Use it when:** Use when two independent dimensions keep multiplying subclasses, such as alert type × delivery channel.

**Trade-offs and pitfalls:** Allows each side to evolve separately. Adds indirection and is harder to justify when there is only one implementation on either side.

**Remember:** Two hierarchies, connected by composition.

## 3. Composite

**Intent:** Represent individual objects and groups with a shared interface so clients can treat both uniformly.

**Java example:**

```java
interface Node { long size(); }
record FileNode(long bytes) implements Node {
    public long size() { return bytes; }
}
final class Directory implements Node {
    private final List<Node> children=new ArrayList<>();
    void add(Node n) { children.add(n); }
    public long size() { return children.stream().mapToLong(Node::size).sum(); }
}
```

**Use it when:** Use for tree-shaped structures where the same operation should work on a leaf and a nested group, such as files, menus, or organization charts.

**Trade-offs and pitfalls:** Makes recursive operations simple. A uniform interface may expose operations that do not make sense for leaves, and large trees may need caching or cycle protection.

**Remember:** Leaf and group share one interface.

## 4. Decorator

**Intent:** Add behavior around an object while keeping the same interface, so features can be composed at runtime.

**Java example:**

```java
interface Notifier { void send(String text); }
final class EmailNotifier implements Notifier {
    public void send(String text) { System.out.println(text); }
}
final class AuditNotifier implements Notifier {
    private final Notifier next;
    AuditNotifier(Notifier n) { next=n; }
    public void send(String text) { System.out.println("audit"); next.send(text); }
}
// Notifier n = new AuditNotifier(new EmailNotifier());
```

**Use it when:** Use for composable cross-cutting behavior such as metrics, compression, encryption, buffering, or audit logging.

**Trade-offs and pitfalls:** Avoids a subclass for every feature combination. Many wrappers can make debugging, ordering, and object identity harder to follow.

**Remember:** Same interface; add behavior by wrapping.

## 5. Facade

**Intent:** Provide a small, task-oriented entry point over a subsystem with many components.

**Java example:**

```java
final class CheckoutFacade {
    private final Inventory inventory;
    private final PaymentGateway payments;
    private final OrderStore orders;
    CheckoutFacade(Inventory i, PaymentGateway p, OrderStore o) {
        inventory=i; payments=p; orders=o;
    }
    OrderId checkout(Cart cart) {
        inventory.reserve(cart);
        PaymentId id=payments.authorize(cart.totalCents());
        return orders.save(cart,id);
    }
}
```

**Use it when:** Use when callers repeat the same coordination steps or need a simpler API over a complex subsystem.

**Trade-offs and pitfalls:** Reduces caller coupling and gives a natural use-case boundary. A facade can become a god object if it accumulates unrelated workflows.

**Remember:** One convenient front door for a subsystem.

## 6. Flyweight

**Intent:** Share immutable intrinsic state across many logical objects and keep per-instance state outside the shared object.

**Java example:**

```java
record GlyphKey(char symbol,String font) {}
record Glyph(char symbol,String font) {}
final class GlyphFactory {
    private final Map<GlyphKey,Glyph> pool=new HashMap<>();
    Glyph get(char c,String font) {
        GlyphKey key=new GlyphKey(c,font);
        return pool.computeIfAbsent(key,k -> new Glyph(k.symbol(),k.font()));
    }
}
// Position and color belong to each drawing operation, not to Glyph.
```

**Use it when:** Use when profiling shows many objects repeat the same expensive, immutable data, such as glyph definitions or tile metadata.

**Trade-offs and pitfalls:** Can substantially reduce memory use. Requires clear separation of shared intrinsic state from caller-owned extrinsic state.

**Remember:** Share stable data; pass changing state from outside.

## 7. Proxy

**Intent:** Stand in for another object to control access, defer work, add security, cache results, or cross a process boundary.

**Java example:**

```java
interface Document { String text(); }
final class LazyDocument implements Document {
    private final Supplier<Document> loader;
    private Document real;
    LazyDocument(Supplier<Document> loader) { this.loader=loader; }
    public synchronized String text() {
        if (real==null) real=loader.get();
        return real.text();
    }
}
```

**Use it when:** Use when access needs a policy or lifecycle step, such as lazy loading, authorization, remote calls, or metrics.

**Trade-offs and pitfalls:** Keeps access control near the boundary and can delay expensive work. Adds hidden latency and may make object behavior less obvious.

**Remember:** Same contract; control access to the real subject.

---

Reference: [GoF pattern catalog (publisher page)](https://www.informit.com/store/design-patterns-elements-of-reusable-object-oriented-software-9780201633610). The patterns are summarized in original wording with small Java examples; consult the linked source for fuller treatment.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)