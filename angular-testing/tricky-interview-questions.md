# Angular and Frontend Testing: Tricky Interview Questions and Answers

**40 questions.** Prefer assertions about user-visible outcomes and public contracts; use mocks to control boundaries, not to recreate the implementation.

## Testing foundations and setup

### 1. Does TestBed.createComponent automatically render every binding immediately?

**Answer:** Not necessarily. Run change detection or use the test environment’s automatic detection before asserting the rendered view.

### 2. Does fixture.detectChanges only update the DOM?

**Answer:** It can also trigger lifecycle hooks and other Angular work, so repeated calls may affect the component under test.

### 3. Can a unit test pass while the Angular template is broken?

**Answer:** Yes, if it tests only the class; compile and exercise a fixture to cover the template.

### 4. Does a spy automatically call the original method?

**Answer:** Not always. Spy defaults depend on the framework and configuration; explicitly set a return value or call-through behavior.

### 5. Is a mock the same thing as a stub?

**Answer:** They are both test doubles; a stub supplies behavior, while a mock typically also verifies expected interactions.

### 6. Does 100 percent line coverage mean every branch was tested?

**Answer:** No. Line coverage can be complete while conditional outcomes and important behavior remain untested.

### 7. Does a test assertion inside a detached callback always fail the test when it fails?

**Answer:** Not necessarily if the runner does not await or observe that callback; return or await asynchronous work.

### 8. Can an unreturned Promise make an async test pass too early?

**Answer:** Yes. The runner may finish before the Promise settles; return or await it.

### 9. Are fake timers interchangeable with Angular fakeAsync utilities?

**Answer:** No. They come from different test environments and control different scheduling layers; use the project’s supported approach.

### 10. Will jsdom behave exactly like Chrome?

**Answer:** No. It simulates many DOM APIs but does not implement every layout, rendering, media, or browser behavior.

## Components services and HTTP

### 11. Does dispatching an input event always update Angular’s form model synchronously?

**Answer:** It depends on event binding and change detection; dispatch the right event and run or await the configured rendering cycle.

### 12. Is clicking a disabled button a valid success path?

**Answer:** No. A disabled control should not trigger its action; test that it remains unavailable and no request is sent.

### 13. Does clicking an element by CSS class prove it is accessible?

**Answer:** No. It proves a selector can find it; query by role and accessible name and test keyboard behavior.

### 14. Can a component harness test an internal private implementation detail?

**Answer:** It can technically query internals, but doing so defeats the harness’s stability benefit; expose user-centered methods.

### 15. Does TestBed.inject always retrieve the dependency used by the component?

**Answer:** No. A component-level provider can shadow the test module’s provider; inspect the component injector.

### 16. Will provideHttpClientTesting configure HttpClient features automatically?

**Answer:** No. Configure HttpClient features first when needed, then provide the testing backend.

### 17. Can HttpTestingController.flush be called before the request exists?

**Answer:** No. The test must trigger the code and capture the matching request before flushing it.

### 18. Does HttpTestingController.verify ensure all expected requests got a response?

**Answer:** It detects unmatched outstanding requests; also assert expected requests and flush them deliberately.

### 19. Should a test assert exact private method call counts?

**Answer:** Only when that interaction is part of the contract; prefer observable results to reduce coupling to refactors.

### 20. Can a snapshot prove that a button works?

**Answer:** No. It captures rendered output, not activation, keyboard behavior, routing, or side effects.

## Browser interaction and accessibility

### 21. Should every test share one global mutable mock object?

**Answer:** Usually not. Reset it or create fresh mocks per test to prevent call history and behavior leaking.

### 22. Can an end-to-end test replace every integration with mocks?

**Answer:** Then it is not exercising the real end-to-end path; choose the test level based on the boundary being verified.

### 23. Does an E2E test become reliable by adding a long sleep?

**Answer:** No. Fixed sleeps hide synchronization problems and still fail under slow or variable environments.

### 24. Can a test be deterministic but still wrong?

**Answer:** Yes. It can consistently assert the wrong behavior; validate test expectations against product requirements.

### 25. Does a flaky test always indicate a production race condition?

**Answer:** No. Test timing, global state, network dependencies, or runner setup can cause flakiness even when app logic is sound.

### 26. Can testing a private method guarantee a component works?

**Answer:** No. The method may work while event wiring, rendering, accessibility, or user flow is broken.

### 27. Is a service test without TestBed always invalid?

**Answer:** No. Pure services can be directly constructed; use TestBed when Angular injection behavior matters.

### 28. Will a real dependency always make tests more trustworthy?

**Answer:** No. External calls and unrelated behavior reduce determinism; use real collaborators only when their integration matters.

### 29. Does mocking HttpClient test that the backend endpoint exists?

**Answer:** No. It tests client behavior against a simulated response; contract or integration testing is needed for server compatibility.

### 30. Can an HTTP test prove a request is secure in production?

**Answer:** No. It can verify client configuration, but deployment policies, TLS, authorization, and server behavior need separate checks.

## Reliability and test design

### 31. Does a passing test prove a feature has no accessibility defect?

**Answer:** No. Automated tests catch some issues; manual keyboard and assistive-technology review remains important.

### 32. Does screen reader output get tested by a DOM role query?

**Answer:** Not completely. An accessible query checks exposed semantics, not the full experience across real screen readers.

### 33. Does a visual test catch offscreen keyboard focus?

**Answer:** Not necessarily. Use keyboard interaction tests and inspect focus visibility and order.

### 34. Can a test assert a loading indicator immediately after triggering an async request?

**Answer:** Only if the application exposes pending state synchronously; otherwise await the observable state transition.

### 35. Does destroying a fixture automatically cancel every external timer or event listener?

**Answer:** Only framework-managed resources are cleaned; code-owned resources still need correct teardown.

### 36. Does takeUntilDestroyed need to be tested by inspecting the operator call?

**Answer:** No. Verify externally that work stops after destruction or that resources are released.

### 37. Can two tests safely run in parallel if they change global mocks?

**Answer:** Not without isolation; shared globals can race, so isolate modules or avoid global mutable state.

### 38. Should test names describe implementation steps?

**Answer:** Prefer the behavior and expected outcome, so the test remains clear if the implementation changes.

### 39. Can a high mutation score compensate for missing user scenarios?

**Answer:** No. Mutation testing measures test sensitivity to code changes; it does not define product coverage or user needs.

### 40. Does adding more assertions always improve a test?

**Answer:** No. Assertions should verify meaningful behavior; unrelated assertions can make failures harder to diagnose.

## References

- [Angular testing overview](https://angular.dev/guide/testing)
- [Angular component testing basics](https://angular.dev/guide/testing/components-basics)
- [Angular testing services](https://angular.dev/guide/testing/services)
- [Angular HTTP testing](https://angular.dev/guide/http/testing)
- [Angular component harnesses](https://angular.dev/guide/testing/component-harnesses-overview)
- [Vitest documentation](https://vitest.dev/guide/)
