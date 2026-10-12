# Core Java: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Expressions, types, and operators

### Q1. What happens in a colon-style switch when case 1 falls through into case 2?

**Answer:** Execution continues into following case statements until a break, return, or switch end. Arrow-style switch rules do not fall through.

### Q2. Why is Integer x=1000; Integer y=1000; followed by x==y a trap?

**Answer:** Usually false because == compares references and only a small wrapper cache range is guaranteed. Use equals or unbox deliberately.

### Q3. Why can Double.NaN == Double.NaN be false while wrapper equals returns true?

**Answer:** Primitive IEEE comparison says NaN is unequal to every value, including itself; wrapper equality defines a consistent object contract for NaN.

### Q4. What does 1/0 do differently from 1.0/0.0?

**Answer:** Integer division throws ArithmeticException; floating-point division yields positive infinity under IEEE 754.

### Q5. Why can 0.1 + 0.2 == 0.3 be false?

**Answer:** Binary floating point approximates many decimal fractions. Use BigDecimal constructed from strings or compare with a justified tolerance.

### Q6. What is printed by "a" + 1 + 2 versus 1 + 2 + "a"?

**Answer:** The first prints a12; the second prints 3a because + evaluates left-to-right and becomes concatenation once a String participates.

### Q7. Why can a conditional expression with int and Integer unexpectedly unbox null?

**Answer:** Type rules may choose a primitive numeric type, forcing unboxing of the null branch. Avoid mixed primitive/wrapper branches or guard nullable values.

### Q8. What happens when null is passed to overloads f(String) and f(Object)?

**Answer:** The String overload is more specific; if two unrelated reference overloads both match, the call is ambiguous.

### Q9. Why can byte b=1; b=b+1 fail while b+=1 compiles?

**Answer:** Binary numeric promotion makes b+1 an int requiring a cast; compound assignment includes implicit narrowing conversion.

### Q10. Does final make an object immutable?

**Answer:** No. It prevents reassignment of the reference; mutable fields of the referenced object can still change. Encapsulate and defensively copy them.

## Collections and object traps

### Q11. Why can List.of("a", null) fail before the list is returned?

**Answer:** List.of rejects null elements and returns an unmodifiable list. Validate nullable data or use a collection whose contract allows null.

### Q12. What does Arrays.asList(array).set(0,x) do, and what does add(x) do?

**Answer:** set updates the fixed-size list and backing array; add throws UnsupportedOperationException because size cannot change.

### Q13. Why can a TreeSet discard objects that are unequal according to equals?

**Answer:** It treats compare()==0 as duplicate, even if equals says they differ. Make ordering consistent with equality or choose another set.

### Q14. Why is Collections.unmodifiableList(source) not an immutable snapshot?

**Answer:** It is a read-only view over source, so changes through source remain visible. Copy first when an independent snapshot is required.

### Q15. Why can a HashSet contain an object that it can no longer find?

**Answer:** If a field used by hashCode changes after insertion, lookup computes a different bucket. Keep hash keys immutable while stored.

### Q16. Why does Map.get(key)==null not always mean the key is absent?

**Answer:** A map may contain the key mapped to null. Use containsKey when the distinction matters, or prohibit null values.

### Q17. When can removeIf throw ConcurrentModificationException even if its predicate only reads?

**Answer:** If the predicate structurally modifies the same collection, iteration is interfered with. Keep predicates side-effect-free.

### Q18. Why might HashMap iteration order change after adding one entry?

**Answer:** The map does not promise iteration order, and resize or hash distribution may alter traversal. Use LinkedHashMap or a sorted map when order is contractual.

### Q19. What is surprising about List.of(1,2).contains(1L)?

**Answer:** It returns false because wrapper equality does not coerce numeric types; Integer(1) and Long(1) are unequal.

### Q20. Why can remove(1) remove a different thing from remove(Integer.valueOf(1)) on List<Integer>?

**Answer:** The int overload removes by index; the Integer overload removes the matching value. Use an explicit value to disambiguate.

## Strings, streams, and exceptions

### Q21. Why might peek(System.out::println).count() print nothing on a sized list stream?

**Answer:** count may derive its result from exact source size and elide intermediate stages, including peek. Do not use peek for required side effects.

### Q22. Why can var result = List.of() infer List<Object>?

**Answer:** With var there is no target type to guide the generic invocation, so the compiler infers Object. Write List.<String>of() or provide a List<String> target when the element type matters.

### Q23. Does Stream.toList() return a mutable list?

**Answer:** No, its result is unmodifiable. Collectors.toList() also has no guaranteed mutability contract; request a specific collection when needed.

### Q24. Why can parallelStream().forEach print values in a different order each run?

**Answer:** Parallel workers process elements concurrently and forEach does not promise encounter order. Use forEachOrdered only when order is needed.

### Q25. What does String s=new String("java") allocate?

**Answer:** It creates a new String object; the literal is also interned. Reference identity differs even if storage is optimized.

### Q26. Why can String.valueOf((char[]) null) throw while String.valueOf((Object) null) return text?

**Answer:** Overload resolution selects different methods from the static argument type. The char-array overload constructs a String from the array and fails on null; the Object overload returns the text null.

### Q27. What happens when both the try body and close() throw in try-with-resources?

**Answer:** The body exception stays primary and close failures are attached as suppressed exceptions. Inspect getSuppressed() during debugging.

### Q28. Can a finally block hide a return value or exception?

**Answer:** Yes; a return or thrown exception in finally can replace an earlier result or failure. Avoid control flow that exits from finally.

### Q29. Why can catch(Exception e) not catch an Error?

**Answer:** Error and Exception are separate subclasses of Throwable. Catch expected failures and avoid swallowing VM-level conditions.

### Q30. What does Optional.map return if the mapping function returns null?

**Answer:** It produces an empty Optional. If the mapper itself returns Optional, use flatMap to avoid nesting.

## References

- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
