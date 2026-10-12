# Core Java: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Generics and type system

### Q1. What is type erasure, and what limitation does it create?

**Answer:** The compiler uses generic type information for checks, then mostly erases it from runtime types. That is why you cannot write `new T[]` or overload methods only by generic arguments.

**Example:** `List<String>` and `List<Integer>` share the same runtime class.

### Q2. How does PECS help choose a wildcard bound?

**Answer:** Use `? extends T` when reading values as T, and `? super T` when adding T values.

**Example:** `List<? extends Number>` is a producer; `List<? super Integer>` accepts Integer values.

### Q3. Why is `List<Integer>` not a subtype of `List<Number>`?

**Answer:** If it were, code could add a Double to a list that is supposed to contain only Integers. Generic types are invariant.

**Example:** Use `List<? extends Number>` to accept either list for reading.

### Q4. When would you use a method type parameter instead of a wildcard?

**Answer:** Use a type parameter when the method must relate multiple input or output types; use a wildcard when only one side needs flexibility.

**Example:** `<T> T first(List<T> xs)` ties the return type to the list.

### Q5. What is a bridge method?

**Answer:** The compiler may generate one after type erasure so an overridden generic method keeps its polymorphic behavior.

**Example:** A generic `compareTo(T)` implementation may get a bridge with an Object parameter.

### Q6. Why can't Java create a generic array directly?

**Answer:** Arrays know and check their element type at runtime, while generic type arguments are erased, so the two safety models conflict.

**Example:** Prefer `List<T>` or a carefully controlled `Object[]` internally.

### Q7. What can you safely read from `List<? super Integer>`?

**Answer:** You can add an Integer, but a read is only guaranteed to be an Object because the actual list could be `List<Number>` or `List<Object>`.

**Example:** `list.add(1)` is valid; `Integer x = list.get(0)` is not.

### Q8. When is a bound like `<T extends Comparable<T>>` useful?

**Answer:** It lets an algorithm call `compareTo` on values while preserving the caller's concrete type.

**Example:** A generic maximum method can compare its candidates.

### Q9. Comparable vs Comparator: when would you choose each?

**Answer:** Comparable defines a type's natural order; Comparator supplies an external or alternate ordering without changing the type.

**Example:** Sort employees by name with `Comparator.comparing(Employee::name)`.

### Q10. What does a reifiable type mean?

**Answer:** The runtime retains enough type information to check the type directly, as with `String[]`; most parameterized types are not reifiable.

**Example:** `value instanceof List<String>` is not allowed, but `instanceof List<?>` is.

## Collections and object contracts

### Q11. What can go wrong if a map key's fields change after insertion?

**Answer:** If fields used by equals or hashCode change, the map may search a different bucket and fail to find the key.

**Example:** Prefer immutable key objects.

### Q12. Why can HashMap resize change its performance?

**Answer:** When size passes the threshold, it allocates a larger table and redistributes entries; this operation costs time and memory.

**Example:** Set an initial capacity when a map's approximate size is known.

### Q13. What does fail-fast iteration mean, and is it a concurrency guarantee?

**Answer:** Some iterators detect structural modification and throw ConcurrentModificationException as a best-effort bug signal; it is not synchronization.

**Example:** Use an iterator's own `remove()` for supported removal during iteration.

### Q14. Why does iterator.remove() work when removing during traversal?

**Answer:** The iterator updates its own traversal state and collection modification count consistently.

**Example:** `while (it.hasNext()) if (skip(it.next())) it.remove();`

### Q15. How can LinkedHashMap support a simple LRU cache?

**Answer:** Configure access-order iteration and remove the eldest entry when a size limit is exceeded; this is not a full production cache.

**Example:** Override `removeEldestEntry` in a bounded map.

### Q16. When would EnumMap or EnumSet be a good fit?

**Answer:** When keys or values are from one enum type; they are compact and can iterate in enum declaration order.

**Example:** Represent enabled days with `EnumSet<Day>`.

### Q17. Is `List.copyOf` a deep immutable copy?

**Answer:** No. The returned list cannot be structurally changed, but mutable objects inside it may still change.

**Example:** Copy each element too if deep immutability is required.

### Q18. What is the difference between an unmodifiable view and an immutable snapshot?

**Answer:** A view blocks writes through that reference but may reflect changes to its backing collection; a snapshot has independent structure.

**Example:** `Collections.unmodifiableList(source)` is a view; `List.copyOf(source)` copies the structure.

### Q19. When is IdentityHashMap appropriate?

**Answer:** When keys must be compared by reference identity rather than equals, usually for specialized graph or object-processing tools.

**Example:** Do not substitute it for an ordinary domain map.

### Q20. What is a WeakHashMap entry's lifetime tied to?

**Answer:** Its key is weakly referenced, so the entry can disappear after no strong reference to that key remains.

**Example:** A value that strongly references its own key can defeat the expected cleanup.

## Modern Java and APIs

### Q21. What benefit do sealed types provide over an open interface?

**Answer:** They make the permitted subtype set explicit, helping model closed domains and enabling more complete pattern handling.

**Example:** `sealed interface Result permits Success, Failure`.

### Q22. Why can a record still contain mutable state indirectly?

**Answer:** Record components are final references, but a referenced array or mutable object can still change.

**Example:** Copy an input array in the constructor and return a copy from the accessor.

### Q23. What makes a record's generated equals behavior different from identity equality?

**Answer:** It compares component values, so two records with equal component values are equal even if they are separate objects.

**Example:** `new Point(1,2).equals(new Point(1,2))` is true.

### Q24. When is Optional.flatMap preferable to map?

**Answer:** Use flatMap when the mapping function already returns Optional, avoiding a nested `Optional<Optional<T>>`.

**Example:** `userOpt.flatMap(User::email)`.

### Q25. Why can calling peek for business logic be fragile?

**Answer:** Stream stages are lazy and may be optimized; peek is intended mainly for inspection, not required side effects.

**Example:** Use `forEach` for an intentional terminal side effect.

### Q26. How should `Collectors.toMap` handle duplicate keys?

**Answer:** Supply a merge function or choose another collector; without one, duplicate keys cause an exception.

**Example:** `toMap(User::id, identity(), (first, next) -> next)`.

### Q27. What is the difference between Stream.toList() and Collectors.toList()?

**Answer:** `Stream.toList()` returns an unmodifiable list; `Collectors.toList()` does not guarantee a particular mutability contract.

**Example:** Use `toList()` when callers should not mutate the collected structure.

### Q28. How are exceptions from try-with-resources preserved?

**Answer:** If the body and close both fail, the body exception is primary and close failures are attached as suppressed exceptions.

**Example:** Read `getSuppressed()` on the primary exception.

### Q29. Why use BigDecimal for currency calculations?

**Answer:** Binary floating-point cannot exactly represent many decimal fractions; BigDecimal supports explicit decimal precision and rounding.

**Example:** Use `new BigDecimal("19.99")`, not `new BigDecimal(19.99)`.

### Q30. When is `var` safe to use?

**Answer:** Use it when the initializer makes the local type obvious; it remains statically typed and cannot be used for fields or method parameters.

**Example:** `var names = new ArrayList<String>();` is still an ArrayList<String>.

## Exceptions and compatibility

### Q31. How does Java choose a catch block when exceptions are related?

**Answer:** The first compatible catch block wins, so catch specific subtypes before their broader parent type.

**Example:** Catch `FileNotFoundException` before `IOException`.

### Q32. When should an API use a checked exception?

**Answer:** Use one when callers can reasonably recover and the API wants to require handling; unchecked exceptions often fit programming errors or unrecoverable failures.

**Example:** A parser may expose a checked format error if callers can correct input.

### Q33. What happens to a checked exception thrown from a lambda?

**Answer:** It must fit the functional interface method's declared throws clause or be handled inside the lambda.

**Example:** Wrap or translate an `IOException` when using a Consumer.

### Q34. Why should exception messages avoid sensitive details?

**Answer:** Messages may reach logs or clients; secrets and personal data can leak beyond the intended boundary.

**Example:** Return a safe error ID and log protected diagnostic context.

### Q35. What is binary compatibility in Java?

**Answer:** Previously compiled clients can keep linking against a changed library without recompilation for compatible changes.

**Example:** Adding an overload is usually safer than changing a public method descriptor.

### Q36. What can cause NoSuchMethodError at runtime?

**Answer:** Code was compiled against a method that is missing from the library version actually loaded.

**Example:** Check dependency resolution and deployed JAR versions.

### Q37. NoClassDefFoundError vs ClassNotFoundException?

**Answer:** ClassNotFoundException is commonly thrown by explicit class loading; NoClassDefFoundError means normal linkage could not find a class needed at runtime.

**Example:** Compare runtime classpath with compile-time dependencies.

### Q38. Why can changing a public field to private break clients?

**Answer:** Compiled callers link to the field's public descriptor; changing visibility can break source and binary compatibility.

**Example:** Add an accessor and plan a compatible migration.

### Q39. How can you evolve an enum safely when clients persist its names?

**Answer:** Treat stored names as data contracts; new constants may break exhaustive logic or older readers.

**Example:** Use a fallback branch when decoding values from external systems.

### Q40. Why should library code avoid catching Throwable broadly?

**Answer:** It can swallow serious VM errors and hide failures the application cannot safely recover from.

**Example:** Catch expected exception types and let fatal errors propagate.

## References

- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
