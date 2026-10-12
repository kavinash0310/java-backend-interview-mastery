# Module 02 — Multithreading and Concurrency

> **Module guide:** Understand how Java tasks run concurrently, how shared state is coordinated, and how to build reliable asynchronous work.

## Learning outcomes

- Distinguish atomicity, visibility, and ordering problems.
- Select synchronization or concurrent utilities for a concrete use case.
- Use bounded executors and handle task failures, timeouts, and shutdown.

## Subtopic links

- [Threads and tasks](#threads-and-tasks)
- [Shared state and coordination](#shared-state-and-coordination)
- [Executors and asynchronous work](#executors-and-asynchronous-work)
- [Concurrent utilities and practices](#concurrent-utilities-and-practices)

## Topic coverage

### Threads and tasks

- Thread lifecycle
- Runnable and Callable
- Task completion, cancellation, and interruption

### Shared state and coordination

- Race conditions and visibility
- synchronized and intrinsic locks
- volatile and atomic classes
- Deadlocks and lock ordering
- Explicit locks and conditions

### Executors and asynchronous work

- ExecutorService and thread pools
- Pool sizing and task queues
- CompletableFuture composition and error handling
- Timeouts and cancellation

### Concurrent utilities and practices

- Concurrent collections
- Semaphores, latches, and barriers
- Thread-safety and immutability
- Concurrency best practices and troubleshooting

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `multithreading-concurrency/threads-and-tasks/` — threads and tasks question pages
- `multithreading-concurrency/shared-state-and-coordination/` — shared state and coordination question pages
- `multithreading-concurrency/executors-and-asynchronous-work/` — executors and asynchronous work question pages
- `multithreading-concurrency/concurrent-utilities-and-practices/` — concurrent utilities and practices question pages

Use descriptive lowercase kebab-case filenames, for example `multithreading-concurrency/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [Multithreading and concurrency questions](interview-questions.md)

## Related modules

- [Module 01: Core Java](../core-java/README.md)
- [Module 03: JVM and Memory Management](../jvm-memory-management/README.md)
- [Module 15: Testing](../testing/README.md)
- [Module 21: Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
