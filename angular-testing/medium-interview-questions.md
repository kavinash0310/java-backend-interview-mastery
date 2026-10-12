# Angular and Frontend Testing: Medium Interview Questions and Answers

**40 questions.** Prefer assertions about user-visible outcomes and public contracts; use mocks to control boundaries, not to recreate the implementation.

## Testing foundations and setup

### 1. How do you create a component fixture in Angular?

**Answer:** Configure TestBed with the component and dependencies, createComponent, then run change detection before checking rendered output.

### 2. What is the difference between testing a service directly and through TestBed?

**Answer:** Direct construction is simple for pure logic; TestBed is useful when Angular DI or providers are part of the behavior.

### 3. How do you replace an Angular dependency in a test?

**Answer:** Provide a stub or spy with useValue, useClass, or an appropriate provider override.

### 4. When should a real dependency be used in a component test?

**Answer:** Use it when its integration is part of the behavior being verified; replace network, nondeterministic, or unrelated heavy dependencies.

### 5. How do you test an Angular input?

**Answer:** Create the component, set the input through the supported fixture or host binding, run change detection, and assert the resulting UI.

### 6. How do you test an output event?

**Answer:** Subscribe or bind a host listener, trigger the user action, and assert the emitted payload or parent-visible behavior.

### 7. Why prefer interacting through the DOM in a component test?

**Answer:** It verifies template wiring and user-facing behavior rather than only mutating internal fields.

### 8. How do you simulate a text input event?

**Answer:** Set the input element’s value and dispatch an input event, then run change detection if the test setup requires it.

### 9. How do you test a button click?

**Answer:** Find the button by role or accessible name, trigger a click, and assert the resulting behavior.

### 10. How do you test conditional template rendering?

**Answer:** Change the relevant state, run or await rendering, and assert that the expected element appears or disappears.

## Components services and HTTP

### 11. How can you make an Angular service test isolated?

**Answer:** Provide fresh instances and reset mocks before each test; avoid mutable module-level test state.

### 12. How do you verify a dependency method was called with the correct value?

**Answer:** Use a spy and assert its call count and arguments, while also checking the user-visible outcome when appropriate.

### 13. When is asserting a spy call not enough?

**Answer:** When behavior matters more than implementation; assert the returned result or rendered state to avoid over-coupled tests.

### 14. How does provideHttpClientTesting help?

**Answer:** It replaces the network backend with a controllable testing backend and exposes HttpTestingController for expectations and flushes.

### 15. How do you test a service’s GET request?

**Answer:** Subscribe to the service result, expect the request with the correct URL and method, flush a response, then assert the result.

### 16. How do you test an HTTP error response?

**Answer:** Flush the expected request with an error status, then assert the service or component’s mapped error behavior.

### 17. Why call verify on HttpTestingController?

**Answer:** It ensures no unexpected or unflushed requests remain after the test.

### 18. What is the provider order when testing configured HttpClient features?

**Answer:** Provide HttpClient configuration first and provideHttpClientTesting afterward, so the testing backend overrides the real backend.

### 19. How do you test an HTTP interceptor?

**Answer:** Configure the interceptor and testing backend, make a request, inspect modified headers or URL, and flush a response.

### 20. What does TestBed.inject do?

**Answer:** It retrieves a configured dependency from the testing injector, useful for services and testing utilities.

## Browser interaction and accessibility

### 21. What is a component’s element injector?

**Answer:** It is the injector associated with the component or element; use it when the component provides its own dependency.

### 22. How do you test a reactive form validator?

**Answer:** Set control values, trigger validation as needed, and assert validity or the expected error object.

### 23. How do you test async validation?

**Answer:** Await or flush the asynchronous result, then assert the final pending and error states rather than checking immediately.

### 24. How do you test that invalid form input is not submitted?

**Answer:** Enter invalid values, trigger submit, and assert that the submit handler or service call was not invoked.

### 25. How can you test navigation in Angular?

**Answer:** Configure routes and a router testing harness or router providers, navigate, and assert the activated component or URL.

### 26. What should a route guard test cover?

**Answer:** Allowed users, denied users, redirects, and relevant async or error behavior.

### 27. How do you test a component that uses an Observable?

**Answer:** Provide a controlled Observable or Subject, subscribe through the component path, emit test values, and assert the DOM update.

### 28. How should a test clean up a manual subscription?

**Answer:** Destroy the fixture or unsubscribe in teardown, and verify lifecycle cleanup where it is part of the contract.

### 29. How do fake timers help with debounce logic?

**Answer:** Advance time deterministically to just before and after the debounce window, then verify emitted requests.

### 30. What is the risk of using real sleeps in tests?

**Answer:** They make suites slower and flaky because timing varies across machines and CI load.

## Reliability and test design

### 31. What is the difference between unit and integration test scope?

**Answer:** Unit tests isolate logic with doubles; integration tests intentionally exercise interactions among real or realistic components.

### 32. What makes a test deterministic?

**Answer:** It controls time, data, dependencies, and environment so the same code produces the same result each run.

### 33. What is a test pyramid?

**Answer:** It is a guideline to have many fast focused tests and fewer slower broad tests, balancing confidence and maintenance cost.

### 34. How should a test handle a rejected Promise?

**Answer:** Await it with an expected rejection assertion or handle the error deliberately so the runner does not report an unhandled rejection.

### 35. How can tests verify loading and error states?

**Answer:** Use controllable asynchronous dependencies to assert pending UI first, then success or failure UI after resolving the dependency.

### 36. Why use component harnesses?

**Answer:** They offer a stable user-facing API and reduce coupling to private DOM structure or CSS classes.

### 37. How do you test keyboard access to a control?

**Answer:** Focus it, dispatch the relevant keyboard action, and verify the same outcome expected from keyboard activation.

### 38. What is the purpose of a test double for a clock?

**Answer:** It makes time-dependent behavior deterministic and avoids relying on wall-clock delays.

### 39. How should coverage gaps guide test additions?

**Answer:** Add cases for untested important decisions, boundaries, and failures rather than chasing a percentage alone.

### 40. What is a CI test run?

**Answer:** It executes the project’s test suite in automation for each change, exposing failures before merge or deployment.

## References

- [Angular testing overview](https://angular.dev/guide/testing)
- [Angular component testing basics](https://angular.dev/guide/testing/components-basics)
- [Angular testing services](https://angular.dev/guide/testing/services)
- [Angular HTTP testing](https://angular.dev/guide/http/testing)
- [Angular component harnesses](https://angular.dev/guide/testing/component-harnesses-overview)
- [Vitest documentation](https://vitest.dev/guide/)
