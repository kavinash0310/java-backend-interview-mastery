# Multithreading and Concurrency: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Memory model and synchronization

### Q1. What exact guarantee does a volatile write provide to a later volatile read of the same field?

**Answer:** The write happens-before the read that observes it, publishing prior actions. It does not make a compound read-modify-write atomic.

### Q2. Why can double-checked locking fail when the instance field is not volatile?

**Answer:** Without volatile, publication and construction writes can be reordered, allowing another thread to observe a non-null reference before full initialization. Volatile prevents that reordering.

### Q3. When is a final field safely visible, and what weakens that guarantee?

**Answer:** Proper construction gives special initialization safety for final fields. Letting this escape from the constructor can expose a partially initialized object.

### Q4. Why should a Condition await call be inside a loop?

**Answer:** Signals can be spurious, another thread may consume the condition first, or the state may change before reacquisition. Recheck the predicate while holding the lock.

### Q5. How do lock-order rules prevent deadlock, and what limitation remains?

**Answer:** Acquire locks in one global order to remove circular wait. The rule helps only if every code path follows it, including callbacks and third-party code.

### Q6. What is a data race in Java, and why can it invalidate intuitive reasoning?

**Answer:** Conflicting accesses without a happens-before relation create a data race. The JMM permits reorderings and stale reads beyond what sequential reasoning predicts.

### Q7. When does synchronizing on a public object create a library hazard?

**Answer:** External code can acquire the same monitor and block or deadlock library operations. Use a private lock object or dedicated lock field.

### Q8. Why can ThreadLocal values leak in a pooled worker even after a request ends?

**Answer:** Pool threads outlive requests, so values remain attached unless removed. Clear request-scoped values in finally blocks or use lifecycle-aware context propagation.

### Q9. What is safe publication, and name two ways to achieve it.

**Answer:** Publish an object through a happens-before edge, such as a volatile reference, synchronized block, static initialization, or a concurrent collection.

### Q10. Why can a lock-free algorithm still starve a thread?

**Answer:** Lock-free guarantees system-wide progress, not fairness for each participant. A thread may repeatedly lose compare-and-set races under contention.

## Atomics and concurrent structures

### Q11. How do memory reclamation techniques affect an ABA-prone lock-free stack?

**Answer:** A version tag can detect a changed pointer, but it does not make reclaimed nodes safe to dereference. Use a reclamation scheme such as hazard pointers or epochs, and prove reuse cannot race with readers. A location changes A→B→A, so CAS sees A and assumes nothing changed. Version-stamped references or tagged counters detect the intervening update.

### Q12. Why can LongAdder outperform AtomicLong under contention but be unsuitable as a sequence generator?

**Answer:** LongAdder spreads updates across cells and merges them for reads; a read is not an atomic unique sequence assignment. Use AtomicLong when every increment needs one total order.

### Q13. How do you make a multi-key invariant atomic when using ConcurrentHashMap?

**Answer:** ConcurrentHashMap protects map operations, not an invariant across multiple keys. Use a lock/transaction around the invariant or redesign it into one atomic value.

### Q14. Why can computeIfAbsent recursively updating the same ConcurrentHashMap fail?

**Answer:** Recursive modification of the same map from its mapping function can cause an IllegalStateException or deadlock-like contention. Keep mapping functions pure and non-recursive.

### Q15. What consistency does CopyOnWriteArrayList provide to an iterator?

**Answer:** The iterator sees the array snapshot captured when it was created. It will not see later writes, which is useful for read-heavy listener lists.

### Q16. When should you choose StampedLock optimistic reads?

**Answer:** For a read-mostly structure where reads can validate and retry, and code can tolerate a non-locking snapshot attempt. It is not reentrant and needs careful validation.

### Q17. What should a bounded queue do when producers outpace consumers?

**Answer:** Apply backpressure, reject, shed low-value work, or persist it; an unbounded queue converts overload into latency and memory exhaustion.

### Q18. Why is ConcurrentLinkedQueue.size() a poor control signal?

**Answer:** It may require traversal and can change immediately under concurrent updates. Use explicit counters or queue-specific metrics with documented accuracy.

### Q19. What limitation does Phaser address better than a fixed CyclicBarrier?

**Answer:** Phaser supports dynamic party registration and multiple phases. Choose it when task participation changes; otherwise a simpler barrier is easier to reason about.

### Q20. How can a semaphore cap outbound calls without limiting the number of request threads?

**Answer:** Acquire a permit before the remote call and release it in finally. Pair it with a bounded executor or async client so waiting callers do not exhaust threads.

## Executors, async work, and virtual threads

### Q21. How do you size a thread pool when tasks mix CPU work and blocking I/O?

**Answer:** Separate pools or use a measured model: CPU work near available cores, I/O concurrency bounded by downstream capacity. Load-test instead of assuming one universal ratio.

### Q22. Why can an unbounded executor queue hide overload until the service fails?

**Answer:** Tasks accumulate while workers stay saturated, increasing queue delay and memory. Bound the queue and define rejection or caller backpressure behavior.

### Q23. What is the cancellation contract of Future.cancel(true)?

**Answer:** It requests interruption; the task must cooperate and may ignore it. Release resources in finally and avoid treating cancellation as proof work stopped.

### Q24. Why can CompletableFuture.supplyAsync block unrelated tasks?

**Answer:** Without an executor it often uses the common ForkJoinPool; blocking calls can occupy shared workers. Supply a dedicated executor for blocking workloads.

### Q25. How should a timeout on a composed CompletableFuture propagate to the actual HTTP request?

**Answer:** A future timeout alone may only complete the wrapper exceptionally. Configure client-level deadlines and connect cancellation to the underlying request.

### Q26. What is structured concurrency trying to improve compared with detached futures?

**Answer:** It scopes child tasks to a parent operation so cancellation, failure, and lifetime are handled together. In Java 21 it is preview; verify the target JDK contract.

### Q27. How do virtual threads change thread-per-request economics, and what remains bounded?

**Answer:** They make many blocking tasks cheaper to schedule, but CPU, sockets, DB connections, and remote quotas remain finite. Bound scarce resources with semaphores or pools.

### Q28. What does virtual-thread pinning mean, and how do you investigate it?

**Answer:** A virtual thread can keep its carrier occupied during some blocking operations, notably synchronized regions in relevant JDK versions. Use JFR pinning events and keep critical sections short.

### Q29. Why should a virtual-thread service avoid pooling virtual threads?

**Answer:** Virtual threads are intended to be cheap per task; pooling adds lifecycle complexity without conserving them. Pool scarce downstream resources, not the virtual threads.

### Q30. How would you prevent retry amplification across several asynchronous service layers?

**Answer:** Set one end-to-end deadline, budget retries at one layer, use jittered backoff, and propagate cancellation. Independent retries multiply load during outages.

## Progress, cancellation, and diagnostics

### Q31. How do you prove a thread pool cannot grow its queue without bound?

**Answer:** Use a bounded queue, explicit rejection policy, and metrics for active workers and queue age. Load-test overload behavior instead of checking only average throughput.

### Q32. What is the difference between a deadlock, livelock, and starvation?

**Answer:** Deadlock has a cycle of permanent waiting; livelock keeps reacting without progress; starvation denies one task resources. Thread dumps and progress metrics distinguish them.

### Q33. How can you build a deadlock graph from a thread dump?

**Answer:** Map each waiting thread to its requested monitor and each owner to held monitors; a cycle in that wait-for graph indicates deadlock.

### Q34. Why must interrupt status often be restored after catching InterruptedException?

**Answer:** The exception clears the interrupt flag. If the method cannot propagate it, restore with Thread.currentThread().interrupt() so higher layers can observe cancellation.

### Q35. How can cancellation leak a semaphore permit?

**Answer:** If acquisition and release are not paired in try/finally, interruption or exceptions can skip release. Track ownership and release only after successful acquire.

### Q36. Why is Thread.stop() unsafe for terminating stuck work?

**Answer:** It can release locks while shared objects are inconsistent. Use cooperative interruption, deadlines, and process isolation for untrusted or uninterruptible tasks.

### Q37. How would you stress-test a concurrent data structure beyond a unit test?

**Answer:** Run many actors with controlled operations, record histories, and check them against a sequential specification. Add race detectors or stress harnesses where available.

### Q38. What does linearizability mean for a concurrent operation?

**Answer:** Each operation appears to take effect at one instant between invocation and response, while respecting real-time order. It is stronger than eventual consistency.

### Q39. Why can adding synchronized to a hot method reduce total throughput sharply?

**Answer:** It serializes callers and creates contention, even if the protected code is short. Measure wait time and consider sharding or immutable snapshots.

### Q40. How should a service cap work when virtual threads are cheap but a database pool is small?

**Answer:** Use a semaphore or bounded admission around DB access, with a deadline and overload response. Cheap threads do not increase database capacity.


## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Java Language Specification: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
