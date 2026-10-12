# Multithreading and Concurrency Interview Questions

These questions come up together because they test the same habits: know what state is shared, choose the smallest useful coordination tool, and make failure and shutdown visible.

## synchronized vs volatile

Use <code>synchronized</code> when a group of operations must act as one step. It gives mutual exclusion and visibility: a thread leaving a synchronized block publishes its writes to a thread that later enters the same lock.

Use <code>volatile</code> for a simple shared value where reads and writes need to be visible, but no read-modify-write operation needs to be atomic. It does not make <code>count++</code> safe; that operation is a read followed by a write.

~~~java
private volatile boolean running = true; // visibility is enough
private int count;                        // count++ needs coordination
~~~

A useful memory hook: volatile tells threads what value is current; a lock also lets one thread at a time change related state.

## What is a deadlock, and how do you prevent it?

A deadlock is a cycle of threads, each holding a lock the next one needs. For example, thread A holds the account lock and waits for the inventory lock, while thread B holds the inventory lock and waits for the account lock.

The first prevention step is to acquire multiple locks in a consistent global order. Keep lock scopes short, avoid calling unknown code while holding a lock, and use timed <code>tryLock</code> when waiting forever is unacceptable. Thread dumps help confirm a suspected deadlock.

## ExecutorService vs CompletableFuture

They solve related but different problems. <code>ExecutorService</code> owns task execution: submit work, manage a pool, cancel it, and shut it down. <code>CompletableFuture</code> describes a result that may arrive later and lets you compose steps such as thenApply, thenCompose, and exceptionally.

In a service, I usually put blocking work on a deliberately sized executor and use CompletableFuture to combine independent results. Always set timeouts and handle errors; asynchronous code does not make a slow dependency fast.

## What are virtual threads in Java 21?

Virtual threads are lightweight Java threads scheduled by the runtime over a smaller set of operating-system threads. They make thread-per-request code practical for workloads with many tasks that spend time waiting on network or file I/O. They are not a speedup for CPU-heavy work, and a virtual thread should generally be created per task rather than pooled.

~~~java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    Future<String> result = executor.submit(() -> callRemoteService());
    System.out.println(result.get());
}
~~~

They make blocking code easier to scale; they do not remove database connection limits, downstream capacity limits, or the need for timeouts. See [JEP 444](https://openjdk.org/jeps/444).

## synchronized vs ReentrantLock

Both provide mutual exclusion. A synchronized block is usually the clearest default and releases its monitor automatically when control exits the block, including through an exception. <code>ReentrantLock</code> is useful when you need timed or interruptible lock acquisition, fairness options, or multiple Conditions.

~~~java
lock.lock();
try {
    updateSharedState();
} finally {
    lock.unlock();
}
~~~

The finally block matters: forgetting to unlock can stall every other thread.

## How do you create threads in Java?

For real applications, define a task with Runnable or Callable and submit it to an ExecutorService. Creating raw Thread objects is fine for a small demonstration, but an executor gives the application a place to manage concurrency, rejection, shutdown, and task results.

~~~java
try (var executor = Executors.newFixedThreadPool(8)) {
    Future<Integer> result = executor.submit(() -> calculate());
    System.out.println(result.get());
}
~~~

The pool size and queue should reflect the workload and downstream limits; an unbounded queue can hide overload until latency or memory becomes a problem.

## What can go wrong if many threads share a HashMap?

HashMap is not designed for concurrent mutation. Updates can be lost, and a reader may observe inconsistent state. Use ConcurrentHashMap when independent concurrent access is appropriate, or protect the map with one lock if several map operations must form one atomic business action.

A concurrent collection protects its own operations; it does not automatically make a multi-step workflow atomic.

## Duplicate payment requests during a race

Use the [API idempotency design](../system-design-architecture/interview-questions.md#api-idempotency-and-duplicate-requests). The concurrency detail is that two requests can claim the same key at once, so the claim must be atomic in durable storage. A synchronized block protects only one application process; it cannot coordinate several instances.

## Thousands of threads are created and CPU usage spikes. What do you do?

First check whether the CPU is actually doing work or spending time in garbage collection, lock contention, or repeated retries. Use thread dumps and runtime metrics to inspect thread states, then compare active threads, request volume, executor queues, and downstream latency.

Stop unbounded thread creation, use bounded executors or virtual threads where the workload is mostly blocking, and apply backpressure and timeouts. Virtual threads reduce the cost of waiting threads; they do not increase the database's connection capacity.

## Several threads update the same database record

Treat each update as a concurrent database transaction. The database must enforce the invariant even when requests land on different application instances. For the optimistic vs pessimistic locking trade-off and an inventory example, see [two transactions updating the same row](../sql-relational-databases/interview-questions.md#two-transactions-update-the-same-row-how-do-you-handle-it).

## Further reading

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
