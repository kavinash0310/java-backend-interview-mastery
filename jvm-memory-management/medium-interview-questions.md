# JVM and Memory Management: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Garbage collection and allocation

### Q1. What role do regions play in G1 garbage collection?

**Answer:** G1 divides the heap into regions and selects collections based on reclaimable space and pause goals, rather than fixed contiguous generations.

**Example:** Read region and pause details in GC logs.

### Q2. What is a humongous allocation in G1?

**Answer:** An object larger than about half a region is treated specially and may consume contiguous regions, increasing fragmentation risk.

**Example:** Large byte arrays and buffers are common candidates to inspect.

### Q3. What is a thread-local allocation buffer?

**Answer:** A TLAB lets a thread allocate many small objects from its own area quickly, reducing shared allocation contention.

**Example:** High allocation rate can still create GC pressure even when allocation is fast.

### Q4. What happens when a young object survives several collections?

**Answer:** It may be copied into survivor space and eventually promoted to the old generation, depending on age and collector behavior.

**Example:** A workload retaining request objects longer can increase old-generation occupancy.

### Q5. What is a G1 mixed collection?

**Answer:** It collects young regions plus selected old regions to reclaim space while managing pause time.

**Example:** Repeated mixed collections with little reclaimed space can indicate retention or poor region selection.

### Q6. What can trigger a full GC unexpectedly?

**Answer:** Allocation pressure, evacuation failure, explicit GC requests, or insufficient space can cause a broader collection.

**Example:** Check the GC cause field rather than assuming the heap is simply too small.

### Q7. Why can high allocation rate hurt latency even when objects die quickly?

**Answer:** The collector must process the allocation churn, and frequent young collections can consume CPU and create pauses.

**Example:** Profile allocation sites with JFR.

### Q8. What does a G1 pause-time target control?

**Answer:** It guides how much work G1 attempts in a pause; it is a goal, not a strict maximum guarantee.

**Example:** A very aggressive target can reduce throughput.

### Q9. What is a concurrent marking cycle used for?

**Answer:** It identifies old-generation objects that remain reachable while application threads continue running, helping plan reclamation.

**Example:** Inspect concurrent cycle and mixed-GC behavior together.

### Q10. How do G1 and ZGC differ at a high level?

**Answer:** Both target low pause times, but use different collection mechanics and trade-offs; compare them with the actual JDK and workload.

**Example:** Run representative load tests before changing collectors.

## Native memory and runtime areas

### Q11. Why can a container be killed while the Java heap looks healthy?

**Answer:** The container limit includes native memory, stacks, direct buffers, metaspace, code cache, and other processes, not just heap.

**Example:** Compare process RSS and container memory with `-Xmx`.

### Q12. What does Native Memory Tracking report?

**Answer:** When enabled, it breaks down JVM native memory categories such as threads, class metadata, code, and internal allocations.

**Example:** Start with summary data from `jcmd VM.native_memory`.

### Q13. How can direct ByteBuffers exhaust memory outside the heap?

**Answer:** Their payload is allocated in native memory, so a small heap dump may not reveal the dominant usage.

**Example:** Monitor direct-buffer pool metrics and native memory.

### Q14. What can cause OutOfMemoryError: unable to create native thread?

**Answer:** The OS or container may lack memory, process/thread limits, or resources for another native thread stack.

**Example:** Check thread count, cgroup limits, and JVM thread stack settings.

### Q15. Why can Metaspace grow after repeated application redeploys?

**Answer:** Class metadata stays while its defining class loader remains reachable; retained loaders prevent unloading.

**Example:** Inspect class-loader trees and references to old application loaders.

### Q16. What is the JIT code cache?

**Answer:** Native memory where the JVM stores compiled machine code for methods.

**Example:** A full code cache can affect compilation and performance.

### Q17. How can a JNI library leak memory?

**Answer:** Native allocations are not automatically reclaimed by Java GC unless code explicitly releases them or ties them to a managed lifecycle.

**Example:** Audit native allocation/free paths and measure process memory.

### Q18. What is memory-mapped file usage?

**Answer:** The OS maps file pages into a process address space, which can affect RSS and virtual memory without appearing in the Java heap.

**Example:** Inspect mapped buffers and OS process metrics.

### Q19. What does `-Xss` control, and what is its trade-off?

**Answer:** It sets per-thread stack size; smaller stacks may permit more threads but increase stack-overflow risk.

**Example:** Tune only after measuring thread count and stack depth.

### Q20. Why enable Native Memory Tracking before an incident?

**Answer:** It adds overhead and is generally most useful when enabled at startup, so it may not be available retroactively.

**Example:** Use a tested production setting if native-memory diagnosis matters.

## Failure diagnosis and evidence

### Q21. How should you investigate OutOfMemoryError: Java heap space?

**Answer:** Capture the failure context, heap dump if safe, GC logs, and allocation or retained-object evidence; identify the largest retaining path.

**Example:** Compare object counts with a healthy baseline.

### Q22. What often causes OutOfMemoryError: Metaspace?

**Answer:** Too many classes or class loaders remain live, or the configured metadata limit is too small for the workload.

**Example:** Check loaded-class trends and class-loader retention before raising the cap.

### Q23. How is StackOverflowError different from heap exhaustion?

**Answer:** It usually means one thread's call stack grew too deep, often due to recursion; heap exhaustion means object allocation could not be satisfied.

**Example:** Inspect the repeating stack frames for recursion.

### Q24. What does GC overhead limit exceeded usually indicate?

**Answer:** The JVM spends most of its time collecting while reclaiming very little heap, often because live data is near capacity.

**Example:** Find retained objects and growth trends before increasing heap.

### Q25. What can `jcmd GC.class_histogram` tell you?

**Answer:** It summarizes live or heap object counts by class, helping identify unexpectedly common object types.

**Example:** Use it to decide which objects deserve a heap-dump investigation.

### Q26. How can a thread dump reveal a deadlock?

**Answer:** It may show threads waiting on monitors held by one another and can include a detected deadlock report.

**Example:** Look at lock ownership and the full stack of each blocked thread.

### Q27. When is JFR allocation profiling useful?

**Answer:** It identifies code paths creating many objects, helping explain high allocation rate or GC frequency.

**Example:** Record a bounded profile during representative traffic.

### Q28. What evidence helps diagnose latency caused by GC?

**Answer:** Correlate request latency with pause logs, allocation rate, GC CPU, heap occupancy, and concurrent-cycle activity.

**Example:** Use the same time window across metrics and logs.

### Q29. Why should GC logs include timestamps and uptime?

**Answer:** They let you align collection behavior with deployments, request traffic, and other process events.

**Example:** Use a consistent time zone across telemetry.

### Q30. What should you record before changing JVM flags?

**Answer:** Current flags, JDK vendor/version, container limits, workload, GC logs, latency, and the desired outcome.

**Example:** Change one tuning variable at a time.

## JIT, startup, and tuning

### Q31. Why can an application be slower during its first requests?

**Answer:** Classes load, caches warm, and frequently used methods may still be interpreted or compiled at lower tiers.

**Example:** Compare cold-start and steady-state measurements.

### Q32. What is JIT deoptimization?

**Answer:** The JVM can discard compiled assumptions and return code to interpretation when runtime behavior invalidates those assumptions.

**Example:** Unexpected type patterns may trigger deoptimization.

### Q33. What is tiered compilation?

**Answer:** The JVM gathers profiling data and compiles hot code through levels to balance startup and optimization.

**Example:** Warm-up behavior depends on which code becomes hot.

### Q34. Why profile CPU before increasing heap?

**Answer:** CPU saturation can come from expensive methods, lock contention, serialization, or retries rather than memory pressure.

**Example:** Use JFR or a CPU profile to identify hot stacks.

### Q35. How can class loading affect startup time?

**Answer:** Scanning and loading many classes can add work before the app is ready to serve traffic.

**Example:** Review startup traces and trim unnecessary dependencies or scans.

### Q36. Why is changing several JVM flags at once hard to evaluate?

**Answer:** It obscures which change affected behavior and can introduce interacting trade-offs.

**Example:** Keep a baseline and change one factor per comparison.

### Q37. What is string deduplication intended to reduce?

**Answer:** It lets the collector share backing storage among equal strings, reducing some heap usage at CPU cost.

**Example:** Measure duplicate string retention before enabling it.

### Q38. What does a safepoint mean to application threads?

**Answer:** Threads reach a state where the JVM can safely perform certain global operations.

**Example:** Long time-to-safepoint can matter even when GC pauses look short.

### Q39. How does container awareness affect JVM sizing?

**Answer:** Modern JVMs can use container resource limits when sizing some defaults, but actual heap and native use still need validation.

**Example:** Compare detected CPU and memory limits with deployment settings.

### Q40. What is the safest way to evaluate a collector change?

**Answer:** Use the same JDK, representative traffic, realistic heap/container limits, and compare latency, throughput, CPU, and memory.

**Example:** Include warm-up and failure behavior in the test window.

## References

- [Java Garbage Collection Tuning Guide](https://docs.oracle.com/en/java/javase/21/gctuning/)
- [jcmd command reference](https://docs.oracle.com/en/java/javase/21/docs/specs/man/jcmd.html)
- [Java command reference](https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html)
