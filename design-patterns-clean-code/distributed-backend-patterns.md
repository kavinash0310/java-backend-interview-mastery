# Distributed Backend Patterns

These patterns help services communicate, isolate failures, coordinate workflows, migrate systems, and manage data freshness. They introduce costs and operational responsibilities; select them to solve a demonstrated problem.

Each entry covers the intent, a focused Java example, when it helps, trade-offs or pitfalls, and a short memory hook. Examples are illustrative fragments; imports, framework setup, and domain types are omitted where they are not the point. The examples target Java 17 unless a framework is named.

## 1. API Gateway

**Intent:** Provide a controlled entry point that routes client requests to backend services and may handle shared edge concerns.

**Java example:**

```java
// Client -> gateway -> catalog service
Route route = Route.path("/catalog/**")
    .uri("http://catalog-service");
```

**Use it when:** Use when external clients need a stable API boundary over several services or when edge authentication, routing, or throttling must be centralized.

**Trade-offs and pitfalls:** Hides service topology and simplifies clients. Adds a critical hop and can become a bottleneck or a place where business logic accumulates.

**Remember:** One edge, many routes; keep it thin.

## 2. Backend for Frontend (BFF)

**Intent:** Give each client type a backend tailored to its data shape and interaction needs.

**Java example:**

```java
interface MobileOrders {
    MobileOrderSummary getOrder(String id);
}
final class MobileOrdersBff implements MobileOrders {
    private final OrderClient orders;
    public MobileOrderSummary getOrder(String id) {
        return MobileOrderSummary.from(orders.get(id));
    }
}
```

**Use it when:** Use when mobile, web, or partner clients need materially different APIs, aggregation, or release cadence.

**Trade-offs and pitfalls:** Improves client fit and isolates client-specific needs. Creates more services and can duplicate simple orchestration.

**Remember:** A backend shaped for one frontend.

## 3. Circuit Breaker

**Intent:** Stop sending calls to a dependency that is persistently failing; periodically allow a small probe to see whether it recovered.

**Java example:**

```java
if (!breaker.tryAcquire()) throw new DependencyUnavailable();
try {
    Result r=remote.call();
    breaker.recordSuccess();
    return r;
} catch (TransientFailure e) {
    breaker.recordFailure();
    throw e;
}
```

**Use it when:** Use around remote dependencies when repeated slow failures could consume threads or cascade through the service.

**Trade-offs and pitfalls:** Fails fast and gives a dependency room to recover. Thresholds and half-open behavior need tuning; fallback data can be stale or misleading.

**Remember:** Closed passes; open rejects; half-open probes.

## 4. Retry with Backoff and Jitter

**Intent:** Repeat a failed operation when the failure is transient, with delays that increase and vary to avoid synchronized retry bursts.

**Java example:**

```java
for (int attempt=0; attempt<maxAttempts; attempt++) {
    try { return call(); }
    catch (TransientFailure e) {
        if (attempt+1==maxAttempts) throw e;
        sleep(backoffWithJitter(attempt));
    }
}
```

**Use it when:** Use for brief network or service faults when the operation can safely be repeated and the caller has a deadline.

**Trade-offs and pitfalls:** Can recover without surfacing temporary faults. Increases latency and load, especially during an outage.

**Remember:** Retry only transient faults, with a budget.

## 5. Timeout / Deadline Propagation

**Intent:** Bound how long work may wait and pass the remaining time budget to downstream calls.

**Java example:**

```java
long deadline=System.nanoTime()+Duration.ofMillis(800).toNanos();
Duration remaining=Duration.ofNanos(Math.max(0,deadline-System.nanoTime()));
return httpClient.send(request,remaining);
```

**Use it when:** Use for every network or blocking boundary so stalled dependencies do not hold resources indefinitely.

**Trade-offs and pitfalls:** Protects capacity and gives predictable latency. A timeout does not prove that a remote side effect did not happen.

**Remember:** Deadline is shared; timeout is not cancellation guarantee.

## 6. Bulkhead

**Intent:** Isolate resource pools so overload or failure in one dependency or workload cannot consume all capacity.

**Java example:**

```java
final Semaphore inventorySlots=new Semaphore(20);
if (!inventorySlots.tryAcquire()) throw new BusyException();
try { return inventoryClient.reserve(item); }
finally { inventorySlots.release(); }
```

**Use it when:** Use when dependencies have different latency or criticality, or when one tenant/workload must not starve another.

**Trade-offs and pitfalls:** Limits blast radius and bounds concurrency. Capacity tuning can reject useful work or strand resources if release is not guaranteed.

**Remember:** Separate capacity by failure domain.

## 7. Rate Limiter

**Intent:** Restrict requests over time to protect capacity, enforce quotas, or provide fair access.

**Java example:**

```java
// A token bucket allows bursts up to capacity and refills steadily.
if (!bucket.tryConsume(tenantId,1)) {
    throw new TooManyRequestsException();
}
```

**Use it when:** Use at API edges, per-user or per-tenant quotas, expensive operations, and downstream protection.

**Trade-offs and pitfalls:** Controls load and abuse. Limits require clear identity, clock/consistency decisions, and a policy for distributed instances.

**Remember:** Rate controls arrivals; bulkhead controls work in flight.

## 8. Saga

**Intent:** Coordinate a business transaction across services as local transactions with compensating actions for steps that cannot be committed.

**Java example:**

```java
// Orchestrated flow
reserveInventory(order);
try {
    chargePayment(order);
    confirmShipment(order);
} catch (RuntimeException failure) {
    releaseInventory(order);
    refundIfCharged(order);
    throw failure;
}
```

**Use it when:** Use for long-running cross-service workflows where services own separate databases and eventual consistency is acceptable.

**Trade-offs and pitfalls:** Avoids a distributed database transaction and can scale across services. Requires explicit compensation, observability, and handling of partial progress.

**Remember:** Local commits plus explicit undo/compensation.

## 9. Transactional Outbox

**Intent:** Persist a business change and the event to publish in the same local database transaction; a relay publishes pending rows later.

**Java example:**

```java
@Transactional
void placeOrder(Order order) {
    orders.save(order);
    outbox.save(new OutboxEvent(order.id(),"OrderPlaced"));
}
// A relay retries unpublished outbox rows.
```

**Use it when:** Use when a service must update its database and reliably notify Kafka or another broker without a distributed transaction.

**Trade-offs and pitfalls:** Closes the dual-write gap and enables replay. Adds a relay, cleanup, ordering, and operational monitoring.

**Remember:** Atomic DB write; asynchronous relay; idempotent consumer.

## 10. Command Query Responsibility Segregation (CQRS)

**Intent:** Separate write-side commands from read-side queries, allowing each model to serve its workload.

**Java example:**

```java
record PlaceOrder(String customerId,List<Line> lines) {}
record OrderView(String id,String status,BigDecimal total) {}
interface OrderCommands { void handle(PlaceOrder command); }
interface OrderQueries { OrderView find(String id); }
```

**Use it when:** Use when read and write requirements diverge substantially or read projections need different shapes/scaling.

**Trade-offs and pitfalls:** Allows independent models and optimized reads. Introduces synchronization, eventual consistency, and more deployment/runtime complexity.

**Remember:** Separate models where the workload justifies it.

## 11. Event Sourcing

**Intent:** Store a sequence of domain events as the source of truth and derive current state by replaying them.

**Java example:**

```java
sealed interface AccountEvent permits Deposited,Withdrawn {}
record Deposited(BigDecimal amount) implements AccountEvent {}
record Withdrawn(BigDecimal amount) implements AccountEvent {}
BigDecimal balance(List<AccountEvent> events) {
    BigDecimal value=BigDecimal.ZERO;
    for (AccountEvent e:events) {
        if (e instanceof Deposited d) value=value.add(d.amount());
        else if (e instanceof Withdrawn w) value=value.subtract(w.amount());
    }
    return value;
}
```

**Use it when:** Use when the event history itself is valuable for audit, temporal queries, or rebuilding projections.

**Trade-offs and pitfalls:** Preserves history and enables new projections. Event schemas, replay, snapshots, and operational tooling become long-term obligations.

**Remember:** State is derived from immutable facts.

## 12. Cache-Aside

**Intent:** On a read, check the cache first; on a miss, load from the source, then populate the cache.

**Java example:**

```java
Product find(String id) {
    Product p=cache.get(id);
    if (p!=null) return p;
    p=repository.find(id);
    if (p!=null) cache.put(id,p,ttl);
    return p;
}
```

**Use it when:** Use for hot, mostly read data when the application can tolerate a defined freshness window.

**Trade-offs and pitfalls:** Reduces source load and latency. Cache misses, stampedes, invalidation, and stale values require policy.

**Remember:** Application owns the load and cache-fill flow.

## 13. Idempotency Key

**Intent:** Give one logical client operation a stable key so duplicate delivery returns the original result instead of repeating the side effect.

**Java example:**

```java
Result handle(String key,Request request) {
    return idempotencyStore.executeOnce(key,request.hash(),
        () -> paymentService.charge(request));
}
```

**Use it when:** Use for payments, order creation, and retried POST requests where clients may resend after a timeout.

**Trade-offs and pitfalls:** Prevents duplicate effects within the key's scope. Requires durable key storage, expiry, conflict behavior, and concurrency control.

**Remember:** Same key + same request => same logical outcome.

## 14. Dead Letter Queue (DLQ)

**Intent:** Move messages that cannot be processed after a bounded retry policy to a separate destination for inspection or later replay.

**Java example:**

```java
try {
    consumer.process(message);
    offset.commit();
} catch (PermanentFailure e) {
    dlq.publish(message.withFailure(e.getMessage()));
    offset.commit();
}
```

**Use it when:** Use when poison messages must not block a partition or queue indefinitely and an operator/repair flow can resolve them.

**Trade-offs and pitfalls:** Isolates repeated failures and preserves payloads. DLQs need ownership, alerts, retention, and a safe replay procedure.

**Remember:** Bounded retry, durable quarantine, deliberate replay.

## 15. Competing Consumers

**Intent:** Run multiple consumers against a shared queue or partition set so work can be processed concurrently.

**Java example:**

```java
while (!Thread.currentThread().isInterrupted()) {
    Job job=queue.take();
    process(job);
}
```

**Use it when:** Use for independent background jobs where throughput can scale by adding workers.

**Trade-offs and pitfalls:** Increases throughput and absorbs load. Ordering is weaker across workers, and consumers must tolerate retries and duplicates.

**Remember:** More workers increase throughput, not global ordering.

## 16. Strangler Fig

**Intent:** Replace a legacy system incrementally by routing selected capabilities to a new implementation while the old system remains available.

**Java example:**

```java
final class OrderRouter {
    private final OrderService legacy,modern;
    private final FeatureFlags flags;
    OrderRouter(OrderService legacy,OrderService modern,FeatureFlags flags) {
        this.legacy=legacy; this.modern=modern; this.flags=flags;
    }
    Order place(CreateOrder request) {
        return flags.useNewFlow(request.customerId())
            ? modern.place(request) : legacy.place(request);
    }
}
```

**Use it when:** Use for gradual migration when a full rewrite is too risky and traffic can be divided by capability or cohort.

**Trade-offs and pitfalls:** Reduces big-bang cutover risk and lets teams learn incrementally. Temporary dual paths and data synchronization add complexity.

**Remember:** Replace one slice at a time, then remove the old slice.

## 17. Anti-Corruption Layer

**Intent:** Translate an external or legacy model into the vocabulary and rules of the new domain.

**Java example:**

```java
final class LegacyCustomerAdapter {
    Customer toDomain(LegacyCustomerDto old) {
        return new Customer(new CustomerId(old.cust_no()),Email.parse(old.mail_addr()));
    }
}
```

**Use it when:** Use at bounded-context or vendor boundaries when the outside model should not leak through the application.

**Trade-offs and pitfalls:** Protects domain clarity and isolates vendor changes. Adds mapping code and may conceal important external semantics if translation is incomplete.

**Remember:** Translate meanings at the boundary, not just field names.

## 18. Leader Election / Lease

**Intent:** Allow one active worker to own a scheduled task at a time using a coordination service or expiring lease.

**Java example:**

```java
if (lease.tryAcquire("daily-report", ownerId, leaseDuration)) {
    try { generateReport(); }
    finally { lease.release("daily-report",ownerId); }
}
```

**Use it when:** Use when multiple service instances share a scheduled job and duplicate execution is unsafe.

**Trade-offs and pitfalls:** Prevents routine duplicate ownership. Lease expiry, pauses, clock behavior, and network partitions can still create overlapping workers.

**Remember:** Lease ownership can expire; fence stale work.

## 19. Queue-Based Load Leveling

**Intent:** Put a durable queue between producers and workers to absorb bursts and process work at a controlled rate.

**Java example:**

```java
void submit(Command c) { workQueue.publish(c); }
void worker() { for (;;) process(workQueue.receive()); }
```

**Use it when:** Use for slow or bursty tasks that do not need a synchronous response, such as emails, reports, or media processing.

**Trade-offs and pitfalls:** Smooths spikes and decouples availability. Adds queue latency, duplicate delivery, backlog monitoring, and eventual completion.

**Remember:** Buffer the burst; scale workers to sustainable capacity.

---

Reference: [Microsoft cloud design pattern catalog](https://learn.microsoft.com/azure/architecture/patterns). The patterns are summarized in original wording with small Java examples; consult the linked source for fuller treatment.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)