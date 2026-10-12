# Core Java: Easy Q&A

Short answers for quick revision. Read the prompt, answer it aloud in your own words, then check the answer and example where included.

### Q1. Is Java pass-by-value or pass-by-reference?

**Answer:** Java is always pass-by-value. For an object, the copied value is a reference, so a method can mutate the object but cannot replace the caller's reference.

**Example:** A method may change person.name, but assigning person = new Person() does not replace the caller's variable.

### Q2. Primitive vs reference type?

**Answer:** A primitive variable holds a simple value such as an int. A reference variable holds a reference to an object, or null.

### Q3. Static vs instance member?

**Answer:** A static member belongs to the class; an instance member belongs to one object. Use instance state for data that differs from object to object.

### Q4. What does final mean for a variable?

**Answer:** A final variable can be assigned once. If it holds an object reference, the reference cannot be reassigned, but the object may still be mutable.

### Q5. What is a Java record?

**Answer:** A record is a concise way to declare a class whose main purpose is to carry data. The compiler provides accessors and value-based methods such as equals and hashCode.

### Q6. What is an enum?

**Answer:** An enum defines a fixed set of named constants as a type. It is safer and clearer than representing a small fixed choice with arbitrary strings.

### Q7. StringBuilder vs StringBuffer?

**Answer:** Both build strings without creating a new String for every append. StringBuffer synchronizes its methods; StringBuilder is usually preferred for local, single-threaded construction.

### Q8. Checked vs unchecked exception?

**Answer:** A checked exception must be caught or declared. An unchecked exception is a RuntimeException and does not have that compiler requirement.

### Q9. What is try-with-resources?

**Answer:** It closes resources that implement AutoCloseable when the block exits, including when an exception occurs.

**Example:** try (var reader = Files.newBufferedReader(path)) { return reader.readLine(); }

### Q10. throw vs throws?

**Answer:** throw raises an exception at a point in the code. throws declares exceptions that a method may pass to its caller.

### Q11. What are Java generics for?

**Answer:** Generics let classes and methods work with types while preserving compile-time checks. They reduce unsafe casts in collection code.

### Q12. What does <? extends T> mean?

**Answer:** It is a bounded wildcard for a type that is T or a subtype. It is useful when reading values as T from a producer.

### Q13. Array vs ArrayList?

**Answer:** An array has a fixed length and can hold primitives directly. ArrayList resizes and offers collection operations, but stores objects rather than primitives.

### Q14. List vs Set?

**Answer:** A List keeps a sequence and allows duplicates. A Set represents unique elements; ordering depends on the chosen implementation.

### Q15. HashSet vs TreeSet?

**Answer:** HashSet is suited to fast membership checks without sorted iteration. TreeSet keeps elements sorted and supports ordered range operations.

### Q16. What is Optional used for?

**Answer:** Optional represents a value that may be absent, mainly as a return type. It makes absence explicit, but is not a replacement for every nullable field or parameter.

### Q17. What is the difference between == and equals()?

**Answer:** For primitives, == compares values; for objects, == compares references. equals() compares logical equality when the class implements it.

**Example:** `new String("java").equals("java")` is true, but `==` is false.

### Q18. Why override hashCode() when overriding equals()?

**Answer:** Equal objects must return the same hash code so hash-based collections can find them in the same bucket.

**Example:** If equals() says two User objects match, their hashCode() values must match too.

### Q19. How does HashMap find a value?

**Answer:** It hashes the key to choose a bucket, then compares keys in that bucket. Collisions are allowed and handled internally.

**Example:** `map.get("id")` uses the key's hash and equality check.

### Q20. HashMap vs LinkedHashMap?

**Answer:** HashMap does not promise iteration order. LinkedHashMap maintains insertion order by default.

**Example:** Use LinkedHashMap when a predictable iteration order matters.

### Q21. HashMap vs TreeMap?

**Answer:** HashMap offers average constant-time lookup without sorted keys; TreeMap keeps keys ordered and operations are typically logarithmic.

**Example:** TreeMap can return entries in key order.

### Q22. Why is String immutable?

**Answer:** Its value cannot change after construction, which makes sharing safer and supports stable hashing, security, and string pooling.

**Example:** Calling `toUpperCase()` returns a new String.

### Q23. Why are Java wrapper classes immutable?

**Answer:** Wrapper objects such as Integer are immutable, so their value cannot change after creation. This makes them safe to share and use as map keys.

### Q24. What is a functional interface?

**Answer:** An interface with one abstract method; it can be implemented with a lambda.

**Example:** `Predicate<String> nonEmpty = s -> !s.isEmpty();`

### Q25. What is a lambda expression?

**Answer:** A compact way to provide an implementation for a functional interface.

**Example:** `x -> x * 2` can implement a Function.

### Q26. What is the Stream API for?

**Answer:** It describes a pipeline that filters, transforms, and collects data without changing the source collection.

**Example:** `names.stream().filter(n -> n.startsWith("A")).toList()`

### Q27. Intermediate vs terminal Stream operation?

**Answer:** Intermediate operations build the pipeline and are lazy; a terminal operation runs it and produces a result or side effect.

**Example:** `filter()` is intermediate; `count()` is terminal.

### Q28. What is the difference between map() and flatMap()?

**Answer:** map transforms each item once; flatMap transforms each item into a stream and flattens those streams.

**Example:** Use flatMap to turn `List<List<String>>` into a stream of strings.

### Q29. What is an immutable class?

**Answer:** A class whose observable state cannot change after construction. Use final state, controlled construction, and defensive copies.

**Example:** Copy a mutable input list with `List.copyOf(items)`.

### Q30. What is the String pool?

**Answer:** A JVM area that reuses certain String instances so identical literals can share storage.

**Example:** `String a = "java"; String b = "java";`

### Q31. What is method overloading?

**Answer:** Defining methods with the same name but different parameter lists; the compiler selects the matching signature.

**Example:** `print(String)` and `print(int)` are overloads.

### Q32. What is method overriding?

**Answer:** A subclass supplies a compatible implementation of an inherited instance method; runtime dispatch chooses it.

**Example:** A `Dog` can override `Animal.sound()`.

### Q33. What is polymorphism?

**Answer:** The same parent type can refer to different implementations, and an overridden method runs for the actual object.

**Example:** `Animal a = new Dog(); a.sound();` calls Dog's method.

### Q34. What is encapsulation?

**Answer:** Keeping state private and exposing deliberate operations so callers cannot freely violate an object's rules.

**Example:** A bank account exposes `deposit()` instead of a public balance field.

### Q35. What is inheritance?

**Answer:** A class can reuse and specialize behavior from a parent class. Prefer it when there is a true “is-a” relationship.

**Example:** `SavingsAccount extends Account`.

### Q36. What is an interface used for?

**Answer:** It defines a contract that unrelated classes can implement, allowing callers to depend on behavior rather than a concrete class.

**Example:** `List` is implemented by both ArrayList and LinkedList.

### Q37. What is an abstract class?

**Answer:** A base class that cannot be instantiated and can combine shared state, concrete methods, and abstract methods.

**Example:** Use one when related subclasses share implementation.

### Q38. What is a sealed class?

**Answer:** A class or interface that restricts which types may extend or implement it, making a hierarchy explicit.

**Example:** Use `sealed interface Shape permits Circle, Square` when only known shapes are allowed.

### Q39. What is a varargs parameter?

**Answer:** A parameter that accepts zero or more values of one type and is received as an array.

**Example:** `void log(String... messages)`

### Q40. What is an annotation?

**Answer:** Metadata attached to program elements that tools or frameworks can inspect.

**Example:** `@Override` lets the compiler check that a method overrides another.

## References

- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
