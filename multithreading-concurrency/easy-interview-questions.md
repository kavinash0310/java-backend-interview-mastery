# Multithreading and Concurrency: Easy Q&A

Short answers for quick revision. Read the prompt, answer it aloud in your own words, then check the example.

### Q11. What is a race condition?

**Answer:** A race condition occurs when the result depends on the timing of concurrent operations on shared state. Protect the state or redesign the operation to be atomic.

### Q12. What do atomicity and visibility mean?

**Answer:** Atomicity means an operation is indivisible from other threads' point of view. Visibility means one thread can observe another thread's writes.

### Q13. What is AtomicInteger for?

**Answer:** It provides atomic operations such as incrementAndGet for an integer shared by threads. It does not make several related fields one transaction.

### Q14. wait() vs sleep()?

**Answer:** wait() releases the monitor and waits; sleep() pauses without releasing locks. Call wait() inside a loop that rechecks the condition.

### Q15. What does thread interruption mean?

**Answer:** It is a cooperative cancellation signal. Code should stop or propagate InterruptedException, and restore the interrupt flag if it cannot propagate.

### Q16. Future vs CompletableFuture?

**Answer:** Future represents a result that may arrive later and can be waited on. CompletableFuture also lets you compose callbacks and dependent asynchronous steps.

### Q17. What executor types are common?

**Answer:** Common choices include fixed pools for bounded parallel work, scheduled pools for delayed tasks, and virtual-thread-per-task executors for many blocking tasks.

### Q18. How do you size a thread pool?

**Answer:** Consider whether work is CPU-bound or waits on I/O, the downstream limits, and the queue size. Measure under realistic load; an unbounded queue can hide overload.

### Q19. Why is immutability helpful with threads?

**Answer:** An object that cannot change after construction can be safely shared without coordinating later writes to its fields. Its construction must still be safely published.

### Q20. What is ThreadLocal?

**Answer:** It gives each thread its own value, which can hold per-thread context. In a thread pool, remove the value when the task ends to prevent data leaking to the next task.

### Q21. What is a Semaphore?

**Answer:** A Semaphore limits how many threads can access a resource at once. It is useful for protecting a small number of database or network permits.

### Q22. CountDownLatch vs CyclicBarrier?

**Answer:** A CountDownLatch waits until a count reaches zero and cannot be reset. A CyclicBarrier lets a fixed set of threads meet repeatedly at a shared point.

### Q23. What is starvation?

**Answer:** Starvation is when a thread waits indefinitely or for a very long time because other work continually gets the needed CPU time or lock.

### Q24. What is livelock?

**Answer:** Threads are active and reacting to one another but never make useful progress. Backoff or a single clear ownership rule can break the cycle.

### Q25. What does happens-before guarantee?

**Answer:** It is a Java Memory Model ordering guarantee that makes earlier writes visible to a later action. Lock release/acquire and volatile write/read are common examples.

### Q26. Why can parallelStream be a poor choice in a server request?

**Answer:** It may use shared worker resources and add overhead, especially for small or blocking tasks. Measure first and use an explicit executor when isolation is needed.

### Q27. What is ForkJoinPool?

**Answer:** It executes tasks that can be split into smaller subtasks and combined. Worker threads can steal tasks from another worker's queue.

### Q28. How can ThreadLocal cause a leak?

**Answer:** A pooled thread may outlive a request while retaining its ThreadLocal value. Remove request-specific values in a finally block.

### Q29. What is CompletableFuture's common pool?

**Answer:** Many async methods without an explicit executor use ForkJoinPool.commonPool(). Blocking tasks there can interfere with unrelated work.

### Q30. How do you handle exceptions in CompletableFuture?

**Answer:** Use a stage such as exceptionally or handle to observe and recover from failure. Ensure the final future is observed instead of silently dropping an error.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Java Language Specification: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
