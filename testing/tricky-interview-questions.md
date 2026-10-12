# Testing: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Test behavior and isolation traps

### Q1. Why can a test using assertThrows pass for the wrong exception?

**Answer:** It may assert only a broad superclass or wrap the tested call incorrectly. Assert the specific type and relevant context.

### Q2. Can a test pass even if production code never executes the asserted branch?

**Answer:** Yes, if mocks or fixtures directly create the expected output. Verify the actual public action and meaningful side effects.

### Q3. Why can transaction rollback hide a missing commit in an integration test?

**Answer:** The test framework may wrap the test itself in a transaction that automatically rolls back. Exercise commit boundaries and inspect from a separate transaction.

### Q4. Why can parallel test execution expose a test that passed alone?

**Answer:** Tests may share static state, ports, files, schemas, or time-dependent fixtures. Isolate resources and avoid order assumptions.

### Q5. Why are fixed sleeps a weak way to test eventual behavior?

**Answer:** They are slow and flaky across machines. Poll a bounded condition or use a controllable clock and deterministic synchronization.

### Q6. Can a mock verify an implementation detail and still let a regression through?

**Answer:** Yes. Tests can assert an internal call while missing the user-visible result or actual integration semantics. Prefer behavior assertions.

### Q7. Why can a test with a mocked repository miss a database uniqueness race?

**Answer:** The mock does not reproduce transaction isolation or constraints. Add an integration test against the real database engine.

### Q8. What is the danger of reusing a mutable fixture between tests?

**Answer:** One test's mutation changes another's starting state, creating order-dependent failures. Build fresh fixtures or use immutable test data.

### Q9. Why can a snapshot test create a large maintenance burden?

**Answer:** Unreviewed snapshot updates can bless regressions and hide the important difference. Normalize unstable data and review semantic changes.

### Q10. How should a test handle time-zone-dependent behavior?

**Answer:** Use a fixed clock and explicit zone; test daylight-saving transitions and boundaries where the rule depends on local time.

## Integration and concurrency traps

### Q11. Why can a Testcontainers test still differ from production?

**Answer:** Image version, extensions, settings, data volume, and network differ. Pin the target version and configure production-relevant behavior.

### Q12. Why can a Spring test pass in isolation but fail after another test dirties the context?

**Answer:** Cached contexts may retain mutated singleton state or static collaborators. Reset state or isolate configuration rather than relying on test order.

### Q13. How do you test duplicate message delivery without relying on a broker crash?

**Answer:** Invoke the handler twice with the same event identity and assert one durable business effect. Then add an integration case for acknowledgment timing.

### Q14. Why can an outbox test miss the publish-before-mark crash window?

**Answer:** A happy-path fake relay never simulates a crash between steps. Force that interleaving and prove duplicate publication is harmless.

### Q15. What does a contract test fail to prove about a distributed deployment?

**Answer:** It does not prove routing, credentials, capacity, or production network behavior. Keep targeted smoke tests for deployment wiring.

### Q16. Why can a thread-safety test be nondeterministic even with many repetitions?

**Answer:** Scheduler interleavings are not guaranteed and the race may be rare. Use barriers/actors and validate histories against a sequential specification.

### Q17. Can a service mock prove that HTTP timeout cancellation works?

**Answer:** Not if the mock returns immediately. Use a controllable local server and assert the underlying request is canceled.

### Q18. Why should async tests wait for a condition rather than a future count?

**Answer:** A count may advance before the actual durable side effect or cleanup. Await the observable invariant with a deadline.

### Q19. How can a test prove a retry budget is bounded?

**Answer:** Inject failures for all attempts, assert exact maximum calls, total deadline behavior, and that cancellation stops later work.

### Q20. Why can an integration test pass locally but fail in CI due to a port?

**Answer:** Tests may choose a fixed port already occupied or race in parallel. Use ephemeral ports and discover the assigned address.

## Suite and coverage traps

### Q21. Does 100 percent line coverage prove the code is correct?

**Answer:** No. Lines may execute without assertions on outcomes, and edge cases can remain untested. Mutation and risk-based review provide more signal.

### Q22. Can a mutation score be improved by adding assertions that do not reflect requirements?

**Answer:** Yes, but that games the metric. Mutations should represent plausible defects and tests should protect meaningful behavior.

### Q23. Why is a flaky test not harmless if it is quarantined?

**Answer:** It can hide regressions and normalize ignored failures. Track an owner, impact, and fix deadline while keeping visibility.

### Q24. Should every test be an end-to-end test for realism?

**Answer:** No. End-to-end tests are slower and harder to diagnose. Use focused unit/integration tests plus a small set of critical workflows.

### Q25. Why can performance test thresholds be noisy?

**Answer:** Shared CPU, warm caches, GC, and network variance affect timing. Control environment, repeat runs, and compare distributions.

### Q26. Why can a test-managed transaction hide an AFTER_COMMIT listener?

**Answer:** The test framework may roll back instead of committing, so the listener never runs. Commit explicitly in an integration test or test transaction synchronization separately.

### Q27. Why can golden-file tests break after a JDK upgrade without a behavior bug?

**Answer:** Formatting, locale, serialization, or ordering may differ. Normalize unstable fields and verify the contract rather than raw incidental output.

### Q28. Can a green database test suite prove migration is safe at scale?

**Answer:** No. Large tables, lock duration, WAL, and replica lag differ with production volume. Test realistic data size and deployment sequence.

### Q29. Why can deleting a slow test reduce confidence more than expected?

**Answer:** It may be the only coverage for a high-impact boundary. Profile and replace redundancy while preserving risk coverage.

### Q30. How should flaky-test retries be interpreted in CI?

**Answer:** A retry passing signals nondeterminism, not a clean result. Preserve and report the first failure, then fix its shared-state or timing cause.

## References

- [JUnit User Guide](https://docs.junit.org/current/user-guide/)
- [Mockito API documentation](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)
- [Spring Boot testing reference](https://docs.spring.io/spring-boot/reference/testing/index.html)
- [Testcontainers for Java](https://java.testcontainers.org/)
