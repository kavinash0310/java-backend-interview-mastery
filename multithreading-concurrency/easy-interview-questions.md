# Multithreading and Concurrency: Easy Q&A

Short answers for quick revision. Read the prompt, answer it aloud in your own words, then check the answer and example where included.

### Q1. What is a race condition?

**Answer:** A race condition occurs when the result depends on the timing of concurrent operations on shared state. Protect the state or redesign the operation to be atomic.

### Q2. What do atomicity and visibility mean?

**Answer:** Atomicity means an operation is indivisible from other threads' point of view. Visibility means one thread can observe another thread's writes.

### Q3. What is AtomicInteger for?

**Answer:** It provides atomic operations such as incrementAndGet for an integer shared by threads. It does not make several related fields one transaction.

### Q4. wait() vs sleep()?

**Answer:** wait() releases the monitor and waits; sleep() pauses without releasing locks. Call wait() inside a loop that rechecks the condition.

### Q5. What does thread interruption mean?

**Answer:** It is a cooperative cancellation signal. Code should stop or propagate InterruptedException, and restore the interrupt flag if it cannot propagate.

### Q6. Future vs CompletableFuture?

**Answer:** Future represents a result that may arrive later and can be waited on. CompletableFuture also lets you compose callbacks and dependent asynchronous steps.

### Q7. What executor types are common?

**Answer:** Common choices include fixed pools for bounded parallel work, scheduled pools for delayed tasks, and virtual-thread-per-task executors for many blocking tasks.

### Q8. How do you size a thread pool?

**Answer:** Consider whether work is CPU-bound or waits on I/O, the downstream limits, and the queue size. Measure under realistic load; an unbounded queue can hide overload.

### Q9. Why is immutability helpful with threads?

**Answer:** An object that cannot change after construction can be safely shared without coordinating later writes to its fields. Its construction must still be safely published.

### Q10. What is ThreadLocal?

**Answer:** It gives each thread its own value, which can hold per-thread context. In a thread pool, remove the value when the task ends to prevent data leaking to the next task.

### Q11. What is a Semaphore?

**Answer:** A Semaphore limits how many threads can access a resource at once. It is useful for protecting a small number of database or network permits.

### Q12. CountDownLatch vs CyclicBarrier?

**Answer:** A CountDownLatch waits until a count reaches zero and cannot be reset. A CyclicBarrier lets a fixed set of threads meet repeatedly at a shared point.

### Q13. What is starvation?

**Answer:** Starvation is when a thread waits indefinitely or for a very long time because other work continually gets the needed CPU time or lock.

### Q14. What is livelock?

**Answer:** Threads are active and reacting to one another but never make useful progress. Backoff or a single clear ownership rule can break the cycle.

### Q15. What does happens-before guarantee?

**Answer:** It is a Java Memory Model ordering guarantee that makes earlier writes visible to a later action. Lock release/acquire and volatile write/read are common examples.

### Q16. Why can parallelStream be a poor choice in a server request?

**Answer:** It may use shared worker resources and add overhead, especially for small or blocking tasks. Measure first and use an explicit executor when isolation is needed.

### Q17. What is ForkJoinPool?

**Answer:** It executes tasks that can be split into smaller subtasks and combined. Worker threads can steal tasks from another worker's queue.

### Q18. How can ThreadLocal cause a leak?

**Answer:** A pooled thread may outlive a request while retaining its ThreadLocal value. Remove request-specific values in a finally block.

### Q19. What is CompletableFuture's common pool?

**Answer:** Many async methods without an explicit executor use ForkJoinPool.commonPool(). Blocking tasks there can interfere with unrelated work.

### Q20. How do you handle exceptions in CompletableFuture?

**Answer:** Use a stage such as exceptionally or handle to observe and recover from failure. Ensure the final future is observed instead of silently dropping an error.

### Q21. What is a thread?

**Answer:** A path of execution inside a process. Threads share process memory but have their own call stacks.

**Example:** A server can use multiple threads to handle work.

### Q22. Process vs thread?

**Answer:** A process has its own address space; threads within a process share that address space and resources.

**Example:** Two app processes are isolated more than two threads in one app.

### Q23. What does synchronized do?

**Answer:** It provides mutual exclusion on a monitor and establishes visibility when threads use the same monitor.

**Example:** Synchronize access to a shared counter.

### Q24. What does volatile guarantee?

**Answer:** Reads and writes of that variable are visible across threads, but compound operations are not automatically atomic.

**Example:** `count++` is still unsafe with only volatile.

### Q25. synchronized vs volatile?

**Answer:** Use volatile for simple visibility of a value; use a lock or atomic operation when a multi-step update must be indivisible.

**Example:** A stop flag may be volatile; incrementing a shared count needs stronger coordination.

### Q26. What is a deadlock?

**Answer:** Two or more threads wait forever for locks held by one another.

**Example:** Thread A holds lock 1 and waits for lock 2 while B does the reverse.

### Q27. How can you reduce deadlock risk?

**Answer:** Acquire locks in one consistent order, keep lock scope short, and consider timed lock attempts.

**Example:** Always acquire account locks by ascending account ID.

### Q28. What is a critical section?

**Answer:** Code that accesses shared state and must be protected from conflicting concurrent operations.

**Example:** The check-and-update of an inventory count.

### Q29. What is a thread-safe class?

**Answer:** A class whose behavior stays correct when multiple threads call it at the same time, under its stated contract.

**Example:** ConcurrentHashMap supports concurrent map operations.

### Q30. Why is HashMap unsafe for concurrent writes?

**Answer:** It does not coordinate concurrent mutation, so updates can be lost or observations can be inconsistent.

**Example:** Use ConcurrentHashMap for independent concurrent map operations.

### Q31. What is ReentrantLock?

**Answer:** An explicit mutual-exclusion lock that supports features such as timed acquisition and multiple conditions.

**Example:** Unlock it in a `finally` block after a successful `lock()`.

### Q32. What is a Condition?

**Answer:** A wait-and-signal mechanism associated with a lock, used when threads wait for a particular state.

**Example:** A producer can signal a consumer after adding an item.

### Q33. What is a BlockingQueue?

**Answer:** A thread-safe queue whose put/take operations can wait when full or empty.

**Example:** Use it to hand jobs from producers to worker threads.

### Q34. What is a bounded thread pool?

**Answer:** A pool with a fixed or capped number of worker threads and a defined queue/rejection policy.

**Example:** A bounded queue prevents unlimited tasks from consuming memory.

### Q35. What is backpressure?

**Answer:** A way to slow or reject incoming work when downstream processing cannot keep up.

**Example:** Return a busy response instead of adding tasks to an unbounded queue.

### Q36. What is a daemon thread?

**Answer:** A background thread that does not keep the JVM alive after all user threads finish.

**Example:** Do not use daemon threads for work that must be completed before exit.

### Q37. What does join() do?

**Answer:** It makes the current thread wait until another thread finishes.

**Example:** `worker.join()` waits for that worker.

### Q38. What does yield() do?

**Answer:** It hints that the scheduler may let another thread run; it provides no correctness guarantee.

**Example:** Do not use yield() as a synchronization mechanism.

### Q39. What is a thread-safe counter?

**Answer:** A counter whose updates are coordinated, for example with AtomicInteger or a lock.

**Example:** `atomic.incrementAndGet()` performs an atomic increment.

### Q40. When should work be parallelized?

**Answer:** When tasks are independent and the expected speedup exceeds coordination, scheduling, and resource costs.

**Example:** Independent CPU-heavy image transforms may run in parallel.

## References

- [Java concurrency package](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Java Language Specification: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
