# Testing: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## JUnit and test structure

### Q1. How do parameterized tests improve coverage without copy-paste?

**Answer:** They run one behavior against multiple inputs and expected outcomes while keeping the assertion logic shared.

**Example:** Use `@MethodSource` for structured cases.

### Q2. When should you use a dynamic test?

**Answer:** When the set of test cases is discovered or generated at runtime; otherwise ordinary parameterized tests are often simpler.

**Example:** Generate one test per supported parser format.

### Q3. What is the value of strict Mockito stubbing?

**Answer:** It can reveal unused or mismatched stubs that make tests misleading or brittle.

**Example:** Stub only interactions required for the behavior.

### Q4. When is a Mockito spy risky?

**Answer:** It wraps a real object, so unstubbed methods still run and partial mocking can hide poor design.

**Example:** Prefer a small fake or a refactored collaborator.

### Q5. How can a test verify an exception without overfitting its message?

**Answer:** Assert the exception type and only stable, user-relevant details.

**Example:** Avoid matching an entire internal stack or vendor message.

### Q6. Why avoid asserting every field of a large response?

**Answer:** Tests become coupled to incidental representation and make harmless additive changes costly.

**Example:** Assert fields that express the contract under test.

### Q7. How should test names describe behavior?

**Answer:** Name the condition and expected outcome in plain language.

**Example:** `rejectsExpiredToken`.

### Q8. What is a test fixture builder useful for?

**Answer:** It creates valid domain objects with defaults so each test states only the values relevant to the case.

**Example:** `anOrder().withStatus(PAID).build()`.

### Q9. When should assertions be grouped with assertAll?

**Answer:** When independent checks should all run so one failure does not hide the rest.

**Example:** Use it for several fields of one response.

### Q10. What is the danger of a test that only verifies a mock was called?

**Answer:** The interaction may occur without producing the correct result or state change.

**Example:** Assert observable behavior as well as essential interactions.

## Integration and Spring testing

### Q11. When should a Spring MVC test use MockMvc rather than a live server?

**Answer:** MockMvc is fast for handler, validation, and filter behavior without opening a real network port.

**Example:** Use a live-server test when networking or server configuration is part of the risk.

### Q12. What does `@DataJpaTest` typically limit?

**Answer:** It loads repository and JPA-related components rather than the full application, often with transactional test behavior.

**Example:** Add the production database container when dialect behavior matters.

### Q13. Why can test transactions hide production behavior?

**Answer:** A test may roll back automatically and not exercise commit-time constraints, async visibility, or separate connections.

**Example:** Add tests that commit when those semantics are important.

### Q14. How should a Testcontainers database test manage schema?

**Answer:** Start the target database, apply the same migration scripts, and isolate data between tests or suites.

**Example:** Run Flyway against the container before repository assertions.

### Q15. What makes containerized integration tests flaky?

**Answer:** Shared ports, timing assumptions, leaked containers, resource pressure, or nondeterministic test data.

**Example:** Wait on health checks instead of fixed sleeps.

### Q16. When is `@SpringBootTest(webEnvironment = RANDOM_PORT)` useful?

**Answer:** When you need to exercise a real embedded server and HTTP stack while avoiding a fixed port conflict.

**Example:** Use it for serialization and server-filter integration checks.

### Q17. How can test slices accidentally exclude a required component?

**Answer:** They load only a selected part of the context, so custom configuration may need explicit import or mocking.

**Example:** Keep slice tests focused and make added imports obvious.

### Q18. Why should tests verify database migrations against the target engine?

**Answer:** SQL syntax and semantics vary; in-memory substitutes can miss production-specific behavior.

**Example:** Use a container with the same database family.

### Q19. What is a contract test's role compared with an end-to-end test?

**Answer:** A contract test checks an integration boundary cheaply; an end-to-end test checks a larger assembled flow but is more expensive.

**Example:** Use contracts for many service interactions and a few end-to-end paths.

### Q20. How can tests clean up external resources reliably?

**Answer:** Use lifecycle hooks and `try/finally` or managed extensions so cleanup runs after failure.

**Example:** Close clients and delete temporary cloud objects.

## Time, concurrency, and randomness

### Q21. How do you make time-dependent code testable?

**Answer:** Inject a Clock or time source rather than calling the system clock throughout business logic.

**Example:** Use `Clock.fixed()` for a deterministic expiration test.

### Q22. Why are sleep-based async tests unreliable?

**Answer:** They guess when work completes and can be too short or unnecessarily slow on different machines.

**Example:** Wait on a future or synchronization primitive with a timeout.

### Q23. How can a concurrency test increase the chance of a race?

**Answer:** Coordinate threads with barriers or latches so the critical interleaving happens repeatedly.

**Example:** Release many workers from one CountDownLatch.

### Q24. Why is testing concurrency with one successful run insufficient?

**Answer:** Races depend on timing and may disappear under a particular scheduler or machine.

**Example:** Repeat stress tests and use deterministic coordination for key interleavings.

### Q25. How should randomized tests be debugged?

**Answer:** Record the seed and generated input so a failure can be replayed.

**Example:** Print or persist the seed with the failing case.

### Q26. What does property-based testing check?

**Answer:** It generates many inputs and verifies general invariants rather than only hand-picked examples.

**Example:** Sorting should preserve all values and produce an ordered result.

### Q27. What does mutation testing reveal that line coverage cannot?

**Answer:** It checks whether tests fail when code behavior is deliberately changed, highlighting weak assertions.

**Example:** A surviving mutation may indicate an untested branch consequence.

### Q28. How can performance tests avoid noisy conclusions?

**Answer:** Control environment, warm-up, data size, concurrency, and measurement window; compare repeated runs.

**Example:** Report percentiles and throughput, not one duration.

### Q29. What is a flaky test quarantine policy for?

**Answer:** It isolates an unstable test while tracking an owner and deadline so failures do not become normalized.

**Example:** Do not silently ignore it indefinitely.

### Q30. Why should test data avoid sharing a global database state?

**Answer:** Tests can interfere through ordering or parallel execution and produce nondeterministic results.

**Example:** Use unique identifiers and clean up owned rows.

## Test strategy and maintenance

### Q31. How do you decide which layer should own a behavior test?

**Answer:** Test the behavior at the lowest layer that fully proves it, then add a smaller number of broader integration checks.

**Example:** Validate calculations in unit tests and persistence mapping in repository tests.

### Q32. What is a test pyramid imbalance?

**Answer:** Too many slow broad tests or too few integration checks can make feedback slow or leave boundaries untested.

**Example:** Keep fast focused tests numerous and end-to-end checks selective.

### Q33. Why should coverage targets not replace risk analysis?

**Answer:** A percentage treats all lines equally and says little about critical edge cases or assertions.

**Example:** Prioritize money movement, authorization, and concurrency paths.

### Q34. How should a bug fix be tested?

**Answer:** Add a regression case that fails before the fix and passes after it, including the relevant boundary condition.

**Example:** Keep the original failing input in the test.

### Q35. What is a smoke test in a deployment pipeline?

**Answer:** A few fast checks that confirm the built service starts and essential endpoints respond.

**Example:** Call readiness and one representative business route.

### Q36. When is a mock less useful than a fake?

**Answer:** A fake is better when the dependency's stateful behavior matters across several calls.

**Example:** Use an in-memory repository fake for a workflow test.

### Q37. How can test code be refactored without reducing clarity?

**Answer:** Share stable setup helpers but keep each test's inputs and expected behavior visible.

**Example:** Avoid a generic test DSL that hides the scenario.

### Q38. Why should tests assert API error contracts?

**Answer:** Clients rely on status and error shape, so internal refactoring should not silently change them.

**Example:** Check status, stable error code, and correlation ID.

### Q39. How do you test idempotency?

**Answer:** Send the same operation key twice and verify the business effect occurs once and both responses are consistent.

**Example:** Assert one payment record and one external charge stub call.

### Q40. How do you test a retry policy without waiting in real time?

**Answer:** Inject a scheduler or delay strategy and assert attempt count and timing decisions with a controlled clock.

**Example:** Use a fake clock or zero-delay test policy.

## References

- [JUnit User Guide](https://docs.junit.org/current/user-guide/)
- [Mockito API documentation](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)
- [Spring Boot testing reference](https://docs.spring.io/spring-boot/reference/testing/index.html)
- [Testcontainers for Java](https://java.testcontainers.org/)
