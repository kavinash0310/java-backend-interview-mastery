# Core Java: Easy Q&A

Short answers for quick revision. Read the prompt, answer it aloud in your own words, then check the example.

### Q15. Is Java pass-by-value or pass-by-reference?

**Answer:** Java is always pass-by-value. For an object, the copied value is a reference, so a method can mutate the object but cannot replace the caller's reference.

**Example:** A method may change person.name, but assigning person = new Person() does not replace the caller's variable.

### Q16. Primitive vs reference type?

**Answer:** A primitive variable holds a simple value such as an int. A reference variable holds a reference to an object, or null.

### Q17. Static vs instance member?

**Answer:** A static member belongs to the class; an instance member belongs to one object. Use instance state for data that differs from object to object.

### Q18. What does final mean for a variable?

**Answer:** A final variable can be assigned once. If it holds an object reference, the reference cannot be reassigned, but the object may still be mutable.

### Q19. What is a Java record?

**Answer:** A record is a concise way to declare a class whose main purpose is to carry data. The compiler provides accessors and value-based methods such as equals and hashCode.

### Q20. What is an enum?

**Answer:** An enum defines a fixed set of named constants as a type. It is safer and clearer than representing a small fixed choice with arbitrary strings.

### Q21. StringBuilder vs StringBuffer?

**Answer:** Both build strings without creating a new String for every append. StringBuffer synchronizes its methods; StringBuilder is usually preferred for local, single-threaded construction.

### Q22. Checked vs unchecked exception?

**Answer:** A checked exception must be caught or declared. An unchecked exception is a RuntimeException and does not have that compiler requirement.

### Q23. What is try-with-resources?

**Answer:** It closes resources that implement AutoCloseable when the block exits, including when an exception occurs.

**Example:** try (var reader = Files.newBufferedReader(path)) { return reader.readLine(); }

### Q24. throw vs throws?

**Answer:** throw raises an exception at a point in the code. throws declares exceptions that a method may pass to its caller.

### Q25. What are Java generics for?

**Answer:** Generics let classes and methods work with types while preserving compile-time checks. They reduce unsafe casts in collection code.

### Q26. What does <? extends T> mean?

**Answer:** It is a bounded wildcard for a type that is T or a subtype. It is useful when reading values as T from a producer.

### Q27. Array vs ArrayList?

**Answer:** An array has a fixed length and can hold primitives directly. ArrayList resizes and offers collection operations, but stores objects rather than primitives.

### Q28. List vs Set?

**Answer:** A List keeps a sequence and allows duplicates. A Set represents unique elements; ordering depends on the chosen implementation.

### Q29. HashSet vs TreeSet?

**Answer:** HashSet is suited to fast membership checks without sorted iteration. TreeSet keeps elements sorted and supports ordered range operations.

### Q30. What is Optional used for?

**Answer:** Optional represents a value that may be absent, mainly as a return type. It makes absence explicit, but is not a replacement for every nullable field or parameter.

## References

- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
