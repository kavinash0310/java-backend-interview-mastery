# Angular and Frontend Testing: Easy Interview Questions and Answers

**40 questions.** Prefer assertions about user-visible outcomes and public contracts; use mocks to control boundaries, not to recreate the implementation.

## Testing foundations and setup

### 1. Why do frontend applications need tests?

**Answer:** Tests catch regressions, document expected behavior, and make changes safer before users encounter defects.

### 2. What is a unit test?

**Answer:** It checks a small unit of logic in isolation, such as a formatter or service method.

### 3. What is a component test?

**Answer:** It checks a component’s class and rendered template together, including user interaction and visible output.

### 4. What is an integration test?

**Answer:** It checks whether multiple units work together across a meaningful boundary such as routing or HTTP.

### 5. What is an end-to-end test?

**Answer:** It drives the application through a browser-like user journey against a running app or test environment.

### 6. What does Angular TestBed do?

**Answer:** It creates an Angular testing environment with providers and imports so tests can instantiate Angular code.

### 7. What is a ComponentFixture?

**Answer:** It wraps a created component and exposes its instance, host element, and change-detection controls.

### 8. What does fixture.detectChanges do?

**Answer:** It runs change detection for the test fixture so bindings and lifecycle work are reflected in the DOM.

### 9. What is a test assertion?

**Answer:** It checks that an actual result matches an expected result, and fails the test when they differ.

### 10. What is a test double?

**Answer:** It is a substitute for a dependency, such as a stub, fake, spy, or mock.

## Components services and HTTP

### 11. What is a stub?

**Answer:** A stub provides controlled responses so a test can focus on the unit under test.

### 12. What is a spy?

**Answer:** A spy records calls to a function and can optionally return controlled values.

### 13. What is a mock?

**Answer:** A mock is a test double configured to verify expected interactions as well as provide behavior.

### 14. Why mock an HTTP backend in a unit test?

**Answer:** It avoids real network dependency and lets the test control request matching, timing, and response data.

### 15. What is a flaky test?

**Answer:** It passes and fails unpredictably due to timing, shared state, environment, or external dependencies.

### 16. What is a test fixture?

**Answer:** It is the setup that provides the component, data, and environment required for a test case.

### 17. What does beforeEach commonly do?

**Answer:** It creates fresh setup before each test, reducing accidental state sharing.

### 18. What does afterEach commonly do?

**Answer:** It cleans up resources or verifies that a test left no unexpected requests or timers.

### 19. What is code coverage?

**Answer:** It measures which parts of source code were exercised by a test run; coverage alone does not prove quality.

### 20. What is line coverage?

**Answer:** It counts source lines executed during tests, but says little about whether important outcomes were asserted.

## Browser interaction and accessibility

### 21. What is a regression test?

**Answer:** It captures a previously broken behavior so a fix stays in place through future changes.

### 22. What is a snapshot test?

**Answer:** It compares rendered output or serialized data to a saved snapshot, which must be reviewed when updated.

### 23. What is a test runner?

**Answer:** It discovers and executes tests, reports results, and often provides mocking and assertion tools.

### 24. What is Vitest?

**Answer:** It is a JavaScript and TypeScript test runner with a Jest-like API; current Angular CLI projects use it by default.

### 25. What is jsdom?

**Answer:** It is a JavaScript implementation of browser-like DOM APIs used for tests without a full browser.

### 26. What is a browser-based test?

**Answer:** It runs tests in a real browser engine, which can catch behavior a simulated DOM does not model.

### 27. What is an accessible query in a component test?

**Answer:** It finds elements by role, label, or visible text, reflecting how users identify interface controls.

### 28. Why is querying only by CSS class brittle?

**Answer:** A class is an implementation detail that may change while the user-facing behavior remains the same.

### 29. What is a test name supposed to describe?

**Answer:** It should state the behavior or outcome under test, such as “shows an error when save fails.”

### 30. What is a happy-path test?

**Answer:** It checks the expected successful flow with valid input and successful dependencies.

## Reliability and test design

### 31. Why test error states?

**Answer:** Users need clear feedback and the application must recover or remain consistent when dependencies fail.

### 32. What does an asynchronous test need to do?

**Answer:** It must wait for the work being tested to finish before asserting the result.

### 33. What is a promise-based test?

**Answer:** It returns or awaits a Promise so the runner knows when asynchronous work has completed.

### 34. What is a fake timer?

**Answer:** It replaces real time with controllable time so delay-based behavior can be tested quickly and reliably.

### 35. What is a test environment?

**Answer:** It is the runtime and configuration in which tests execute, including DOM, globals, and provider setup.

### 36. What does an Angular service test usually focus on?

**Answer:** It checks the service’s public behavior with dependencies configured or replaced as needed.

### 37. What is a component harness?

**Answer:** It is a stable API for interacting with a component through user-like actions and observations.

### 38. Why test the rendered DOM as well as the component class?

**Answer:** The template can have wiring or accessibility defects that the class state alone will not reveal.

### 39. What should an HTTP test assert besides the response value?

**Answer:** It can assert request method, URL, parameters, headers, and that no unexpected requests remain.

### 40. What is a smoke test?

**Answer:** It quickly checks that the most important application path starts and works at a basic level.

## References

- [Angular testing overview](https://angular.dev/guide/testing)
- [Angular component testing basics](https://angular.dev/guide/testing/components-basics)
- [Angular testing services](https://angular.dev/guide/testing/services)
- [Angular HTTP testing](https://angular.dev/guide/http/testing)
- [Angular component harnesses](https://angular.dev/guide/testing/component-harnesses-overview)
- [Vitest documentation](https://vitest.dev/guide/)
