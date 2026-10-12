# Multithreading and Concurrency: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Visibility and synchronization puzzles

### Q1. Does volatile int count; count++ make increments atomic?

**Answer:** No. The read, add, and write are separate; concurrent updates can be lost. Use AtomicInteger or synchronization for the compound operation.

### Q2. If a thread starts another thread after writing a field, can the child see that write?

**Answer:** Actions before Thread.start happen-before actions in the started thread. This does not make later unsynchronized writes visible.

### Q3. Does returning from Future.get make task results visible to the caller?

**Answer:** Yes, task actions happen-before actions after the corresponding successful get. Visibility does not make shared mutable state generally race-free.

### Q4. Why can synchronized(lock) fail to coordinate with synchronized(this)?

**Answer:** They lock different monitor objects. All participants must synchronize on the same stable lock.

### Q5. Why must wait() be called while owning the object's monitor?

**Answer:** wait atomically releases that monitor and suspends; without ownership it throws IllegalMonitorStateException.

### Q6. Why can notify() appear to lose a signal?

**Answer:** Signals are not stored when no thread waits, and a woken thread must reacquire the monitor and recheck state. Use a guarded condition loop or durable primitive.

### Q7. Can a volatile flag safely publish an object graph?

**Answer:** If the object is fully constructed before the volatile write and not mutated unsafely afterward, the write/read establishes visibility of prior writes.

### Q8. Why can synchronizing on a String literal be unsafe?

**Answer:** Literals are interned and may be shared across unrelated code, allowing accidental lock contention or deadlock. Use a private lock object.

### Q9. Does Thread.sleep() release a monitor?

**Answer:** No. It pauses while holding any monitor already acquired, potentially blocking every thread waiting on that lock.

### Q10. Why should wait() use a while loop instead of if?

**Answer:** Spurious wakeups and condition changes before monitor reacquisition are possible. Recheck the predicate while holding the monitor.

## Executors and cancellation traps

### Q11. What can happen when a single-thread executor task submits another task to the same executor and calls get()?

**Answer:** The outer task occupies the only worker while waiting, so the inner task cannot run: a thread-starvation deadlock.

### Q12. Does shutdownNow() guarantee every task has stopped?

**Answer:** No. It requests interruption and returns queued tasks; running tasks must cooperate with interruption and may ignore it.

### Q13. Why can catching InterruptedException and continuing break shutdown?

**Answer:** The exception clears the interrupt flag, hiding cancellation from callers. Propagate it or restore the flag after cleanup.

### Q14. Can CompletableFuture.orTimeout stop the underlying HTTP operation?

**Answer:** Not necessarily; it can complete the future exceptionally while the I/O continues. Configure a client deadline and connect cancellation explicitly.

### Q15. Why can thenApply run on the thread that completes the prior future?

**Answer:** Non-async continuations may execute inline on the completing thread. Keep them short or choose an explicit executor for blocking work.

### Q16. Why is CallerRunsPolicy not always safe for backpressure?

**Answer:** It runs rejected work on the submitter, which may be an event-loop or latency-sensitive thread. Know the submission context.

### Q17. What bug can arise when two futures race to initialize a shared value?

**Answer:** Both computations can execute, even if only one result is eventually used. Use a single-flight mechanism when duplicate expensive work is unacceptable.

### Q18. Why can a fixed thread pool still consume unbounded memory?

**Answer:** Executors.newFixedThreadPool uses an unbounded work queue. Bound queue capacity and define rejection behavior.

### Q19. When does invokeAll return if one task fails?

**Answer:** It waits for all tasks to complete or time out; failures are stored in their Futures and appear on get().

### Q20. Why may CompletableFuture.exceptionally accidentally turn failure into success?

**Answer:** It replaces the exceptional result with a value. Ensure fallback semantics are valid and preserve failure context when not recovered.

## Locks, concurrent utilities, and virtual threads

### Q21. Can a fair ReentrantLock guarantee no starvation?

**Answer:** It improves queue-order fairness but reduces throughput and is not a universal scheduler guarantee. Fairness applies to lock acquisition policy, not the whole system.

### Q22. Why can readLock.lock() followed by writeLock.lock() deadlock with ReentrantReadWriteLock?

**Answer:** Read-to-write upgrade is not supported atomically; another reader can prevent the write lock. Release read lock and recheck under write lock.

### Q23. What does a Semaphore with one permit guarantee that a mutex does not?

**Answer:** It limits concurrent permit holders but does not enforce thread ownership like a lock. Any thread may release a permit, so maintain correct accounting.

### Q24. Why can CountDownLatch not be reset for a second phase?

**Answer:** It is one-shot; after count reaches zero it remains open. Use CyclicBarrier or Phaser for reusable coordination.

### Q25. Why can ConcurrentHashMap make a compound check-then-act operation unsafe?

**Answer:** Individual map methods are thread-safe, but separate get and put calls are not atomic together. Use compute or external synchronization.

### Q26. Can a virtual thread safely block on a semaphore?

**Answer:** Yes, parking is generally cheap, but the semaphore still bounds underlying resource use. Virtual threads do not make database or remote capacity infinite.

### Q27. What is the difference between Thread.interrupted() and isInterrupted()?

**Answer:** Thread.interrupted() checks and clears the current thread's flag; isInterrupted() checks a thread's flag without clearing it.

### Q28. Why can many virtual threads still exhaust a database?

**Answer:** Each can wait on the same finite connection pool, creating a huge waiting population. Apply admission control and deadlines before acquiring scarce resources.

### Q29. Does ThreadLocal behave like request scope with virtual threads automatically?

**Answer:** Each virtual thread has its own values, but they consume memory for the task lifetime. Prefer scoped context mechanisms where available.

### Q30. Why can a lock-free algorithm use more CPU than a lock under heavy contention?

**Answer:** Threads may repeatedly fail CAS and retry, wasting cycles. Measure contention and consider sharding or a blocking lock.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Java Language Specification: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
