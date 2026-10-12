# HashMap vs ConcurrentHashMap — what is the difference?

## Interview-ready answer

HashMap is a general-purpose map for code that is not concurrently modified without coordination. ConcurrentHashMap supports concurrent access and updates, with thread-safe individual operations and good read concurrency. Use it for a shared map; use HashMap for local or externally synchronized state.

## Simple explanation

ConcurrentHashMap coordinates updates without locking the whole map for every operation. Its computeIfAbsent, putIfAbsent, and related methods help make a single map operation atomic. That does not make a sequence of separate calls atomic, nor does it make mutable values thread-safe.

## Easy way to remember

ConcurrentHashMap protects the map’s operations; it does not automatically protect the objects stored inside it.

## Example

A shared in-memory registry can use ConcurrentHashMap. A request-local collection usually does not need it.

## Code example

```java
ConcurrentHashMap<String, LongAdder> counts = new ConcurrentHashMap<>();
counts.computeIfAbsent("checkout", k -> new LongAdder()).increment();
```

## Common mistakes

ConcurrentHashMap disallows null keys and values. Do not assume iteration is a frozen snapshot. If correctness spans multiple keys or steps, use a higher-level lock or an atomic data-store operation.

## Interview follow-ups

Why is get often lock-free? Is computeIfAbsent enough to protect a mutable value?

## References

- [Java 21 ConcurrentHashMap API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html)
- [Java 21 HashMap API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashMap.html)

## Quick revision

HashMap is not concurrent; ConcurrentHashMap protects per-map operations, not arbitrary multi-step logic or value objects.
