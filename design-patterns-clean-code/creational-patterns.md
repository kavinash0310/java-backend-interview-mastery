# GoF Creational Patterns

The five classic creational patterns manage how object graphs are constructed. The central question is which part of creation is changing: a family, a workflow hook, a long constructor, a reusable prototype, or shared lifetime.

Each entry covers the intent, a focused Java example, when it helps, trade-offs or pitfalls, and a short memory hook. Examples are illustrative fragments; imports, framework setup, and domain types are omitted where they are not the point. The examples target Java 17 unless a framework is named.

## 1. Abstract Factory

**Intent:** Create matching families of related objects without exposing their concrete classes. The client asks one factory for every member of a product family.

**Java example:**

```java
interface Button { String label(); }
interface Checkbox { boolean checked(); }
interface WidgetFactory {
    Button button();
    Checkbox checkbox();
}
final class LightFactory implements WidgetFactory {
    public Button button() { return () -> "light button"; }
    public Checkbox checkbox() { return () -> false; }
}
final class Screen {
    private final Button button;
    private final Checkbox checkbox;
    Screen(WidgetFactory f) { button=f.button(); checkbox=f.checkbox(); }
}
```

**Use it when:** Use when a configuration selects a coherent family, such as light/dark UI widgets or provider-specific storage clients.

**Trade-offs and pitfalls:** Keeps product families consistent, but adding a new product type changes every factory. One implementation per object is often unnecessary when there is only one variant.

**Remember:** Family of related objects.

## 2. Builder

**Intent:** Construct an object in steps, especially when it has many optional values or must be validated before creation.

**Java example:**

```java
final class ServerConfig {
    final String host;
    final int port;
    final boolean tls;
    private ServerConfig(Builder b) {
        if (b.port < 1 || b.port > 65535) throw new IllegalArgumentException();
        host=Objects.requireNonNull(b.host); port=b.port; tls=b.tls;
    }
    static class Builder {
        private String host="localhost"; private int port=8080; private boolean tls;
        Builder host(String x) { host=x; return this; }
        Builder port(int x) { port=x; return this; }
        Builder tls(boolean x) { tls=x; return this; }
        ServerConfig build() { return new ServerConfig(this); }
    }
}
// new ServerConfig.Builder().host("api.example").tls(true).build()
```

**Use it when:** Use for immutable configuration or request objects with several optional fields, validation rules, or a readable construction call.

**Trade-offs and pitfalls:** Makes valid construction explicit and call sites readable. Adds a builder type and can be boilerplate; a record constructor or a few overloads may be clearer for small objects.

**Remember:** Readable, validated construction.

## 3. Factory Method

**Intent:** Define a creation hook so subclasses or implementations choose which concrete product to create while the creator keeps the workflow.

**Java example:**

```java
interface Exporter { void export(String text); }
abstract class ExportJob {
    final void run(String text) {
        Exporter out=createExporter();
        out.export(text);
    }
    protected abstract Exporter createExporter();
}
final class CsvJob extends ExportJob {
    protected Exporter createExporter() { return text -> System.out.println("CSV: "+text); }
}
```

**Use it when:** Use when a workflow is stable but the concrete dependency varies by product type, deployment, or extension point.

**Trade-offs and pitfalls:** Separates creation from use and supports extensions. Extra subclasses can obscure simple construction; constructor injection or a supplier may be smaller in a modern Java application.

**Remember:** Subclass chooses one product.

## 4. Prototype

**Intent:** Create a new object by copying a configured prototype rather than rebuilding it from scratch.

**Java example:**

```java
final class ReportTemplate {
    private final String title;
    private final List<String> columns;
    ReportTemplate(String t, List<String> c) { title=t; columns=List.copyOf(c); }
    ReportTemplate copyWithTitle(String t) { return new ReportTemplate(t, columns); }
    String title() { return title; }
    List<String> columns() { return columns; }
}
```

**Use it when:** Use when objects are expensive or cumbersome to configure and many variants share a prepared baseline.

**Trade-offs and pitfalls:** Avoids repeating setup and can hide concrete classes. Copying object graphs with cycles, shared references, resources, or mutable state needs an explicit policy.

**Remember:** Copy a configured instance; define shallow versus deep.

## 5. Singleton

**Intent:** Provide one shared instance and a single access point for a type.

**Java example:**

```java
enum MetricsRegistry {
    INSTANCE;
    private final AtomicLong requests=new AtomicLong();
    void recordRequest() { requests.incrementAndGet(); }
    long requests() { return requests.get(); }
}
```

**Use it when:** Use rarely for truly process-wide, stateless coordination whose lifetime is intentionally the whole process.

**Trade-offs and pitfalls:** Makes sharing simple, but introduces global state, hidden dependencies, and test coupling. In Spring, a singleton-scoped bean means one bean per container, not a JVM-wide GoF singleton.

**Remember:** One instance has a lifecycle and synchronization cost.

---

Reference: [Design Patterns: Elements of Reusable Object-Oriented Software (publisher page)](https://www.informit.com/store/design-patterns-elements-of-reusable-object-oriented-software-9780201633610). The patterns are summarized in original wording with small Java examples; consult the linked source for fuller treatment.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)