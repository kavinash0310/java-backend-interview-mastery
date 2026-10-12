# Core Java: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Generics and type safety

### Q1. How can generic varargs create heap pollution, and how do you make a varargs API safe?

**Answer:** An Object[] backs the varargs parameter, so storing an incompatible generic value can violate callers' assumptions. Avoid writes, use @SafeVarargs only when the body is demonstrably safe, and test aliasing.

### Q2. Why can a generic method accept a wildcard list but still fail to add an item?

**Answer:** ? extends T captures an unknown subtype: reads are safe as T, but a T might not fit the actual list. Use a separate destination or a super bound.

### Q3. What is wildcard capture, and when does a helper method solve a compiler error?

**Answer:** The compiler assigns a hidden type to a wildcard capture. A generic helper can name that same captured type so two operations remain type-consistent.

### Q4. Why does Java generate bridge methods, and how can they appear in reflection?

**Answer:** Erasure changes a generic override's JVM descriptor, so the compiler emits a bridge preserving dispatch. Reflection may show synthetic bridge methods; filter isBridge when analyzing user declarations.

### Q5. When can two overloads become ambiguous because of generic inference?

**Answer:** When overloads accept unrelated functional interfaces or inference has multiple valid targets, the compiler cannot choose a most-specific method. Give an explicit type witness or cast at the call site.

### Q6. Why is an unchecked cast not made safe by checking only the raw container type?

**Answer:** Runtime erasure can verify List but cannot verify its element parameter. Copy and validate each element at the boundary instead of trusting a cast.

### Q7. How would you design a type token for List<String> despite erasure?

**Answer:** Capture the generic superclass type in an anonymous subclass, then inspect its ParameterizedType. This preserves metadata for frameworks but does not reify the list's runtime elements.

### Q8. What does a collector's CONCURRENT characteristic promise, and what does it not promise?

**Answer:** It permits concurrent accumulation when the stream and collector conditions allow it; it does not make arbitrary accumulator state thread-safe or guarantee parallel execution.

### Q9. Why can a parallel reduction return a wrong answer if its combiner is inconsistent?

**Answer:** Parallel execution partitions and recombines partial results, so the combiner must be associative and compatible with the accumulator. Subtraction is not a valid sum combiner.

### Q10. How do sealed hierarchies improve exhaustive handling, and what is the compatibility cost?

**Answer:** They constrain permitted implementations so switches can cover a closed set. Adding a permitted subtype can require clients to revisit exhaustive logic and recompile.

## Collections and object contracts

### Q11. How does HashMap split a bucket during resize without rehashing every key?

**Answer:** For power-of-two capacity growth, a node stays at its index or moves by the old capacity depending on one hash bit. This reduces redistribution work.

### Q12. When do HashMap collision bins become trees, and what should an interviewer avoid claiming?

**Answer:** OpenJDK may treeify a sufficiently large bin after capacity thresholds are met. This is an implementation detail, not a portable worst-case guarantee for every Map.

### Q13. Why can a comparator that is inconsistent with equals break sorted collections?

**Answer:** TreeSet and TreeMap use compareTo or Comparator equality to identify duplicate keys. If that differs from equals, elements may be lost or violate Set expectations.

### Q14. How should equals and hashCode behave for a mutable entity whose database ID is assigned later?

**Answer:** Choose a stable identity policy and keep hash behavior stable while the entity is in hash collections. A mutable generated ID can make lookup fail after insertion.

### Q15. Why is array covariance less type-safe than generic invariance?

**Answer:** String[] is assignable to Object[], so a runtime store check is needed and may throw ArrayStoreException. List<String> cannot be assigned to List<Object> at compile time.

### Q16. When can a WeakHashMap entry unexpectedly remain reachable?

**Answer:** A strong path from the value back to its key keeps the key alive. Weak keys only help when the rest of the object graph does not retain them.

### Q17. What is the practical difference between copy-on-write and fail-fast iteration?

**Answer:** CopyOnWriteArrayList iterators traverse a stable snapshot at allocation cost; fail-fast iterators may detect structural changes but provide no concurrency safety.

### Q18. How do you preserve a Map's iteration order when returning an immutable snapshot?

**Answer:** Copy into an insertion-ordered map, then wrap or expose an immutable copy with documented order. Map.copyOf does not promise preserving the source's iteration order.

### Q19. Why is ConcurrentHashMap.computeIfAbsent mapping code expected to be short and side-effect limited?

**Answer:** Competing updates may coordinate around the mapping operation; recursive updates or slow external work can block progress or trigger failures. Compute a value cheaply and publish it once.

### Q20. What bug appears when equals uses a superclass check that is asymmetric with a subclass?

**Answer:** A base instance may consider a subclass equal while the subclass rejects the base object, violating symmetry. Prefer final value types or use a consistent canEqual design.

## Runtime and modern APIs

### Q21. Why must a parallel stream reduction use an associative operation?

**Answer:** Partitioning changes grouping, and associativity ensures regrouping keeps the same result. Floating-point addition can differ slightly because rounding is not mathematically associative.

### Q22. When should you replace flatMap with mapMulti?

**Answer:** Use mapMulti when one input emits a small number of outputs and avoiding temporary streams matters. Benchmark first; flatMap is clearer for general nested streams.

### Q23. What is the difference between a stream's encounter order and its execution order?

**Answer:** Encounter order defines semantic ordering when the source has one; parallel workers may execute elements out of order. Use forEachOrdered only when ordered side effects are required.

### Q24. Why can Stream.toList() surprise code that mutates the result?

**Answer:** It returns an unmodifiable list; mutation throws UnsupportedOperationException. Choose an explicitly mutable collection when later updates are part of the contract.

### Q25. How can a Spliterator's characteristics improve a parallel algorithm?

**Answer:** SIZED, SUBSIZED, ORDERED, or IMMUTABLE lets the framework split and process elements with fewer checks. Incorrectly advertising a characteristic can produce wrong behavior.

### Q26. Why is BigDecimal scale relevant when comparing decimal values?

**Answer:** equals includes scale, so 2.0 and 2.00 are unequal, while compareTo treats them as numerically equal. Normalize values when equality is a domain requirement.

### Q27. What problem does a defensive copy solve for a record containing an array?

**Answer:** Records make component references final, not the referenced array. Copy on construction and access to prevent external mutation of the record's apparent value.

### Q28. How should you handle a DateTimeFormatter shared across threads?

**Answer:** Reuse it: DateTimeFormatter is immutable and thread-safe, unlike legacy SimpleDateFormat. Keep locale and zone explicit to avoid environment-dependent parsing.

### Q29. Why is ServiceLoader discovery sensitive to class-loader boundaries?

**Answer:** Providers are discovered through a chosen class loader and module configuration. Plugin containers should use the intended loader and report missing or duplicate providers clearly.

### Q30. What is the trade-off of using reflection to access non-public members in a modular application?

**Answer:** Strong encapsulation may reject deep reflection unless packages are opened. Prefer public APIs or explicit module opens, which widen the maintenance and security surface.

## Exceptions and API evolution

### Q31. Why can adding a default method to an interface still break a downstream implementation?

**Answer:** An implementor may inherit conflicting defaults from two interfaces or already define a method with incompatible meaning. Compile representative clients and resolve conflicts explicitly.

### Q32. What is the risk of catching an exception and returning a default value in a library?

**Answer:** It can turn failure into plausible but incorrect data and erase diagnostic context. Use an explicit result or preserve the cause when translating exceptions.

### Q33. How can a multi-release JAR provide optimized code without excluding older runtimes?

**Answer:** Place version-specific classes under META-INF/versions/N and retain compatible base classes. The runtime selects an applicable version; test the artifact on each supported JDK.

### Q34. Why can serialization compatibility fail after changing a class field?

**Answer:** Java's default serial form depends on class shape and serialVersionUID. Define an explicit version and migration strategy, or use a schema-based format for long-lived data.

### Q35. What does adding a checked exception to a public method do to source compatibility?

**Answer:** Callers may need new catch clauses or throws declarations, even if existing binaries still link. Consider a stable unchecked boundary or a new method contract.

### Q36. Why should an API avoid exposing implementation exceptions directly?

**Answer:** Callers become coupled to internal libraries and refactoring changes leak into the public contract. Translate failures into stable domain errors while retaining the cause.

### Q37. How can a class-loader leak survive after an application redeploy?

**Answer:** Static references, threads, ThreadLocals, or registered drivers can retain classes from the old loader. Stop owned threads and clear registrations during shutdown.

### Q38. Why is deserializing untrusted Java object streams dangerous?

**Answer:** Object graphs can trigger gadget code during reconstruction, not merely instantiate expected data. Avoid native serialization for untrusted input and use constrained data formats.

### Q39. When is a sealed interface preferable to a public extensible interface?

**Answer:** Use sealed when the domain needs a controlled set of variants and exhaustive handling. Keep it open when third parties must add implementations independently.

### Q40. Why can changing a public method's generic signature affect clients even when erasure is unchanged?

**Answer:** Source recompilation and reflection see generic metadata, and type inference may change. Treat generic signatures as part of the source-level API and compile downstream fixtures.


## References

- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
