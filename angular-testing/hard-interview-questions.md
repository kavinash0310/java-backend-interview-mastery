# Angular and Frontend Testing: Hard Interview Questions and Answers

**40 questions.** Prefer assertions about user-visible outcomes and public contracts; use mocks to control boundaries, not to recreate the implementation.

## Testing foundations and setup

### 1. How would you test a component with a child component that emits events?

**Answer:** Use a host or real child to exercise the interaction, trigger the child event, and assert the parent’s rendered or service-level response.

### 2. How can a component test accidentally pass without rendering the template?

**Answer:** If it asserts only on class fields, it may miss template errors; create a fixture and assert observable DOM behavior.

### 3. How do you avoid over-mocking an Angular component test?

**Answer:** Keep framework wiring and relevant child behavior real; replace only external or nondeterministic boundaries and assert outcomes.

### 4. How do you test a standalone component with imports?

**Answer:** Add the standalone component to TestBed imports, then provide or import the dependencies it actually needs.

### 5. How can you override a component-level provider?

**Answer:** Use TestBed’s component override APIs before fixture creation and retrieve the instance from the component injector.

### 6. Why can TestBed.inject return a different service instance than the component uses?

**Answer:** The component may provide its own scoped instance that shadows the testing module’s root provider.

### 7. How do you test an Angular component harness?

**Answer:** Build a harness loader from the fixture, use the harness’s public interaction methods, and assert the user-visible state.

### 8. Why should a harness avoid exposing internal DOM selectors?

**Answer:** Consumers would become coupled to private markup; expose actions and observations that represent the component’s public behavior.

### 9. How do you test a dialog rendered outside the fixture root?

**Answer:** Use a document-root harness loader or query the appropriate document-level overlay container, then clean up after the test.

### 10. How can an HTTP test detect an accidental duplicate request?

**Answer:** Assert expected request count and call verify so extra unmatched requests fail the test.

## Components services and HTTP

### 11. How would you test a retrying HTTP service without waiting real time?

**Answer:** Use fake time or inject a retry delay policy, then control the request failures and verify the bounded retry sequence.

### 12. How do you test that a canceled search request does not update the view?

**Answer:** Emit a newer query, ensure the old inner subscription is canceled, then resolve responses in reverse order and assert only the newest appears.

### 13. How do you test an interceptor that refreshes tokens?

**Answer:** Control the refresh response, send concurrent requests, and assert one refresh operation, correct retries, and no infinite loop on failure.

### 14. How should tests handle time-dependent signals or effects?

**Answer:** Trigger state changes explicitly, flush relevant scheduled work, and assert the externally visible result rather than effect internals.

### 15. How can you test component destruction cleanup?

**Answer:** Spy on teardown or emit after fixture destruction and ensure no stale UI update, listener, or request remains.

### 16. What is the trade-off between shallow and integrated component tests?

**Answer:** Shallow tests are fast and focused but can miss wiring; integrated tests give stronger confidence with more setup and maintenance.

### 17. How do you keep a test robust to harmless DOM refactoring?

**Answer:** Query by accessible role/name or use a public harness instead of relying on deep CSS selectors.

### 18. How do you test accessible names?

**Answer:** Use accessibility-aware queries and assert role and name, then complement automated checks with manual assistive-technology review.

### 19. Why is a screenshot assertion insufficient for accessibility?

**Answer:** It cannot verify keyboard operation, accessible names, reading order, or announcements.

### 20. How would you test responsive behavior?

**Answer:** Use browser tests at representative viewport widths and verify content, controls, focus, and layout rather than only a single screenshot.

## Browser interaction and accessibility

### 21. How do you test route-based lazy loading?

**Answer:** Verify navigation and rendered behavior, and use build or network checks when the chunk boundary itself matters.

### 22. How do you test that a guard redirects without brittle router internals?

**Answer:** Drive navigation through the router, then assert the final URL and visible destination behavior.

### 23. How can tests cover a form’s server validation errors?

**Answer:** Mock the service response, submit valid client data, and assert the relevant field or summary displays an actionable error.

### 24. How would you test a file upload component?

**Answer:** Provide a File in a controlled change event, verify validation and request metadata, and avoid relying on an operating-system picker.

### 25. What is contract testing for a frontend API client?

**Answer:** It verifies that request/response shapes and semantics match an agreed API contract, often with schema or consumer-provider checks.

### 26. How can property-based testing help frontend code?

**Answer:** It generates many input combinations to check invariants, useful for parsers, formatters, and validation logic.

### 27. How do you choose between unit, component, and browser tests?

**Answer:** Use the narrowest level that proves the behavior; add browser tests for platform integration, navigation, and critical journeys.

### 28. How do you keep end-to-end tests reliable when a backend is slow?

**Answer:** Use deterministic test data and environments, wait on observable conditions, and reserve a small set of true end-to-end checks.

### 29. Why should E2E tests avoid fixed-time sleeps?

**Answer:** They wait too long when the app is fast and fail when it is slow; wait for a user-visible or network state instead.

### 30. How would you investigate a test that passes alone but fails in the full suite?

**Answer:** Look for shared state, leaked timers, global mocks, environment mutations, unclosed subscriptions, and order-dependent fixtures.

## Reliability and test design

### 31. What is test isolation?

**Answer:** Each test starts from known state and cleans up side effects so its result does not depend on neighboring tests.

### 32. How can a test suite become too slow?

**Answer:** Excessive broad setup, real network/time, redundant UI mounting, and serial work can add cost; profile before optimizing.

### 33. How do you keep mock behavior realistic?

**Answer:** Model only the contract the unit relies on, including errors and async timing, and avoid duplicating production implementation.

### 34. How should you test loading state around a request?

**Answer:** Use a controllable Subject or Promise: assert loading before resolution and verify it clears after success, failure, and cancellation.

### 35. What can cause false confidence from high line coverage?

**Answer:** Code may execute without checking meaningful outcomes, edge cases, security constraints, or failure paths.

### 36. How do Angular test runner choices affect tests?

**Answer:** Vitest is the current CLI default for new projects, while existing projects may use Karma; keep assertions and app behavior largely runner-independent.

### 37. How would you migrate a Karma/Jasmine suite to Vitest?

**Answer:** Inventory runner-specific APIs, migrate setup and mocks incrementally, and compare behavior while keeping the same meaningful assertions.

### 38. How do you test a signal-based component without coupling to implementation?

**Answer:** Update its public inputs or user interaction, run rendering, and assert the resulting accessible DOM.

### 39. Why verify no unexpected HTTP requests after each test?

**Answer:** It catches unawaited work and accidental extra calls that otherwise may leak between tests.

### 40. What is a visual regression test best suited for?

**Answer:** It catches unintended visual changes across known browsers and viewports, but should complement semantic and interaction tests.

## References

- [Angular testing overview](https://angular.dev/guide/testing)
- [Angular component testing basics](https://angular.dev/guide/testing/components-basics)
- [Angular testing services](https://angular.dev/guide/testing/services)
- [Angular HTTP testing](https://angular.dev/guide/http/testing)
- [Angular component harnesses](https://angular.dev/guide/testing/component-harnesses-overview)
- [Vitest documentation](https://vitest.dev/guide/)
