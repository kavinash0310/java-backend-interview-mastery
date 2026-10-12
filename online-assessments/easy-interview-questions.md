# Online Assessment Practice: Easy Q&A and MCQs

Choose one answer, then read the explanation and say why the other options do not fit.

### Q06. Which collection is designed to keep insertion order and allow duplicates?

**Answer:** Choose one:

A. Set
B. List
C. Map

**Correct answer:** B. List. A List preserves a sequence and allows duplicates.

### Q07. Average lookup by index in ArrayList?

**Answer:** Choose one:

A. O(1)
B. O(log n)
C. O(n)

**Correct answer:** A. O(1), because the backing array can be indexed directly.

### Q08. What does String immutability mean?

**Answer:** Choose one:

A. Contents cannot change after creation
B. It cannot be compared
C. It cannot be stored in a collection

**Correct answer:** A. Operations that appear to modify a String return another String.

### Q09. What happens to a try-with-resources resource?

**Answer:** Choose one:

A. It closes at block end
B. It commits automatically
C. It retries on failure

**Correct answer:** A. It closes when the block exits, including on exceptions.

### Q10. Which creates an Optional from a possibly-null value?

**Answer:** Choose one:

A. Optional.of(value)
B. Optional.ofNullable(value)
C. Optional.empty(value)

**Correct answer:** B. Optional.ofNullable(value). Optional.of(null) throws.

### Q11. Which is a terminal Stream operation?

**Answer:** Choose one:

A. filter
B. map
C. collect

**Correct answer:** C. collect triggers pipeline processing and produces a result.

### Q12. For object references, what does == compare?

**Answer:** Choose one:

A. Same object reference
B. Equal fields
C. Same hash code

**Correct answer:** A. Whether both references point to the same object.

### Q13. What does volatile provide?

**Answer:** Choose one:

A. Visibility of writes
B. Atomicity of every compound operation
C. Mutual exclusion for all methods

**Correct answer:** A. Visibility; count++ is still not atomic.

### Q14. What does synchronized provide on the same monitor?

**Answer:** Choose one:

A. Mutual exclusion and visibility
B. Faster garbage collection
C. Thread creation

**Correct answer:** A. Mutual exclusion and a happens-before visibility guarantee.

### Q15. What is a benefit of constructor injection?

**Answer:** Choose one:

A. Required dependencies are explicit
B. It starts a thread for each dependency
C. It removes constructors

**Correct answer:** A. It makes dependencies clear and helps tests instantiate the class.

### Q16. Spring singleton scope is one instance per what?

**Answer:** Choose one:

A. Spring container
B. Internet
C. HTTP request

**Correct answer:** A. A Spring IoC container, not every JVM.

### Q17. What does @Repository commonly indicate?

**Answer:** Choose one:

A. Persistence component
B. Scheduled job
C. REST response body

**Correct answer:** A. A persistence component; it may also translate persistence exceptions.

### Q18. Which method is idempotent for intended effect?

**Answer:** Choose one:

A. PUT
B. POST
C. CONNECT

**Correct answer:** A. PUT. Repeating the same PUT has the same intended effect.

### Q19. Which status commonly signals creation?

**Answer:** Choose one:

A. 201
B. 302
C. 503

**Correct answer:** A. 201 Created.

### Q20. Which SQL expression counts all rows?

**Answer:** Choose one:

A. COUNT(*)
B. COUNT(NULL)
C. SUM(*)

**Correct answer:** A. COUNT(*).

### Q21. Which clause filters groups?

**Answer:** Choose one:

A. WHERE
B. HAVING
C. ORDER BY

**Correct answer:** B. HAVING; WHERE filters input rows.

### Q22. What does a foreign key help enforce?

**Answer:** Choose one:

A. Valid reference to another key
B. Sorted output
C. Cached query

**Correct answer:** A. A valid relationship, helping prevent orphan references.

### Q23. A common cost of an index is?

**Answer:** Choose one:

A. Extra storage and write work
B. It disables SELECT
C. It guarantees every query is faster

**Correct answer:** A. Indexes consume storage and make writes do more work.

### Q24. What does a Kafka consumer group allow?

**Answer:** Choose one:

A. Consumers share partition work
B. Producers skip topics
C. Messages bypass storage

**Correct answer:** A. Group members share partition assignments.

### Q25. What does Redis TTL do?

**Answer:** Choose one:

A. Sets when a key expires
B. Makes every write durable
C. Sorts keys

**Correct answer:** A. It sets how long a key remains before expiry.

### Q26. What is JPA dirty checking?

**Answer:** Choose one:

A. Detects changes to managed entities
B. Deletes unchanged entities
C. Runs a unit test

**Correct answer:** A. It detects changes and writes updates during synchronization.

### Q27. What does atomicity in ACID mean?

**Answer:** Choose one:

A. All transaction changes happen or none do
B. Every query uses an index
C. Transactions never wait

**Correct answer:** A. The transaction applies as a unit or is rolled back.

### Q28. Which SQL command removes rows but keeps the table?

**Answer:** Choose one:

A. DROP TABLE
B. DELETE
C. ALTER TABLE

**Correct answer:** B. DELETE removes selected or all rows but keeps the definition.

### Q29. Why validate API input before saving?

**Answer:** Choose one:

A. Reject invalid values and protect rules
B. Avoid all network errors
C. Remove need for backups

**Correct answer:** A. Validation protects business rules before persistence.

### Q30. What is integer division 7 / 2 in Java?

**Answer:** Choose one:

A. 3.5
B. 3
C. 4

**Correct answer:** B. With two integer operands, the fractional part is discarded.

### Q31. Which access modifier makes a member private to its declaring class?

**Answer:** Choose one:

A. public
B. protected
C. private

**Correct answer:** C. private, subject to Java's nested-class access rules.

## References

- [Java SE 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [Java Language Specification, Java 21](https://docs.oracle.com/javase/specs/jls/se21/html/)
- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [PostgreSQL SQL documentation](https://www.postgresql.org/docs/current/sql.html)
- [Apache Kafka documentation](https://kafka.apache.org/documentation/)
- [Jakarta Persistence 3.2 specification](https://jakarta.ee/specifications/persistence/3.2/jakarta-persistence-spec-3.2)
