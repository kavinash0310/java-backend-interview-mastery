# Multithreading and Concurrency: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Memory visibility and safe publication

### Q1. Which happens-before edges matter when starting and joining a thread?

**Answer:** Actions before `Thread.start()` happen-before its actions; a thread's actions happen-before another thread successfully returns from `join()`.

**Example:** Initialize task state before start, then read the result after join.

### Q2. What does safe publication guarantee for an object?

**Answer:** Other threads see a fully initialized object through a defined synchronization path, such as a final field, volatile reference, lock, or static initialization.

**Example:** Publish a shared reference through a volatile field.

### Q3. Why is double-checked locking unsafe without volatile?

**Answer:** The reference write may become visible before the constructor's writes, allowing another thread to observe a partially initialized object.

**Example:** Declare the singleton reference `volatile` if using this pattern.

### Q4. How does a final field help with initialization safety?

**Answer:** The Java memory model gives special guarantees for properly constructed final fields, but it does not make the whole object thread-safe.

**Example:** Do not let `this` escape during construction.

### Q5. What visibility edge does unlocking and later locking the same monitor create?

**Answer:** A monitor unlock happens-before a subsequent lock of that same monitor, making prior writes visible.

**Example:** Protect both read and write paths with the same lock.

### Q6. What is safe publication through a static initializer?

**Answer:** Class initialization is synchronized by the JVM, so a properly initialized static field is safely visible to users of the class.

**Example:** An initialization-on-demand holder can create a lazy singleton.

### Q7. What memory guarantee does ExecutorService submission provide?

**Answer:** Actions before submitting a task happen-before the task begins; task actions happen-before a successful result retrieval with Future.get().

**Example:** Use the Future result to observe task output.

### Q8. Why does a volatile counter still lose increments?

**Answer:** Volatile makes each read and write visible, but `read + 1` and `write` remain separate operations.

**Example:** Use `AtomicInteger.incrementAndGet()` for an atomic increment.

### Q9. Why should code restore the interrupt flag after catching InterruptedException?

**Answer:** Many blocking methods clear the flag when throwing; restoring it preserves the cancellation signal for outer code.

**Example:** `catch (InterruptedException e) { Thread.currentThread().interrupt(); return; }`

### Q10. What does a lock's happens-before rule not guarantee?

**Answer:** It only orders accesses that use the relevant synchronization relationship; unrelated unsynchronized state can still race.

**Example:** Do not guard one field with a different lock and expect visibility.

## Atomics and concurrent collections

### Q11. What is the ABA problem in compare-and-set algorithms?

**Answer:** A value changes from A to B and back to A, so a CAS sees A and misses the intermediate change.

**Example:** Use a stamped or versioned reference when the change history matters.

### Q12. When can LongAdder outperform AtomicLong?

**Answer:** Under heavy contention, it spreads updates across cells and combines them when read; the sum is not an atomic snapshot during updates.

**Example:** Use it for metrics, not for a strict sequence number.

### Q13. What restrictions apply to ConcurrentHashMap.compute functions?

**Answer:** The function should be short and must not recursively modify the same map in a way that causes recursive update errors or contention.

**Example:** Compute a value locally, then return it.

### Q14. Why does ConcurrentHashMap reject null keys and values?

**Answer:** A null result from get can then unambiguously mean no mapping, which is useful during concurrent operations.

**Example:** Use Optional or an explicit sentinel if absence needs representation.

### Q15. When is CopyOnWriteArrayList a good choice?

**Answer:** When reads and iteration are frequent but writes are rare; each mutation copies the backing array.

**Example:** Listener registries are a common fit.

### Q16. What coordination does BlockingQueue provide?

**Answer:** It safely transfers items between threads and can block producers or consumers when a capacity boundary is reached.

**Example:** Use `put` and `take` for a bounded work handoff.

### Q17. What is the trade-off of ConcurrentLinkedQueue?

**Answer:** It supports nonblocking concurrent access but is unbounded, so it does not itself provide backpressure.

**Example:** Pair it with an admission limit if producers can overwhelm consumers.

### Q18. How does StampedLock optimistic reading work?

**Answer:** A reader takes a stamp without acquiring a read lock, reads state, then validates the stamp and retries under a real lock if a write intervened.

**Example:** Copy a consistent snapshot only after validation succeeds.

### Q19. When might ReadWriteLock reduce throughput instead of improving it?

**Answer:** Lock bookkeeping and writer contention can outweigh concurrent-read gains, especially for short critical sections or frequent writes.

**Example:** Measure with the actual read/write mix.

### Q20. Why should Condition.await() be called in a loop?

**Answer:** A wakeup does not guarantee the condition is true; spurious wakeups and competing consumers are possible.

**Example:** `while (!queueHasItem()) condition.await();`

## Executors and asynchronous work

### Q21. Why is an unbounded executor queue risky?

**Answer:** When work arrives faster than workers can finish, queued tasks can grow until memory or latency becomes unacceptable.

**Example:** Use a bounded queue and an explicit rejection policy.

### Q22. What does CallerRunsPolicy do to a saturated executor?

**Answer:** The submitting thread executes the task, which can slow producers and apply backpressure.

**Example:** Use carefully if the submitting thread is allowed to run that work.

### Q23. shutdown() vs shutdownNow(): what is the practical difference?

**Answer:** shutdown stops accepting new tasks but lets submitted tasks finish; shutdownNow requests interruption and returns queued tasks.

**Example:** Wait for termination after shutdown, with a bounded timeout.

### Q24. How do invokeAll() and invokeAny() differ?

**Answer:** invokeAll waits for all submitted tasks; invokeAny returns one successful completed result and cancels unfinished tasks when appropriate.

**Example:** Use invokeAny when any replica can answer.

### Q25. thenApply() vs thenCompose() in CompletableFuture?

**Answer:** thenApply maps a value; thenCompose chains a function that already returns a future, avoiding nested futures.

**Example:** `fetchUser(id).thenCompose(this::fetchOrders)`.

### Q26. When should thenApplyAsync receive an explicit executor?

**Answer:** When the continuation is expensive or blocking and should not run on the completing thread or common pool.

**Example:** `thenApplyAsync(this::encode, cpuPool)`.

### Q27. exceptionally() vs handle()?

**Answer:** exceptionally recovers only from failure; handle receives either the value or error and can map both outcomes.

**Example:** Use handle when building a result that represents success or failure.

### Q28. How should a service apply a deadline across multiple async calls?

**Answer:** Propagate one overall deadline and allocate each sub-call only the remaining budget; per-call timeouts alone can exceed the request budget.

**Example:** Set a deadline at request entry and pass it downstream.

### Q29. Does Future.cancel(true) guarantee a task stops?

**Answer:** No. It requests interruption; the task must cooperate and blocking APIs may respond differently.

**Example:** Check interruption in long loops and clean up in finally.

### Q30. What can go wrong if scheduled tasks throw uncaught exceptions?

**Answer:** A periodic task may stop running after a failed execution, depending on the scheduler API and wrapper.

**Example:** Observe futures and log task failures explicitly.

## Virtual threads and performance

### Q31. When are virtual threads a good fit?

**Answer:** They suit large numbers of mostly blocking tasks where each task can use a simple synchronous style.

**Example:** A request waiting on several network calls can use a virtual thread.

### Q32. Do virtual threads make CPU-bound work faster?

**Answer:** No. They reduce the cost of waiting tasks; CPU work is still limited by processor capacity.

**Example:** Use bounded parallelism for CPU-heavy image processing.

### Q33. What could pin a virtual thread in Java 21?

**Answer:** Blocking while inside some synchronized regions or native/foreign calls can pin its carrier, limiting scheduler flexibility.

**Example:** Keep synchronized sections short and check JFR pinning events.

### Q34. Why is one virtual thread per task different from pooling virtual threads?

**Answer:** Virtual threads are intended to be cheap and task-scoped; pooling them can add unnecessary lifecycle and thread-local reuse assumptions.

**Example:** Create a new virtual thread per independent task.

### Q35. How should ThreadLocal be used with pooled platform threads?

**Answer:** Clear request-specific values in a finally block because a worker is reused for another request.

**Example:** Call `threadLocal.remove()` at request completion.

### Q36. Why can ThreadLocal still consume memory with virtual threads?

**Answer:** Each virtual thread may carry its own thread-local values, so creating many threads with large values can use substantial memory.

**Example:** Store only small, short-lived values.

### Q37. How can a semaphore complement virtual threads?

**Answer:** It limits concurrent access to a scarce resource even if many virtual threads are waiting.

**Example:** Acquire a permit before calling a database with a fixed connection limit.

### Q38. What measurements help size a worker pool?

**Answer:** Measure task service time, queue wait, CPU use, blocking ratio, and downstream limits under representative load.

**Example:** A pool larger than the database's connection capacity may only increase waiting.

### Q39. How can JFR help diagnose lock contention?

**Answer:** Flight Recorder can show lock wait events, thread states, and time spent blocked with relatively low overhead.

**Example:** Correlate hot locks with the request or task workload.

### Q40. Why should thread dumps be captured more than once?

**Answer:** A sequence shows whether threads are making progress or stuck in the same states and stacks.

**Example:** Capture a few dumps several seconds apart during the issue.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Java Language Specification: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
