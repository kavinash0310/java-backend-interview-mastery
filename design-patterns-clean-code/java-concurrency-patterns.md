# Java Concurrency Patterns and Idioms

These patterns manage ownership, work handoff, waiting, and limits in concurrent Java programs. Several are usage patterns for standard Java concurrency primitives rather than GoF catalog entries.

Each entry covers the intent, a focused Java example, when it helps, trade-offs or pitfalls, and a short memory hook. Examples are illustrative fragments; imports, framework setup, and domain types are omitted where they are not the point. The examples target Java 17 unless a framework is named.

## 1. Producer–Consumer

**Intent:** Decouple work creation from work processing with a thread-safe queue.

**Java example:**

```java
BlockingQueue<Job> jobs=new ArrayBlockingQueue<>(500);
// producer
jobs.put(job);
// worker
Job next=jobs.take();
process(next);
```

**Use it when:** Use for background jobs, bounded handoff, and smoothing differences between incoming work and processing speed.

**Trade-offs and pitfalls:** Separates producer and worker rates. Queue depth adds latency and memory; unbounded queues can turn overload into an out-of-memory incident.

**Remember:** Queue is the handoff; bounds are part of the design.

## 2. Thread Pool / Worker Pool

**Intent:** Reuse a bounded set of worker threads to execute many tasks without creating a thread per request.

**Java example:**

```java
ExecutorService pool=new ThreadPoolExecutor(
    8, 16, 30, TimeUnit.SECONDS,
    new ArrayBlockingQueue<>(200),
    new ThreadPoolExecutor.AbortPolicy());
Future<Result> result=pool.submit(this::compute);
```

**Use it when:** Use for controlled CPU or blocking work where concurrency, queue size, and rejection behavior need explicit limits.

**Trade-offs and pitfalls:** Amortizes thread creation and caps concurrency. Poor sizing, blocking tasks, or an unbounded queue can increase latency and resource use.

**Remember:** Bound threads and queue; plan what happens when full.

## 3. Future / Promise

**Intent:** Represent a result that will be available later and compose dependent asynchronous work.

**Java example:**

```java
CompletableFuture<User> user=userClient.fetch(id);
CompletableFuture<List<Order>> orders=user.thenCompose(
    u -> orderClient.fetchFor(u.id()));
CompletableFuture<View> view=orders.thenApply(this::toView);
```

**Use it when:** Use for asynchronous I/O or independent work whose result is needed later.

**Trade-offs and pitfalls:** Can overlap waits and compose callbacks. Error paths, cancellation, executor choice, and context propagation become explicit concerns.

**Remember:** A future is a value in progress.

## 4. Monitor / Synchronized Guard

**Intent:** Protect a shared invariant by allowing only one thread at a time into a critical section guarded by the same monitor.

**Java example:**

```java
final class Counter {
    private long value;
    synchronized long incrementAndGet() { return ++value; }
    synchronized long get() { return value; }
}
```

**Use it when:** Use for short, simple critical sections with one object-level invariant.

**Trade-offs and pitfalls:** Built into Java and releases the monitor automatically on exit. Long-held locks reduce throughput and lock ordering can deadlock.

**Remember:** One monitor protects one invariant.

## 5. Guarded Suspension

**Intent:** Wait until a condition becomes true before proceeding, using a lock/condition or wait/notify.

**Java example:**

```java
lock.lock();
try {
    while (queue.isEmpty()) notEmpty.await();
    return queue.removeFirst();
} finally {
    lock.unlock();
}
```

**Use it when:** Use for bounded queues, resource availability, and state machines where a thread should wait for a predicate.

**Trade-offs and pitfalls:** Avoids busy waiting and coordinates state changes. Waiting can tie up threads; missed signals and incorrect visibility cause bugs.

**Remember:** Wait for a condition, not for a notification.

## 6. Read–Write Lock

**Intent:** Allow multiple readers concurrently while excluding writers that modify shared state.

**Java example:**

```java
final ReentrantReadWriteLock rw=new ReentrantReadWriteLock();
String read() {
    rw.readLock().lock();
    try { return value; }
    finally { rw.readLock().unlock(); }
}
```

**Use it when:** Use for a read-heavy in-memory structure where reads are long enough to offset lock overhead.

**Trade-offs and pitfalls:** Can increase read concurrency. Writers may starve depending on policy, and lock upgrades are tricky.

**Remember:** Many readers or one writer.

## 7. Immutable Object

**Intent:** Make state unchangeable after construction so readers can share it without synchronization.

**Java example:**

```java
record Money(BigDecimal amount,Currency currency) {
    Money {
        Objects.requireNonNull(amount);
        Objects.requireNonNull(currency);
    }
}
```

**Use it when:** Use for value objects, configuration, messages, and shared snapshots.

**Trade-offs and pitfalls:** Makes reasoning and sharing safer. Replacing state creates new objects; nested mutable fields must also be copied or immutable.

**Remember:** Immutability must cover the whole reachable state.

## 8. Thread Confinement / Single Writer

**Intent:** Give mutable state to one thread or event loop so coordination is unnecessary for that state.

**Java example:**

```java
final ExecutorService actor=Executors.newSingleThreadExecutor();
actor.submit(() -> account.apply(command)); // all writes are serialized
```

**Use it when:** Use for per-key actors, UI state, event-loop handlers, or deterministic state machines.

**Trade-offs and pitfalls:** Reduces lock contention and simplifies invariants. A hot key becomes a bottleneck and cross-thread reads need snapshots or message passing.

**Remember:** One owner changes the state; others send messages.

## 9. ThreadLocal

**Intent:** Associate a value with the current thread rather than sharing one mutable instance among threads.

**Java example:**

```java
private static final ThreadLocal<String> REQUEST_ID=new ThreadLocal<>();
void handle(String requestId) {
    REQUEST_ID.set(requestId);
    try { process(); }
    finally { REQUEST_ID.remove(); }
}
```

**Use it when:** Use sparingly for thread-bound context or non-thread-safe objects when the runtime uses stable worker threads.

**Trade-offs and pitfalls:** Avoids locks for per-thread state. Thread pools retain threads, which can retain request data and class loaders.

**Remember:** Thread-local follows the worker thread, not the request.

## 10. Double-Checked Locking

**Intent:** Initialize a lazily created shared instance while avoiding a synchronized read on every access.

**Java example:**

```java
final class ConfigHolder {
    private static volatile Config instance;
    static Config get() {
        Config x=instance;
        if (x==null) synchronized (ConfigHolder.class) {
            x=instance;
            if (x==null) instance=x=new Config();
        }
        return x;
    }
}
```

**Use it when:** Mostly for legacy code with measured need for lazy one-time initialization.

**Trade-offs and pitfalls:** Correct on modern Java only with `volatile`, but harder to review than simpler lazy-holder or enum patterns.

**Remember:** If used, the instance field must be volatile.

## 11. Fork/Join and Work Stealing

**Intent:** Split CPU-bound work into smaller tasks; idle workers steal tasks from busy workers.

**Java example:**

```java
ForkJoinPool pool=new ForkJoinPool();
long total=pool.invoke(new RecursiveTask<>() {
    protected Long compute() {
        if (smallEnough()) return sequentialSum();
        var left=new SumTask(from,mid);
        var right=new SumTask(mid,to);
        left.fork();
        return right.compute()+left.join();
    }
});
```

**Use it when:** Use for divide-and-conquer CPU work with many independent subtasks.

**Trade-offs and pitfalls:** Balances uneven task trees. Tiny tasks add scheduling overhead; blocking I/O can starve a pool.

**Remember:** Split CPU work; do not park workers on I/O.

## 12. Semaphore / Permit Limit

**Intent:** Allow only a fixed number of threads to access a resource or operation concurrently.

**Java example:**

```java
Semaphore permits=new Semaphore(20);
permits.acquire();
try { return partnerApi.call(); }
finally { permits.release(); }
```

**Use it when:** Use to bound concurrent access to a scarce connection, partner API, or expensive operation.

**Trade-offs and pitfalls:** Simple concurrency cap. It does not limit requests per second and can queue callers unless `tryAcquire` or a timeout is used.

**Remember:** Permits bound in-flight work, not arrival rate.

## 13. Reactor / Event Loop

**Intent:** Use a small number of event-loop threads to handle many I/O operations by reacting to readiness and completion events.

**Java example:**

```java
// Reactive-style illustration
return webClient.get().uri("/items/{id}",id)
    .retrieve().bodyToMono(Item.class)
    .map(this::toView);
```

**Use it when:** Use for high-concurrency I/O workloads when the full call chain can remain non-blocking.

**Trade-offs and pitfalls:** Can serve many connections with fewer threads. Requires non-blocking discipline and changes debugging, context, and backpressure behavior.

**Remember:** Non-blocking all the way through, or the event loop blocks.

---

Reference: [Java concurrency utilities](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/package-summary.html). The patterns are summarized in original wording with small Java examples; consult the linked source for fuller treatment.

[Design Patterns module](README.md) · [Pattern selection guide](pattern-selection-guide.md) · [Course home](../README.md)