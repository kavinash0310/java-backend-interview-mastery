# Angular and Frontend Testing

Practice service, component, HTTP, accessibility, and browser testing for modern Angular applications using TestBed, controlled dependencies, and the current CLI test setup.

## What you will practice

- Choose a test level that proves behavior without unnecessary setup.
- Test Angular components, services, forms, routes, and HTTP requests.
- Use stable, accessible user-facing queries and controlled asynchronous work.
- Recognize flaky tests, weak coverage, and over-mocking.

## Topic map

- [Testing foundations and setup](easy-interview-questions.md#testing-foundations-and-setup) — questions 1–10
- [Components services and HTTP](easy-interview-questions.md#components-services-and-http) — questions 11–20
- [Browser interaction and accessibility](easy-interview-questions.md#browser-interaction-and-accessibility) — questions 21–30
- [Reliability and test design](easy-interview-questions.md#reliability-and-test-design) — questions 31–40


## Quick example

Controlled Angular HTTP test:

```ts
TestBed.configureTestingModule({
  providers: [provideHttpClient(), provideHttpClientTesting()]
});

const http = TestBed.inject(HttpTestingController);
service.search("java").subscribe(result => expect(result.items.length).toBe(1));
http.expectOne("/api/search?q=java").flush({ items: ["Angular"] });
http.verify();
```

## Question sets

- [Easy — 40 questions](easy-interview-questions.md)
- [Medium — 40 questions](medium-interview-questions.md)
- [Hard — 40 questions](hard-interview-questions.md)
- [Tricky — 40 questions](tricky-interview-questions.md)

## Current test setup

New Angular CLI projects currently use Vitest with a simulated DOM environment. Existing projects may still use Karma/Jasmine; focus on the behavior being tested and check the project's configured runner before using runner-specific helpers.

## Study tip

For each scenario, state the behavior, choose the narrowest useful test boundary, control async work, and assert what a user or caller observes.

## References

- [Angular testing overview](https://angular.dev/guide/testing)
- [Angular component testing basics](https://angular.dev/guide/testing/components-basics)
- [Angular testing services](https://angular.dev/guide/testing/services)
- [Angular HTTP testing](https://angular.dev/guide/http/testing)
- [Angular component harnesses](https://angular.dev/guide/testing/component-harnesses-overview)
- [Vitest documentation](https://vitest.dev/guide/)
