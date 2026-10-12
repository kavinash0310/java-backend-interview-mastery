# JVM and Memory Management: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q02. What are the JDK, JVM, and Java runtime?

**Answer:** The JDK contains tools for developing Java programs, including a compiler. The JVM runs bytecode; a runtime installation supplies what is needed to run applications.

**Example:** Compile with javac; run with java.

### Q03. What is bytecode?

**Answer:** Bytecode is the platform-neutral instruction format produced by the Java compiler. A JVM loads and executes it on a particular operating system and processor.

### Q04. What does a class loader do?

**Answer:** A class loader finds class definitions and makes them available to the JVM. Applications can also define specialized class loaders.

### Q05. What is the Java heap?

**Answer:** The heap is the runtime area where class instances and arrays are generally allocated. Garbage collection reclaims objects that are no longer reachable.

### Q06. What is a thread stack?

**Answer:** Each thread has its own stack of method frames, including local variables and call state. Unbounded recursion can exhaust a thread's stack.

### Q07. Heap vs stack in simple terms?

**Answer:** The heap holds shared objects; each thread's stack holds active calls and local execution state. This is a useful model, though JVM optimizations can change physical allocation.

### Q08. What is Metaspace?

**Answer:** Metaspace stores class metadata in native memory in modern HotSpot JVMs. Too many loaded classes or retained class loaders can make it grow.

### Q09. When is an object eligible for garbage collection?

**Answer:** When it cannot be reached from any live root, such as active thread state or static references. Eligibility does not mean collection happens immediately.

### Q10. Does garbage collection prevent memory leaks?

**Answer:** No. A leak can occur when code keeps references to objects it no longer needs, so the collector still sees them as reachable.

### Q11. What does a garbage collector do?

**Answer:** It reclaims memory occupied by unreachable objects. It reduces manual memory management but still consumes CPU and can affect latency.

### Q12. What is a stop-the-world pause?

**Answer:** It is a pause when application threads stop so the JVM can perform some runtime work. Check GC logs and latency data for the actual impact.

### Q13. Throughput vs pause time?

**Answer:** Throughput measures the share of time doing application work; pause time measures time when application threads stop. Tuning may trade one goal against the other.

### Q14. How do you recognize high garbage-collection pressure?

**Answer:** Look at pause duration and frequency, allocation rate, heap use after collection, and request latency together. A high heap percentage alone is not proof.

### Q15. What is a heap dump?

**Answer:** A heap dump is a snapshot of objects and references in the Java heap. It can reveal what consumes memory and what retains it.

### Q16. What is a thread dump?

**Answer:** It records thread names, states, and stack traces at one moment. Comparing dumps can show blocked threads, deadlocks, or repeated hot stacks.

### Q17. What is a class-loading leak?

**Answer:** A class loader stays reachable after an application or plugin should be unloaded, retaining its classes and metadata. Static references and thread locals can keep it alive.

### Q18. What does -Xms control?

**Answer:** In HotSpot, -Xms sets the initial and minimum Java heap size. Choose it with the host's memory budget in mind.

### Q19. What does -Xmx control?

**Answer:** In HotSpot, -Xmx sets the maximum Java heap size. It does not cap total process memory because native memory and thread stacks also consume memory.

### Q20. What do GC logs tell you?

**Answer:** They record collection events and heap information. Use them to see collection frequency, pause time, and memory remaining after collections.

### Q21. How can jcmd inspect a running JVM?

**Answer:** jcmd sends diagnostic commands to a Java process, such as requesting VM information or a thread dump. Use commands supported by the deployed Java version.

### Q22. What is a native-memory problem?

**Answer:** It is memory use outside the Java heap, such as direct buffers, native libraries, or thread stacks. A process can run out of memory while heap looks healthy.

### Q23. Why can too many threads cause memory exhaustion?

**Answer:** Each thread needs stack and runtime resources. Excessive thread counts can exhaust native memory or OS limits before the heap is full.

### Q24. What is a memory leak in a cache?

**Answer:** It is an unbounded cache retaining more entries than the application can afford. Add a size or time policy and monitor memory and evictions.

### Q25. What is allocation rate?

**Answer:** It is how quickly the application creates objects. A high rate is not automatically a leak, but can trigger more collection and latency.

### Q26. What is a safepoint?

**Answer:** A JVM state where threads have reached a point at which certain runtime operations can safely proceed. Slow safepoint arrival can affect pauses.

### Q27. What is JIT compilation?

**Answer:** The just-in-time compiler turns frequently executed bytecode into optimized machine code while the program runs. Warm-up can affect early-request performance.

### Q28. Why can an application be slower just after startup?

**Answer:** It may be warming up, loading classes, building caches, or initializing dependencies. Compare startup and steady-state profiles before drawing conclusions.

### Q29. What is a direct byte buffer?

**Answer:** It stores data outside the ordinary Java heap and can help some I/O paths. It still consumes process memory and should be monitored.

### Q30. What should you do before increasing the heap?

**Answer:** Identify which memory area is exhausted, inspect dumps and trends, and check host capacity. Increase limits only after understanding the growth.

## References

- [Java Garbage Collection Tuning Guide](https://docs.oracle.com/en/java/javase/21/gctuning/)
- [jcmd command reference](https://docs.oracle.com/en/java/javase/21/docs/specs/man/jcmd.html)
- [Java command reference](https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html)
