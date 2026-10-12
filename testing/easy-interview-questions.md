# Testing: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q01. What is a unit test?

**Answer:** It checks a small piece of behavior in isolation, usually one class or function. It should be fast and easy to diagnose.

### Q02. What is an integration test?

**Answer:** It checks components working together across a boundary, such as app code with a database or web framework.

### Q03. What is an end-to-end test?

**Answer:** It exercises a user-visible flow through most of the system. It gives broad confidence but is slower and may be fragile.

### Q04. What is the test pyramid?

**Answer:** A guideline to use many fast focused tests, fewer integration tests, and a small number of end-to-end tests.

### Q05. What is an assertion?

**Answer:** It checks whether an observed result matches expected behavior. A useful failure message says what differed.

### Q06. What is a test fixture?

**Answer:** The data and setup a test needs. Keep it small and make important values obvious.

### Q07. What is a test double?

**Answer:** A substitute used in a test, such as a stub, mock, fake, or spy. Use one when it improves isolation or control.

### Q08. Stub vs mock?

**Answer:** A stub provides predetermined answers; a mock can verify calls. Prefer checking outcomes unless the interaction itself is behavior.

### Q09. What is a fake in testing?

**Answer:** A lightweight working implementation used in tests, such as an in-memory repository. It should preserve behavior the test depends on.

### Q10. What is a Mockito spy?

**Answer:** It wraps a real object while allowing calls to be observed or stubbed. A plain fake or mock is often easier to reason about.

### Q11. What is JUnit used for?

**Answer:** A Java testing framework for organizing and running tests, including assertions and lifecycle hooks.

### Q12. What does @BeforeEach do?

**Answer:** Runs setup before each test method. Use it to create fresh per-test state instead of sharing mutable fixtures.

### Q13. What does @AfterEach do?

**Answer:** Runs cleanup after each test, including after a failed assertion. Use it to release resources.

### Q14. How do you test an exception?

**Answer:** Assert the specific exception and verify useful details when they are part of the contract.

**Example:** assertThrows(IllegalArgumentException.class, () -> service.find(null));

### Q15. What is a parameterized test?

**Answer:** It runs the same test logic with several input cases, making boundaries visible without repeating methods.

### Q16. What does Arrange-Act-Assert mean?

**Answer:** Arrange setup, Act invokes behavior, Assert checks the result. This helps readers see what the test proves.

### Q17. What makes a test deterministic?

**Answer:** It gives the same result for the same controlled inputs. Avoid uncontrolled time, randomness, races, and external availability.

### Q18. What is a flaky test?

**Answer:** It passes and fails without relevant code change, often due to timing, shared state, or environment. Fix the source instead of rerunning indefinitely.

### Q19. What is test coverage?

**Answer:** A measure of which code was exercised. High coverage does not prove tests checked meaningful outcomes.

### Q20. What is a Spring Boot test slice?

**Answer:** It loads a focused part of the app, such as MVC, instead of the full context, making feedback faster.

### Q21. What does @WebMvcTest test?

**Answer:** It loads Spring MVC components for focused controller tests. Dependencies outside the slice are usually test doubles.

### Q22. What is MockMvc?

**Answer:** It exercises Spring MVC request handling without a real HTTP server and can verify routes, status codes, headers, and bodies.

### Q23. What does @SpringBootTest do?

**Answer:** It loads a broader Spring Boot context for integration tests when application wiring is part of what must be proved.

### Q24. What is Testcontainers?

**Answer:** It runs disposable containerized dependencies for tests, such as a real database, helping test production-like behavior.

### Q25. Why test against the production database type?

**Answer:** Databases differ in SQL, types, constraints, and locking; a real engine catches mismatches an in-memory substitute can miss.

### Q26. What is a contract test?

**Answer:** It checks that a provider and consumer agree on an interface or message shape, catching breaking changes before full deployment.

### Q27. What is a performance test?

**Answer:** It measures behavior under a defined workload, including latency and throughput. State the load and environment.

### Q28. How should tests handle time?

**Answer:** Inject a Clock or time source where practical, then use a fixed clock in tests to avoid waiting and make expiry behavior repeatable.

### Q29. How do you test asynchronous code?

**Answer:** Wait on a bounded future or condition and assert the result, rather than sleeping arbitrarily. Set a timeout so failures do not hang.

### Q30. What is a good test name?

**Answer:** It states the behavior and condition, such as rejects an expired token, so a failure is understandable.

## References

- [JUnit User Guide](https://docs.junit.org/current/user-guide/)
- [Mockito API documentation](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)
- [Spring Boot testing reference](https://docs.spring.io/spring-boot/reference/testing/index.html)
- [Testcontainers for Java](https://java.testcontainers.org/)
