# Module 03 — JVM and Memory Management

> **Module guide:** Learn how Java code is loaded and executed, how memory is managed, and how to diagnose runtime and garbage-collection issues.

## Learning outcomes

- Explain where objects and thread-local execution state live at a high level.
- Relate garbage collection to reachability and allocation patterns.
- Use diagnostic evidence before changing JVM settings.

## Subtopic links

- [Runtime foundations](#runtime-foundations)
- [Memory areas and object lifecycle](#memory-areas-and-object-lifecycle)
- [Garbage collection and performance](#garbage-collection-and-performance)
- [Troubleshooting](#troubleshooting)

## Topic coverage

### Runtime foundations

- JDK, JRE, and JVM
- Class loading and class loaders
- Bytecode and runtime execution

### Memory areas and object lifecycle

- Heap and stack
- Metaspace and other runtime data areas
- Object allocation and reachability

### Garbage collection and performance

- Garbage collection concepts and collectors
- Memory leaks in garbage-collected applications
- JVM flags and tuning fundamentals
- Runtime performance and profiling

### Troubleshooting

- Heap dumps and thread dumps
- OutOfMemoryError and high-GC symptoms
- CPU, memory, and latency investigation

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `jvm-memory-management/runtime-foundations/` — runtime foundations question pages
- `jvm-memory-management/memory-areas-and-object-lifecycle/` — memory areas and object lifecycle question pages
- `jvm-memory-management/garbage-collection-and-performance/` — garbage collection and performance question pages
- `jvm-memory-management/troubleshooting/` — troubleshooting question pages

Use descriptive lowercase kebab-case filenames, for example `jvm-memory-management/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [Beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [JVM and memory troubleshooting questions](interview-questions.md)

## Related modules

- [Module 01: Core Java](../core-java/README.md)
- [Module 02: Multithreading and Concurrency](../multithreading-concurrency/README.md)
- [Module 19: Logging, Monitoring and Observability](../logging-monitoring-observability/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
